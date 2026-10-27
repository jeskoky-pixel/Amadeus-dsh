# Amadeus 四插件 v1.0 联合验收（2026-10-27）

## 结论与范围

Life 1.0.1、Memory 1.0.0、State 1.0.0、QQ 1.0.0 的最终候选包已在**同一个全新隔离的官方 DSH Desktop 0.2.1-rc.3 Profile** 安装并同时加载。本轮未发现跨 Scene 泄露、权限提升、数据库损坏或四插件启动冲突。按本轮有限门禁，四个独立插件达到 v1.0 **交付候选**标准；这不表示所有 PR 已合并、已公开发行或已部署生产。

本轮隔离 Profile 无可路由模型 Provider，QQ 设置为 `connect:false`。因此本轮没有在这个新 Profile 中重新执行模型工具链或真实 QQ 外发；这些能力的判断复用同一运行时代码此前在隔离官方 DSH 与授权 NapCat/QQ 的实机报告。下面分别标明直接证据与复用证据。

## 版本和仓库状态

| 插件 | 版本 | 最终候选 Commit | 独立测试 | 当前代码状态 |
| --- | --- | --- | --- | --- |
| Life | 1.0.1 | `6f303f8ab9a645187773c52a381396205d6a991e` | 178 通过；build/package 通过 | 1.0.0 已合并；1.0.1 在 PR #3 |
| Memory | 1.0.0 | `e54fe04f019f4c99728e1801598ddb1434a097b3` | 540/540 通过；同步、隐私和 package 检查通过 | RC 已合并；正式版本在 PR #4 |
| State | 1.0.0 | `d9c5bcf795220a404bb627d736cf2e613b5bbe2a` | 108 通过、1 环境跳过；package 通过 | 已合并 main |
| QQ | 1.0.0 | `fb595cda5e3e73a5a3c5ccf2d14dc3846ba7a81b` | 281 通过、4 个 Windows 符号链接跳过；build/package 通过 | 在原 PR #1，未合并 |

四个最终 tgz 的包名、Bundle ID、SHA-256 和可选依赖见 [兼容矩阵](../FOUR_PLUGIN_COMPATIBILITY.md)。Memory 保持 `@modusensus/dsh-mneme` 原身份；这次正式版仅改版本、发行材料、断言及安装产物，没有改运行时代码。QQ 本次发行提交也没有改运行时代码。四个 tgz 均由官方 Plugin Manager 从最终安装包安装，不是开发目录临时导入。安装包未包含已知 QA 私有文件或凭据；没有 npm 发布或 GitHub Release。

## 新隔离 Desktop 的直接结果

隔离根目录为 `private/four-plugin-v1/qa-joint-final`，使用独立 `DSH_HOME`、Electron userData、Life/State SQLite、QQ 状态及媒体目录。Memory 安装时先停用，指定独立 `memoryDir` 后才启用；SQLite `quick_check=ok`，数据库路径实际落在该隔离目录。QQ 仅用合成 Bot/Scene ID，NapCat 传输为 `disabled`。生产 Profile、生产 Memory、日常 QQ 登录及 Life 数据未修改。

