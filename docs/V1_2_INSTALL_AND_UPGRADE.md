> 历史 v1.2 发行准备记录。当前 main 版本与本轮验收以[2026-10-31 整合报告](../AMADEUS_MAIN_INTEGRATION_2026-10-31.md)为准；Memory 已完成根目录整理。

# Amadeus v1.2.0 安装、升级和回滚

目前是 `@fg-lab/*@1.2.0` 正式发行验收阶段。五个独立预构建包将分别通过 npm 公共 Registry 和固定 GitHub Release 提供；在实际发布前，不应把开发分支或尚未生成的下载地址当作正式安装源。第三方 DSH Market 条目本轮只准备，不投稿。宿主测试目标为官方 DSH Desktop `0.2.1-rc.3`。

| 插件 | GitHub 仓库 | 包位置 | 首次配置 |
| --- | --- | --- | --- |
| QQ | [amadeus-qq](https://github.com/jeskoky-pixel/amadeus-qq) | 根目录 | Workspace、Bot、Owner、NapCat、授权 Scene |
| Memory | [amadeus-memory](https://github.com/jeskoky-pixel/amadeus-memory) | 根目录（v1.2 候选迁移中） | 核对 `memoryDir`、Workspace 与 Scene 安全设置；只启用一个 `dsh-mneme` |
| Life | [amadeus-life](https://github.com/jeskoky-pixel/amadeus-life) | 根目录 | Workspace、活动与唤醒策略 |
| State | [amadeus-state](https://github.com/jeskoky-pixel/amadeus-state) | 根目录 | Workspace、会话规则 |
| Persona | [amadeus-persona](https://github.com/jeskoky-pixel/amadeus-persona) | 根目录 | Workspace、核心人格、风格和 Scene 绑定 |

## 单独或联合安装

公开发行后，在目标 DSH Profile 的插件管理器中逐个安装固定版本的 `@fg-lab/amadeus-qq`、`@fg-lab/amadeus-memory`、`@fg-lab/amadeus-state`、`@fg-lab/amadeus-life`、`@fg-lab/amadeus-persona`，确认已安装列表、设置页和版本。首次安装 QQ 不会自动连接或外发，Persona 没有内置强制人格。其他插件是可选协作，不需为了安装其中一个而强制安装其余四个。联合部署时，推荐按 QQ → Memory → State → Life → Persona 配置；每项仍可独立停用。

新 `@fg-lab/*` 本地包已经用官方 `dsh plugin --profile web add <versioned.tgz>` 在隔离 Profile 联合安装，并完成旧名→新名的合成数据演练；同版 Desktop 图形插件管理器也完成五包本地安装、启用、设置页加载和一次重启。**公开 npm Registry、GitHub Release 与 Market 下载入口尚未验收**；首次配置的真实 NapCat/Workspace 联动也仍待验证。

## 升级前

1. 记录五个旧包版本、当前 DSH 版本、Profile、Workspace ID 和各数据路径；保留旧 `.tgz`。
2. 停止插件写入并导出 DSH Profile。对 QQ JSON、Memory 的完整 `memoryDir`、Life/State SQLite（含 WAL/SHM）、Persona 目录做一致性备份，计算校验值。包名迁移时先卸载旧 `@amadeus/*` 包，再安装对应 `@fg-lab/amadeus-*` 包；Memory 必须确认只存在一个 `dsh-mneme` Bundle。
3. 在隔离 Profile 使用旧版创建合成数据，先演练新包替换、重启、内容与关系检查，再升级个人 Profile。

升级时沿用原安装 Profile 和数据路径，绝不通过重建空库或新 Session 掩盖丢失。Memory 的原根目录 `memory.db` 不会被自动收养；出现“空白”时先检查 Workspace 绑定和实际路径。Memory v1.2 首次打开未标记的既有库时，在 schema 变化前创建 `backups/memory-before-schema-v1-*.db`。

## 禁用、卸载和回滚

禁用停止功能但不删除数据。卸载代码后，独立数据目录在本轮 CLI 演练中保持；DSH Profile 配置是否保留仍取决于宿主实现，卸载前必须导出。重新安装可读取原数据，但要重新确认配置路径及 Workspace。

代码回退与数据回退分开：旧程序是否能读取新 schema 必须逐版本验证。本轮所列五个旧版对合成数据的代码回退通过；未来发生不可逆迁移时，需同时恢复匹配的旧数据库、Profile 配置和旧包。每个插件的详细规则见自身仓库的 `docs/UPGRADE_POLICY.md`、`docs/DATA_MIGRATION.md` 和 `docs/RELEASE_CHECKLIST.md`。

## Market 条目

五个市场 YAML 草案均在各自仓库的 `market/`，Memory v1.2 草案已经改为根目录形式。市场正式收录、GitHub Release 和 DSH Market 下载验收尚未发生；条目不可当作已上架链接。npm `@fg-lab` 组织 Owner 权限已核实，但正式发布仍取决于最终 CI、真实联动、2FA 和实际 Registry 安装结果。
