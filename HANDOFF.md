# Amadeus 项目交接

## 最新检查点：Life 0.1.0 质量修复完成，v1.0 连续开发中（2026-10-25）

- Life 原 Creator Session 已恢复并复用，现为 9 轮 completed、116 steps；仍为 cordis / deepseek-official / deepseek-flash / High / Workspace Write。Workspace 列表未登记此 Session 的旧差异仍在，但按 ID 可读，不能据此新建。
- Life 四项缺口已先提交失败回归 `889be0a`（14 项中 11 项失败），再由原 Creator 主要修复 `92fc18b`，审查返工后全套 47/47 通过。Life PR #2 当前质量交付 `fc977b1`，未合并；主仓 PR #2 将同步本页与验收状态。详见 [质量关卡](docs/verification/LIFE_QUALITY_FIX_2026-10-25.md)。
- 真实隔离 DSH 的加载、禁用/启用、卸载/重装、强制重启恢复通过；一次私有探针命令重放造成假失败，清除旧探针命令后复验通过。诊断探针已移除，Life 保持启用，原数据库、会话与工作空间保留。生产 SnowLuma、QQ Bridge、生产 DSH 未改，无真实平台外发。
- 用户已一次授权持续推进 Life v1.0；质量关卡完成后立即研究并实现官方 DSH 自动唤醒，再推进统一 Persona、活动 Provider、注意力、预算与恢复。不以 0.2.0 为暂停点。此文件下方较早的“尚未修复”表述是账号交接时的历史状态，以本节和 Life 当前源码为准。

## 账号切换：先读这里（2026-10-25）

唯一启动入口：[NEXT_CODEX_START](docs/handoff/NEXT_CODEX_START.md)；另见 CURRENT_STATE、DSH_DEVELOPMENT、REPOSITORY_MAP。本机 private/handoff 保存真实绝对路径、恢复步骤、初始 Git 库存、复现与最终交付 SHA；已忽略，不上传。九仓实查初始干净，原 DSH 会话可读、5轮完成且无排队任务。

本轮只做交接，未修复功能。Life 29旧测试重跑通过，但四项质量缺口已核查/部分合成复现，仍待修复：伪造 running 完成、关联对象 scope/存在性、公开 close 遗留 lease、非法日期/ADR schema。历史“已验收”仅限原测试与集成范围，不能视为这些边界完整。下一轮先失败回归→原 Creator 修复→Codex 独立全套及真实开发 DSH 启停→更新既有PR。质量完成后规划0.2.0并持续到Life v1.0，不转其他插件，不触生产。


更新日期：2026-10-25（Asia/Shanghai）。**Life 0.1.0 开发版核心已验收；QQ 路线 M0 仍部分完成，二者分别记录。**

## 最新：Life 独立开发交付

