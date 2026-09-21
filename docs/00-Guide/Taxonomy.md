# Category Taxonomy · 分类与嵌套规则

[← 指南](README.md) · [Home](../Home.md)

## 1. 知识组织模型

**主目录（where）**按研究对象而非提问日期；**副标签（what）**表达可跨领域的机制；**事件卡（when）**记录特定时点的观察；**来源（why trust）**记录可追溯依据。避免把“AI、A股、2026-09-21、风险”同时作为平行文件夹造成重复。

| 一级 category | 二级 category | 典型三级子类/词条 |
| --- | --- | --- |
| 01-Foundations | Economics / Finance / Behavioral / History | Scarcity-Rent；金融概念、行为偏差 |
| 02-Macro | China / Global / Monetary / Fiscal / External | China/Government-Finance；财政、货币、国际收支 |
| 03-Markets | Equities / Fixed-Income / FX / Derivatives / Cross-Asset | Equities/A-Shares/Industry-Rotation；Fixed-Income/Sovereign-Debt |
| 04-Sectors | AI-Infrastructure / Biotechnology / Manufacturing / Energy / Consumption | AI-Infrastructure/Optical-Communications；Biotechnology/HK-Biotech |
| 05-Risk | Contagion / Liquidity / Credit / Network / Crisis | Contagion/Second-Order-Effects；信用与网络脆弱性 |
| 06-Quant-Data | Data-Engineering / Alpha / Portfolio / Backtesting / Validation | Yahoo-Finance-Pipeline；Formulaic-Alphas |
| 07-Methods | Evidence / Workflow / Modeling / Reviews | 来源交叉核验、反证、指标设计 |
| 08-Events | YYYY / MM | YYYY-MM-DD-event-slug.md（唯一事件卡） |
| 09-Weekly | YYYY | YYYY-Www.md（周度导航，不代替主题分析） |
| 10-Sources | Books / Papers / Official / Data | Source-Ledger.md（元数据与可公开书目） |
| 90-Archive | YYYY | 仅存公开许可的历史资料索引；原始私聊不自动发布 |

## 2. 入库决策树

1. 属于定义、长期机制或研究模型？放进 01/02/03/04/05/06/07 对应的 **canonical page**。
2. 属于某天的价格、公告、会议或行情解释？写 `08-Events/YYYY/MM/YYYY-MM-DD-*.md`；链接到主题页，并注明观测日期和未证实信息。
3. 属于同一周多个话题的整理？写 `09-Weekly/YYYY/YYYY-Www.md`，只保留提要、变化与跳转。
4. 属于论文、书籍、数据链接？只在 `10-Sources` 登记书目与可合法公开的网址；任何引用仍需要在主题页附近标注。
5. 新话题同时属于两个目录？依据**主要研究对象**选一个主归属，另一个目录的 README 增加链接；不要镜像复制正文。

## 3. 文件命名与层级

最大推荐深度为 `领域/子市场或行业/研究对象/页面.md`；层级确有必要才拓展。路径用英文字母、数字及短横线，阅读标题用中文；固定概念用短名，事件用 ISO 日期。每个实际创建的分类目录含 `README.md` 导航。未形成有效内容的类别保留在 taxonomy 中，不提前创建空文件夹（Git 不保存空目录）。

## 4. Wiki 风格要求

每页包含标题、面包屑导航、页面元数据（如日期、状态、主类别、跨领域标签）、正文、相关词条、证据/来源与更新记录；相关词条**双向链接**。标准 GitHub 仓库中的 `_Sidebar.md` 不会自动变成 GitHub Wiki 的侧栏：主页和各层 README 是实际可用的导航。若以后迁移到独立 GitHub Wiki，可复用 `_Sidebar.md` 内容，但需要单独同步 Wiki 仓库。