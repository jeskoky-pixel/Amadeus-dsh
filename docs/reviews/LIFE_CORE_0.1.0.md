# Life 0.1.0 阶段交付卡

> 交接复查修正：四项质量缺口尚未修复，见 [当前状态](../handoff/CURRENT_STATE.md) 与 Life QUALITY_GAPS。下列原测试和集成记录有效，但不等于完成可信证据、关联完整性和公开 close 生命周期的完整验收。当前文档 Head 以 REPOSITORY_MAP 为准，46064e0 仍是未变的功能基线。

日期：2026-10-25；阶段：T-010 / Life 独立最小核心。结论：本轮核心通过审查与实际加载验收，未部署生产。

## 版本与链接

- 仓库：https://github.com/jeskoky-pixel/amadeus-life
- PR：https://github.com/jeskoky-pixel/amadeus-life/pull/2
- Head：46064e0856fc980ddd2d3e10e5d46afa94e3862c；主要功能提交 dfe15c4。
- 安装、API、完整报告与机器可读证据：该提交下 docs/INSTALL.md、docs/verification/REPORT.md、creator-and-runtime.json、source-manifest.json。
- 本地主入口：plugins/amadeus-life/；独立开发实例的克隆/home/userData 位于主项目忽略的 private/life-development/。

## 实现与验证

SQLite schema v2 意图/活动账本、revision CAS、有界复查 tick、持久 outbox、scope 与工具参数边界、单运行时 lease、存储错误和 Cordis 生命周期；不依赖任何其他 Amadeus 插件。

官方 DSH Desktop 0.2.1-rc.3 的真实 Creator（cordis）会话完成主要实现，Provider/Route deepseek-official/deepseek-flash，High；安装包官方 1017 标签匹配已核实。Workspace 为 Amadeus Life Development，会话 session-fa594d5f-9c84-4e51-aca1-f8ba8a3ee621。未使用 headless 冒充 Creator、子 Agent 或 Full Access。

- Codex 独立测试：node --test test/life-core.test.js test/revision.test.js，29 passed / 0 failed / exit 0；覆盖 T01—T14、真实双线程并发与针对性回归。
- 真实 DSH Plugin Manager 安装/停用/启用/卸载/重装 applied，服务与 9 个工具正确撤销/恢复。
- 独立开发进程强制重启后意图保留，遗留活动 unknown，不伪造完成或重放。
- 原 Creator 实际调用 health/create/update/cancel/get 成功，最终 cancelled / revision 3。
- 临时私有探针已撤销，Life 留在开发配置供继续开发；原始日志和认证不上传。

## 审查重点

Codex 先拒绝只通过 20 项测试的首稿，补取消确认、重复实例恢复、隐私、真实并发及清理。28 项通过后又复现 extra scope 覆盖可信会话身份的实际越权；原会话补执行时字段白名单，最终 29 项通过。不能把 JSON Schema 或 worker SUCCESS 当作授权/验收事实。

用量已记录：总计 82 模型步骤；uncached input 96,313、cached input 12,036,352、output 141,506 tokens。没有把缓存当免费，也没有猜测实际币种扣费；金额未读取供应商账单核实。Life 空 tick 不调用模型。

## 边界与未验证项

当前只产生 local-only reconsideration 事件。自动获授权的 DSH 唤醒、真实 provider、运行预算适配和平台外发未实现；更多崩溃点、多机器压力与通用宿主/网络对抗隔离未验收。QQ/NapCat 的 M0 不因本轮而变成通过。

本轮没有改动生产 QQ Bridge、SnowLuma、DSH 配置、私聊、记忆或登录资料，没有真实平台发送。8 个插件仍未确定独立许可，package private=true 防误发；仓库已公开。

优先请外部审阅：scope 与可信证据边界；SQLite revision/outbox/lease 的竞争与恢复；生命周期与真实 DSH 插件接口。不要仅看测试数量或文档完成声明。
