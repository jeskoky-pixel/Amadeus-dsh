# Amadeus 四插件 v1.0 兼容矩阵

本矩阵记录 2026-10-27 在官方 DSH Desktop **0.2.1-rc.3** 的一次隔离安装结果；它不声明更早或未来 DSH 版本的兼容性。四个插件保持独立安装、独立数据目录和独立停用能力。

| 插件 | 最终候选 Commit | npm 包 / Bundle ID | 版本 | SHA-256（最终 tgz） | GitHub main SHA |
| --- | --- | --- | --- | --- | --- |
| Life | `339128c94bc188fd3eb2a15bcd22ff5d0ed1b05c` | `@amadeus/life` / `amadeus-life` | 1.0.1 | `A79E4DF9C3797167F540234E620DD9A26547C047D1C64F6C7C005564174D4D13` | `105447060d2336ae50dc570bc7de286cd57b40cf` (PR #3) |
| Memory | `ac4e843e02331709acc3fb86888fbb8296ab83ff` | `@modusensus/dsh-mneme` / `dsh-mneme` | 1.0.0 | `359B1B5874B77597954AE8ED80BF0F6CD44B819EBBC3F25E3527F8EAA76DF591` | `879aa328f1be61bb271b2912f0aeea8c3fefb05b` (PR #4) |
| State | `d9c5bcf795220a404bb627d736cf2e613b5bbe2a` | `@amadeus/state` / `amadeus-state` | 1.0.0 | `9439B73CA18D2B41EDAC675E0593BE5DFAB159C4637C4C4902AEBC9E1AB0DAB9` | `afad15c6e473172cf7b78aae024232e0aa747555` (PR #1 + README PR #2) |
| QQ | `73002cca3952a741f61e504428c0d8df7a4d4ea4` | `@amadeus/qq` / `amadeus-qq` | 1.0.0 | `63EFAEAE29E55A6E9D9DD9FE047E6B1F66C16F8CBE22054FEAC9195B75EF2A87` | `756fdc6f6513b8d2ff9d8309166b0300c0888f9b` (PR #1) |

## Host 契约与可选依赖

| 插件 | 必需的 DSH 能力 | 跨插件关系与降级 |
| --- | --- | --- |
| Life | 单一 DSH Session、工具注册、Connection owner admission、独立 SQLite | QQ 外发是可选的；Life 的 `externalMessageSessionIds` 是意图提示，QQ 执行期 Guard 才是外发权限。无 QQ 时本地 Intent 与活动可用。 |
| Memory | Mneme 原有 Bundle ID、工具/Settings、独立 `memoryDir`、SQLite | 可信 QQ Session 可以提供 Scene 来源；无可信来源的 QQ 访问在严格模式拒绝。新的 `amadeus.memory` 可信 Scene ACL Read 契约与持久化 `scene_read_grants` 已有[专项验收](https://github.com/jeskoky-pixel/amadeus-memory/blob/main/docs/verification/SCENE_READ_ACL.md)：授权单向、同 Bot、只读、不可传递。默认无跨群授权；本次全新四插件 Profile 未重复完整授权模型调用。旧 `hostContract:false` 临时机制不代表新契约未实现。State 的 Memory 自动写入仍默认关闭。 |
| State | 工具/Settings、Connection owner admission、独立 SQLite | Life 1.0.1 可选读取契约已显示可用；Memory 引用可选，自动写入关闭。Life、Memory、QQ 分别缺席时可加载。 |
| QQ | 受限 `amadeus-qq` Preset、DSH Session/工具/Connection、显式独立 `statePath` | NapCat 是可选连接；`connect:false` 时本地 Scene 管理和权限持久化可用。Memory、Life、State 缺席时仍可加载。 |

实际部署应一次只启用一个 `dsh-mneme` 实例写入同一数据库；QQ 的 `statePath`、媒体目录和发送目录应明确指向目标环境，先备份再灰度。详情见 [统一验收报告](verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)。
