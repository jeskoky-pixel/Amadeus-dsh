# Amadeus 四插件 v1.0 兼容矩阵

本矩阵记录 2026-10-27 在官方 DSH Desktop **0.2.1-rc.3** 的一次隔离安装结果；它不声明更早或未来 DSH 版本的兼容性。四个插件保持独立安装、独立数据目录和独立停用能力。

| 插件 | 最终候选 Commit | npm 包 / Bundle ID | 版本 | SHA-256（最终 tgz） | GitHub 状态 |
| --- | --- | --- | --- | --- | --- |
| Life | `6f303f8ab9a645187773c52a381396205d6a991e` | `@amadeus/life` / `amadeus-life` | 1.0.1 | `DA74B884A2354CD4D284A5FC1FA4BA949CAA68465FAEE90548362478A301A4F8` | 1.0.0 已在 main；1.0.1 为 PR #3 |
| Memory | `e54fe04f019f4c99728e1801598ddb1434a097b3` | `@modusensus/dsh-mneme` / `dsh-mneme` | 1.0.0 | `359B1B5874B77597954AE8ED80BF0F6CD44B819EBBC3F25E3527F8EAA76DF591` | 1.0.0 为 PR #4；原 RC 已在 main |
| State | `d9c5bcf795220a404bb627d736cf2e613b5bbe2a` | `@amadeus/state` / `amadeus-state` | 1.0.0 | `9439B73CA18D2B41EDAC675E0593BE5DFAB159C4637C4C4902AEBC9E1AB0DAB9` | 已在 main |
| QQ | `fb595cda5e3e73a5a3c5ccf2d14dc3846ba7a81b` | `@amadeus/qq` / `amadeus-qq` | 1.0.0 | `848F1F7F465F64E2E58D01808A50AD6A0B4F0F916CC4948755E9E50206DDE140` | PR #1；未合并 |

## Host 契约与可选依赖

| 插件 | 必需的 DSH 能力 | 跨插件关系与降级 |
| --- | --- | --- |
| Life | 单一 DSH Session、工具注册、Connection owner admission、独立 SQLite | QQ 外发是可选的；Life 的 `externalMessageSessionIds` 是意图提示，QQ 执行期 Guard 才是外发权限。无 QQ 时本地 Intent 与活动可用。 |
| Memory | Mneme 原有 Bundle ID、工具/Settings、独立 `memoryDir`、SQLite | 可信 QQ Session 可以提供 Scene 来源；无可信来源的 QQ 访问在严格模式拒绝。跨群读取白名单的 Host 契约目前关闭，不得声称已启用。State 的 Memory 自动写入仍默认关闭。 |
| State | 工具/Settings、Connection owner admission、独立 SQLite | Life 1.0.1 可选读取契约已显示可用；Memory 引用可选，自动写入关闭。Life、Memory、QQ 分别缺席时可加载。 |
| QQ | 受限 `amadeus-qq` Preset、DSH Session/工具/Connection、显式独立 `statePath` | NapCat 是可选连接；`connect:false` 时本地 Scene 管理和权限持久化可用。Memory、Life、State 缺席时仍可加载。 |

实际部署应一次只启用一个 `dsh-mneme` 实例写入同一数据库；QQ 的 `statePath`、媒体目录和发送目录应明确指向目标环境，先备份再灰度。详情见 [统一验收报告](verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)。
