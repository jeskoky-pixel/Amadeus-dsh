# Amadeus

基于官方 DeepSeek Harness（DSH）的持续陪伴系统，目标是在不同场景维持统一人格、积累获授权的共同经历，并通过 QQ、浏览器、手机等能力开展真实活动。

**当前状态：M0 部分完成。已核实 DSH / 现用 bridge / SnowLuma，保存脱敏历史轨迹、接口草案并完成独立 DSH 开发验证；独立无头 NapCat 尚未确认，尚无可安装生产插件。**

## 项目组织

采用“主仓库 + 独立插件仓库 + 统一接口规范”。一个能够独立安装、升级、卸载的插件原则上对应一个仓库；插件内部一起升级的普通功能不再拆仓。

本仓库负责需求、架构、插件目录、兼容版本、安装组合与未来集成验收。插件代码在各自仓库维护，通过版本清单协同，不使用 Git Submodule 强绑定。

| 仓库 | 职责 | 优先级 | 状态 |
| --- | --- | --- | --- |
| [Amadeus-dsh](https://github.com/jeskoky-pixel/Amadeus-dsh) | 项目主入口与集成文档 | 基础 | 公开；文档初始化 |
| [amadeus-qq](https://github.com/jeskoky-pixel/amadeus-qq) | 外部 NapCat 适配、连续交流、媒体与主动发送 | P0 | 私有；M0 基线与契约演示 |
| [amadeus-life](https://github.com/jeskoky-pixel/amadeus-life) | 注意力、心跳、活动、未来意图与预算协调 | P0 | 私有；M0 接口草案 |
| [amadeus-memory](https://github.com/jeskoky-pixel/amadeus-memory) | 授权的跨场景经历、关系与记忆后端适配 | P1 | 私有；仅初始化 |
| [amadeus-state](https://github.com/jeskoky-pixel/amadeus-state) | 心情、精力、兴趣与状态演化 | P1 | 私有；仅初始化 |
| [amadeus-browser](https://github.com/jeskoky-pixel/amadeus-browser) | 受控浏览与长时间浏览活动 | P1 | 私有；仅初始化 |
| [amadeus-bilibili](https://github.com/jeskoky-pixel/amadeus-bilibili) | 视频阅读、评论、收藏与获授权互动 | P2 | 私有；仅初始化 |
| [amadeus-android](https://github.com/jeskoky-pixel/amadeus-android) | 绑定安卓设备与应用操作 | P2 | 私有；仅初始化 |
| [amadeus-games](https://github.com/jeskoky-pixel/amadeus-games) | 游戏能力集成与游戏活动提供器 | P3 | 私有；仅初始化 |

私有插件仓库目前仅对获授权账号可见。P0—P3 表示计划顺序，不代表开发已经开始。`amadeus-sdk` 暂不独立建仓，基础契约稳定后再决定。

## 文档入口

- [交接与下一步](HANDOFF.md)
- [M0 验收记录与未完成项](docs/verification/m0/REPORT.md)
- [QQ ↔ Life 最小接口草案](docs/contracts/qq-life-v0.1.md)
- [需求规格 v0.2](docs/AMADEUS_SPEC.md)
- [多仓库架构约定](docs/ARCHITECTURE.md)
- [插件登记表](modules/registry.yaml)
- [兼容与发布状态](compatibility/versions.yaml)
- [仓库初始化检查与插件提交版本](docs/verification/REPOSITORY_SETUP.md)

## 必须保留的行为

- 使用官方 DSH 作为推理与工具运行基础，不另建主 Agent 推理循环。
- QQ 支持持续收发、工具操作、等待与续接；bridge 是交互效果基准，允许修改或重写实现。
- NapCat 独立运行，只开发自己的适配器，不修改或捆绑 NapCat 本体；不提供退群动作。
- 统一人格与隔离的场景上下文并存；记忆检索前授权，派生材料继承来源限制。
- 可选插件缺失不拖垮无关能力；身份、授权或必要记录不可确认时，暂停受影响动作。
- 活动与投递状态必须有证据，模拟验证不能代替真实平台验收。

## 本地布局

项目根目录是本主仓库。`plugins/` 下是独立克隆的插件仓库，各自包含 README、HANDOFF 和独立 `.git`，该目录被主仓库忽略。主仓库不会替插件提交或推送代码，也不记录插件工作树。

当前尚无安装命令、稳定契约版本或正式发布。QQ 仓库有可独立运行的事件形状校验演示与记录检查，不代表 QQ 插件已经实现。后续先补独立 NapCat 与完整交互基线，再推进 P0 插件。
