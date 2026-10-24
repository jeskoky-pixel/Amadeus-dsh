# Amadeus 当前状态


## 2026-10-25 Life v1 最终加固状态

Life PR #2 `e9e0764`，schema v9，161/161 标准测试通过，真实隔离 DSH 迁移、生命周期、无人输入本地活动、旧工具/预算/重绑拒绝和首次预设重启通过。五项最终缺陷及额外审查边界见 [报告](../verification/LIFE_V1_FINAL_HARDENING_2026-10-25.md)。包仍为 0.1.0 开发版，无正式 release/许可选择/生产部署/真实平台 Provider。PR 未合并。


## 2026-10-25 Life v1 能力检查点

Life PR #2 `7855cdd`：schema v8、统一身份与场景、idle wake、受控本地 Provider、注意力/预算/并发、可信成本预留接口。Codex 标准测试 137/137，八组 `npm test` 通过；真实隔离 DSH v7→v8 冷迁移与原会话恢复通过。开发版包仍 0.1.0，真实平台与支付 Provider 未接入，PR 未合并。详见 [验收](../verification/LIFE_V1_CAPABILITY_2026-10-25.md)。

## 2026-10-25 Life v4 接线中

Life PR #2 `4f690c1`：v4 持久消费/turn-end 证据接口与 v2/v3→v4 安全迁移已提交，真实隔离库迁移/重启通过。Host 仍未接线；H20 失败回归先行提交，当前完整测试预期失败。原 Creator 第 18 轮正在修复实时事件和崩溃对账。主仓当前提交请以 `git rev-parse HEAD` 与 PR #2 核查；详细边界见 [v4 记录](../verification/LIFE_WAKE_CONSUMPTION_V4_2026-10-25.md)。

## 2026-10-25 Life Host 唤醒首段

Life PR #2 `ce93ff9`：到期意图 Host adapter 已默认关闭、管理员 allowlist 限定，标准 85/85 测试通过。真实隔离 DSH 的合成 Session 无用户新消息时收到 `amadeus-life` wake，在 completed turn 读取 Life 并自主取消意图；无外部工具，重启不重投。当前 wake ledger 对同 turn 内取消只记录 superseded，消费与决策事实需拆分；admitted/uncertain 对账、idle、身份、Provider、注意力和预算仍待实现。见 [Host 首段验收](../verification/LIFE_HOST_WAKE_SLICE_2026-10-25.md)。

## 2026-10-25 Life SQLite v3 增量

Life PR #2 当前 `b21ecb2`：持久 `intent_due` wake ledger、v2→v3 增量迁移，Codex 三项失败回归经原 Creator 返工，66/66 独立标准测试通过。真实隔离开发库在冷备份后从 v2 成功迁至 v3，原意图/事件/活动保留；原 Session 可恢复，Life bundle active。见 [v3 验收](../verification/LIFE_WAKE_LEDGER_V3_2026-10-25.md)。Host 自动投递、真实执行证据、idle、身份、Provider、注意力和预算仍待开发；下文较早的 schema 2 快照不代表现状。

## 2026-10-25 质量修复后更新

Life PR #2 的 0.1.0 四项缺口已修复：测试先失败 11/14，原 Creator 实施修复并经 Codex 返工审查，现全套 47/47；当前 Life 交付 SHA `fc977b1`，schema 仍为 2。真实隔离 DSH 启停、卸载/重装、强制重启恢复复验通过，原 Session/Workspace/数据库保留。详细证据见 [Life 质量关卡](../verification/LIFE_QUALITY_FIX_2026-10-25.md)。自动唤醒、Persona、Provider、注意力与预算尚未实现；用户已授权持续推进到 v1.0。下文“尚未修复”是接手前历史记录。

2026-10-25 实查。交接模式：不写新功能，所有仓库起始工作树干净。最终交接只改文档和证据路径脱敏，原功能源码保持不变。主仓库和八插件公开；GitHub 当前用户 jeskoky-pixel、九仓 ADMIN；八插件未选独立 LICENSE，主仓库既有 MIT。

## 版本与工作成果

