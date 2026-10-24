# Life v1.0 连续开发路线

状态：2026-10-25 执行中。用户一次授权持续开发到完整 v1.0；以下关卡用于内部验收，不是重新授权点。当前只开发 Life，主仓保存规格、契约和集成状态；实际代码在独立 Life 仓。

## 已完成：0.1.0 质量关卡

伪造 running 完成、关联对象存在性/作用域、公开 close/lease 与非法日期/ADR 已修复，失败回归先行；全套 47/47 和真实隔离 DSH 生命周期验收见 [质量记录](../verification/LIFE_QUALITY_FIX_2026-10-25.md)。当前仍只有本地 reconsideration outbox。

## 0.2 纵切：真正无用户输入唤醒

状态更新：`52d6284` 已增加 SQLite v4 消费证据和安全迁移，真实隔离开发库迁移通过。`4f690c1` 已提交同 turn 取消的失败回归；原 Creator 正把官方 Session 事件接入持久证据并处理 pre-admission 与崩溃对账。当前全套测试预期暂不通过，见 [v4 检查点](../verification/LIFE_WAKE_CONSUMPTION_V4_2026-10-25.md)。下一关仍是 idle 无待办机会，之后依次推进身份、Provider、注意力与预算；不以 v4 为完成点。

状态更新：Life PR #2 `ce93ff9` 已使合成到期意图在无人工新消息时真正唤醒隔离 DSH Session；模型读取 Life 后自主取消，turn completed、重启不重投。见 [Host 首段验收](../verification/LIFE_HOST_WAKE_SLICE_2026-10-25.md)。下一段先拆分“wake 已消费”与“旧意图已失效”的记录并对账崩溃窗口，再实现 idle 自主机会。完整 v1.0 未完成。

状态更新：SQLite v3 wake ledger 已在 Life PR #2 `b21ecb2` 完成且真实开发库迁移验收。可持久准备/领取/准入/记录 wake，但还没有 Host 自动投递与真实 turn 证据；下一步从第 2 点的 Host 部分继续。见 [v3 验收](../verification/LIFE_WAKE_LEDGER_V3_2026-10-25.md)。

1. 在独立开发 DSH 中验证 Host 调用的真实语义。已实测：`sessionController.prompt` 可无人工输入唤醒并完成 turn，但持久来源为 `user` 且返回 `accepted` 不代表执行；受控 Host `resolveAgent → followup → sessions.flush` 可保留 `amadeus-life` 来源与 wake ID 并完成 turn。私有探针构造错误的初次调用曾产生缺少 `role` 的消息和失败 turn，已保留并纠正；详情见 [Host 唤醒研究](../verification/LIFE_HOST_WAKE_SMOKE_2026-10-25.md)。
2. SQLite v2→v3 只增量迁移，保留全部意图、活动和 outbox。新增持久 wake ledger：稳定 wake ID、目标 Session、来源 scope/意图 revision、状态、领取、准入、执行、失败与对账。入队前重查意图/权限；忙碌会话只排队，不 steer/覆盖 turn；`accepted`、持久 inbox 和已执行 turn 分开记录。取消、改期、过期使旧 wake 无效。
3. 先让到期意图自动唤醒，再加入无待办时的空闲自主机会。空闲机会需管理员启用的可信 Persona/Session 绑定、低成本 eligibility 检查、频率与 token 预算、退避和可选择休息；不把每个 tick 变为模型调用。真实隔离 DSH 验证无人输入也能启动，模型可选择保持安静。

## 0.3 身份与 Provider

- 明确 Persona、Scene、Session、Principal 和 Data Scope 的独立键与可信绑定修订；模型参数不能自造绑定或提升范围。跨会话只共享被显式允许的生活状态，场景私密内容不默认流通。
- Activity Provider 协议最少 `describe/start/status/cancel`，按能力可选 `pause/resume/checkpoint/reconcile`。Provider 结果必须有受信任来源和证据引用；模型文本不能完成外部活动。用受控本地虚拟 Provider 完整运行，不对 QQ/Browser 等真实平台外发。

## 0.4 注意力、预算与可靠恢复

- 多活动优先级和资源租约；新消息只按规则暂停/切换，不直接摧毁正在进行的活动，长任务不能永久挡住用户聊天。活动结果绑定原 scope 与 revision，不发往偶然空闲的会话。
- 自主唤醒、模型调用/token、并发活动、重试/错误退避和可选付费能力由统一预算入口控制，管理员配置额度；预算耗尽推迟自主工作，不阻断合法普通聊天。
- 进程强制中断、同进程停启、重复事件、取消/完成竞争、Provider 无响应、权限撤销、损坏库、lease 冲突及可选模块缺失均需确定性测试与隔离宿主验证。未知外部结果保持 unknown，不自动重复真实动作。

## v1.0 完成关卡

以用户授权的十个端到端场景验收：无人输入自主启动、到期意图、活动闭环、新消息期间的注意力切换、跨会话 Persona 与隔离、强制中断恢复、预算、模块降级、取消/权限撤回、插件生命周期。每关同时保留自动化与真实 DSH 证据；虚拟 Provider 不冒充真实平台。插件独立许可未确定，不发布、部署或合并 PR。
