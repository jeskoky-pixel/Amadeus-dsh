# Amadeus

Amadeus 是一组基于官方 DeepSeek Harness（DSH）的独立插件，希望让助手在授权范围内保持连续的生活上下文。每个插件可以单独安装；本主仓库记录产品路线、接口约定、兼容性与验收，不包含插件源码。

## 现有插件

| 插件 | 使用者能做什么 | 当前状态 |
| --- | --- | --- |
| [Life](https://github.com/jeskoky-pixel/amadeus-life) | 管理未来意图、本地活动、心跳、注意力与自主预算；通过 DSH 设置查看状态、修改参数和备份 | 1.0.0 候选版，[PR #2](https://github.com/jeskoky-pixel/amadeus-life/pull/2) 审阅中 |
| [Memory](https://github.com/jeskoky-pixel/amadeus-memory) | 保存与检索获授权记忆，管理标签/实体权重、自动记忆、Embedding、备份和原 Mneme 面板 | 0.9.5-amadeus.1 候选版，[PR #1](https://github.com/jeskoky-pixel/amadeus-memory/pull/1) 审阅中 |
| [State](https://github.com/jeskoky-pixel/amadeus-state) | 维护情绪、精力、好奇心和兴趣，在设置页预览、调整、备份和恢复 | 1.0.0 候选版，[PR #1](https://github.com/jeskoky-pixel/amadeus-state/pull/1) 审阅中 |

这些候选版已分别在隔离 DSH Desktop 中从安装包进行过 GUI 与数据生命周期验收。三个插件的最终联合运行按当前开发计划暂缓；目前未公开发行、未部署生产，也未合并上述 PR。真实 QQ 发送、支付及外部活动需要单独授权和 Provider，不包含在这些候选版的验收结论中。

## 从哪里开始

1. 先阅读所需插件仓库的 README 和安装说明。Life、Memory、State 均可独立使用，不要求一次装齐。
2. 在兼容的 DSH Desktop 中通过 Plugin Manager 安装经过核验的 tarball，并在 DSH 设置窗口打开对应页面。Memory 升级前先备份旧库，并在隔离测试中明确检查 `memoryDir`。
3. 在 GUI 中查看运行状态、修改常用参数、保存并按页面提示重启；需要回退时使用各插件的备份和升级文档。

当前 tarball 是本地受控验收制品，公开下载与生产迁移仍待后续发行决定。不要从未合并的 PR 推断已有公开 Release。

## 其他模块

- [QQ](https://github.com/jeskoky-pixel/amadeus-qq)：真实平台消息适配路线；尚未作为这组三插件的联合产品验收。
- [Browser](https://github.com/jeskoky-pixel/amadeus-browser)、[Bilibili](https://github.com/jeskoky-pixel/amadeus-bilibili)、[Android](https://github.com/jeskoky-pixel/amadeus-android)、[Games](https://github.com/jeskoky-pixel/amadeus-games)：独立仓库，按后续路线开发。

## 项目文档

- [交接与当前开发状态](HANDOFF.md)
- [需求规格](docs/AMADEUS_SPEC.md)
- [多仓库架构](docs/ARCHITECTURE.md)
- [插件登记表](modules/registry.yaml)
- [阶段性产品化验收](docs/verification/THREE_PLUGIN_PRODUCTIZATION_2026-10-25.md)

Amadeus 使用 DSH 的模型、Session 和工具运行机制，不另建主推理循环。身份与权限不确定时，受影响的动作会停止或降级；可选插件缺席时，其余插件应保持独立可用。