- [主仓库 PR #2](https://github.com/jeskoky-pixel/Amadeus-dsh/pull/2)：codex/life-development，交接前 073164a2f637fb207b5bd580ad2416d870800765；本轮交接提交由该分支最新 git log / PR head 查询，避免自引用 SHA。
- [Life PR #2](https://github.com/jeskoky-pixel/amadeus-life/pull/2)：codex/life-core，当前文档交付 `61663064d9056a932ac4a5c984bea6e43f771b08`；功能/29项验证基线 46064e0856fc980ddd2d3e10e5d46afa94e3862c。源码 SHA256 清单仍与 Git blob 全部一致。
- 未合并旧 M0 草稿：主仓库 PR #1、Life PR #1、QQ PR #1。不得把合入功能分支当成合入 main，不自动关闭/合并这些 PR。
- 完整逐仓路径、remote、当前分支/HEAD、工作树、其他克隆与本地分支见 REPOSITORY_MAP。所有最终精确提交（包括主仓库交接提交）另存本机 FINAL_DELIVERY.json，并在交付消息给出。

## Life 当前能力与证据边界

包 @amadeus/life 0.1.0，Node >=22.13.0，无第三方 runtime 依赖。SQLite schema 2/WAL/BEGIN IMMEDIATE，意图 CRUD/revision CAS、活动状态机、有界 tick、持久本地 reconsideration outbox、去重、取消/重启恢复、adapter lease、Cordis service 与九个工具、会话 scope 隔离；没有其他 Amadeus 插件也可运行。

历史真实隔离 DSH 安装/禁用/启用/卸载/重装/强制重启、原 Creator 五次 Life 工具调用已验证。临时探针已移除，Life 留在开发 profile；本轮没有再次扰动宿主启停，API listBundles 仍确认启用。交接轮重新执行 node --test test/life-core.test.js test/revision.test.js，29 passed / 0 failed / exit 0，Node 22.23.2。该测试集不能覆盖全部外部审查缺陷。

原 Creator 的 cwd 正确且可按 Session ID 读取，但 Workspace.sessionIds 为空；不要通过工作区列表空推定会话不存在，详见 DSH_DEVELOPMENT。

原 DSH Creator 五轮完成、82 model steps；uncached input 96,313、cache input 12,036,352、output 141,506。用量是可观测事实，实际金额未核供应商账单，不把缓存假设为免费。空 tick 不调用模型。

## 四项缺口：尚未修复

原代码重新检查并用全新合成 DB 复现：伪造引用可将 running 完成（trustedEvidence=false）；两向跨 scope 关联及不存在 intentId 可插入；公开 ctx.life.close 后旧 dispose 仍无法释放已关闭连接上的 lease，同进程重启 degraded；非法 2月30日被归一化。ADR schema=1 与源码=2 不一致。详见 Life [QUALITY_GAPS](https://github.com/jeskoky-pixel/amadeus-life/blob/codex/life-core/docs/verification/QUALITY_GAPS.md)。跨 scope 对受害意图的直接读写未被证实；取消状态耦合仍需正式回归。模型工具完成路径也需端到端失败用例，不夸大 core 复现范围。

下一轮先保存覆盖四项的失败回归，再交原 Creator 修复；Codex 独立全量测试、diff 审查与真实开发 DSH 生命周期验收；推送既有 PR #2。此次没有声称已完成这项修复任务。

## Life 持续目标

质量修复后单独规划 0.2.0：可信自动唤醒、活动 Provider、跨会话生活身份与预算。继续逐关迭代到 v1.0：自主心跳/空闲机会、持续未来意图、注意力与活动协调、Provider 结果回执、取消/超时/恢复/对账、统一生活身份与场景隔离、预算/频率/资源限制、可选依赖降级、实际 DSH 端到端。可先用合成事件/虚拟 Provider 完整验收，不依赖 QQ。不要每完成小版本就停下等指令。

当前无自动模型唤醒、可信 provider 执行器、预算运行适配、跨会话生活身份或真实平台外发。QQ M0 仍部分完成：现用历史基线 SnowLuma 2.1.1，独立无头 NapCat 未确认；本轮未重新探查生产。QQ 与其他插件不进入本轮开发。

## 隔离与本机资料

禁止干扰生产 SnowLuma、QQ Bridge、生产 DSH 配置、登录/私聊/记忆。不使用 Computer Use、不真实发送、不擅自发布/付款/部署/merge。生产路径仅在 private/handoff 标识以防误入，不作为开发 workspace。

本机已忽略 private/（DSH_HOME、userData、已有认证、SQLite、原始会话日志/探针、工具脚本等）与 plugins/（独立仓库，不是 submodule）。本轮不复制认证、不整体打包私有运行目录。初始无未提交工作，无需补丁恢复；私有复现与库存/测试报告仅存本机。新账号请按 LOCAL_RECOVERY 恢复，不盲目运行旧启动脚本。
