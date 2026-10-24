# Life 真实隔离 Host 唤醒首段

日期：2026-10-25（Asia/Shanghai）。Life PR #2 代码至 `ce93ff9`，主仓 PR #2 同步状态；均未合并。原官方 DSH cordis Creator 第 13–16 轮完成主要开发与定点返工，非 headless。

Codex 首稿审查发现并用失败回归覆盖跨 Session 的 admission 前伪证、超过 500 行旧 wake 遮蔽未对账项、缺失 `ctx.on` 关闭整个 Life。真实隔离 DSH 首次加载又发现普通 `ctx.get` 取不到 Session Controller，因此补可选 `ctx.inject` 回归；随后补依赖撤销及异步 resolve/flush 竞态。最终独立标准 `node --test` 五文件 85/85 / exit 0，`npm test` 五组分别 18/20/9/19/19 全通过 / exit 0。Life 仓库 ADR 0003 和 `docs/verification/HOST_WAKE_SLICE_2026-10-25.md` 记录代码契约。

真实验收只在独立 DSH Desktop 的既有合成 Session 运行。停止隔离实例后冷备份 profile patch 与 SQLite/WAL/SHM，临时配置只允许该合成 Session。空 ledger drive 未调用模型；创建一条到期合成意图后，官方 `resolveAgent → followup → sessions.flush` 返回持久 admission。Session 事件中同一 wakeId/messageId 进入 next-turn 队列，在第 2 个 turn 被 `source.kind='amadeus-life'` 消费，`turn/end` 为 completed。模型只调用 `life_health`、`life_intent_list`、`life_intent_cancel`，自行取消意图，无真实平台工具调用。重复 drive 与隔离 Desktop 重启后再次 drive 均零投递，wake attempts 仍为 1。最后逐字节恢复原隔离 profile patch，私有探针不再加载；Life bundle active，原 Creator Session、Workspace、SQLite 保留，schema 3 integrity ok。

当前 wake 因模型在同一 turn 内取消意图而保守记 `superseded`，没有独立持久记录“已消费并 completed”事实。下一段须拆分消费证据与意图有效性，并解决 admitted/uncertain 崩溃对账。仅到期意图与 allowlist 已验证，默认关闭；idle、Persona、Provider、注意力、预算和 v1.0 十个端到端场景仍待完成。无生产、外发、付款、发布、部署或 PR 合并。