- [Life PR #2](https://github.com/jeskoky-pixel/amadeus-life/pull/2)，Head 46064e0856fc980ddd2d3e10e5d46afa94e3862c。主要实现由独立官方 DSH Desktop 的 cordis Creator 完成，非 headless；模型 deepseek-official/deepseek-flash，High，官方 1017 标签匹配已核实。
- 独立会话 session-fa594d5f-9c84-4e51-aca1-f8ba8a3ee621，原会话两次返工；29/29 自动化测试通过，真实 DSH 安装、禁用/启用、卸载/重装、强制重启恢复和原 Creator 工具调用均通过。
- 正常本地克隆 plugins/amadeus-life/ 已切换 codex/life-core，README、HANDOFF、代码和证据已同步。活跃开发实例使用独立克隆 private/life-development/workspace/amadeus-life/；独立 home/userData、Cookie、模型原始日志等保持忽略，临时探针已撤销。
- 生命周期、SQLite schema v2、作用域、参数越权、取消确认和实际并发均有具体回归。Life 只有本地 outbox，不自动唤醒 DSH 或外发；授权 wake/provider/预算适配尚未实现。
- 生产 QQ Bridge、SnowLuma、DSH 配置与个人数据未改，本轮没有真实平台发送。用户要求停止 Computer Use 后，后续均由后台正式接口完成。
- 9 个仓库已核查，默认分支的旧私有说明已在独立维护提交修正；只有主仓库有既定 MIT，8 个插件许可仍待拥有者明确。[维护记录](docs/verification/REPOSITORY_MAINTENANCE_2026-10-25.md)
- 完整入口见 [Life 阶段交付卡](docs/reviews/LIFE_CORE_0.1.0.md)。下一步继续限定 Life 的授权事件消费/唤醒适配，或按用户后续要求安排；不要擅自转去改造生产 QQ。

## 本轮 M0 交接

- 实际 DSH Desktop / CLI 为 0.2.1-rc.3；bridge 为 Derpyu520/qq-bridge@a49877f4e58d04e47532d44b803360d0f001160d + 本地修改，package 0.2.1-r1；当前运行后端为 SnowLuma 2.1.1。
- 找到 NapCatQQ Desktop 3.5.4 安装器与桌面目录，未定位可确认的独立无头 NapCat。用户曾部署过但不确定是否删除；不能推定已删除，也不能将桌面壳当核心版本。
- QQ 仓库保存 20 个脱敏历史工具事件、当前桥隔离回归 10/10 摘要、DSH 事件形状校验演示；发布位置检查 16/16 通过。
- 独立 DSH 开发执行器首稿被 Codex 以语法错误拒绝，同会话修订后通过 12/12；真实工具、范围、持久 completed 与产物哈希已核查。
- 主仓库与 QQ / Life 保存一致的最小接口草案。M0 当时尚未实现运行时总线或 Life 调度；后续 Life 核心以本页最新交付为准，跨插件总线仍未实现，不把形状校验通过当作权限/集成通过。
- 报告见 [M0 验收](docs/verification/m0/REPORT.md)，任务见 [T-004](docs/tasks/T-004.md)，精确分仓版本见本轮报告目录的 delivery.json。
- M0 历史分支 codex/m0-baseline；主仓库本轮交付分支 codex/life-development，Life 为 codex/life-core。插件仓库已按用户 2026-10-25 的明确要求全部公开，未修改生产桥、未安装 NapCat、未对真实 QQ 发测试消息。

本地私有证据位于主项目忽略目录 private/m0/；wrapper 原始日志另留在本机 CodexDshDelegator 运行目录。不得将其整体上传或加载进公共运行上下文。

## 已完成

- 已核实主仓库 `jeskoky-pixel/Amadeus-dsh` 存在，公开，默认分支 `main`，用户创建时已选择 MIT。
- 已创建规划中的 8 个独立插件仓库，创建时均为私有，现已按用户要求全部公开；默认分支 `main`。
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
6. 主仓库与 8 个插件仓库均公开（2026-10-25 用户明确要求）。后续修改可见性或生产部署需要明确指示。

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

## QQ 路线剩余事项（不阻塞 Life 独立开发）

- 定位或安排部署独立无头 NapCat，核实核心版本与测试链路，保留当前 SnowLuma 工作路径。
- 确定测试账号/群，补看图、引用、跨群、活动中接消息、收尾再唤醒与断线不双发的完整黑盒实测。
- 对实际 DSH 接口和 QQ 复用/重构路线形成 ADR；研究 ref 不冒充安装包源码 commit。
- 固定第一版身份、权限与模块契约，再给 QQ / Life 分派有范围和证据要求的实现任务。

Codex 负责拆解、审查、验收与提交；DSH 承担有边界的开发任务。默认一个 worker，同一任务返工复用会话，不把这份仓库规划理解为已经启动多个 worker。

## 未运行与限制

- DSH 与现用 bridge / SnowLuma 已核实；独立 NapCat 未核实，安装包源码 commit 未知。
- Life 有已验收的开发核心与安装/卸载路径；其他插件仍为初始化或演示，尚无完整产品正式发布。
- 尚未验证连续对话、跨场景权限、降级、活动取消、记忆或实际设备操作。
- 当前兼容表只记录未知状态，不承诺任意插件版本能组合运行。
- 插件尚未选择独立许可证；主仓库 MIT 不自动覆盖其他仓库与第三方代码。
- 仓库创建与文档同步检查不等同于任何产品验收通过。

## 交接者注意

先阅读用户最新要求、README、本文件、架构约定及相关规格章节。原规格中“单仓库 / packages”是旧的建议布局，多仓库以本次用户明确要求为准；其余产品、权限和验收要求保留。

秘密、真实私聊、记忆、登录资料、浏览器 profile 和原始运行日志不进入 Git。提交前检查实际 staged diff。GitHub 登录凭据由本机凭据存储管理，不复制到项目或 handoff。
