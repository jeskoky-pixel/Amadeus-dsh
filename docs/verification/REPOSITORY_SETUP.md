# 仓库初始化检查

日期：2026-10-24（Asia/Shanghai）。范围：仓库、文档和 Git 同步；不包含产品功能验收。

## 已检查

- 9 个本地仓库各有 README.md 与 HANDOFF.md，未残留模板占位符。
- 新编写的 staged 文档通过 git diff --cached --check；原始规格副本单独保留，不纳入格式改写。
- 工作区规格副本的 SHA256 与用户提供文件一致。
- 检查 staged 文件清单，仅包含所需文档、目录清单与忽略规则。
- 准备的文档未发现 GitHub/OpenAI 令牌或私钥形状标记；该模式扫描不代表完整安全证明。
- 主仓库 git check-ignore 确认 plugins/ 不被跟踪。
- 下列 8 个插件通过 git ls-remote 核对：远端 main SHA 等于本地 HEAD，工作区干净。

## 插件交付版本

| 仓库 | 已核对本地与远端 SHA | 状态 |
| --- | --- | --- |
| amadeus-android | 587a28f118e6720ff5a239484c07f8458826bfa1 | clean / synced |
| amadeus-bilibili | 9472e4d3fcdbf746fe6202ebb73bac772bb4c2a9 | clean / synced |
| amadeus-browser | a620ff5090855eb7c0d4cdef420caf4a5fc1e89c | clean / synced |
| amadeus-games | 034b82c5832fbc92e9ca06283bbef4d799ba4635 | clean / synced |
| amadeus-life | b7faeaf20f28782171932c2164706212f3389fb0 | clean / synced |
| amadeus-memory | 35154407a30656875e8818524d64845c6a6b8024 | clean / synced |
| amadeus-qq | e97ae4bb5c043ea2f9b60a1756d7d9ad5b1621c6 | clean / synced |
| amadeus-state | fbfb8d671e43e855460f5c880a2b7684a4755dbe | clean / synced |

主仓库最终 SHA 见交付消息或 git log，提交后单独核实远端一致。本表记录的是文档初始化版本，不是插件发布或已验证的运行组合。

## 未运行

DSH / QQ / NapCat 实际版本基线、插件安装与升级、平台联调、连续对话、记忆、权限、降级和活动验收均未运行。未启动 DSH 开发 worker。
