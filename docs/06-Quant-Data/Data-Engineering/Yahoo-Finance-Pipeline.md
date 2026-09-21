# Yahoo Finance · 历史数据采集与存储

[← 量化与数据](../README.md)

**主类别**：06-Quant-Data / Data-Engineering  
**状态**：历史需求索引，代码及测试产物未归档。  
**标签**：#YahooFinance #SQLite #Loguru #ETL #Schema #Multithread

## 项目原始需求索引

2026-08-27 的可见项目历史包含：获取可接触到的约十年 Yahoo Finance 数据、设计数据库表和 Schema、使用 Python/SQLite/Loguru、处理超时/冲突/错误/警告、建立单元测试以及多线程和 nohup 运行方式。此处只整理需求，不能声称当时已开发、测试或交付 ZIP。

## 待设计模块

`Universe/Identifiers → Raw data 与数据来源日志 → Corporate actions / Currency / Calendar → Normalized OHLCV/Financials → Validation → Incremental ingestion → Query/Export`。

数据库建议至少区分 `instrument`、`provider_symbol_history`、`daily_bar`、`corporate_action`、`fundamental_statement`、`fetch_run`、`fetch_error` 和 `data_version`；真实字段、授权边界及数据供应商接口需在独立设计文档核验后确定。

## 实现核查表

记录 API 限流和退避、线程竞争和 SQLite 单写者约束、幂等 upsert、跨时区时间戳、复权与币种、一致性检查、断点续传、隐私及服务条款、回测的 point-in-time 数据要求。

## 关联

[公式型 Alpha](../Alpha/Formulaic-Alphas.md) · [验证规范](../../07-Methods/README.md)