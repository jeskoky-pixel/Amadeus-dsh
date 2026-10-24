# Life Host 自动唤醒接口实测

日期：2026-10-25（Asia/Shanghai）。仅在独立官方 DSH Desktop 0.2.1-rc.3 开发 profile 运行，使用长期复用的合成测试 Session；未触生产、其他插件或真实平台。

- Host 插件的 `ctx.sessionController.prompt({requestId,sessionId,mode:'queue',content},signal)` 返回 `accepted:true`；随后 Session 持久日志出现相同 `rpcId` 的 `user/message`、`turn/start`、`turn/end completed` 和仅文本回复 `WAKE_SMOKE_OK`。这是无人工新输入的真实模型 turn，但来源被记录为 `user`，不适合作为 Life 最终来源标识；`accepted` 本身不足以判定执行。
- 官方 Schedule 包展示 `resolveAgent → agent.followup → sessions.flush` 的持久投递范式。私有 Host 探针以 `source.kind='amadeus-life'` 和稳定 `wakeId` 进行受控测试。初次构造缺少 `role`/`id`，虽然写入持久日志，模型请求因消息缺字段而失败；该合成 Session 保留以供诊断，没有删除。修正消息结构后，在新的长期测试 Session 中，`sessions.flush` 返回成功，持久 `user/message` 保留 `amadeus-life` 与 wakeId，`turn/end` 为 completed，回复 `WAKE_SMOKE_OK`。
- 该实测证明当前 DSH Host API 可无用户输入唤醒正确的 Session，且来源/关联 ID 可持久化。还没有 Life 内的自动调度、SQLite wake ledger、预算、忙碌队列/重启去重或取消失效验收。私有探针已经从 profile 撤下，原 Creator Session、Workspace、开发数据库均保留。
- 当前隔离 DSH 模型目录只显示 `deepseek-official` 为可路由 Provider。用户允许余额不足时改用 OpenCode Go DeepSeek V4.1 Flash；若触发，须先在隔离 profile 验证该路由和凭据可用，再切换原 Creator Session，不改生产、不复制认证到仓库。
