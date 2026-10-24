# 新 Codex 唯一启动入口

## 2026-10-25 真实 Host 唤醒检查点

Life PR #2 已至 `ce93ff9`，默认关闭且 allowlist 受控的官方 Host wake adapter 已通过 85/85 测试与真实隔离 DSH 合成 Session 投递、消费、取消和重启不重投验收；原 profile 已恢复，原 Creator Session/Workspace/SQLite 保留。见 [Host 首段验收](../verification/LIFE_HOST_WAKE_SLICE_2026-10-25.md)。下一段处理同一 turn 内取消导致 wake 只记 superseded 的双重事实、崩溃对账、idle 自主机会，再继续 Life v1.0；下方较早快照按历史理解。

## 2026-10-25 Life v3 检查点

Life PR #2 已至 `b21ecb2`，SQLite v2→v3 wake ledger 通过 66/66 独立标准测试和真实隔离 DSH 冷备份、迁移、重启验收。原 Creator Session、Workspace、SQLite 保留；自动 Host 唤醒、idle、自主预算等尚未接入。下一段按 [主仓 v3 验收](../verification/LIFE_WAKE_LEDGER_V3_2026-10-25.md) 接续真实 Host adapter，不要重复实现 0.1 质量修复。下方早期快照按当时历史理解。

## 2026-10-25 后续状态

原 Life Creator Session 已恢复并继续开发，四项 0.1.0 质量缺口现已修复；失败回归提交 `889be0a`、修复提交 `92fc18b`、Life PR #2 质量交付 `fc977b1`。独立 47/47 测试与真实隔离 DSH 生命周期/重启恢复验收见 [主仓质量记录](../verification/LIFE_QUALITY_FIX_2026-10-25.md)。用户已授权连续开发至 Life v1.0；下一步研究并接入真实官方 DSH 无用户输入自动唤醒，再推进身份、Provider、注意力与预算。以下初始交接文字保留为当时快照，不要把其中“四项尚未修复、五轮会话”当作当前状态。

核查日期：2026-10-25。Amadeus 是基于官方 DeepSeek Harness 的持续陪伴 Agent，采用主仓库加八个独立可安装插件的多仓库架构。目标是统一人格、长期经历、自主意图、活动与时间感知，能力可选且有权限边界；不另造主 LLM Agent，不硬编码生活剧情。

当前开发 Life，0.1.0 是真实运行的本地 SQLite 核心，未等于完整自主生活。已有 29 项测试与真实开发 DSH 集成记录；独立审查发现四项尚未修复的边界问题。上一轮修复请求被本轮账号切换交接任务暂停，未发新修复任务给 DSH。此次只保存上下文，源码未改。

## 读取顺序

1. 根 HANDOFF.md → [CURRENT_STATE.md](CURRENT_STATE.md) → [DSH_DEVELOPMENT.md](DSH_DEVELOPMENT.md) → [REPOSITORY_MAP.md](REPOSITORY_MAP.md)。然后按需读取 docs/AMADEUS_SPEC.md、docs/ARCHITECTURE.md、Life HANDOFF 与 QUALITY_GAPS。
2. 本机存在 private/handoff 时读取 LOCAL_PATHS.md、DSH_LOCAL_RUNTIME.md、UNCOMMITTED_WORK.md、LOCAL_RECOVERY.md、FINAL_DELIVERY.json。该目录已忽略，不得上传。公开克隆没有该目录时，请用户指明原电脑的项目根，不要猜测路径或凭据。
3. 实查九仓 status/remote/HEAD/worktree/branch、GitHub 权限和 PR；本目录是日期快照，不替代新检查。
4. 只读确认原 Creator Workspace/Session 的投影、权限、模型、空队列和 completed 状态，优先复用；正常情况下无需创建新 Workspace。所有恢复接口从项目根运行。

## 可直接复制的接手指令

> 接手 Amadeus。先在当前本机工作区定位含 docs/handoff/NEXT_CODEX_START.md 的主项目；如未打开，使用用户原本的 Amadeus 项目目录，不重新创建项目。读取该入口、根 HANDOFF、CURRENT_STATE、DSH_DEVELOPMENT、REPOSITORY_MAP，以及本机 private/handoff 的路径/恢复/最终提交记录。实际核对九仓 Git、未提交内容、worktree 和所有打开 PR；核实 GitHub 当前账号及权限，需要认证时正常登录，不复制旧令牌。检查原 cordis Creator 会话 session-fa594d5f-9c84-4e51-aca1-f8ba8a3ee621、workspace cde4653e-bea0-4285-b098-059003a3714c，确认其是否 idle/completed，再复用原会话。先阅读 Life 实际源码和 QUALITY_GAPS，按失败回归→原 DSH Creator 主要修复→Codex 独立复测/审查→真实隔离 DSH 启停的顺序完成 0.1.0 四项质量缺口，更新现有 Life PR #2 和主项目 PR #2。完成后单独规划 0.2.0 的可信自动唤醒、活动 Provider、跨会话生活身份映射与预算，再持续推进 Life v1.0；给出具体执行计划并在授权范围内继续工作，不为每个小关卡反复索要批准。禁止 Computer Use、干扰生产 SnowLuma/QQ Bridge/生产 DSH、真实外发/付款/发布/部署/自动合并。不得因账号切换重建整个项目、重写现有 Life 核心、丢弃旧 DSH 有效成果或删除数据库/会话。不能读取旧聊天时，以本机核查资料和 GitHub 为准。

账号切换后的真实调用与权限仍须重新确认。本轮只确认原电脑当前 API 可访问，没有模拟新 Codex 账号登录，未新增付费模型调用。
