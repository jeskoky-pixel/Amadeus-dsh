# Amadeus 项目交接

记录日期：2026-10-24（Asia/Shanghai）。本文件记录仓库初始化交付，不表示功能开发或 M0 已完成。

## 已完成

- 已核实主仓库 `jeskoky-pixel/Amadeus-dsh` 存在，公开，默认分支 `main`，用户创建时已选择 MIT。
- 已创建规划中的 8 个独立插件仓库，创建时私有，现已全部公开；默认分支 `main`。
- 主仓库保存 README、规格副本、多仓库架构约定、插件目录、兼容状态及本交接文档。
- 各插件本地克隆位于 `plugins/<仓库名>/`，各自维护 README 与 HANDOFF；主仓库忽略整个 `plugins/`。
- README 与 HANDOFF 随所属仓库提交、推送；最终提交 SHA 以本次交付消息及 `git log` 为准。
- 插件提交 SHA 和同步检查见 [仓库初始化检查](docs/verification/REPOSITORY_SETUP.md)。

## 用户已经确定的架构

1. 一个可以独立安装、升级、卸载的插件原则上一个仓库；不把普通内部功能继续细拆。
2. 主仓库是产品入口、文档、兼容与集成中心，不汇集插件源码。
3. 通过固定版本的依赖与模块清单协作，不使用 Git Submodule；当前尚无可固定的插件发布版本。
4. 身份、权限与契约先采用小型基础包；`amadeus-sdk` 是否独立发布，待接口稳定决定。
5. 可选依赖必须真的可选，权限失败时拒绝受影响动作。
6. 主仓库和全部 8 个插件均公开；后续修改可见性或生产部署需要明确指示。

## 仓库与本地入口

主仓库：https://github.com/jeskoky-pixel/Amadeus-dsh

| 插件 | 本地相对路径 | 开发顺序 |
| --- | --- | --- |
| amadeus-qq | plugins/amadeus-qq | P0 |
| amadeus-life | plugins/amadeus-life | P0 |
| amadeus-memory | plugins/amadeus-memory | P1 |
| amadeus-state | plugins/amadeus-state | P1 |
| amadeus-browser | plugins/amadeus-browser | P1 |
| amadeus-bilibili | plugins/amadeus-bilibili | P2 |
| amadeus-android | plugins/amadeus-android | P2 |
| amadeus-games | plugins/amadeus-games | P3 |

完整远端地址见 README 和 `modules/registry.yaml`。以上路径相对于主仓库根目录，插件不是主仓库子模块。

## 后续第一步：M0

- 核实本机官方 DSH Desktop / CLI 版本、来源与可用接口。
- 识别实际 QQ bridge 仓库、commit、本地修改，形成脱敏交互轨迹；文档列出的候选来源不能当作已安装基线。
- 核实独立无头 NapCat 的来源、版本与测试链路，暂不修改生产桥。
- 比较候选机制与实际兼容性，明确 QQ 连续活动的黑盒验收。
- 在独立开发 home、配置和工作区中验证 DSH headless 小任务；开发执行器不继承生产 QQ、记忆或发布凭据。
- 固定第一版身份、权限与模块契约，再给 QQ / Life 分派有范围和证据要求的实现任务。

Codex 负责拆解、审查、验收与提交；DSH 承担有边界的开发任务。默认一个 worker，同一任务返工复用会话，不把这份仓库规划理解为已经启动多个 worker。

## 未运行与限制

- 尚未核实产品 DSH、QQ bridge 和 NapCat 的实际版本。
- 尚无插件实现、打包配置、安装/升级/卸载流程或正式发布。
- 尚未验证连续对话、跨场景权限、降级、活动取消、记忆或实际设备操作。
- 当前兼容表只记录未知状态，不承诺任意插件版本能组合运行。
- 插件尚未选择独立许可证；主仓库 MIT 不自动覆盖其他仓库与第三方代码。
- 仓库创建与文档同步检查不等同于任何产品验收通过。

## 交接者注意

先阅读用户最新要求、README、本文件、架构约定及相关规格章节。原规格中“单仓库 / packages”是旧的建议布局，多仓库以本次用户明确要求为准；其余产品、权限和验收要求保留。

秘密、真实私聊、记忆、登录资料、浏览器 profile 和原始运行日志不进入 Git。提交前检查实际 staged diff。GitHub 登录凭据由本机凭据存储管理，不复制到项目或 handoff。
