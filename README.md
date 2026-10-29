# Amadeus

Amadeus 以官方 DeepSeek Harness（DSH）作为同一智能主体的运行基础。五个独立插件分别负责人格、记忆、当前状态、自主活动和 QQ 交流；可独立安装、停用与升级，不通过切换 Agent Preset 制造不同的“她”。

**v1.2.0 内部发行候选**已完成预构建包和隔离 DSH 运行时的安装、升级与数据保留演练；本轮选择固定 GitHub Release 包作为发行源，不计划 npm 发布。公开 GitHub Release 和外部 DSH Market 收录仍待最终发行确认。当前 GitHub `main` 中的稳定开发基线仍是下表旧版本，候选版本在各插件的 v1.2 PR 中。阅读 [v1.2 安装与升级](docs/V1_2_INSTALL_AND_UPGRADE.md)、[兼容矩阵](docs/V1_2_COMPATIBILITY.md)和[发行准备报告](AMADEUS_V1_2_RELEASE_READINESS.md)。

| 插件 | main 中的包版本 | 职责 |
| --- | --- | --- |
| [Amadeus Persona](https://github.com/jeskoky-pixel/amadeus-persona) | `@amadeus/persona@0.1.0` | 一份核心人格、可复用场景风格、选定 Workspace 内的可信 System Prompt 注入 |
| [Amadeus Memory](https://github.com/jeskoky-pixel/amadeus-memory) | `@amadeus/memory@1.0.1` | 长期记忆、近期活动、实体图谱与跨 Scene 安全召回 |
| [Amadeus State](https://github.com/jeskoky-pixel/amadeus-state) | `@amadeus/state@1.1.0` | 心情、精力、好奇心和兴趣的动态演化 |
| [Amadeus Life](https://github.com/jeskoky-pixel/amadeus-life) | `@amadeus/life@1.1.0` | 自主意图、活动调度、预算和唤醒 |
| [Amadeus QQ](https://github.com/jeskoky-pixel/amadeus-qq) | `@amadeus/qq@1.1.1` | QQ 连续交流、可信 Scene、NapCat 接口和平台权限 |

表内五个版本已合并到各自仓库的 `main`，构成 v1.1 阶段的个人电脑实机测试基线。v1.2 候选均为 `@amadeus/{qq,memory,life,state,persona}@1.2.0`。Persona、QQ 和 Memory 等插件可单独使用；其他插件缺席时，各自按其文档降级。Persona 只决定“她是谁、在当前场景如何表达”，不替换 DSH Agent Preset，不增加模型工具，也不授予 QQ 或文件权限。

## v1.2 控制台预览

下图来自隔离 DSH Web Host 的真实浏览器，显示首次安装的空状态；尚未代表 Desktop 图形安装器或真实 QQ 接入验收。

| QQ 控制台 | Persona 控制台 |
| --- | --- |
| ![QQ v1.2 空状态](docs/screenshots/qq-v12-overview.png) | ![Persona v1.2 空状态](docs/screenshots/persona-v12-overview.png) |

QQ 1.1.1 在中文 Owner Web Console 加入“QQ 接管”开关，可暂停与恢复插件对 NapCat 的连接，供本地 AstrBot 与 DSH 轮流使用同一个 QQ 登录。补丁已通过自动化测试；真实 NapCat、AstrBot 与 DSH Desktop 的交接流程仍待个人电脑实测。

## 人格与场景

Persona 只绑定一个由管理员选择的 DSH Workspace。该 Workspace 的主 Session 和后续普通 Session 共用核心人格；群聊、私聊或指定 Session 可以选择不同的表达风格。QQ Scene 绑定以 QQ Host 提供的可信 Bot 与 Peer 身份为准；群里所有参与者使用该群 Scene 的风格。修改人格在下一模型步骤注入现有 Session，历史与工具保持，切换 Workspace 会备份并归档旧绑定。

Memory 负责她记得什么，State 负责她此刻的状态，Life 负责她想做什么及何时行动，QQ 负责交流渠道与执行期权限。人格文字本身不是权限边界；跨 Scene 记忆访问仍由 Memory 与 QQ 的可信契约检查。

## 安装与验证边界

目前核对的宿主是隔离 DSH Desktop `0.2.1-rc.3`。从各仓库的实际包目录构建安装包，再通过 DSH 插件管理器安装与首次配置。**Memory 的包位于仓库 `dsh-mneme/` 子目录，Bundle ID 仍是 `dsh-mneme`**；不要安装第二个 Mneme 实例同时写入同一数据库。Persona 的管理界面选择 Workspace、编辑核心人格及场景风格；QQ、Memory、State、Life 各有独立设置和数据目录。

[五插件版本与验收边界](docs/FIVE_PLUGIN_COMPATIBILITY.md)记录了 main SHA、隔离 Desktop 同装、真实模型调用、离线 QQ Host 场景和仍未通过的实机项目。早期[四插件 v1.0 兼容矩阵](docs/FOUR_PLUGIN_COMPATIBILITY.md)与[原始联合验收](docs/verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)作为历史证据保留。

Persona 的长期测试会话在切回风格后曾复述旧风格，尽管最终 System Prompt 已更新；用户接受其作为当前开发基线的**已知模型上下文局限**，详情见 [Persona 验收报告](https://github.com/jeskoky-pixel/amadeus-persona/blob/main/docs/VERIFICATION_2026-10-29.md)。本轮五插件联测未连接真实 NapCat，也未完成 State 演化、Life 自主唤醒与长期生产负载测试。**合并 main 不等于生产部署许可**；五个插件均未创建正式 GitHub Release、发布 npm 包或提交 DSH Market。

## 项目文档

- [多仓库架构与职责](docs/ARCHITECTURE.md)
- [插件登记表](modules/registry.yaml)与[版本清单](compatibility/versions.yaml)
- [五插件兼容与回滚要点](docs/FIVE_PLUGIN_COMPATIBILITY.md)
- [第一阶段四插件验收历史](docs/verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)

[Browser](https://github.com/jeskoky-pixel/amadeus-browser)、[Bilibili](https://github.com/jeskoky-pixel/amadeus-bilibili)、[Android](https://github.com/jeskoky-pixel/amadeus-android) 与 [Games](https://github.com/jeskoky-pixel/amadeus-games) 属于后续计划，不在这次五插件基线内。
