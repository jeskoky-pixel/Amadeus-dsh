# Codex ↔ DSH Creator 开发协议

## 2026-10-25 Life v3 增量

原 cordis Creator Session 完成第 11 轮 v3 首稿、第 12 轮 Codex 三项回归定点返工；独立 Desktop 冷备份/迁移/重启后同 Session 仍可按 ID 读取，12 轮响应、156 steps、空队列，配置保持 deepseek-official / deepseek-flash / High / Workspace Write。Life PR #2 `b21ecb2`；标准测试 66/66。下一轮从真实 Host adapter 继续，不重复派 v3 任务。用户指定官方余额耗尽时切 OpenCode Go V4.1 Flash；当前 modelCatalog 只有 deepseek-official 可路由，须先在隔离 profile 完成正常配置与验证，不复制认证或改生产。

## 2026-10-25 增量

原 Session `session-fa594d5f-9c84-4e51-aca1-f8ba8a3ee621` 已在独立 Desktop 重启后恢复并复用，质量测试、修复与两轮针对性返工后共有 9 轮 response、116 steps，空队列。配置仍为 cordis / deepseek-official / deepseek-flash / high / workspace-write / never。Life 质量修复已推至 PR #2 `fc977b1`，真实隔离宿主验收见 [质量记录](../verification/LIFE_QUALITY_FIX_2026-10-25.md)。后续继续在同一会话分派 v1.0 任务；下文五轮和 82 steps 是账号切换时快照。

2026-10-25 现场核查。以下为本机已验证的**官方 Desktop Creator 后台 API**，不是 Headless CLI 替身。官方 Desktop/自带 CLI 均 0.2.1-rc.3。CLI 不在 PATH；本机完整路径见 private/handoff。跨未来 DSH 版本接口稳定性未承诺。

## 现有实例

- Workspace 标题 Amadeus Life Development；ID cde4653e-bea0-4285-b098-059003a3714c；目录逻辑名 private/life-development/workspace/amadeus-life。
- 原 Creator session-fa594d5f-9c84-4e51-aca1-f8ba8a3ee621，preset cordis；provider deepseek-official、route deepseek-flash、reasoning high。官方前端 1017 标签匹配已从安装源核实，后端没有公开不可变构建 ID，不额外臆造。
- 独立 DSH_HOME=private/life-development/dsh-home，Electron userData=private/life-development/electron-data，profile=desktop；不是生产 home。sandbox workspace-write；approval never；permission defaultPreset 同样 workspace-write，不开 Full Access。
- 真实 profile 依赖 link 到独立 Life 开发克隆；Plugin Manager bundles 含 @amadeus/life，enabled。测试/开发 SQLite 由此独立 home 保存。会话、权限和 workspace 存储均在独立 home；Codex 账号与 DSH provider 认证是分开的逻辑配置，但账号切换后的访问仍需实测。
- 重要现场差异：workspace 存储中上述工作区确实存在、路径有效，但其 sessionIds 是空数组；原 Session 不在任何 Workspace 的 sessionIds 中。session/projections 仍可按原 ID读取，workingDirectory 精确指向开发克隆。此前 session/create({cwd,agentPreset}) 建了可恢复会话，但没有证明完成 Workspace 列表归属。不得因工作区列表空而重建/丢弃会话；本轮保留现状，尚未验证会话绑定/迁移API。
- 本轮 API 可访问，seq=721，5 轮有 response，历史 durable turn/end 均 completed，82 steps；inbox next-turn/next-step 与 userQuestions 为空。没有派新任务，不能仅凭“prompt 已接受”认定运行完成。

## 经验证的协作步骤

Codex 先读需求与源码→写精确任务包（允许文件、明确缺陷/触发、约束、验收、预算）→原 Creator 主要实现→读取结果与工具执行证据→Codex 独立测试与 diff→有问题具体返工→同会话修复→Codex 最终 commit/push/PR/交接。默认持续使用同一会话，不批量一次性 Subagent。已有 worker SUCCESS 不替代独立验收。29 测试仍漏掉新四项，必须先失败回归。

Flash 适合界定路径和输入输出、短批次、可执行验收；避免泛化整项目重设计。此前初稿/返工/安全返工/真实工具 smoke 共五轮、82 steps。Windows worker 内 node --test 子进程可能遇限制，可直接运行两个 node:test 文件，但 Codex 必须另跑标准 runner。预算记录未缓存输入/缓存输入/输出，不捏造账单金额。不要读取或公开模型内部推理。

