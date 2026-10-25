# M0 核查与验收记录

日期：2026-10-25（Asia/Shanghai）。**阶段结论：部分完成；独立无头 NapCat 版本与实际链路未确认，M0 不标记整体完成。**

## 产出与证据

| 用户要求 | 本轮结果 | 证据与边界 |
| --- | --- | --- |
| 核实实际版本 | DSH 与正在使用的 bridge / SnowLuma 已确认；NapCat 未确认 | baseline.json；未把桌面壳、QQ 客户端或候选项目当成无头 NapCat |
| 保存脱敏连续交互记录 | 保存 20 个同会话历史工具元数据；另跑现用桥隔离回归 | QQ 仓库 docs/m0/continuous-trace.redacted.json、BASELINE.md；不是本轮真实 QQ 发消息测试 |
| QQ / Life 最小接口 | 旁路事件、独立观察游标、版本化唤醒排队、取消、活动状态与回执草案 | docs/contracts/qq-life-v0.1.md；三个仓库保存相同副本，无运行时集成实现 |
| 独立 DSH 开发测试 | 分派、初稿拒绝、同会话返工、代码审查与 12 项测试通过 | docs/tasks/T-004.md、worker-review.json；发布位置再验 16/16 |
| 分仓交付 | QQ 保存基线、轨迹、演示代码与测试；Life 保存接口边界；主仓库保存总报告 | 各分支 codex/m0-baseline；精确插件提交与 PR 见 delivery.json |

## 实际版本与来源

DSH CLI --version、安装包 app.asar package.json、runtime 清单均为 **0.2.1-rc.3**；运行进程来自该官方分发目录。更新配置指向 `download.deepseek.com/dsh-desk/feeds/win-x64/`，nightly 渠道。安装包 app.asar SHA256 与 payload digest 已记录；未取得构建对应的源码 commit，不把文档研究 ref 当成本机 commit。安装目录的 version 文件为 44.7.0，不能将其误报为 DSH 产品版本。

实际 bridge 仓库为 Derpyu520/qq-bridge，HEAD `a49877f4e58d04e47532d44b803360d0f001160d`，package version **0.2.1-r1**。存在 9 个 tracked 工作区修改标记与 3 个 untracked 文件，其中两项 tracked 标记没有 Git 规范化后的内容 diff。baseline.json 保存全部 12 个文件的 SHA256，不上传私人角色、配置或原始差异。

已安装 preset bundle **0.2.1-r3**，SDK/MCP **1.14.20**；运行通信组件 manifest/release 为 **SnowLuma 2.1.1**，QQ 客户端目录版本 **9.9.36-53644**。bridge.lock 与运行 Node 进程、桌面 MCP 配置、后端进程路径共同构成运行关联证据，但没有证明全部本地改动均已热加载进该进程；回归测试针对磁盘源码快照。

## NapCat 核查结论

检查了运行进程、组件目录、常见 Program Files / 用户 Programs / Documents / Desktop、Kurisu 工作区、Downloads 与 D 盘 Tools / 下载目录。找到 **NapCatQQ-Desktop-3.5.4-x64.msi**、桌面壳安装目录及其数据目录，当前 components 下为 QQ 与 SnowLuma；未确认独立 NapCat 核心或可连接的无头服务。

用户补充：以前部署过，不确定是否已删除。本轮只能给出“所检查位置未发现可核实的独立无头部署”，不能证明所有磁盘或远端都没有，也不能断言它被删除。没有安装、迁移、重启或更改生产后端。后续需定位可运行的 NapCat 或另行安排部署，再记录核心版本、来源、鉴权与实际 OneBot 联调。

## 兼容性差异

- 本机 `dsh headless --help` 支持 --json / --session-id；不提供规格示例中的 --permission-mode / --approval-policy CLI flags。实测使用 profile overlay 配置 sandbox-policy=workspace-write、approval=never。
- headless 默认不加载 GUI 保存的凭据；本轮只选择当前模型请求所需的一个 credential reference，注入独立进程环境。生产 profile、QQ、记忆、登录环境和 GitHub 发布凭据未装入开发执行器。
- 本机 JSON final 事件只有 type/text，完成依据为进程退出码 0 加持久 turn/end completed。已验证同一会话续接。
- 安装包包含 SessionController、Jobs、agent preset registry 等组件；“包存在”不等于相关 QQ/Life 集成已验证。
- 当前 bridge 依赖 SnowLuma SDK；向独立 NapCat 迁移仍需自己的 OneBot 适配器，不能仅改 URL 就宣告兼容。

## 本轮运行检查

测试运行环境 Windows / Node 22.23.2；DSH CLI 来自已核实安装包；wrapper 使用捆绑 Python 3.12.14。

| 检查 | 实际命令或过程 | 结果 |
| --- | --- | --- |
| 现用 bridge 隔离等待回归 | node --test <本机 bridge>/scripts/test-reply-wait.mjs | 退出 0；10/10，无真实 QQ 投递 |
| DSH 初稿独立检查 | node --test private/m0/worker-workspace/validate-envelope.test.mjs | 退出 1；模块语法错误，拒绝初稿 |
| 同会话修订 | headless --json --session-id <原会话>，同 home / cwd / profile | 退出 0；持久 completed；仅修订允许文件 |
| 修订后独立检查 | node --test private/m0/worker-workspace/validate-envelope.test.mjs | 退出 0；12/12 |
| QQ 发布位置与轨迹记录检查 | node --test plugins/amadeus-qq/tests/validate-envelope.test.mjs plugins/amadeus-qq/tests/continuous-trace.test.mjs | 退出 0；16/16 |
| 三仓契约与产物校验 | 校对契约副本及 worker publishedArtifacts SHA256、检查 staged diff | 见 checks.json |

TAP、NDJSON 与内部会话只留私有本地证据。历史轨迹保留来源快照哈希与原始行号；移除正文、返回体、平台账号、原始会话 ID、凭据、URL、路径与绝对时刻。时间仅是相对顺序，不把 wait 结果推断为消息到达或独立投递确认。

## 验收状态

| 验收 ID | 状态 | 依据 |
| --- | --- | --- |
| A-01 | partial | 实际 DSH/bridge 分发版本、来源、修改范围已记录；安装包源码 commit 未知，NapCat 另列未确认 |
| A-02 | partial | 真实历史多次发送/等待/续接元数据 + 10 项隔离测试；图像/引用、切群、收尾再唤醒、断线与新后端实测未覆盖 |
| A-03 | blocked | 没有可核实版本和链路的独立无头 NapCat；未运行其适配验收 |
| A-06 | not_run | QQ / Life 只有接口草案，没有事件游标与运行时故障联调 |
| A-19 | scoped_pass | 本次新增材料来源与 staged diff 检查；不上传生产代码差异、秘密、私聊、角色原文或原始模型日志 |
| A-20 | passed | 有任务包、真实 DSH 运行、初稿失败、原会话返工、Codex 代码与范围审查、独立测试及精确交付版本 |

## 下一步

1. 先定位或安排部署独立无头 NapCat，核实核心版本与合法测试链路；不覆盖当前 SnowLuma 工作路径。
2. 明确测试账号/群后补完整黑盒场景，不能用陌生公开群作为默认试验对象。
3. 依据实际 DSH 接口评估 QQ 模块复用/重构路线，形成 ADR，再推进 M1 身份/权限和可选模块基础。

本轮没有部署生产、自动发布安装包或大规模重构。只有验收已覆盖的增量进入本次 PR，剩余工作保持可见。
