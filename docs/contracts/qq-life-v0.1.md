# QQ ↔ Life 最小契约草案 v0.1

状态：M0 设计草案，尚未接入生产。契约版本 `0.1` 是项目设计版本，不代表 DSH API 或插件发布版本。需求：R-02、R-06、R-09、R-12、R-13、R-15、R-17；验收关注 A-02、A-06、A-11、A-12、A-14—A-16。

## 归属与依赖

QQ 独占入站去重、消息水位、持续交流、发送队列与回执；Life 独占未来意图与自主活动调度。Life 不调用 mark_read、不推进 QQ 水位、不直接发送 QQ，不另建主推理循环。Life 缺失时 QQ 连续对话继续。普通用户消息不额外经过一次全局调度模型。

## 旁路事件

```ts
interface EventEnvelope {
  schemaVersion: 1;
  eventId: string;          // 来源生成的稳定 ID；重放保持不变
  type: 'qq.message.observed' | 'qq.delivery.updated'
      | 'qq.scene.activity.changed' | 'activity.status.changed';
  personaId: string;
  sceneId: string;          // 来源场景，绝非当前碰巧空闲场景
  occurredAt: string;       // UTC ISO 时间
  observedAt: string;
  sourceRef: string;
  dataScopeRef: string;
  payloadRef?: string;      // 通过授权接口读取；不在总线上广播原始正文
  correlationId?: string;
  causationId?: string;
}

interface ObserverPage {
  events: EventEnvelope[];
  nextCursor: string;
  replayGap: boolean;
}
```

`observeEvents({ consumerId, cursor?, limit })` / `ackObserverCursor({ consumerId, cursor })` 只改变该观察者的旁路 checkpoint。消费者身份与获准范围由服务端绑定，不接受模型自报授权。游标不包含 QQ 的 readThroughSeq，不能拿来 mark_read。分页 limit 上限由部署限制；消费至少一次，按 eventId 去重；不得承诺 exactly-once。

服务端在读取事件或 payload 前按调用者权限过滤；游标失效明确返回 replayGap，由 Life 对账后继续，不能偷偷从空历史恢复。禁用 Life 时生产者不无限积压；保留期限和限额由配置声明。入站去重至少包含平台、账号、场景和平台事件 ID，文本相同不代表同一事件。

v0.1 演示校验器只验证上述 envelope 的形状、字段、事件类型和时间格式；它不是鉴权、去重、消息队列或产品插件实现。

## 排队与唤醒

```ts
interface WakeRequest {
  contractVersion: '0.1';
  requestId: string; intentionId: string; intentionRevision: number;
  sceneId: string; sceneBindingRevision: number;
  authorizationRevision: string; dataScopeRef: string;
  purposeRef: string;       // 意图目的与依据的授权引用，不预存要发送的台词
  expiresAt: string;
}
type WakeAck = {
  requestId: string;
  status: 'queued' | 'duplicate' | 'rejected';
  reasonCode?: 'busy' | 'expired' | 'stale_revision' | 'unauthorized'
    | 'unavailable' | 'storage_unavailable' | 'budget_unknown';
};
```

`enqueueWake(request)` 在可信网关检查场景绑定、来源 scope、当前授权、到期时间、意图版本、预算和必要存储。Life 对 requestId 去重，QQ 对入队幂等；已忙时只排队或明确拒绝，不覆盖当前 turn。`queued` 只确认记录入队，不等于运行、发出消息或投递成功。

`cancelWake({requestId, intentionRevision})` 返回 `cancelled | already_running | settled | unknown`。已运行不能伪报取消完成，应通过活动取消路径请求停止。新消息、共享撤回或意图改期使旧版本失效，出队和外发前再次检查；无法保证校验与外发原子性时，由出口网关拒绝旧授权，不仅靠调度前检查。

QQ 接受唤醒后走正常 DSH 场景入口，模型可以继续活动或保持安静。无 Life 时不影响这个入口处理正常用户输入。

## 活动提供器

```ts
type ActivityStatus = 'pending' | 'running' | 'waiting' | 'stopping'
  | 'completed' | 'failed' | 'cancelled' | 'unknown';
interface ActivityHandle {
  activityId: string; providerId: string; ownerSceneId: string;
  sceneBindingRevision: number; sourceScopeRef: string;
  authorizationRevision: string; revision: number;
  status: ActivityStatus; evidenceRefs: string[];
  checkpointRef?: string;
}
```

最小接口为 `describe / start / status / cancel`，检查点与恢复可选。start 带幂等 requestId、目的/输入引用、归属场景、scope、授权版本、预算预留与 deadline。status/cancel 查询同一 handle；不切换到当前聊天对象。设备租约只锁设备，不锁全局对话。

取消先进入 stopping，提供器确认后才能标 cancelled；超时或进程失联进入 unknown 并对账。completed 必须有真实 evidenceRefs，不能由 deadline 到期推出。迟到结果仍绑定原活动；过期、撤权或取消后的结果先归档/对账，不自动分享或恢复旧授权。恢复检查点重验来源与当前授权。

QQ 交流本身可对 Life 暴露 busy/idle 场景事件，但这些旁路通知不授予 Life 控制 QQ 内部等待、结束或已读水位的权力。

## 回执与故障

QQ 的发送事实来源仍为 `DeliveryReceipt(actionId, sceneId, providerId, platformMessageId?, confirmed|failed|unknown, evidenceRef?)`。Life 只引用回执。回执丢失不能猜测重发；平台不支持幂等时不承诺不可能实现的 exactly-once。

所有接口需有限超时与可观察错误。Life 账本损坏暂停新的自主意图，QQ 在自身必要身份/权限/记录仍有效时继续；基础身份、授权或必要发送记录故障则暂停受影响外部写动作。取消失败要隔离迟到结果；只用 Promise.race 返回超时并不足够。

## M1 必须补做的验证

1. Life 重复消费/重启/退出不改变 QQ 水位，不引起漏接或双发。
2. 活动中到新消息仍能续接；Life 唤醒不覆盖当前场景工作。
3. 旧意图改期、到期、新消息、撤权后排队请求被拒或重算。
4. 多场景、迟到结果、unknown 回执、预算/账本/策略故障均有正反例。
5. 取消与完成竞争、重连和实际 NapCat 链路分别验证；演示校验器通过不等于以上通过。
