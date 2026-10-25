# Life / Memory / State 三插件产品化验收

> **2026-10-26 更新：**本文是 2026-10-25 的阶段性报告，表内旧提交与旧包 SHA 仅供追溯。最新独立插件状态见 [主仓交接](../../HANDOFF.md) 与三个原 PR。Memory 新产品门禁 489/489，归档与并发初始化缺陷已修复。用户已明确暂缓最终三插件联合运行；文中既有联合 Profile 检查只算历史探针，不构成当前版本联合验收 PASS。没有公开发行、生产部署或 PR 合并。

日期：2026-10-25。官方 DSH Desktop 0.2.1-rc.3，三个独立 Profile 和一个联合 Profile。所有安装、停启和重启均在 QA Desktop；没有真实平台外发、付款、公开发布、生产部署或 PR 合并。主要插件代码由各自持续的 DSH Creator 会话完成，Codex 独立审查、运行测试并通过官方 Plugin Manager 验收。Memory 初期 QA 发现数据目录没有按 Profile 隔离，相关数据观察已撤销，见下方事故说明。

| 项目 | Life | Memory | State |
| --- | --- | --- | --- |
| 原 PR | [#2](https://github.com/jeskoky-pixel/amadeus-life/pull/2)，`1c2a2e2` | [#1](https://github.com/jeskoky-pixel/amadeus-memory/pull/1)，`0004c17` | [#1](https://github.com/jeskoky-pixel/amadeus-state/pull/1)，`633adc0` |
| 版本 | `@amadeus/life@1.0.0` | `@modusensus/dsh-mneme@0.9.5-amadeus.1`（同包名兼容升级） | `@amadeus/state@1.0.0` |
| tarball SHA-256 | `34d6cf69088d0fb64e2ee16dc7b9bfd05a6c67e2799b42f777c68febee02127c` | `cf584d7c410047e656a0f3385b3e9f3c9a4068be52102e1d4d19ab0fc3e0e0fc` | `df88e747ae72832e72db847022bcffb143f696c475ff6c36fe5cd516dfe4efc9` |
| 独立官方 Plugin Manager 安装 | 通过 | 修正数据目录后重装最终包通过 | 通过 |
| DSH 原生设置入口 | 七页签与插件详情基础卡通过 | 入口与原专业面板通过 | 独立页与详情基础卡通过，重复注册已修复 |
| GUI 保存/冲突 | 合法/非法值、取消、双窗口冲突、刷新/硬重启通过 | 结构参数保存、取消、恢复默认、双窗口冲突、刷新/硬重启通过；单页误报已修复 | 敏感度保存/硬重启通过；其余既有隔离验收见 State PR |
| Codex 独立测试 | 167 pass、0 fail | 完整 2390 tests：2368 pass、21 fail、1 skip；**未通过全套门槛**。产品相关定向 129/129 通过 | 100 pass、0 fail、1 skip，包校验通过 |
| 生命周期与数据 | 停用/启用、卸载/再安装和 SQLite 数据保留通过 | 修正目录后停用/启用、卸载/再安装、SQLite 数据保留通过 | 停用/启用、卸载/再安装和 SQLite 数据保留通过，GUI 备份完整性通过 |

三个候选包通过官方 Plugin Manager 同时安装后，三个 Host API 均返回 200，系统设置三个入口各出现一次；分别停用任一插件时，其 API 变为 404，另外两个维持 200，重新启用后全部恢复。State 联合验收曾发现 `settings.section` 和 `plugins.detail.section` 重复注册；原 Creator 修复并重新安装最终 State 包后，三个页面打开无相关浏览器错误。
Memory 最终包重新安装到明确的独立空库后，联合 Profile 硬重启复验：三个 Host API 仍为 200，三个设置入口各 1 个，两个 Memory QA 库仍各 0 条且 SQLite `quick_check=ok`。

Memory 最终全套测试的 21 项失败均来自导入树缺失的上游项目级 release pipeline 与 GitHub workflow 检查。当前私有 Amadeus fork 没有复制上游自动发布流水线；不能把这些失败称为通过。产品相关定向测试 129/129 通过。新包重装、双页冲突、取消/默认、独立库生命周期与三插件联合硬重启已补验；旧版真实回滚及跨版本数据恢复仍待验。

### Memory QA 数据目录纠正

Memory 的默认目录不跟随 `DSH_HOME`。早期两个 QA Profile 虽隔离了 DSH Home，却意外解析到另一未隔离的本地记忆库。该库创建于本轮 QA 开始时；生产 Profile 明确指向另一目录，其数据库创建于前一天，随后只读 `quick_check=ok`。这些证据支持误连库很可能由 QA 创建，尚无证据显示实际生产 Memory 库被修改，但不能据此断言绝对零影响。发现后立即停用两个 QA Memory，保存误连库的只读一致性备份，并把两个 QA 配置改成明确、互不共享的本地目录；硬重启后两个新库都是 0 条。没有自动回滚，以保留现场。**纠正前所有声称 Memory 数据隔离的验收均作废。** 新包的 GUI、数据、生命周期和联合硬重启已在两个独立空库重新执行。详细路径与副本只保存在本机忽略目录，不进公开仓库。

包均为本地可安装 tarball，未上传 Git。Life 和 State 的 manifest 仍为 private / `UNLICENSED`，独立许可和公开分发需另行决定。私人数据库、会话、凭据、原始日志未进入上述包或此公开记录。
