# Amadeus

Amadeus 基于官方 DeepSeek Harness（DSH）运行第一阶段的四个独立插件：Life、Memory、State 和 QQ。DSH 保持唯一的 Agent、Persona 与模型工具运行主体；插件分别提供活动与唤醒、记忆、状态和 QQ 交互，各自可以安装、停用和回滚。

## 第一阶段 v1.0

| 独立仓库 | 版本 | 主要能力 |
| --- | --- | --- |
| [Amadeus Life](https://github.com/jeskoky-pixel/amadeus-life) | 1.0.1 | 持久化活动与意图、预算、受控唤醒及原生设置页 |
| [Amadeus Memory](https://github.com/jeskoky-pixel/amadeus-memory) | 1.0.0 | 基于 Mneme 的长期与近期记忆、实体图谱、Scene 隔离及可信读取授权 |
| [Amadeus State](https://github.com/jeskoky-pixel/amadeus-state) | 1.0.0 | 心情、精力、好奇心与兴趣的持久演化 |
| [Amadeus QQ](https://github.com/jeskoky-pixel/amadeus-qq) | 1.0.0 | 连续 QQ Session、NapCat 接口、媒体、Owner 工具、Scene 权限与 Web Console |

四个版本已通过[隔离官方 DSH Desktop 0.2.1-rc.3 联合验收](docs/verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)，并合并到各自的 `main`。具体版本、来源提交、安装包 SHA-256 和降级关系见[兼容矩阵](docs/FOUR_PLUGIN_COMPATIBILITY.md)。QQ 的授权 NapCat 实机结果、低频能力边界与长期运行限制见其[验收记录](https://github.com/jeskoky-pixel/amadeus-qq/blob/main/docs/verification/V1_REAL_NAPCAT_2026-10-27.md)。

本阶段建立稳定开发基线；**尚未部署到日常 DSH / QQ、公开创建 GitHub Release 或发布 npm 包**。生产迁移、数据备份与灰度仍需单独执行。不能把隔离 Profile 的结果当作长期生产负载结论。

## 仓库与文档

本仓库维护架构、插件目录、兼容版本与联合验收，不复制四个插件的源码，也不使用 Git Submodule 强绑定。各插件在自己的仓库维护独立数据和安装包。其他 [Browser](https://github.com/jeskoky-pixel/amadeus-browser)、[Bilibili](https://github.com/jeskoky-pixel/amadeus-bilibili)、[Android](https://github.com/jeskoky-pixel/amadeus-android) 与 [Games](https://github.com/jeskoky-pixel/amadeus-games) 仓库属于后续计划，不属于本次 v1.0 交付。

- [联合验收报告](docs/verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)
- [四插件兼容矩阵](docs/FOUR_PLUGIN_COMPATIBILITY.md)
- [项目规格](docs/AMADEUS_SPEC.md)与[多仓库架构](docs/ARCHITECTURE.md)
- [插件登记表](modules/registry.yaml)与[版本清单](compatibility/versions.yaml)

安装前先核对目标 DSH 版本、四个 tarball 的 SHA-256 和独立数据目录。Memory 沿用 Mneme 的 Bundle ID `dsh-mneme`，同一数据库只能由一个实例写入。QQ 默认断开平台连接；Life 的外发 opt-in 只改变提示，实际发送由 QQ Scene Guard 授权。详细备份、灰度和回滚步骤见[联合报告](docs/verification/AMADEUS_V1_FOUR_PLUGIN_ACCEPTANCE.md)。
