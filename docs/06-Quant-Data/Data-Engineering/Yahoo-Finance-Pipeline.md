# Yahoo Finance历史数据工程：十年数据、Schema与增量采集设计

[← 量化与数据](../README.md) · [公式型Alpha](../Alpha/Formulaic-Alphas.md) · [研究规范](../../00-Guide/Editorial-Policy.md)

> **性质**：详细技术设计文档，不是已交付的可运行工程。**更新**：2026-09-21。2026-08-27可见历史需求包括约10年数据、SQLite、Loguru、线程、nohup、重试和单元测试；旧对话附件与完整代码尚未归档。实际可用数据、速率限制与授权边界须核对服务提供方当期条款，不假定能无限制下载‘所有’数据。

## 1. 系统边界与成功定义

采集对象分为证券标识与历史映射、日线OHLCV、公司行为、财务报表、标的元数据、交易日历、汇率及数据来源日志。不同交易所、指数、ETF、基金、加密资产的交易日和调整方法不同，必须在采集配置中逐类声明。成功标准：可断点续传、同一输入幂等、原始响应可追踪、规范化数据能通过一致性校验、每次下游研究能锁定不可变数据版本，采集错误可复现且不会悄悄覆盖有效历史值。

## 2. 逻辑Schema与主键

| 表 | 建议主键/唯一约束 | 关键字段 |
| --- | --- | --- |
| instrument | instrument_id | asset_type, exchange_mic, quote_ccy, timezone, first_seen, last_seen |
| provider_symbol_history | provider, symbol, valid_from | instrument_id, valid_to, mapping_evidence, retrieved_at |
| daily_bar | instrument_id, session_date, data_version | open, high, low, close, volume, quote_ccy, adjusted_close, provider, available_at |
| corporate_action | instrument_id, ex_date, action_type, source_event_id | split_ratio, cash_dividend, record_date, pay_date, announced_at |
| fundamental_statement | instrument_id, period_end, statement_type, item_code, reported_at, version | amount, currency, fiscal_period, source_ref |
| fetch_run | run_id | request_fingerprint, start_at, end_at, status, worker_id, code_version |
| fetch_attempt | run_id, request_key, attempt_no | http_status, error_class, retry_after, elapsed_ms, response_hash |
| raw_manifest | content_sha256 | provider, endpoint, fetched_at, schema_version, storage_path, licensing_flag |
| data_version | version_id | asof_time, source_run_ids, validation_report, commit_sha |

上述是研究提案，不冒充任何当前供应商正式API字段。标的符号可能重用或更名，因此不要仅以ticker为所有资产的永久主键。`session_date`表示交易所本地交易日，不等于UTC自然日；财报的`period_end`和首次可得的`reported_at`必须分离。公司行为调整规则需有独立版本，避免供应商回溯调整导致研究结果静默变化。

## 3. 分层数据处理流程

`Universe发现 → request计划 → 速率受控拉取 → 原始响应及哈希留痕 → 字段解析 → 标的/币种/时区映射 → 公司行为检查 → 规范化入库 → 质量门禁 → 快照发布`。原始层保留供应商原值；规范层生成明确字段含义；研究层只能读取通过质量门禁且锁定版本的数据。不要用后来披露的财报覆盖过去信息可得性，亦不要在回测中使用今天的指数成分代替历史股票池。

## 4. 并发与SQLite单写者设计

网络I/O可采用有限线程池，但SQLite写入应集中到单一writer线程/进程，其他worker只负责请求、解析和产生有界队列任务。启用WAL与适当busy_timeout以改善读写共存，但WAL并不意味着多写者可无限并行；写事务应短、批量可控、按request_key原子upsert。对写入冲突设置有界退避和日志，队列达到上限时阻止继续抓取，避免内存无限增长。线程池大小由供应商限流、稳定性与本机资源测试确定，不将线程数越多视作性能越好。

## 5. 断点、重试和失败分类

用`request_fingerprint = hash(provider, endpoint, normalized_params, window, schema_version)`标识请求；拉取完成须在同一事务内提交结果记录与状态。超时、连接失败和部分5xx可按指数退避与抖动重试；429应遵循明确的Retry-After并降低并发；4xx鉴权/参数错误通常不可盲目重试；数据不存在、空结果、字段变更、解析失败和数据库冲突要分不同状态。最大重试次数和失败请求的死信队列必须可审计。停止信号后等待已在途写入完成，并从上次成功水位续抓，防止数据缺口。

## 6. 数据质量断言

价格非负且OHLC内部符合定义、成交量与市场单位一致；除停牌和异常交易日外，单个交易日重复行禁止出现。拆分和派息前后检查调整价格与原价格的关系；复权价可能随之后的公司行为重述，不允许把新调整序列当成历史实时价格。财报需核对会计期间、币种、原币金额、累计/单季及重述；无法核实时置为待核验。历史收益极值不能仅靠阈值删除，先区分公司行为、币种错误、符号映射和真实行情。

## 7. 项目模块与测试矩阵

建议模块：`config/`, `providers/`, `universe/`, `normalizers/`, `storage/`, `scheduler/`, `validators/`, `exports/`, `tests/`。Loguru日志关联run_id、instrument_id、request_key、attempt、duration和重试原因；严禁输出凭证或含敏感字段的完整响应。单测覆盖时区跨日、符号重用、空响应、拆分调整、字段缺失、重复upsert；集成测用受控模拟HTTP服务演练429/超时/断连/半程中止和恢复；并发压测检验单写队列无丢失、提交前失败不被标记成功；端到端测核对快照记录数、哈希和可复现查询。`nohup`仅是进程启动方式，须另有PID、结构化日志、退出码和恢复说明。

## 8. 发布和合规边界

Git仓库保存代码、Schema、合成测试数据和可公开元数据，不提交API密钥、未经许可的供应商完整历史数据或私人研究输入。许可证、数据保留规则与API调用配额以当期正式服务条款为准。本文没有运行爬虫、创建SQLite数据库或运行测试；实施阶段每项测试必须附实际命令、结果与commit。

**关联**：[Alpha复现](../Alpha/Formulaic-Alphas.md) · [编辑规范](../../00-Guide/Editorial-Policy.md)

**修订**：2026-09-21 由需求提纲扩展为数据库设计、幂等、并发、质量与测试的技术章节。