| 场景 | 本轮直接结果 | 证据边界 |
| --- | --- | --- |
| 四插件同时装载 | 四个管理/健康 API 同时返回 200；State 为 `ready`，Life 健康为 `ok`，Memory 数据库检查通过，QQ `enabled:true` 且传输关闭。安装与启用没有注册失败警告。 | 没有逐项枚举 Host 内部工具表。 |
| 原生 Settings | 在这个隔离 Desktop 提供的真实 Web 页面中，分别打开“Amadeus 记忆”“Amadeus 生活”“Amadeus 状态”“Amadeus QQ”；四页均渲染，Memory 显示管理内容，Life 显示健康 `ok`，State 显示版本 1.0.0/引擎正常，QQ 显示插件运行中和 OneBot 连接已禁用。 | 页面操作未改变生产设置；未逐项点击所有低频控件。 |
| QQ ↔ Memory | 从**已安装 Memory 包**在隔离 SQLite 写入合成群 A、群 B、私聊记忆；关键词检索各群只见自己的 1 条，普通私聊只见自己的 1 条，可信 Owner 私聊可见 3 条，未绑定 Scene 为 0。重启和 Memory 停用/启用后隔离仍成立。 | 这是已安装包的 Scene 服务契约测试；本轮无 NapCat 事件，因此新的 QQ Host 事件绑定未在本 Profile 触发。此前同 Host 真实绑定 Session 的精确查询见 QQ 实机报告。 |
| QQ 权限 | 合成群请求 Owner Agent 得到 403；授权合成私聊可设置 Owner Agent，群可设置 Enhanced。重启后两项持久；随后把 Owner 私聊降为 Safe Chat，群权限不变。 | 本轮未执行模型原生工具；此前隔离 Host 已实测 Owner `pwsh`、`read`、`web_fetch`、Life 工具和撤权拒绝。 |
| Life 持久化 | 在新 DSH 创建合成 Session，通过本地管理员入口 Bootstrap 并创建远期 Intent；硬重启后同 Session/Intent 仍在。Wake delivery 保持关闭，没有外发。 | 该 Intent 的来源是管理员设置，不冒充 `life-tool:session`。可信工具来源、QQ 回执、重复/撤权由此前 Life 1.0.1 + QQ 的真实授权测试及 178 项回归覆盖；本轮未重新实测 Life→QQ Wake。 |
| State | `state/status` 显示 1.0.0/ready、SQLite ready；Life 集成 available/enabled。Memory 自动写入依旧 disabled，未擅自启用。 | 本轮没有构造 State Mood/Interest 事件，也没有模型调用 `state_get`；此前 State 独立 Host 验收与 108 项测试是相应证据。 |
| 硬重启 | 两次停止并重启隔离 Desktop 后四个 API 均恢复；Memory Scene、Life Intent、QQ 权限与撤权持久。未产生 QQ 投递。 | State 数据库健康得到验证，但非空 State 事件的重启读取沿用其独立验收。 |
| 模块降级 | 依次停用 Life、Memory、State、QQ：被停用者 API 为 404，另外三者均为 200；每次重新启用后四者均为 200。 | 仅验证本地加载/管理及健康路径，不等同于断开模块后的完整模型会话。 |

统一 Agent/Persona 的代码边界保持 DSH 为唯一推理与 Persona 主体：Life 提供活动、Memory 提供上下文、State 提供状态、QQ 提供 Session/工具和平台投递。新 Profile 的模型目录 `routableProviders=[]`，故不能声称本轮实际执行过四插件共同注入的模型轮次。此前 QQ 实机报告证明相同 QQ 运行时代码在官方 DSH 中使用单一 Agent、原生工具、Life Wake 和可信 Memory Scene。

## 真实环境证据与未验证项

QQ 的 [授权 NapCat/QQ 验收报告](https://github.com/jeskoky-pixel/amadeus-qq/blob/codex/m0-baseline/docs/verification/V1_REAL_NAPCAT_2026-10-27.md)记录私聊和受控群的文本、图片、语音、附件、引用/@/回应/戳一戳、原生 ASR、Owner 工具、签名/头像、仅自己可见的 QQ 空间发布、投递幂等、硬重启、Web Console。Life 1.0.1 的可信 `life-tool:session` Intent、opt-in Wake、QQ 单次投递与关闭 opt-in 的零发送也在同一报告中；本轮只复验最终包安装和本地可靠性。QQ 运行时代码未在 1.0.0 发行提交中改变。

尚未实测：新四插件 Profile 中的模型轮次、非空 State 事件与模型 `state_get`、该 Profile 的 QQ Host 事件到 Memory Scene 的绑定、跨群白名单实际 Host 契约、长期生产负载、QQ 空间低频互动和移动端布局。Memory `host_contract=false`，跨群读取白名单仍关闭；严格模式对来源不明的 QQ 派生记忆拒绝，不能把合成 Scene 测试写成生产 Host 联动的完整证明。State 到 Memory 的自动写入保持关闭。Life `externalMessageSessionIds` 是提示层 opt-in，QQ 执行期 Guard 是外发硬权限。

这些边界不影响本轮已验证的安装、默认隔离、权限拒绝及独立降级结论。若部署目标要求立即启用跨群白名单或依赖新 Profile 的模型工具验收，应先补该目标环境的 Host 契约与模型测试，再开启对应选项。

## 部署准备、回滚和 PR 建议

部署前备份现有 Mneme 数据库、Life/State SQLite、QQ 状态与媒体目录以及原 DSH Profile；核对四包 SHA-256，并明确唯一 Mneme 写入实例。先在停用 NapCat 外发的目标副本安装四包，确认目录、插件健康、Scene/Owner 白名单与 `connect:false`；再仅对授权私聊/小群灰度。若出现权限或来源异常，先关闭 QQ 连接与 Life Wake，停用相应插件，恢复原包和对应数据库/Profile 备份；未知 QQ 投递只人工对账，不自动重发。

合并建议：State 无新 PR；Life PR #3 可在审查其软 opt-in 边界后合并；Memory PR #4 是纯正式版收口，可合并；QQ PR #1 已更新为正式包与文档，可审查后合并。**本轮未自动合并、部署、公开发行或启用生产数据。**
