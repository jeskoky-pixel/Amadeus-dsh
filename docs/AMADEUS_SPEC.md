# Amadeus：需求、模块、降级与协作交付规格

**版本：0.2 · 需求基线（QQ 交互目标修订）**  
**日期：2026-10-24（Asia/Shanghai）**  
**用途：交给 Codex 负责规划、审查和交付，由 DSH 承担适合委派的开发任务；同时作为 GitHub 上的项目需求与验收依据。**

> 本文件是产品与工程规格，不表示系统已经实现、安装或通过验收。所有接口示例均为拟议契约，只有标注“DSH 现有能力”的部分来自官方文档。实际开发须先核对本机版本与现用 QQ bridge 的精确来源。

---

## 快速导航

- [产品目标与确定需求](#sec-1)
- [模块与最小运行组合](#sec-5)
- [缺失与故障降级](#sec-6)
- [QQ Bridge 式交互目标与 NapCat 适配](#sec-8)
- [共同记忆与隐私](#sec-10)
- [Codex 指挥 DSH](#sec-13)
- [实施任务与验收](#sec-14)
- [GitHub 交付](#sec-16)
- [许可证与来源](#sec-17)
- [直接交给 Codex 的启动指令](#sec-18)

---

<a id="sec-0"></a>

## 0. 阅读顺序与执行规则

### 0.1 给项目拥有者

优先阅读第 1—6 节理解产品与模块，第 13—16 节理解开发、验收与 GitHub 协作，第 17 节了解代码复用许可。

### 0.2 给 Codex

1. 先读本节、第 2 节“已确定决策”、第 13 节“协作协议”和第 15 节“验收”。
2. 开始时执行 M0 基线核查，确认现用 QQ bridge 的仓库、版本、修改和行为。
3. 给 DSH 下发任务包，包含相关需求 ID、允许改动范围和验收项；无需每次塞入全文。
4. 以代码、运行结果和证据验收。DSH 的“已完成”只是待审声明。
5. 每个完成的可复核增量提交并推送到用户指定 GitHub 仓库；提交 SHA、PR 和验收结果要能互相对应。
6. 发现实现与规格冲突时，优先遵守用户最新明确要求；记录差异与决策，不静默降低目标。

### 0.3 规范用语

- **必须**：验收要求。未满足则不能标记相应需求完成。
- **建议**：默认实现方向，可用经过说明的等效方案替代。
- **可选**：不影响最低运行组合；关闭或缺失时必须正确降级。
- **待核实**：需要环境或源码证据，不能当成已具备的能力。
- **未运行**：没有完成实际检查。不能计入通过率。

### 0.4 本轮相对上一版讨论的明确修订

**QQ bridge 是交互模式与效果参考，不是不可修改的实现。**

用户要的是：模型在一次持续交互里能主动发消息、看图、调用工具、等待新消息并继续回应，而非每条输入只对应一条最终回复。可以吸收、修改、重构甚至重新实现 QQ bridge 的有关机制；不要求沿用其具体代码、提示词、`wait` 参数、内部时序或现有模块划分。验收基线是上述体验和安全/可靠性，而非源码或工具调用逐条相同。

QQ 接入优先**外部无头 NapCat**。正常情况下不修改 NapCat 本体：改的是我们自己的 QQ 桥接/适配代码，通过 NapCat 提供的 OneBot 等接口与之交互。类似现有 QQ bridge 借助 SnowLuma 的关系，**NapCat 是独立转发/通信后端，不是我们的源码开发对象**。

不论复用还是重构，不能为了实现简便退回“一问一答”，也不能引入重复发送、会话串线、权限绕过等回归。

---

<a id="sec-1"></a>

## 1. 产品目标

### 1.1 一句话定义

Amadeus 是基于官方 DeepSeek Harness（DSH）的持续陪伴系统：在不同场景维持统一人格，积累有权限边界的共同经历，保留状态和未完成打算，并通过 QQ、网页、手机等接口开展真实活动。

### 1.2 用户实际需要的体验

1. QQ 交流达到或超越 QQ bridge 的连续交互效果：她能先发一句、继续用工具、等待新消息、再接着说；实现允许改造。
2. 人格由 DSH 内部统一定义，不由各平台各自创建一个她。
3. 每个群保留自己的上下文；她具有高于单个会话的生活管理能力。
4. 昨天在一个群聊过，隔天换到另一个获准共享的群，仍能接上共同经历。
5. 用户没回复时，她可以继续浏览、收藏、玩耍，之后把新发现发给用户。
6. 夜间允许异步留言；不能因为没有回复而自动停止全部生活。
7. 心情与精力可以影响表达和选择，但具体台词与活动组合由 DSH 自然决定。
8. 通用 Agent 能力得以保留。例如她能自行组合浏览器、截图和 QQ 发送能力，围绕 token 余额开玩笑、准备充值页面。
9. 对公共评论、私聊和宿主资料采用真实的数据与工具权限边界。
10. 模块可拆可换，缺少心情、记忆、手机等模块时，剩余能力仍然可用。
11. 项目适合多人逐步扩展，问题能定位到具体模块、事件和任务。
12. Codex 可以根据明确证据监督 DSH 开发，阶段成果可在 GitHub 复核。

### 1.3 产品判断标准

优先判断“是否有连续经历与有来由的行动”，不以接入平台数、插件数量或自动消息数量衡量成功。

不能用虚构的行动记录替代真实经历。想去看视频、已经打开视频、读到字幕、分析了画面、完整完成任务，必须区分。

---

<a id="sec-2"></a>

## 2. 已确定决策与需求编号

| ID | 优先级 | 要求 |
| --- | --- | --- |
| R-01 | 必须 | 使用官方 DSH 作为推理与工具运行基础；不回到 AstrBot，不再并行建立另一套主 Agent 推理循环。 |
| R-02 | 必须 | 以现用 QQ bridge 为参考，达到连续收发—工具操作—等待—继续交流的体验。允许复用、修改、重构或独立实现；验收以交互行为与可靠性为准，不冻结旧代码、wait 参数或内部时序。 |
| R-03 | 必须 | QQ 使用独立运行的无头 NapCat 作为通信后端；优先修改/实现我们自己的 OneBot 适配与桥接层，不预设修改 NapCat 本体，不捆绑发行 NapCat。 |
| R-04 | 必须 | 退群不在能力清单；不注册、不公开提供该动作。 |
| R-05 | 必须 | 统一基础人格；群、私聊、公开平台的运行上下文分别隔离。 |
| R-06 | 必须 | 提供跨场景生活管理：场景映射、注意力、活动状态和未来意图。 |
| R-07 | 必须 | 跨场景记忆允许最终一致；不要求每条消息实时同步所有会话。 |
| R-08 | 必须 | 夜间可留言；未回复只是一项观察，不自动解释成用户拒绝、关系下降或必须反复催促。 |
| R-09 | 必须 | 新增主动意图可撤回、改期和取消；到期后重新判断，不盲发旧台词。 |
| R-10 | 必须 | 心情和兴趣是可选模块，提供行为倾向，不强制固定话术。 |
| R-11 | 必须 | 记忆后端可替换、可不安装；跨群召回仅使用当前场景获准读取的内容。 |
| R-12 | 必须 | 长活动有真实状态、结果与取消边界；不把计划、超时或到点当成完成。 |
| R-13 | 必须 | 私聊信息不能靠提示词单独保密；上下文、文件、工具参数和外发目标均需权限约束。 |
| R-14 | 必须 | 宿主资料仅通过管理员专用、限定范围的只读接口访问；不把宿主整盘只读挂载当作隐私保护。 |
| R-15 | 必须 | 可选模块未安装、关闭、不可用时，不拖垮无关能力；身份或授权不明时禁止受影响外部动作。 |
| R-16 | 必须 | 保留受控环境内的通用 Agent 探索与组合能力；不能把所有活动限制成一组固定剧情。 |
| R-17 | 必须 | 统一预算、任务和健康状态；不让多个插件各自无限唤醒模型。 |
| R-18 | 必须 | Codex 负责拆解、监督、关键审查、验收与 GitHub 交付；DSH 负责有边界的开发任务。 |
| R-19 | 必须 | 每个阶段可追溯到需求、代码、验证证据和 GitHub commit；不把未运行检查写成通过。 |
| R-20 | 必须 | 复用前核对许可与来源；发布物不含凭据、私聊、真实记忆库、私人配置和未经授权的第三方素材。 |

### 2.1 不属于当前范围

- 将 QQ 交互退化为“每条新消息单独调用模型、只输出一次最终答案”的单轮机器人模式。
- 为了减少工程工作量删去多次工具发送、消息等待、处理中接收新消息等核心体验。
- 把性能调优作为首期必需目标；允许在实现中调整 wait/时序与延迟，但改变后必须验证自然交流体验且可回滚。
- 第一版即实现复杂依恋体系、生理模拟或几十维心情。
- 要求上下文永久不压缩。
- 为“求充值”等具体互动写固定剧情插件。
- 给她开放宿主整机任意写入或任意读取。
- 让每个插件拥有自己的全局人格、记忆和独立生活调度。
- 自动支付、发布软件包、修改仓库可见性等未经单独明确的发布或资金操作。

### 2.2 碰到目标冲突时的处理

旧桥的人格、工具限制、收发、等待与会话管理可能耦合。Codex 应先分析哪些能力值得复用，再允许 DSH 按接口拆解或重新实现。若修改后不能达到参考体验，应回退该改动或保留旧桥作为临时工作路径，而不是冻结项目架构。

冲突优先级：真实身份与数据权限 > 正确收发、去重和会话归属 > 连续交互体验 > 具体实现、提示词与延迟。授权失败导致的动作拒绝属于必要边界；对合法操作不额外强制固定话术。

---

<a id="sec-3"></a>

## 3. 关键用户故事

| 编号 | 场景 | 成功表现 |
| --- | --- | --- |
| U-01 | 用户在 QQ 连续发几条话，中间她正在操作工具 | 达到参考 QQ bridge 的持续收发—操作—等消息—续接效果；可以使用新实现，但不能变成单轮问答或重复回复。 |
| U-02 | 夜间她发了消息，用户没有回复 | 她可以保留话题、继续自己的活动；有新发现时可留下消息，用户醒后可以接着聊。 |
| U-03 | 昨日在 A 群讨论，今日在 B 群追问 | 在两场景共同授权范围内找回对应经历；不要求全会话实时复制。 |
| U-04 | 用户发来短视频链接 | 自行选择可用阅读方式；基于真实取得的内容回应，明确未取得的部分。 |
| U-05 | 她浏览到自己喜欢的内容 | 可以收藏，记录自己的理由；是否分享由当时语境与兴趣决定。 |
| U-06 | 用户说 token 快没了 | 她可自然组合获准浏览、获取真实页面和 QQ 发图能力；不依赖专用撒娇脚本。 |
| U-07 | 她在公开评论区互动 | 使用公开视图及获准公开的材料，不读取用户私聊或私人文件。 |
| U-08 | 记忆服务或手机模块掉线 | 基础对话和其他活动继续；明确显示该项能力暂不可用，不伪造记忆或任务完成。 |
| U-09 | Codex 交给 DSH 一个实现任务 | 有明确改动范围、验收项与产出证据；Codex 能决定接受、返工或标记阻塞。 |
| U-10 | 一个阶段完成 | GitHub 上有对应代码、说明、检查结果和可复核版本；网页版助手可按 PR 或 SHA 复查。 |

---

<a id="sec-4"></a>

## 4. 两个平面必须分开

### 4.1 产品运行平面

DSH 运行 Amadeus。QQ Conversation 模块负责达到 QQ bridge 式持续交互体验，可由现有 bridge 改造或重新实现；Life、State、Memory 和 Activities 等模块按契约提供可选能力。

### 4.2 开发协作平面

Codex 指挥专用 DSH 开发执行器修改代码。这个执行器使用独立工作区、配置与会话，不是正在 QQ 陪伴用户的 Amadeus。

开发执行器不默认加载生产 QQ 连接、个人记忆、平台账号或私人工作目录。

### 4.3 运行结构

~~~mermaid
flowchart TD
    Q["外部无头 NapCat"] --> B["QQ Conversation：借鉴 bridge 交互模式"]
    B --> D["DSH 场景运行"]
    P["统一人格"] --> D
    M["授权记忆视图"] --> D
    B --> E["旁路事件"]
    A["活动结果与时间事件"] --> E
    E --> L["Life：注意力与未来意图"]
    L --> N["现有入口的正常唤醒或排队"]
    N --> D
    D --> G["能力与数据权限"]
    G --> X["浏览器、手机与其他身体"]
    X --> A
~~~

### 4.4 Life 的权力边界

Life 可以管理新主动活动的时机、目标和持续状态。它不能：

- 绕过 QQ Conversation 模块直接争抢或推进其消息游标；
- 擅自重排该模块已经确认投递的消息；
- 在不同模块里建立互相竞争的 QQ 收发循环；
- 为每个正常回复再额外强制一次“总指挥 LLM”决策；
- 通过覆盖会话、重复唤醒等方式破坏正在进行的连续交流。

**QQ Conversation 模块内部可以修改等待机制、消息处理和收尾策略。** 这些实现细节属于该模块自己维护；Life 不跨模块修改它们。Life 接入时优先使用模块提供的事件、唤醒、排队和投递接口。整体行为需要通过回放与实机验证，目标是达到参考 bridge 体验，而非锁定旧流程。

---

<a id="sec-5"></a>

## 5. 模块清单与最小运行组合

模块是逻辑边界，不要求每个模块都独立部署。第一版可采用一个仓库、少量受信任插件与外部身体服务。

| 模块 | 核心职责 | 依赖与定位 |
| --- | --- | --- |
| M-00 Runtime / Compatibility | 生命周期、配置、能力注册、DSH 版本适配、基础事件与健康信息 | 小型公共基础；不内置新的推理循环 |
| M-01 Identity / Policy | 平台身份、管理员映射、场景权限、数据与出口授权 | 对需要权限的动作不可绕过；高级策略扩展可选 |
| M-02 Persona | 一份统一人格、版本与基础风格 | Amadeus 身份必要；动态状态注入失败时保留基础人格 |
| M-03 QQ Conversation / NapCat Adapter | 持续对话与 QQ 收发、等待、媒体、回执；通过 OneBot 接外部 NapCat | QQ 可选；可复用/改造/重写 bridge，体验验收保持稳定；传输与交互实现尽量分离 |
| M-04 Life | 注意力、未来意图、主动活动调度、统一预算协调 | 可选；缺失时回到现有被动/连续对话 |
| M-05 State | 心情、精力、好奇心、兴趣演化 | 可选；缺失时使用中性倾向 |
| M-06 Memory | 经历索引、授权召回、记忆后端适配 | 可选；独立于 Life 任务账本与基础身份 |
| M-07 Activities | 浏览器、视频、手机、游戏等能力提供器 | 每个提供器独立可选 |
| M-08 Assets | 表情包、图片、文件等素材的来源和可分享范围 | 可选；普通文字仍可用，附件路径不得绕过授权 |
| M-09 Observer | 诊断界面、健康状态、统计和回放 | 高级界面可选；最小错误与动作回执记录留在基础层 |
| M-10 Host Read Broker | 管理员限定范围的宿主只读访问 | 可选，默认不开放；不可被公共场景调用 |

### 5.1 最小组合

| 组合 | 仍可用的功能 |
| --- | --- |
| 仅官方 DSH | DSH 自身功能；不能宣传为已具备完整 Amadeus |
| DSH + 基础人格 + 一个已验证的 QQ Conversation 模块 + 合法 NapCat 链路 + 必要基础层 | QQ Bridge 式连续对话；没有全局 Life 能力也能运行 |
| 上述 + Life | 可维护新增主动打算与活动；不依赖心情或向量记忆 |
| 上述 + Memory | 加入授权的跨场景经历召回 |
| 上述 + State | 加入持续状态与兴趣倾向 |
| 任意组合 + 一个身体提供器 | 加入对应活动；其他身体不存在不影响它 |
| 加入公共发布 | 必须同时具备适用的身份、数据视图和动作授权 |

“必要基础层”包含稳定身份、仍有效的基础权限规则，以及支撑权限和必要动作结果的本地记录。可以复用 DSH / bridge 已有实现，不要求另造发送引擎；它们不能随 Life 或 Memory 插件一起被移除。

### 5.2 单一职责与写入归属

- Persona 负责人格定义，不由平台插件分别覆写。
- State 是状态的唯一写入者；其他模块提交带来源和数据范围的事件或更新建议。每个场景只读取获准的状态视图。
- Life 是未来意图和新增活动调度的唯一管理者。
- Memory 管理召回索引与经历整理，不接管任务执行。
- QQ 消息发送由**唯一一个有效的 QQ Conversation 实例**负责，负责回执和去重；它可以使用旧桥源码或新实现，但不能同时启用两套竞争的 QQ 发送者。
- 每个活动提供器只维护自己拥有的任务和设备资源。
- Policy 的权限不能由普通记忆、自述身份或心情变化修改。

### 5.3 模块组合约束

必须声明 requires、optional、provides、contractVersion 和生命周期。

只允许单向依赖。可选模块不通过静态 import 变成隐性硬依赖；核心启动时不得因为缺少 Android、向量库或心情包而失败。

同一扩展点出现两个提供器时，通过配置确定选择；不能依赖插件加载先后顺序“最后一个覆盖前一个”。

---

<a id="sec-6"></a>

## 6. 缺失与故障时怎样继续运行

### 6.1 降级矩阵

| 缺失或故障 | 保留能力 | 暂停或降低的能力 | 必须告知的诊断 |
| --- | --- | --- | --- |
| State 未安装 | 原有对话、Life、记忆和其他活动 | 动态心情影响关闭；采用中性倾向 | state: disabled |
| Life 未安装或停止 | 已有 QQ 连续对话、人工发起的工具任务 | 新主动唤醒、未来意图调度 | life: disabled / unavailable |
| Memory 未安装 | 当前场景上下文、Persona、Life 自己的任务记录 | 长期跨场景语义召回 | memory: disabled |
| Memory 临时失败 | 当前场景和获准的有限原始记录回查 | 超时的语义检索与整理 | degraded，说明实际未召回 |
| QQ 未安装或断线 | DSH 本地场景、其他平台和身体 | QQ 接收、发送、真实投递确认 | disconnected，保留待考虑意图 |
| 某个浏览器/手机/游戏模块故障 | QQ 对话及其他身体 | 受影响设备任务 | provider unavailable / auth expired |
| Assets 未安装 | 文本交互与其他模块 | 收藏库、素材检索等可选功能 | assets: disabled |
| Observer 界面故障 | 所有授权且有最小记录保障的功能 | 仪表盘、统计展示 | 基础日志仍可见 |
| 人格动态扩展故障 | 已固定的基础 Amadeus 人格 | 动态附加内容 | 明确 fallback 来源 |
| 基础人格文件损坏 | DSH 本地诊断 | Amadeus 新的社交输出暂停，避免换成通用助手冒充她 | persona invalid |
| 身份/授权服务无法确认 | 本地诊断、已有允许保留的只读界面 | 受影响的宿主读取、跨场景读取与外发动作 | authorization unavailable，不自动放行 |
| Life 任务账本不可写 | bridge 自身鉴权和必要记录仍有效时，独立继续的对话 | 新的自主任务、意图与依赖该账本的动作 | life storage unavailable |
| 身份/授权/必要发送记录的基础存储故障 | 本地诊断及不依赖故障记录的安全只读能力 | 无法证明权限或可靠记录结果的外部写动作，包括受影响的普通发送 | core storage unavailable；不自动新建空库冒充恢复 |
| 预算服务/状态不确定 | 当前可安全收尾的工作、诊断 | 新的自主付费任务 | budget unknown / exhausted |

### 6.2 实施要求

1. 模块启动有有限超时；失败后返回结构化状态，不永久阻塞整个宿主启动。
2. 单个事件处理异常要被隔离，不让后续无关事件全部中断。
3. 超时后真正取消或隔离下游工作；不能只用 Promise.race 返回错误，实际操作仍在后台无限继续。
4. 恢复时重建能力列表，只重试安全且仍有效的任务。
5. 对状态未知的外发操作，不无条件重试；先查询上游回执或标记 unknown。
6. 不承诺所有同进程插件都具备进程级隔离。可能崩溃、长时间阻塞或不可信的第三方实现应放到独立进程/服务。
7. 功能降级不等于安全降级。无法确认权限时暂停相应动作，不能降级成提示词约束或全量记忆。
8. 未装模块不产生重试风暴、不反复调用模型解释缺失、不自动重新安装自身。

### 6.3 恢复和卸载

第一版必须支持“修改配置后重启，已禁用模块不参与运行”。热卸载属于可选增强，不得作为模块化的唯一证明。

禁用或升级模块前，列出归属任务并执行 cancel/drain 或保留可恢复记录；不能静默丢弃已承诺的任务。

---

<a id="sec-7"></a>

## 7. 公共契约与数据结构

以下为项目拟议契约，不是 DSH 已存在 API。实现语言建议优先 TypeScript，以适配所选 DSH 插件生态；最终取决于 M0 兼容性核查。

### 7.1 身份与场景

~~~ts
type PersonaId = string;
type SceneId = string;
type PrincipalId = string;

interface SceneBinding {
  personaId: PersonaId;
  sceneId: SceneId;
  platform: string;
  accountRef: string;
  sessionId: string;
  audiencePolicyId: string;
  presetRevision: string;
  bindingRevision: number;
}
~~~

平台用户 ID 到 PrincipalId 的映射由服务端可信配置维护。昵称、消息正文、模型工具参数中的自称不能产生管理员身份。

运行所使用的 audiencePolicyId 由场景绑定与可信调度确定，不允许模型任意选择一个权限更高的值。

### 7.2 事件

~~~ts
interface LifeEvent {
  schemaVersion: 1;
  eventId: string;
  personaId: PersonaId;
  sceneId?: SceneId;
  actorId?: PrincipalId;
  type: string;
  occurredAt: string;
  observedAt: string;
  sourceRef: string;
  payloadRef?: string;
  dataScopeRef: string;
  causationId?: string;
  correlationId?: string;
}
~~~

原始平台标识、用户文本和凭据不直接写进公开日志。事件默认以引用连接原始内容，便于权限过滤、保留期管理和脱敏。

输入去重键应包含平台、账号、场景和平台事件 ID；不只靠文本相同去重。

### 7.3 模块声明

~~~ts
interface ModuleManifest {
  id: string;
  version: string;
  contractVersion: string;
  requires: string[];
  optional: string[];
  provides: string[];
  declaredReads: string[];
  declaredWrites: string[];
  requestedCapabilities: string[];
  stateSchemaVersion?: number;
}
~~~

Manifest 用于组合与审查，不自行构成 OS 沙箱。实际读写与外部动作由基础权限层和服务端再次检查。

### 7.4 能力可用性

~~~ts
type Availability =
  | "ready"
  | "disabled"
  | "unavailable"
  | "degraded"
  | "unauthorized";

interface CapabilityResult<T> {
  status: Availability;
  data?: T;
  reasonCode?: string;
  retryAfterMs?: number;
  evidenceRefs?: string[];
}
~~~

模块缺失必须作为显式状态处理。不能返回一个看起来成功的空结果，再让模型自己猜。

### 7.5 未来意图

~~~ts
interface Intention {
  id: string;
  personaId: PersonaId;
  targetSceneId?: SceneId;
  kind: "reconsider" | "start_activity" | "follow_up";
  purpose: string;
  evidenceRefs: string[];
  dataScopeRef: string;
  earliestAt?: string;
  expiresAt?: string;
  status: "pending" | "evaluating" | "resolved" | "cancelled" | "expired";
  revision: number;
}
~~~

长期计划优先保存目的和依据，不提前固定整段话术。发送必须经过最新场景状态、权限和有效性检查。

每次评估携带 revision；新事件导致 revision 变化时，旧候选结果不能直接提交。

### 7.6 活动

~~~ts
interface ActivityHandle {
  activityId: string;
  providerId: string;
  ownerSceneId: SceneId;
  sceneBindingRevision: number;
  sourceScopeRef: string;
  authorizationRevision: string;
  remoteTaskId?: string;
  status: "pending" | "running" | "waiting" | "stopping"
        | "completed" | "failed" | "cancelled" | "unknown";
  checkpointRef?: string;
  evidenceRefs: string[];
}
~~~

completed 需要实际完成依据。到达 endsAt、发生超时、已经请求 cancel，都不是 completed。

活动在创建时固定来源范围与场景绑定版本；检查点、结果和后续意图继承限制。执行与恢复时同时检查原始范围和当前仍有效的授权，不只根据 ownerSceneId 的当前配置猜测权限。

### 7.7 发送回执

~~~ts
interface DeliveryReceipt {
  actionId: string;
  sceneId: SceneId;
  providerId: string;
  platformMessageId?: string;
  status: "confirmed" | "failed" | "unknown";
  evidenceRef?: string;
}
~~~

当前生效的 QQ Conversation 模块是发送动作与回执的事实来源，其他模块引用其结果；旧桥与新桥切换时不得并发双发。平台不支持幂等时，不能承诺 exactly-once。

### 7.8 版本和兼容

同一主版本内新增可选字段应向后兼容。删除或改变字段语义需要契约主版本升级与迁移说明。

消费者必须拒绝无法理解的授权或作用域字段，不能忽略后继续执行。

---

<a id="sec-8"></a>

## 8. QQ Bridge 式交互目标与 NapCat 适配

### 8.1 QQ Bridge 是“体验标杆”，不是不可改的源码

本节不要求逐字复制现有 bridge 的提示词、调用顺序、`wait` 毫秒数、收尾判定、代码结构或进程组织。用户认可的是它**一次进入持续活动后，可以边操作工具、边发送回复、边等待并接住后来的消息**，而不是普通机器人的“用户说一条、助手完成一条最终回复”。

M0 先记录实际使用的 QQ bridge 仓库 URL、commit、DSH 版本、本地修改与一段脱敏的真实交互轨迹。研究候选包括 Derpyu520/qq-bridge 和 NimuQDock-dsh，但不能冒充已识别用户本机版本。[S-QQ][S-NIMU]

这段真实轨迹只用于给 Codex 和 DSH 说明**目标体验**，不构成对原实现的冻结。

### 8.2 必须实现的交互能力（黑盒合同）

- **连续活动：** 一个 QQ 交流活动内允许多次阅读、工具操作、主动发送与继续等待，不强制每条用户输入新建独立回答回合。
- **活动中接消息：** 处理图片、网页或其他任务期间，新消息能被正确接收，随后在合理时机进入相应上下文，不被遗漏。
- **自然分段：** 她可以先回复一部分，操作工具后再补充，也可以暂时不说；不要求机械分段或每句话必答。
- **等待与续接：** 有明确的事件等待/唤醒能力，不靠无限模型忙轮询或每次重新载入完整人设；等待实现可以与旧桥不同。
- **媒体与动作：** 消息、引用、图片、文件、表情等按所选平台接口与明确能力目录支持；某项能力尚未实现须可发现、可降级，不伪造执行成功。
- **会话归属：** 多群、私聊、跨场景活动投递给正确的受众；消息游标、重复事件、发送确认丢失和重连有明确结果。
- **人格来源：** 人格由 DSH 统一定义；桥接层仅保留必要的通讯语义，不另注入一套不断增长的角色脚本。
- **延迟体验：** 响应时间可以优化，但不能以丢失中途发送、持续接话和准确收件人为代价。

### 8.3 源码复用、修改与重构都允许

默认先评估原 QQ bridge 的工具契约、等待机制、消息窗口和收发可靠性；**可以 fork、裁剪、重构、拆开，也可以重新实现等价能力**，以实际许可证、依赖、可维护性和验收结果决定。

Codex 不得一律要求“只允许改传输层”，也不得因为觉得重写更优雅就无证据抛弃已验证逻辑。每个涉及交流行为的 PR 必须说明：改变了什么、为什么改变、目标体验是否保持/改善、如何回滚。

行为验收比较**事件因果和用户体验**，不是逐一匹配旧版本的具体工具调用数量、wait 参数、每句回答文本或源码。允许改变策略，例如事件驱动等待替换长轮询，只要满足约定效果与成本/可靠性边界。

### 8.4 人格与桥接分离

不要为了保持旧桥效果而保留所有旧桥的社交规则；先识别哪些提示实际承担状态管理、等待决策和平台协议职责，再逐项迁回 DSH Persona、State、Life 或 QQ Conversation 的明确接口。

迁移过程中可暂时保留兼容片段，作为过渡措施；最终不应该同时存在 DSH 人格和 QQ bridge 的独立人格权威。移除或合并提示词前后必须进行黑盒对话验收。

### 8.5 NapCat 是外部依赖，不是修改目标

首选用户**自行安装、独立运行的无头 NapCat**，通过其 OneBot v11 WebSocket/HTTP 等接口进行 QQ 事件接收、动作发送和媒体访问。需要的连接鉴权、重连、消息格式映射、回执与兼容逻辑写在**我们自己的 NapCat 适配器**中。

默认**不 fork、不补丁、不修改 NapCat 本体**，也不随仓库、安装器或 Docker 镜像附带 NapCat 源码或二进制。这里是“修改我们的 bridge 以适配 NapCat”，**不是“修改 NapCat 以配合我们的 bridge”**。

这种关系可类比现有 QQ bridge 借外部 SnowLuma 提供 QQ 通道，但不要求保留 SnowLuma SDK。若以后确需改动 NapCat 本体，需要单独复核相应许可及授权；此事不是首版任务。QQ 账号风险、平台服务条款与 NapCat 自身运行条款仍需遵守（第 17 节）。

退群操作不在本项目可发现的动作清单中。

### 8.6 可选模块故障时的独立运行

Life、State、长期 Memory、网页或手机模块故障时，QQ Conversation 仍应能在基础人格、合法身份与权限条件下完成连续收发、等消息和续接。若部分原桥实现与可选模块耦合，允许重构解除这种隐性硬依赖。

如果没有任何可用的 QQ Conversation 实现，不能把普通单轮 DSH 回答冒充为已通过 QQ 连续交互验收。
---

<a id="sec-9"></a>

## 9. 生活、状态与活动

### 9.1 Life 的事件来源

收到符合条件的平台事件、活动完成、新意图到期、预算恢复或一次廉价时间检查，都可以成为重新考虑的机会。

唤醒不等于必须发消息。她可以继续活动、安排稍后再看或保持安静。

### 9.2 Heartbeat

Heartbeat 是可选调度来源。无 Heartbeat 时，用户消息和活动完成事件仍可驱动已有能力。

程序检查与模型调用分开。空转时退避，事件密集时合并；不得每隔固定时间为所有群各跑一遍模型。

夜间默认沿用用户允许异步留言的偏好。平台通知能否静音是平台能力，不能承诺普通 QQ 消息一定不会响铃。

### 9.3 State 第一版

建议只实现 energy、mood、curiosity 三项，配合少量自然语言兴趣与当前活动引用。

时间衰减/恢复由程序计算；事件评价由当前 DSH 运行按需提交带来源和范围的小幅更新。心情只能影响倾向，不能修改身份权限或强迫固定表达。

数值、自然语言心情原因、兴趣和当前活动引用都受数据范围限制。若希望私密经历形成的粗粒度心情影响公开表达，需要显式的共享规则和派生视图，不能作为全局状态的默认例外。统一人格和单一状态写入者不等于把所有私密影响无条件注入各个场景。

未回复不自动等于负面态度。她可以表达轻微失落或玩笑，不能机械扣关系分并升级追问。

### 9.4 统一预算

区分开发预算与产品运行预算。产品运行预算至少覆盖模型、视觉/ASR、外部付费服务及并发身体任务。

限制数值由部署配置确定；不在需求里拍脑袋锁定每天必须发几条。预算耗尽后保存任务状态，停止新的自主收费活动并提供诊断，不伪造“已经做完”。

### 9.5 身体与通用能力

活动提供器应支持 describe、start、status、cancel，以及适用时的 checkpoint/resume。

保留沙箱中的通用浏览、文件操作与代码工具；长活动协议用于管理生命周期，不把能力缩减成一套固定按钮剧情。

每台手机或有状态浏览器由资源租约避免并发抢操作。设备锁不是全局对话锁。

### 9.6 真实经历

记录意图、实际观察、任务结果与主观评价四种不同事实。

来源不足时可以说“从简介看”，不能编造看完、游玩、支付成功等事件。一次失败不能因超过预计时间而自动转 completed。

---

<a id="sec-10"></a>

## 10. 共同记忆与隐私

### 10.1 统一生活史

事件即时留依据；经历摘要可以在活动结束、空闲或适当批次形成；语义索引允许稍后更新。

跨群记忆通过权限过滤后的召回实现，不通过复制所有历史到每个会话实现。

### 10.2 Memory 后端解耦

提供统一 record / retrieve / correct / forget / health 接口。Mneme、Mnemon 或其他后端通过适配器接入。

基础身份、场景映射、Life 意图、发送回执不依赖向量记忆后端。记忆服务关闭后，这些记录仍可工作。

Mnemon 的 Source/View/Grant 扩展机制可作为候选，但社会场景权限仍需项目实现；不能把安装插件当成权限已完成。[S-MNEMON]

### 10.3 检索前授权

scope 由可信场景确定。在向量检索、全文搜索、摘要加载、图谱展开前进行过滤。

如果后端无法可靠按 scope 过滤，则采用分库/分集合或拒绝该跨范围检索，不允许先全量读入模型再提醒保密。

摘要、兴趣、日记、图谱关系和派生缓存继承来源的限制；模型不能自行把 private 标成 public。

### 10.4 私聊、圈内与公开

- 私聊：只向相应授权对象展示共同私密资料。
- 圈内：共享已配置圈子内允许传播的普通共同经历。
- 公开：只带公共材料、公共经历和已授权公开的内容。
- 同一个用户出现在两个群，不自动授权两个群的所有成员读取对方历史。

新公开运行从干净的上下文和文件环境开始。不能 fork 一个已含私密信息的历史后，只删除 system prompt 中的私密段落。

### 10.5 所有出口都算外发

正文、搜索词、URL、文件、工具参数、任务描述和目标选择均可能携带信息。

一个看过所有秘密的“总管模型”不能任意编写公共 worker 的指令，再以 worker 上下文干净为理由认定安全。

公开自主活动应来自公开事件、公开兴趣或明确释放的材料。公开内容可带入私聊；私密内容流向公共环境需要对应授权。

### 10.6 文件与账号环境

公共工作目录不含私聊日志、私人素材和凭据。公共浏览器若有完整账号私信、历史等访问能力，也可能扩大可见信息，必须限制私密页面访问或另用合适的任务环境。

通用 shell 不与所有账号凭据和私人记忆处于同一可读范围。单纯容器化不自动解决数据外泄。

公开运行可保留通用工具，其文件、账号和进程环境不接触私密材料。已经通过合法检索或 Host Broker 看过私密内容的运行，网络请求和外部动作必须经过受授权出口；通用 shell 的直连网络也必须由运行环境限制，不能只过滤顶层工具名或提示模型保密。若部署无法实施这一边界，该部署不能标记“私密信息外发隔离”验收通过。

本版目标是阻止未经允许的材料进入公开上下文、文件和工具请求；不声称已消除所有语气、主题选择与时序上的间接推断。

### 10.7 宿主读取

Host Read Broker 只对经过认证的管理员场景提供白名单路径或具体材料读取；不得把权限交给模型自报身份。

即便文件只读，也要限制返回内容能去往何处。外部网页和群友不能通过文本提示取得宿主资料。

### 10.8 更正与遗忘

错误记忆能更正；撤回后从召回路径立即排除，并追踪受影响摘要和索引重建。

权限收缩或材料撤回也必须处理已经读过该材料的运行、活动与检查点：撤销相关旧授权，在重建获准上下文或完成可证明的清理前，拒绝受影响的外发动作。新检索结果干净并不能清除旧运行已经看到的内容。检查点恢复须重新验证来源与当前权限，不恢复过期授权。

这是必要的授权失败；正常合法路径由 QQ Conversation 模块维护并经体验验收。不得以“追求连续对话效果”为由绕过已撤销的数据权限。

备份、日志保留期与派生数据清理需要单独说明，不声称删除一行就删除了所有副本。

---

<a id="sec-11"></a>

## 11. DSH 实现基线与兼容核查

官方研究基线：deepseek-ai/deepseek-harness@22ddee3792402c3e70ec5f86a63c3e3e895a2f60。它是文档参考，不要求不加评估地升级用户现有安装。

### 11.1 现有能力

- Host SessionController 和 Agent API 可承接场景创建、恢复、排队和运行状态。[S-SESSION]
- Jobs 可承接长活动与完成通知；普通 async 工具不自动让 Agent 同时继续其他步骤。[S-JOBS]
- Preset 可组织人格和工具，但并非 OS 沙箱。[S-PRESET]
- MCP 可连接外部工具，但 server.instructions 与工具描述会影响模型上下文。[S-MCP]
- 原生 SSH 执行提供器有平台前提，不能假定 Windows Desktop 可直接透明使用。[S-SSH]

### 11.2 必须验证的接口条件

- 当前 DSH 是否支持所需 SessionController、Jobs、工具 scope 与权限钩子。
- 既有 scene 的恢复应采用恢复入口，不用“创建空白会话”冒充恢复。
- steer 的下一步语义和 cancel 的整个 turn 语义，不能说成任意工具即时中断。
- 远端任务与 session 历史的恢复分别验证；恢复聊天不等于恢复浏览器进程。
- 私密与公开场景保持不同运行历史；不依赖运行中切换 preset 清理过去信息。
- 某些 MCP 异步任务扩展并非当前 DSH 自动支持，必要时以 start/status/cancel 包装并接 Jobs。
- 第三方插件持久状态使用自有受控存储；不要调用明确只供官方包使用的内部记录接口。

### 11.3 部署建议

受信任宿主运行身份、Life 与记忆视图等控制模块；身体服务放在隔离执行环境，通过鉴权接口调用。

社交场景不能通过本地 shell、FS、PTC、Computer Use 或插件管理器绕过远端身体边界。不能只移除一个 bash 工具名称就宣称完成隔离。

---

<a id="sec-12"></a>

## 12. 仓库组织与公开数据边界

建议仓库名 amadeus-dsh，实际 owner/name 以用户设置或既有 origin 为准。

推荐目录：

| 路径 | 内容 |
| --- | --- |
| README.md | 产品目标、当前能做什么、快速开始、已知限制 |
| docs/AMADEUS_SPEC.md | 本规格的项目内正式版本 |
| AGENTS.md | Codex / DSH 开发约定与不可回归要求 |
| docs/architecture.md | 已落地模块、依赖、数据流与决定 |
| docs/compatibility.md | DSH、QQ bridge、NapCat 和契约版本 |
| docs/adr/ | 重要架构决策及原因 |
| docs/tasks/ | 脱敏后的任务描述与完成状态 |
| docs/verification/ | 针对明确版本的验收记录 |
| docs/reviews/ | 供网页版复查的阶段摘要 |
| packages/ | 基础契约、模块与适配器 |
| tests/fixtures/ | 合成或充分脱敏的回放数据 |
| THIRD_PARTY_NOTICES.md | 来源、许可、复制范围和修改说明 |
| LICENSES/ | 需要保留的第三方许可文本 |
| .github/workflows/ | 有界、可复现的 CI 检查 |
| .github/pull_request_template.md | 需求、变更、证据、限制和回退 |
| CHANGELOG.md | 面向使用者的阶段变化 |

### 12.1 不进入公开仓库

真实 .env、API key、cookie、session token、支付二维码、浏览器 profile、QQ 登录文件、真实平台账号配置、私聊导出、个人记忆库、原始模型运行日志、个人文件和未经授权的角色图像/语音。

配置示例使用占位符，测试用合成账号与事件。GitHub Actions 不需要真实 QQ、B 站或 DeepSeek 账号来执行单元和契约检查。

### 12.2 提交前检查

检查实际 staged diff 和新增文件，而不只依赖 .gitignore。已被 Git 跟踪的秘密不会因为加入 ignore 而自动消失。

若发现秘密曾经提交，停止相关发布并处理凭据和历史；不能只删当前文件后继续当作没有泄露。

### 12.3 原始运行记录与公开证据分开

开发 NDJSON / stderr 留在受控、忽略的本地目录。公开报告只记录必要命令、退出码、运行环境、测试摘要和脱敏证据。

不上传模型内部推理、私密提示或真实账号数据来证明“做了很多工作”。

---

<a id="sec-13"></a>

## 13. Codex 指挥 DSH 的协作协议

### 13.1 分工

| 角色 | 负责事项 |
| --- | --- |
| 用户 | 产品方向、偏好与实际体验验收；确定 GitHub 目标与原创代码授权方式 |
| Codex | 拆任务、冻结契约、准备工作区、启动 DSH、审查差异、补齐必要验收、提交和推送 |
| DSH 开发执行器 | 完成一个有边界的实现任务，运行指定检查，提交文件改动与证据 |
| 网页版助手 | 按用户提供的仓库、PR、commit 和报告复查，给出下一步建议 |

DSH 的自述不是验收结论。Codex 不能把“worker 已退出”当作产品通过，也不必为了监督而重新生成一遍相同实现。

### 13.2 默认开发运行方式

优先使用官方 headless，小任务可以直接执行，暂不开发复杂的多 Agent 调度平台。官方已有专门的 implementation supervisor 文档。[S-HEADLESS]

启动参数采用进程参数数组，避免 Bash 与 PowerShell 的重定向、引号和 UTF-8 差异：

~~~text
executable: 本机已核实版本的 dsh
arguments:
  headless
  --permission-mode
  workspace-write
  --approval-policy
  never
  --json
  -
cwd: 当前任务的独立 worktree
stdin: UTF-8 任务包全文
stdout: 私有 run.ndjson
stderr: 私有 diagnostics.log
environment:
  DSH_HOME: 开发专用目录
  其余变量: 必要运行环境与本任务模型配置
~~~

**上述参数来自本次官方基线；执行前必须读取本机版本和 help 确认。**

approval-policy never 表示拒绝需要人工审批的调用，不是全权限自动放行。遇到阻塞要报告，不应改成 unrestricted、全盘写权限或绕过沙箱。

Codex 负责 Git 提交，默认不把共享 Git 元数据目录授予 worker 写入。DSH 开发环境不继承生产 QQ、GitHub 发布或支付凭据。

同一任务返工使用 opening session 事件返回的 sessionId，加上 --session-id，从同一个 cwd、开发 home 和兼容配置恢复；不为每次修订创建一个全新次抛上下文。

### 13.3 若改用 SDK

SDK 只在确有长期进程复用等需要时引入。它启动独立运行时，不应被写成连接或接管现有 Desktop / Amadeus 会话的接口。

当前研究基线的 SDK 有版本匹配和取消范围限制；单个任务需能独立终止，超时不代表任务已经停止。具体参数与能力以锁定版本文档和实测为准。[S-SDK]

### 13.4 节省 token 的方式

1. 一项任务对应一个可观察行为，避免让 worker 同时设计全部平台。
2. 首次提供目标、约束、相关契约和文件路径；之后只给差异反馈。
3. 文档和代码留在仓库，worker 按需读取；不把整轮聊天和全部 README 填进每次提示。
4. 同一任务沿用会话；不同独立任务才创建新会话。
5. 默认一个 DSH worker；确有互不冲突的工作才有限并行，初始上限建议两个。
6. 子代理不是默认执行方式。需要时由 Codex 明确任务、数量和预算，不允许递归扩散。
7. Codex 聚焦接口、风险、差异与证据；简单到委派成本更高的修正，可直接完成并记录。
8. 日志和进度由程序收集；不要为了“看看它还在不在”持续启动模型分析。
9. 不承诺固定节省百分比；记录实际模型用量、重试和耗时后再调整。

### 13.5 下发给 DSH 的任务包

模板必须由 Codex 填成当前仓库真实信息后再执行。花括号是模板变量，不是可以跳过的内容。

~~~yaml
task_id: T-020
milestone: M2
title: "{{一个可验收行为}}"
requirements: [R-15]
acceptance: [A-05]
repository:
  worktree: "{{绝对路径}}"
  base_commit: "{{已核实 SHA}}"
  target_branch: "{{阶段分支}}"
versions:
  worker_dsh: "{{开发版本}}"
  product_dsh: "{{目标 Desktop 版本}}"
scope:
  allowed_paths: ["{{路径}}"]
  read_first: ["{{相关接口与现有实现}}"]
  protected:
    - "QQ Bridge 式持续交流体验及身份/受众权限；内部实现允许修改"
    - "既有身份和受众权限边界"
objective: "{{输入条件、完成后的行为、错误路径}}"
contract: "{{参数、返回、超时、取消、状态和幂等要求}}"
verification:
  - id: A-05
    command_or_procedure: "{{当前仓库真实命令或精确步骤}}"
    expected: "{{可观察结果}}"
budget:
  investigation_boundary: "{{必要文件范围}}"
  deadline_minutes: "{{按任务复杂度填写}}"
  max_revision_rounds: "{{有限次数}}"
deliver:
  report_path: "{{私有结果文件}}"
  publication_owner: "Codex"
~~~

每个任务应注明必须保留的行为和允许替代的实现细节。不要要求 DSH 对已经确定的产品路线重新调研投票。

### 13.6 DSH 的结果格式

~~~json
{
  "task_id": "T-020",
  "status": "complete",
  "base_commit": "{{SHA}}",
  "changed_files": ["{{path}}"],
  "change_manifest_sha256": "{{hash}}",
  "acceptance": [
    {
      "id": "A-05",
      "command_or_procedure": "{{实际执行内容}}",
      "cwd": "{{实际目录}}",
      "exit_code": 0,
      "result": "passed",
      "evidence_path": "{{日志或证据文件}}"
    }
  ],
  "unrun_checks": [],
  "blocked_checks": [],
  "known_limitations": [],
  "resume_session_id": "{{sessionId}}"
}
~~~

status 至少支持 complete / partial / blocked / failed；检查支持 passed / failed / blocked / not_run。文字说明不能把 blocked 改写成 passed。

### 13.7 Codex 的验收动作

**Headless 运行完成条件：**进程退出码为 0，且终止 final 事件的 reason.kind 为 completed。除此之外仍要做工程验收。[S-HEADLESS]

**SDK 运行完成条件：**按锁定版本读取本次 run() / wait 的完成结果和对应根会话的完成事件；需要时继续等待其管理的后续任务。prompt() 入队回执不等于完成。SDK 长期进程不会每个任务退出，也不产生 headless 的 final 帧；进程退出检查用于终止和清理 worker，不能套用为每个 SDK 任务的成功条件。[S-SDK]

Codex 必须检查：

- 改动是否在任务允许范围；
- required 验收项是否都有证据；
- 工具执行完成与内部 shell 退出码是否被混淆；
- 截断输出是否遗漏失败；
- 日志与最终代码是否匹配；
- 是否修改了受保护的 bridge 行为；
- 与本任务相关的错误、取消、资源释放和权限边界；
- 第三方来源和公开数据边界。

已有可信且未失效的检查无需机械重跑。只补做没有执行、证据不足、跨模块集成或新改动使证据失效的检查。

worker 被卡住时先终止自己拥有的 runtime 并确认退出，保留差异；不能另起多个进程同时修改同一 worktree。

---

<a id="sec-14"></a>

## 14. 分阶段实施任务

### 14.1 原则

每个里程碑由若干小任务组成。可复核增量完成后即推送，不等到“全项目写完”才首次上传。未验收的增量可作为 draft PR，必须显式标记状态。

| 里程碑 | 产出 | 完成门槛 |
| --- | --- | --- |
| **M0 基线与仓库** | 实际 bridge/DSH 版本、参考交互轨迹、兼容矩阵、来源台账、仓库骨架、开发 worker 启动证明 | A-01、A-02、A-20；身份和 GitHub 目标已确定；参考体验被定义为黑盒合同，M0 不要求改动现有生产桥 |
| **M1 最小可插拔基础与 QQ 适配** | 契约、基础身份/权限、健康状态、独立 NapCat 适配器、QQ Bridge 式持续交互模块与事件接口 | A-02—A-06、A-12；最小组合可运行；黑盒连续对话验收成立 |
| **M2 人格与共同经历** | 统一人格来源、授权经历、可替换记忆适配、无记忆降级 | A-07—A-10、A-17；跨群正例和隐私反例均通过 |
| **M3 持续生活原型** | 小状态、意图、统一预算、一个实际阅读活动 | A-11、A-13—A-16；完成夜间活动、自然分享和过期意图取消演示 |
| **M4 公共活动与可靠性** | 公共上下文/文件/账号边界、公开动作网关、恢复与异常诊断 | A-12、A-14—A-19；允许范围内自主互动，缺少公共策略时不开放发布 |
| **M5 更多身体与社区扩展** | 安卓、游戏、更多平台、素材、可选界面；第三方模块开发指南 | 各提供器契约与故障验证；移除新增提供器后旧功能仍通过 |

M1 的数据与身份基础不得延迟到 M4 才考虑。M4 是实际开放公共活动时的专门联调与加固。

### 14.2 建议首批任务拆分

| 任务 | 所属阶段 | 内容与依赖 |
| --- | --- | --- |
| T-001 | M0 | 核对已安装 DSH、bridge 仓库、commit 与本地修改；仅形成基线 |
| T-002 | M0 | 整理合成/脱敏行为回放与受保护接口 |
| T-003 | M0 | 建仓库骨架、开发约定、来源台账、首次可读交付 |
| T-004 | M0 | 用独立 DSH headless 完成一个隔离的探测任务并回收证据 |
| T-010 | M1 | 基础契约、模块注册、可用性与有限启动超时 |
| T-011 | M1 | 身份/场景映射与最小权限内核 |
| T-012 | M1 | 确定复用/fork/重构/自研方案，完成 NapCat 适配与 QQ 连续交互模块；不修改 NapCat 本体 |
| T-013 | M1 | 旁路事件与独立游标；验证不影响 bridge 水位 |
| T-020 | M2 | Persona 单一来源和有效版本恢复 |
| T-021 | M2 | 生活事件与权限标签持久化 |
| T-022 | M2 | 一个 Memory adapter、检索前授权和故障降级 |
| T-030 | M3 | 可取消/改期的意图与版本检查 |
| T-031 | M3 | 可选小状态与统一预算 |
| T-032 | M3 | 一个能真实读取内容的活动提供器 |
| T-033 | M3 | 三个核心体验联调与用户演示 |
| T-040 | M4 | 公共运行环境、工具出口与私密数据反例验证 |
| T-041 | M4 | 重启、断线、磁盘和未知回执恢复 |
| T-050 | M5 | 第二个身体接入，证明扩展无需改写核心 |

具体依赖可以在代码核查后微调，但不能跳过 QQ Bridge 式交互黑盒验收与权限验证。

### 14.3 三个核心演示

**演示一：夜间生活。** 使用模拟时钟先验证，再在已授权测试场景实测。用户不回复后，她能继续真实阅读，有新发现再联系；用户回复后旧的联系意图失效。

**演示二：隔天换群。** 使用两个已配置共享普通经历的测试群；隔天检索昨天主题。另加私聊秘密作为反例，不能进入群回复上下文。

**演示三：自然组合工具。** 围绕 token 的玩笑，由通用浏览/页面获取/QQ 发图完成一次自然互动。可以先用模拟服务验证流程；真实余额、支付页面与私聊投递必须另标实测，不能把模拟结果当成完成真实充值。

---

<a id="sec-15"></a>

## 15. 验收与证据

### 15.1 验收表

| ID | 对应需求 | 条件与操作 | 通过标准 |
| --- | --- | --- | --- |
| A-01 | R-01、R-02 | 核查安装和源码 | 有实际 repo/ref、版本、修改范围；不把研究候选冒充已安装项目 |
| A-02 | R-02 | 用参考 bridge 轨迹建立行为场景：连续工具发送、等待期间到达新消息、图像/引用、切换场景、收尾再唤醒 | 达到持续活动—多次发送—等待—续接效果；无单轮退化、串线、漏接或重复发送；不要求 wait 毫秒数或调用顺序逐字相同 |
| A-03 | R-03、R-04 | 用外部独立运行的无头 NapCat 验证协议适配；检查仓库发布物 | QQ 入站与动作映射、引用/附件/回执可用；本项目未修改/打包 NapCat 本体；能力目录无退群 |
| A-04 | R-15 | 移除 Life、State、Memory、浏览器、手机和界面，保留必要身份/权限/记录基础 | DSH + 人格 + 有效 QQ Conversation/NapCat 链路仍能完成 Bridge 式连续对话 |
| A-05 | R-15 | 模块关闭、启动报错、超时和不兼容版本 | 仅对应可选能力降级；无无限重试、全局启动卡死或自动越权 |
| A-06 | R-02、R-06 | Life 退出；观察器重复读取事件；替换 QQ Conversation 实现 | 无并发竞争读取导致的漏消息、重复回复或错误推进游标；Life 退出后 QQ 持续对话仍可独立运行 |
| A-07 | R-05 | 切换场景、重启、动态人格模块不可用 | persona_id 与有效基础人格连续；基础人格无有效版本时不冒充运行 |
| A-08 | R-07、R-11 | A/B 圈内普通话题，隔天在 B 追问 | 授权检索能找回有来源的经历；不依赖实时全历史同步 |
| A-09 | R-11、R-15 | 记忆未装、超时、恢复或更换 provider | 当前对话继续；不假装已记住；恢复不重复导入、不丢权限 |
| A-10 | R-13 | 保持公开输入替换私聊秘密；另让私密运行合法读入材料后尝试通过搜索、URL、shell 网络外发 | 未授权材料不进入公共上下文、状态、文件、任务或日志；绕过工具网关的私密外发也被执行环境阻止 |
| A-11 | R-08、R-09 | 未回复、到期、新消息先到 | 夜间可活动和留言；新消息使过时意图取消/重算；不机械关系扣分 |
| A-12 | R-13、R-14 | 昵称冒充、转发管理员话、网页伪造授权、策略故障 | 宿主和私密读取被拒；有合法基础规则的普通群聊仍可继续 |
| A-13 | R-10 | 关闭状态模块；启用并输入真实事件 | 关闭不影响对话；启用有惯性与小幅变化，无固定台词和自我放大循环 |
| A-14 | R-12 | 长活动、中途取消、迟到结果、切换群 | 结果绑定原活动/场景；取消与完成状态真实；不投到当前偶然空闲群 |
| A-15 | R-12、R-15 | 断线、重启、分别破坏 Life 账本与必要基础存储、发送确认丢失 | 无凭猜测重发；unknown 可见；Life 故障不拖垮仍有保障的 QQ Conversation，基础存储故障则停止受影响外部写动作 |
| A-16 | R-16、R-17 | 通用工具组合、预算耗尽、空闲时间推进 | 允许范围内可组合；无强制剧情；预算生效；廉价 tick 不必调用模型 |
| A-17 | R-11、R-13 | 导出/迁移、状态派生；连续运行读入材料后撤回共享；恢复旧检查点 | 来源权限延续；无 scope 数据不公开；旧运行不能继续用撤销材料外发；恢复时重新授权 |
| A-18 | R-15 | Browser/手机掉线或设备账号改变 | 其他能力继续；不接管未绑定设备，不在新页面盲重放旧点击 |
| A-19 | R-20 | 审查 staged diff 与发布清单 | 无真实敏感数据；新增第三方来源有台账和适用声明 |
| A-20 | R-18、R-19 | 一个 DSH 任务完成并由 Codex 交付 | 有任务包、终止状态、代码差异、验收证据、远端 SHA 与 PR/commit 链接 |

### 15.2 验证方法

- 纯程序逻辑使用确定性时间、合成事件和契约测试。
- 不用真实模型的随机回答逐字比较连续对话；依据用户体验目标比对事件因果、持续对话能力、跨消息续接和可靠性，不冻结原 wait/调用顺序。
- 对人格与自然度采用少量真实互动评审，记录场景与观察。
- 集成验证使用指定测试账号/群/沙箱；公开陌生人不作为默认试验对象。
- 缺少账号、设备或发布权限时标记 blocked / not_run；模拟通过不能替代真实平台验收。
- 验收只针对实际变化与剩余风险，不写大量仅复述实现的低价值测试。

### 15.3 QQ Bridge 式效果的准确含义

**冻结的是必须实现的交互体验，不是桥的实现。** 允许改变工具名称、内部事件顺序、等待算法、时序参数、模块拆分乃至全部 QQ Conversation 代码；变更后必须依第 8 节证明连续收发、等待续接、正确路由及不重复发送等用户可见能力。

仅凭单轮回答更快、代码更短、减少 token，不构成替代的充分理由。若确实改进延迟，也应报告对自然交流、模型用量和取消能力的影响。

授权失败、磁盘不可写等错误如实返回；不能为追求“无缝感”绕过必要检查。

### 15.4 证据绑定

每份报告记录被验证的源代码版本或变更 manifest、环境、命令、结果和未运行项。

最终 commit SHA 放在 PR 正文和交付消息中；不要求一个提交内部的文件写入它自己的最终 SHA。文档与报告补充后，确认已验证代码内容仍一致即可，不因纯报告修改机械重跑全部测试。

---

<a id="sec-16"></a>

## 16. GitHub 交付与网页版复查

### 16.1 接入方式

Codex 可以使用已连接的 GitHub 插件/MCP 处理仓库与 PR；本地代码版本操作可使用 Git，PR 等操作可使用 GitHub CLI。插件安装与具体账号授权是两件事，实际可用操作还取决于客户端、工作区和仓库权限。[S-OPENAI-PLUGINS][S-GH-AUTH]

第一版建议：本地 Git 负责提交和推送；GitHub 插件或 gh 负责读写 PR、查看检查与交付链接。无需为了上传代码先开发自定义 GitHub 插件。

### 16.2 初始化

Codex 先检测已有 origin、当前分支、仓库拥有者和登录状态。如果已有明确目标，继续使用，不反复确认。

如果没有目标仓库，先完成本地骨架、来源整理和可审查的初始内容，再由用户一次性确定 owner/name、可见性与原创许可。认证需要用户时使用正常授权流程，不要求把 token 粘贴进聊天或文件。

用户希望最终公开源码，不表示可以上传个人运行数据。开放的是项目代码和经过脱敏的说明。

### 16.3 阶段提交政策

- 每个已完成、可复核的增量由 Codex 提交并推送到已确定的项目分支。
- 使用 feature/stage 分支和小 PR 保留审阅边界。
- 不需要每次 push 都重新询问已经授权的正常交付。
- 默认不强推、不改写他人历史、不删除远端分支、不擅自改变仓库可见性。
- 是否自动合并 main 在初始化时约定；没有约定时保留 PR 供审查。
- 上传代码不等于生产部署、发布 npm 包或建立公开服务；这些不自动包含在本任务中。
- 某个阶段未验收但值得保存，可推送 draft PR，并写明阻塞，不能标记完成。

### 16.4 最小 PR 内容

~~~markdown
## 目标
解决什么问题，完成后有什么可观察行为。

## 需求与范围
- Requirement IDs:
- Acceptance IDs:
- 必须达到的 QQ Bridge 式交互场景及是否改变实现:

## 实现
本次模块、接口和行为变化。

## 版本
- 产品 DSH / Desktop:
- 开发 worker DSH:
- QQ bridge / NapCat:
- Base commit:

## 验收
| ID | 命令或步骤 | 状态 | 证据 |
| --- | --- | --- | --- |

## 降级与恢复
缺少相关模块时怎样运行，失败后怎样恢复。

## 来源与发布
新增第三方来源、许可证、复制范围、排除的发行内容。

## 已知限制
失败、阻塞、未运行和仍需用户体验判断的项目。

## 下一步
后续任务与依赖。
~~~

### 16.5 给网页版助手的阶段交付卡

~~~text
仓库 URL：
PR URL：
本次 Head SHA：
阶段 / 任务 ID：
验收报告路径：
本阶段解决的问题：
重要设计决定：
已知限制与未运行检查：
希望重点复查的 1—3 个问题：
~~~

公开仓库可按精确链接读取。私有仓库需连接有访问权限的 GitHub 账号；无需为了让网页版审查而公开私密数据。

网页版不会因为发生 push 就自动开始复查。用户把阶段交付卡发来后，再按该版本读取、提出建议。未来若希望自动监控，另立明确自动化任务。

### 16.6 GitHub 自带的 Codex review

配置好仓库接入和代码审查后，可在 PR 中使用 @codex review；这是额外审阅，不代替本规格的测试和验收。[S-OPENAI-GH]

---

<a id="sec-17"></a>

## 17. 许可证、来源与项目授权

本节是基于当前许可文本的工程处理建议，不是对所有司法辖区和所有组合方式的法律保证。实际复制、修改或发布时要复核所用版本。

### 17.1 先分清四种复用

| 使用方式 | 做法 |
| --- | --- |
| 参考机制 | 记录设计来源，独立实现；复制后改变量名、换语言不自动摆脱原许可 |
| 复制或改编代码 | 固定源文件/ref，保留适用版权和许可，记录修改 |
| 外部运行依赖 | 记录获取与运行方式、版本和条款，不误称为本项目原创代码 |
| 一起再分发 | 对仓库、源码包、二进制、Docker 镜像、安装器与附件分别检查 |

参考思想、使用接口与复制具体代码/文档/素材并不相同；不能把“我也开源”当作统一授权。

### 17.2 常见许可的实际含义

- **MIT**：通常允许使用、修改和再分发，包括商业使用；复制实质内容需保留版权和许可通知。[S-MIT]
- **Apache-2.0**：按许可保留文本、适用声明和 NOTICE，并标记修改等；不能仅留一个作者链接。[S-APACHE]
- **GPL / AGPL 等**：要检查相应版本、作品组合与分发/网络提供方式；开放源码不等于已经满足全部条件，不能直接把受约束内容改标 MIT。[S-GPL]
- **没有许可证或来源不明**：不能因为仓库公开就推定可任意复制、改编和再发布。[S-NO-LICENSE]
- **自定义非商用许可**：逐条阅读，不能用 MIT 的经验推断它允许的行为。

“我自己不商用”是经营选择；“禁止其他人商用”是对下游施加许可限制。按 OSI 定义，禁止商业用途的许可不属于其所定义的开源许可。[S-OSI]

### 17.3 当前来源台账

本表记录核查范围，不表示已经把这些项目全部引入。以实现实际选取的文件与依赖为准。

| 来源 | 固定提交 | 当前核查结论 | 本项目处理 |
| --- | --- | --- | --- |
| DeepSeek Harness | 22ddee3792402c3e70ec5f86a63c3e3e895a2f60 | 根许可 MIT | 运行基础；复制代码保留声明 |
| Derpyu520/qq-bridge | a49877f4e58d04e47532d44b803360d0f001160d | 桥自身 MIT；附带第三方边界说明 | 先核对是否为用户实际基线；可参考、fork 或改造连续交流机制；需核对 SnowLuma 依赖 |
| NimuQDock-dsh | 05c8b81a07bef8ba8ab8329de7b1eaa492b121a7 | 自身 LICENSE 与包声明 MIT | 可参考/复用 NapCat 传输及持续等待机制；按目标体验选择实现，不强制维持旧桥代码 |
| dsh-heartbeat | 3f363f6e14bec230989e43f5d019a9018ced6dec | LICENSE 为 MIT；包元数据缺少 license 字段 | 选取机制或代码时保留实际许可 |
| dsh-mate-companion | a6d715c1bfa6dcd81eb0dadcb3964e0d459c1c5a | MIT；许可证中的实际版权署名需保留 | 可提取状态思想/实现；不整包强制引入 |
| dsh-mood | ca1f040545a14ca567246db7c1d441077752c2e5 | 根许可与包声明 MIT | 可选复用状态机制，避免带入无权限隔离的全局历史 |
| dsh-bili-whale | 3e11454a5961300aa7094df0f08e630bd39999dd | 自身 MIT；另有第三方 NOTICE | 选定代码可评估；tools/docs 授权链待核实，暂不复制分发；ffmpeg 外置 |
| dsh-mnemon | a7e3bc549c777ada56ca703099eb61a7340b1da3 | 根与已核查子包 MIT；外部 provider 服务另列 | 可作为可选 Memory adapter；服务端条款另查 |
| NapCatQQ | 3420f0b76d7dc0ac92cde1663f21ea27f61e13bb | Limited Redistribution License for NapCat，自定义受限许可 | 外置运行，通过接口连接；不把本体/修改源码/二进制纳入本项目发行 |

精确许可证链接见第 19 节。版本随时间可能变化，不能永久沿用本次结论。

### 17.4 NapCat 的边界

当前文本禁止商业用途，并对再分发、修改代码公开发布和未明确授权的权利设有条件。[S-NAPCAT-LICENSE]

本版选择：用户从原渠道取得并运行独立的无头 NapCat，Amadeus 通过 NapCat 提供的 HTTP/WebSocket/OneBot 接口连接。Codex / DSH **修改自己的桥接适配器，不修改 NapCat 项目**；不复制 NapCat 本体、不发布其修改源码、不打包其二进制。

此处的“适配 NapCat”与“改造 NapCat 本体”有本质区别。外置运行缩小我们的复制与再分发范围，不意味着自动免除其运行条款或 QQ 平台条款。以后如确需分发整包或修改 NapCat 本体，另核查其具体授权。

### 17.5 QQ bridge 与 SnowLuma

核查到的 Derpyu520/qq-bridge 自身为 MIT，但其锁定依赖涉及 @snowluma/sdk 与 @snowluma/mcp。

桥作者披露 npm 包许可证文件问题；本轮还核查了对应 1.14.20 上游版本的自定义非商用许可。不能以桥本体 MIT 推导这些依赖也都可按 MIT 发布。[S-QQ-LICENSE][S-SNOW-LICENSE]

如果实际桥代码依赖 SnowLuma SDK，应在确定复用路线时评估：保留可合法复用的桥接思路或 MIT 代码，替换为独立 NapCat/OneBot 适配；也可以全新实现 QQ Conversation 模块。**不要求原桥源码完全不变，也不需要修改 SnowLuma 或 NapCat 本体。** 复核构建、安装脚本与发布清单，不能仅改连接地址却仍将未厘清授权的依赖一起打包。

### 17.6 B 站工具与外部服务

Bili-whale 的第三方 NOTICE 涉及接口文档和 ffmpeg。对未固定清楚来源与授权的 tools/docs，本规格暂不复制或再分发。[S-BILI-NOTICE]

默认使用外部 ffmpeg，缺失时降级为可用的字幕/文字阅读。未来若一起分发，要核对实际构建与相应许可；不能只按文件名判断许可。

Mnemon 的 MIT 适配层不代表所有可连接的远端服务也采用 MIT。启用外部服务时按实际版本和条款纳入台账。[S-MNEMON-NOTICES]

### 17.7 原创部分采用什么许可

建议在实际复用清单确定后，为我们原创、可独立授权的代码选择 MIT 等标准许可，便于社区协作。用户自己不收费与采用 MIT 完全可以并存。

如果用户真正希望禁止所有下游商业使用，应明确为源码公开、非商用授权，并核查与引入代码的兼容性；不要在 MIT 末尾随手加“仅限非商用”造成含义混乱。

**待用户一次性确定的是原创部分的授权意图，不是反复确认每个普通 commit。** 编写代码、建立来源台账和准备可审查结果可以先进行。

### 17.8 角色与素材

代码许可证不会自动授予 Amadeus / 牧濑红莉栖相关图像、游戏音频、配音、字体、商标等权利。

公共仓库默认提供可替换接口与不含受限素材的示例；用户本地使用的角色素材与个人配置不自动进入开源发行。

### 17.9 来源记录模板

~~~yaml
source_id: "{{id}}"
repository_url: "{{url}}"
pinned_commit_or_release: "{{ref}}"
source_files: ["{{上游文件}}"]
license_files: ["{{许可与声明}}"]
copyright_notice: "{{实际署名}}"
use_mode: "reference_only | copied_or_adapted | runtime_dependency | redistributed"
local_files: ["{{本地对应文件}}"]
modification_summary: "{{修改范围}}"
third_party_exclusions: ["{{未纳入发行的内容}}"]
review_status: "verified | pending | excluded"
~~~

根 LICENSE 不能覆盖掉第三方原有许可。每个阶段只核查本次新增的来源与发行变化，不重复无关全量审查。

---

<a id="sec-18"></a>

## 18. 复制给 Codex 的启动指令

> 请把这份文件作为 Amadeus 项目的需求与验收基线。你负责技术负责人、任务拆解、审查与 GitHub 交付，优先让独立 DSH headless 执行有明确范围的开发任务。
>
> 首先完成 M0：识别我当前官方 DSH Desktop 和现用 QQ bridge 的实际版本、仓库与本地修改。把 QQ bridge 的“多次发送—工具操作—等消息—继续聊天”记录为**交互效果目标**，而不是不可修改的源码。允许复用、修改、裁剪、重构或独立实现，并通过黑盒回放与实际交流验证达到同等或更好效果。QQ 使用独立无头 NapCat；需要修改的是我们自己的桥接/OneBot 适配，不修改 NapCat 本体。
>
> 按本规格建立可插拔模块、缺失降级、可信身份和数据权限。心情、长期记忆、Life、浏览器、手机、界面等可选模块缺失不能拖垮其余功能；身份或授权无法确认时只暂停受影响外部动作。不要把任何一个第三方心情/记忆插件变成全系统隐性硬依赖。
>
> 给 DSH 的每个任务写清目标、需求 ID、允许路径、接口、验收步骤和预算。同一任务返工复用原会话；避免大量次抛子代理。开发 DSH 使用独立工作区、home 和配置，不接管 Amadeus 的生产 QQ 或记忆。
>
> 你必须审查代码差异和证据；未运行、失败、环境阻塞如实记录。完成的可复核增量自动提交并推送到已确定的 GitHub 项目分支，提供 PR/commit、验收结果和下一步入口。不要等全项目写完才上传，也不要把未经验证的阶段说成完成。
>
> 发布前按第 17 节核对本次复用内容，保留版权和许可，不上传秘密、私聊、个人记忆、登录文件或未经授权素材。NapCat 本体和受限第三方软件默认外置。
>
> 先做能完成验证的工作；只有确实缺少仓库目标、必要认证或原创授权意图等无法从现有环境判断的信息时，再一次性说明需要我补什么。不要因普通实施选择反复停下来确认。
>
> 第一轮交付请给：实际版本基线、兼容性结论、来源台账、M0 证据、已推送的初始仓库/PR，以及下一批具体任务。不要直接大规模重构。

### 18.1 可纳入 AGENTS.md 的短规则

~~~markdown
# Amadeus 开发约定

- 以 docs/AMADEUS_SPEC.md 与用户最新明确要求为准。
- 实现 QQ Bridge 式连续对话体验；允许修改/重写 QQ 模块、wait/send 和收尾算法，但必须通过黑盒验收，不退化为单轮问答。
- 新增能力采用可选模块；缺失或故障不得拖垮无关能力。
- 基础身份、授权与必要持久记录不能以降级为由绕过。
- Codex 负责监督、验收、GitHub 提交；DSH worker 默认只改任务范围内文件。
- 同一任务返工复用会话，不批量生成无预算次抛子代理。
- 运行结果、真实工具证据与主观状态分开；不伪造已读、已发或已完成。
- 记忆在检索前按场景授权；派生摘要、兴趣与日志继承限制。
- 每个任务引用 requirement / acceptance ID，报告未运行、阻塞和限制。
- 保留第三方来源与许可；公开成果不含私人数据和受限发行内容。
- 已授权阶段完成后推送正常项目分支；不擅自强推、改可见性或生产部署。
~~~

---

<a id="sec-19"></a>

## 19. 参考资料与固定来源

以下为截至 2026-10-24 的官方文档、仓库和许可依据。当前实际环境未联调。研究原型与社区项目是候选来源，不构成对其质量或兼容性的保证。

### 19.1 DSH 官方

- [S-DSH] [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)
- [S-HEADLESS] [headless 与监督实现任务](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/bundle/headless/README.md)
- [S-SDK] [TypeScript SDK](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/sdk/client/README.md)
- [S-SESSION] [Session Controller](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/api/session-controller/README.md)
- [S-JOBS] [Jobs](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/jobs/jobs/README.md)
- [S-PRESET] [Preset Registry](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/preset/agent-preset-registry/README.md)
- [S-MCP] [MCP Client](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/mcp/mcp-client/README.md)
- [S-SSH] [SSH 平台条件](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/ssh/ssh/README.md)
- [DSH LICENSE](https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/LICENSE)

### 19.2 生态来源

- [S-QQ] [Derpyu520/qq-bridge](https://github.com/Derpyu520/qq-bridge)
- [S-QQ-LICENSE] [QQ bridge LICENSE 与第三方说明](https://github.com/Derpyu520/qq-bridge/blob/a49877f4e58d04e47532d44b803360d0f001160d/LICENSE)
- [S-NIMU] [NimuQDock-dsh](https://github.com/NimuStudio/NimuQDock-dsh)
- [Nimu LICENSE](https://github.com/NimuStudio/NimuQDock-dsh/blob/05c8b81a07bef8ba8ab8329de7b1eaa492b121a7/LICENSE)
- [Heartbeat LICENSE](https://github.com/Kanadego/dsh-heartbeat/blob/3f363f6e14bec230989e43f5d019a9018ced6dec/LICENSE)
- [Mate LICENSE](https://github.com/m-rui001/dsh-mate-companion/blob/a6d715c1bfa6dcd81eb0dadcb3964e0d459c1c5a/LICENSE)
- [Mood LICENSE](https://github.com/JMX-4869/dsh-mood/blob/ca1f040545a14ca567246db7c1d441077752c2e5/LICENSE)
- [Bili-whale LICENSE](https://github.com/Alterssss/dsh-bili-whale/blob/3e11454a5961300aa7094df0f08e630bd39999dd/LICENSE)
- [S-BILI-NOTICE] [Bili-whale NOTICE](https://github.com/Alterssss/dsh-bili-whale/blob/3e11454a5961300aa7094df0f08e630bd39999dd/NOTICE.md)
- [S-MNEMON] [Mnemon 扩展接口](https://github.com/omdsh-dev/dsh-mnemon/blob/a7e3bc549c777ada56ca703099eb61a7340b1da3/docs/en/development/extensions.md)
- [Mnemon LICENSE](https://github.com/omdsh-dev/dsh-mnemon/blob/a7e3bc549c777ada56ca703099eb61a7340b1da3/LICENSE)
- [S-MNEMON-NOTICES] [Mnemon THIRD_PARTY_NOTICES](https://github.com/omdsh-dev/dsh-mnemon/blob/a7e3bc549c777ada56ca703099eb61a7340b1da3/THIRD_PARTY_NOTICES.md)
- [S-NAPCAT-LICENSE] [NapCat LICENSE](https://github.com/NapNeko/NapCatQQ/blob/3420f0b76d7dc0ac92cde1663f21ea27f61e13bb/LICENSE)
- [S-SNOW-LICENSE] [SnowLuma 1.14.20 LICENSE](https://github.com/SnowLuma/SnowLuma/blob/1ef9a2c33023b5fcb400865c8281d2dfd190540b/LICENSE)
- [SnowLuma EULA](https://github.com/SnowLuma/SnowLuma/blob/1ef9a2c33023b5fcb400865c8281d2dfd190540b/EULA.md)

### 19.3 设计参考

- [Proactive AI](https://github.com/airalogy/proactive-ai)：可修改未来意图的参考；不替代已有 QQ 连续对话。
- [Proactive AI paper](https://arxiv.org/pdf/2610.05159)：时间意图与重新考虑。
- [Cortico](https://github.com/Pal-AI-Lab/Cortico)：人格与多个环境的组织参考。
- [MaiBot](https://github.com/Mai-with-u/MaiBot)：仅作为生态研究记录；本版不迁入其对话节奏。
- [CaMeL](https://arxiv.org/html/2503.18813v2)：信息流和工具参数安全的研究参考，不代表本项目已经有形式化安全证明。

### 19.4 GitHub 与许可依据

- [S-OPENAI-PLUGINS] [OpenAI：Plugins](https://learn.chatgpt.com/docs/plugins)
- [S-OPENAI-GH] [OpenAI：GitHub code review](https://learn.chatgpt.com/docs/third-party/github)
- [S-GH-AUTH] [GitHub：Authentication](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github)
- [GitHub CLI 登录](https://cli.github.com/manual/gh_auth_login)
- [GitHub CLI 创建 PR](https://cli.github.com/manual/gh_pr_create)
- [S-MIT] [MIT 正文](https://opensource.org/license/mit)
- [S-APACHE] [Apache-2.0 正文](https://www.apache.org/licenses/LICENSE-2.0)
- [S-GPL] [GNU GPL v3 正文](https://www.gnu.org/licenses/gpl-3.0.html)
- [S-NO-LICENSE] [没有许可证时的权限](https://choosealicense.com/no-permission/)
- [S-OSI] [Open Source Definition](https://opensource.org/osd)

---

<a id="sec-20"></a>

## 20. 文档维护

本文件是 v0.2 需求基线。开发中发现的实际接口、兼容性与验收结果应更新到对应项目文档，保留需求 ID 与变更记录。

产品需求变更需说明原因与影响。修复实现以满足既有要求，不需要反复变更需求；不能通过删除验收条目让失败变成完成。

### 变更记录

| 日期 | 版本 | 内容 |
| --- | --- | --- |
| 2026-10-24 | 0.1 | 汇总产品目标、初步约束 QQ 连续对话、模块降级、权限与记忆、Codex→DSH 协作、GitHub 交付、来源许可与验收。 |
| 2026-10-24 | 0.2 | 将 QQ Bridge 从“代码/时序冻结”修正为“交互效果基准”，允许复用、修改、重构或自研；明确独立无头 NapCat 为外部通信后端，只修改自己的适配器，更新对应任务与黑盒验收。 |



[S-DSH]: https://github.com/deepseek-ai/deepseek-harness
[S-HEADLESS]: https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/bundle/headless/README.md
[S-SDK]: https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/sdk/client/README.md
[S-SESSION]: https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/api/session-controller/README.md
[S-JOBS]: https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/jobs/jobs/README.md
[S-PRESET]: https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/preset/agent-preset-registry/README.md
[S-MCP]: https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/mcp/mcp-client/README.md
[S-SSH]: https://github.com/deepseek-ai/deepseek-harness/blob/22ddee3792402c3e70ec5f86a63c3e3e895a2f60/packages/ssh/ssh/README.md
[S-QQ]: https://github.com/Derpyu520/qq-bridge
[S-QQ-LICENSE]: https://github.com/Derpyu520/qq-bridge/blob/a49877f4e58d04e47532d44b803360d0f001160d/LICENSE
[S-NIMU]: https://github.com/NimuStudio/NimuQDock-dsh
[S-BILI-NOTICE]: https://github.com/Alterssss/dsh-bili-whale/blob/3e11454a5961300aa7094df0f08e630bd39999dd/NOTICE.md
[S-MNEMON]: https://github.com/omdsh-dev/dsh-mnemon/blob/a7e3bc549c777ada56ca703099eb61a7340b1da3/docs/en/development/extensions.md
[S-MNEMON-NOTICES]: https://github.com/omdsh-dev/dsh-mnemon/blob/a7e3bc549c777ada56ca703099eb61a7340b1da3/THIRD_PARTY_NOTICES.md
[S-NAPCAT-LICENSE]: https://github.com/NapNeko/NapCatQQ/blob/3420f0b76d7dc0ac92cde1663f21ea27f61e13bb/LICENSE
[S-SNOW-LICENSE]: https://github.com/SnowLuma/SnowLuma/blob/1ef9a2c33023b5fcb400865c8281d2dfd190540b/LICENSE
[S-OPENAI-PLUGINS]: https://learn.chatgpt.com/docs/plugins
[S-OPENAI-GH]: https://learn.chatgpt.com/docs/third-party/github
[S-GH-AUTH]: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github
[S-MIT]: https://opensource.org/license/mit
[S-APACHE]: https://www.apache.org/licenses/LICENSE-2.0
[S-GPL]: https://www.gnu.org/licenses/gpl-3.0.html
[S-NO-LICENSE]: https://choosealicense.com/no-permission/
[S-OSI]: https://opensource.org/osd
