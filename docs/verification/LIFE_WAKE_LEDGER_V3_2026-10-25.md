# Life SQLite v3 wake ledger 检查点

日期：2026-10-25（Asia/Shanghai）。Life PR #2 提交 `b21ecb2`，未合并；主仓 PR #2 同步本记录。开发仍复用原隔离 DSH cordis Creator，会话与工作空间未替换。

原 Creator 首稿后，Codex 加入 3 个稳定失败的回归：过期 admitted 状态回滚、非十六进制 Session 形状造成有界准备饥饿、已确认本地事件仍可 claim。基线 `node --test test/wake-ledger.test.js` 为 14 pass / 3 fail / exit 1。同一 Creator 会话修复并补充本地确认与 admission 竞争测试。Codex 独立验收标准 `node --test` 四文件 66/66 / exit 0；`npm test` 四组分别 18/20/9/19 全通过 / exit 0，`git diff --check` 通过。Life 仓库详见 `docs/verification/WAKE_LEDGER_V3_2026-10-25.md` 和 ADR 0002。

真实隔离 DSH 中，确认 Creator 空闲后，按 exe、userData 和子进程核对停止独立 Desktop；对 `life.sqlite`、WAL、SHM 做本机忽略目录的冷备份并校验哈希。备份 schema=2、`integrity_check=ok`。启动同一实例后真实库 schema=3、`integrity_check=ok`，原 9 意图、4 事件、8 活动、1 租约均在，意图/事件/活动不可变业务字段逐行摘要一致。原 Creator Session 可读，cordis / deepseek-official / deepseek-flash / High，队列空；Life bundle active。私有数据库、凭据和日志未上传。

当前仅有持久 wake ledger，没有 Life 自动向 DSH 投递、完成 turn 的证据核对、idle 心跳、统一 Persona、Provider、注意力或预算。包仍标 0.1.0，不宣称 0.2.0 或 v1.0 完成；下一关是可信 Host adapter 和真实隔离 Session 验收。无生产实例或真实平台操作。
