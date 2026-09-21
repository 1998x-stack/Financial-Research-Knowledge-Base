# Formulaic Alphas · 公式型因子研究入口

[← 量化与数据](../README.md) · [数据工程](../Data-Engineering/Yahoo-Finance-Pipeline.md)

**主类别**：06-Quant-Data / Alpha  
**状态**：论文书目与研究计划；未运行本地回测。  
**标签**：#因子 #截面排序 #回测 #交易成本

## 文献线索

项目附件中有 Zura Kakushadze（2015），*101 Formulaic Alphas*。这篇论文提出公式型量化信号及相关实证讨论，适合作为数据字段、运算符、延迟机制、行业中性化和交易成本的**复现研究起点**。本仓库不复制论文附录中的整套受版权保护公式；记录正式书目信息见 [来源台账](../../10-Sources/Source-Ledger.md)。

## 复现协议

按逐日可得性核查 `open/high/low/close/volume/VWAP`、公司行为和行业分类时间戳；分开定义 delay-0 与 delay-1 的实际下单时刻；将缺失数据、涨跌停、停牌、滑点、费用、容量和样本外测试纳入结果表。论文样本结果不能被直接当作今天 A 股或港股可实现收益。

## 相关

[数据工程](../Data-Engineering/Yahoo-Finance-Pipeline.md) · [研究规范](../../00-Guide/Editorial-Policy.md)