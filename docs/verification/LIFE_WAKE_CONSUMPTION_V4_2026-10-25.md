# Life v4 消费证据与 Host 返工检查点

日期：2026-10-25（Asia/Shanghai）。Life PR #2 未合并。

## 已验证

- Life `52d6284` 增量加入 `wake_consumption`，把消费时有效性、Session/message/turn 坐标和 turn 结束证据单独持久化，模型工具仍为九个。v2/v3 增量迁移至 schema 4，不删除旧库。ADR 0004 与 Life 仓验收记录说明数据契约。
- Codex 在 Host 尚未接线的版本独立运行六个标准测试文件：100 pass / 0 fail / exit 0；`npm test` 六组分别 18、20、9、19、19、15 pass。`git diff --check` 通过。
- 真实隔离 DSH Desktop 的原开发 SQLite 在冷备份后从 v3 迁至 v4，`integrity_check=ok`。原 10 intents、5 intent_events、8 activities、1 wake_request、1 runtime_lease 保留，业务字段与 wake 行摘要一致；原 Creator Session 和 Life bundle 可恢复。备份与原始核验脚本只在本机忽略目录。

## 正在修复

`4f690c1` 单独提交 Host 回归 H20：可信 Session `user/message` 已消费 wake，同 turn 模型取消意图，然后 turn completed。现有 Host 未调用 v4 接口，定点测试 exit 1（`undefined !== 1`），完整测试此刻预期失败。原 Creator 第 18 轮负责实时消费固定、pre-admission 竞态、flush 后崩溃及重启保守对账；不得把当前 v4 API 或迁移通过宣称为该缺口已经修复。后续需 Codex 独立复测和真实隔离 DSH 验收。

尚未实现 idle 自主机会、可信 Persona、Provider、注意力和统一预算；不宣称 Life v1.0 完成。无外部平台发送、发布、部署或 PR 合并。