## 后台接口与最小示例（均从主项目根运行）

本机已验证 helper `private/life-development/rpc.mjs` 导出 rpc(method, request, wrapper='request')。它从本机既有 connection.private.json 读取 loopback origin/Cookie，内部 POST `/api/<method>`。认证值不得打印、复制或上传。HTTP envelope：`{type:'client-request',rpcId:<uuid>,method,payload:{args:{request:<request>}}}`；无参方法使用 args={}，PluginManager 多参数方法直接 args。

只读检查：

```js
import { rpc } from './private/life-development/rpc.mjs';
const sessionId = 'session-fa594d5f-9c84-4e51-aca1-f8ba8a3ee621';
const p = await rpc('session/projections', { sessionId });
// 只显示 preset/model/permissions/workingDirectory/统计和完成标志；不要打印整段会话。
console.log(p.values.agentPreset, p.values.modelSelection, p.values.permissions);
```

已完成阅读/任务准备、确认 session 空闲后派单：

```js
await rpc('session/prompt', {
  sessionId, requestId: crypto.randomUUID(), mode: 'queue',
  content: [{type:'text', text:'在已核查 Life 开发克隆，先添加指定失败回归；不要改生产或其他插件。完整任务正文另由 Codex 准备。'}],
  clientTimeZone:'Asia/Shanghai'
});
```

queue/steer 已验证；`followup` 是无效 mode。默认 queue，steer 仅明确纠偏。使用唯一 requestId 保存返回；超时先查 projection/inbox/新 turn，不能盲目重复派单。HTTP 401/连接失效先恢复开发实例认证；404 不代表不存在 session，可能方法/包装错误。helper 45秒超时，PluginManager install 需要更长时限（现有脚本180秒）。轮询只输出摘要，不频繁全量 dump。

其他验证过的 API：session/modelCatalog（无参）；session/selectModel({sessionId,provider,model,reasoningEffort})；pluginManager/listBundles 与 listPlugins（无参）。`workspace/list`、`session/models` 旧探针返回404，禁止拿旧404当环境失效。

Plugin Manager 入口（仅开发 profile）：

```text
node private/life-development/manage-plugin.mjs installBundle
node private/life-development/manage-plugin.mjs setBundleEnabled false
node private/life-development/manage-plugin.mjs setBundleEnabled true
node private/life-development/manage-plugin.mjs removeBundle
```

其正式 API args：installBundle {spec:<开发克隆绝对路径>,options:{enabled:true,requestId:<uuid>}}；setBundleEnabled {name:'@amadeus/life',enabled:<bool>}；removeBundle {name:'@amadeus/life'}。需区分 application applied 与仅安装成功，并核查服务/九工具撤销和恢复。不要对生产 profile 调用。

## 恢复与完成判定

优先 `node private/life-development/status-light.mjs`，它只输出摘要但会在 private 更新完整 projection，不能上传该文件。核对 preset/model/permission/cwd、队列、最近 response；durable 日志只提取 turn/end 的 reason/completed，不显示原始推理。已验证原 session 在独立进程强制重启后可恢复；本轮未发新的模型请求验证新 Codex 账号身份。

连接过期时本机 connect.mjs 可读取**独立 Desktop 自身**日志中的启动认证 URL，内部交换 Cookie 并写回既有私有文件；不打印原 URL/Cookie。启动日志原文含凭据，不可 cat。现有 launch-desktop.cjs 曾用于独立启动，包含初始化写操作，复用前审查，不盲目覆盖现有 profile。详细物理路径/安全重启方法见 private/handoff/DSH_LOCAL_RUNTIME.md。禁止为了交接重建 workspace 或清空 home。

仅当原会话确实不可恢复：保留全部旧数据；在同开发 home 的同克隆调用 workspace/create({path})（若未登记）、workspace/rename({workspaceId,title})、session/create({cwd,agentPreset:'cordis'})、session/selectModel。此前这些方法已用过，但本轮不重新执行创建。将新 ID 另存并注明替代理由；注入 handoff、源码基线、质量缺口与任务，不能冒充原会话连续性。若 API 版本变化，先读安装源/实际 RPC 元数据，不猜新方法。

Codex 账号切换不需要把 DSH API Key 搬进新 Codex。新系统身份无法读取原本机文件、官方 provider 凭据失效或 GitHub 会话失效时走各自正常认证，不复制旧账号 token。禁止 GUI 自动操作、改生产认证或全电脑搜密钥。
