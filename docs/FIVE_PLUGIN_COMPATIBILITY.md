# Amadeus 五插件开发基线与兼容记录

截至 2026-10-29，下列版本已在各自仓库的 `main`。这是供个人电脑实机测试的源码基线，**不是正式 Release、生产部署许可或长期稳定性证明**。隔离验收使用官方 DSH Desktop `0.2.1-rc.3`；其他版本须重新核对 Host 契约。

| 插件 | npm 包 / 版本 | main SHA | 独立职责 |
| --- | --- | --- | --- |
| [QQ](https://github.com/jeskoky-pixel/amadeus-qq) | `@amadeus/qq@1.1.1` | `7832323c0604dc0a78c38d17cd4d8f1fb54db099` | QQ 连续交流、可信 Scene、平台权限与接管开关 |
| [Memory](https://github.com/jeskoky-pixel/amadeus-memory) | `@amadeus/memory@1.0.1` | `218244c1f510bbbf59781c1a0019393f1e76be3b` | 长期记忆、近期活动、图谱和安全召回 |
| [State](https://github.com/jeskoky-pixel/amadeus-state) | `@amadeus/state@1.1.0` | `73c8ded7d0850fd50293ce10f8d4177b69244f40` | 心情、精力、好奇心及状态演化 |
| [Life](https://github.com/jeskoky-pixel/amadeus-life) | `@amadeus/life@1.1.0` | `ad8863e7b520410faa2f3c7fad49ad580083b079` | 自主意图、活动调度和唤醒 |
| [Persona](https://github.com/jeskoky-pixel/amadeus-persona) | `@amadeus/persona@0.1.0` | `3319f8623a31c574e7fd2a6e3a73e46486f179e0` | 核心人格、场景风格和 System Prompt 注入 |

## 安装与契约

- 五包各自安装，不复制源码到本仓库。Memory 的包目录是仓库内的 **`dsh-mneme/`**，原 Bundle ID 仍为 `dsh-mneme`；同一记忆数据库只能由一个 Mneme 实例写入。
- Persona 由管理员选择一个 DSH Workspace；普通 Session 共用核心人格，可信 QQ Scene 可单独选择风格。QQ 的只读 `listAuthorizedScenes()` 支持授权 Scene 预绑定，活跃 Session 仍由 `resolveTrustedSessionScene()` 校验。QQ 撤权后 Persona 立即停止信任该 Scene。
- Persona、Memory、State、Life、QQ 均保持独立。Persona 不切换 Agent Preset，不改变工具或执行权限；Memory 负责历史及访问控制，State 负责动态状态，Life 负责自主活动，QQ 负责收发。Memory 的可选人格卡默认关闭；有效状态缺少跨插件可信读取契约时，Persona 会告警而不会擅改 Memory 设置。
- 首次配置通过各插件的 DSH Owner 页面完成。生产迁移前，分别备份目标 Profile、Memory 数据库、QQ 状态和 Persona 配置；先在隔离副本验证，再灰度。不要把测试用配置或 QQ 标识带入日常 Profile。

## 已取得的证据

- [四插件 v1.1 联合记录](https://github.com/jeskoky-pixel/amadeus-qq/blob/main/docs/verification/FOUR_PLUGIN_V11_JOINT_2026-10-28.md)记录了 QQ、Memory、State、Life 的既有隔离验收；[四插件 v1.0 矩阵](FOUR_PLUGIN_COMPATIBILITY.md)和[原始报告](verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)保留历史版本与安装包哈希。
- [Persona 0.1 验收报告](https://github.com/jeskoky-pixel/amadeus-persona/blob/main/docs/VERIFICATION_2026-10-29.md)记录 **49/49** 项测试、中文/英文截图、真实模型热更新和工具调用。QQ 只读 Scene 契约测试 **2/2** 通过。
- [QQ 接管补丁 PR #4](https://github.com/jeskoky-pixel/amadeus-qq/pull/4)及[定向验收记录](https://github.com/jeskoky-pixel/amadeus-qq/blob/main/docs/verification/QQ_ASTRBOT_HANDOFF_2026-10-29.md)记录 QQ 1.1.1 的自动化测试与打包检查。其接管功能尚未经过真实 NapCat、AstrBot 和 DSH Desktop 交接实测；不能把先前的五插件同装验收视为此补丁的实机结论。
- 在同一隔离 Desktop Profile 安装并启用五插件，重启后五个 Owner HTTP 入口均返回 200；普通 DSH Session 的真实 DeepSeek V4.1 Flash 回合成功调用 `read`，同装后模型可见工具为 72 项。两个合成的离线 QQ 群 Scene 分别绑定不同风格，撤权即时使绑定失效，重新授权后恢复。

## 尚未宣称通过

- 本轮没有接入隔离真实 NapCat，因此未完成五插件组合下的 QQ 入站、出站与两个群的实际模型交流。先前 QQ 版本的实机测试不能替代这次联测。
- QQ 1.1.1 接管开关尚未验证同号从 DSH 交给 AstrBot、再交回 DSH 的实际流程；启用前应在隔离 Profile 中确认断连、未完成外发和重启恢复行为。
- 一个长期测试 Session 在风格切回后仍复述旧风格，虽然最终 System Prompt 已更新。用户接受这项**模型上下文局限**作为开发基线的已知限制；不能把模型表达写成每次都准确。真实 KV 缓存成本也未量化。
- State 演化、Life 自主唤醒、Memory 实际检索、双 Workspace GUI 切换及子 Agent 的 Desktop 级联测在本轮未完整执行；健康接口响应不等于这些行为已通过。

## 回滚与发布状态

QQ 与 Persona 合并前 main 已分别备份在各仓库的 `backup/pre-persona-merge-20261029`：QQ 为 `a0f2454d174dd5a80781fa7d6c480887766e40f0`，Persona 为 `815479b5a46413d055d5d65122836c55053fc7be`。回滚源码可从备份引用建立修复分支；实际配置与数据库应使用部署前各自的备份恢复，不能仅回退 Git 代码。

QQ 1.1.1 接管补丁合并前的 QQ main 另备份于 `backup/pre-qq-handoff-20261029`，指向 `1bd1538e9eaae56329f4a713fa4a84d723c0c72b`。

截至本记录，五仓库均无正式 GitHub Release，本轮未运行 `npm publish` 或提交 DSH Market。
