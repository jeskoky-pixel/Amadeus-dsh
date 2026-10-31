# Amadeus main 整合验收 — 2026-10-31

## 结论与发行边界

五仓库最新候选已进入 main，可进入下一轮产品化与正式发行准备。不是“全部生产实机验收通过”。用户追加要求兼容缺陷应修复并升级版本，因此 QQ / Life 为 1.2.1；Memory 为 1.3.0，State / Persona 保持 1.2.0。用户再次确认：仅合并 main，正式发布等下一轮。

没有创建 Tag、GitHub Release、npm 发布或 Market 投稿。五仓库合并前后 Release / Tag 列表一致。发布 workflow 仅接受手动触发且受 Tag 条件保护，main push 只执行检查和上传 CI 测试产物。

## 最终主分支

| 仓库 | 包名与版本 | main Commit SHA |
| --- | --- | --- |
| qq | `@fg-lab/amadeus-qq@1.2.1` | `5aba529aff84ef4eb43b1c1d12aec32286fc06b8` |
| memory | `@fg-lab/amadeus-memory@1.3.0` | `c2fea2f2c28cb43c1eedda85c9ab1dfa8076213b` |
| life | `@fg-lab/amadeus-life@1.2.1` | `9e973b5c2da53ff8a1ccb17176a8c7c22199b7d3` |
| state | `@fg-lab/amadeus-state@1.2.0` | `5ca357ea0d43735532433656742b6c1ef94b085c` |
| persona | `@fg-lab/amadeus-persona@1.2.0` | `74597508b456a020c91be0e1274b3cc515458086` |

各 main tree 与最终本地受测候选 tree 一致；通过 GitHub API 读取远端 SHA、package.json、检查状态，而非依赖旧本地追踪分支。普通 Merge Commit 保留历史，无强推、无绕过保护。

## PR 处理结果

| PR | 状态 / 目标 | Merge Commit |
| --- | --- | --- |
| qq [#5](https://github.com/jeskoky-pixel/amadeus-qq/pull/5) | Merged → `main` | `caff213bba525a8a1fb647247b0516c5f61ac418` |
| qq [#6](https://github.com/jeskoky-pixel/amadeus-qq/pull/6) | Merged → `main` | `54374676e09b22daf28125a261b0d0509c8f2544` |
| qq [#7](https://github.com/jeskoky-pixel/amadeus-qq/pull/7) | Merged → `main` | `c79fc89b0c74379fa0889a2af60a09b4e393c7c1` |
| qq [#8](https://github.com/jeskoky-pixel/amadeus-qq/pull/8) | Merged → `main` | `5aba529aff84ef4eb43b1c1d12aec32286fc06b8` |
| memory [#6](https://github.com/jeskoky-pixel/amadeus-memory/pull/6) | Merged → `main` | `ba6da1569d9378a7b04412ba07f2b9162835ef5a` |
| memory [#7](https://github.com/jeskoky-pixel/amadeus-memory/pull/7) | Merged → `main` | `c2fea2f2c28cb43c1eedda85c9ab1dfa8076213b` |
| life [#5](https://github.com/jeskoky-pixel/amadeus-life/pull/5) | Merged → `main` | `f70d6ce8b0184104f3527ecadd74451aa60584c0` |
| life [#6](https://github.com/jeskoky-pixel/amadeus-life/pull/6) | Merged → `main` | `9e973b5c2da53ff8a1ccb17176a8c7c22199b7d3` |
| state [#4](https://github.com/jeskoky-pixel/amadeus-state/pull/4) | Merged → `main` | `5ca357ea0d43735532433656742b6c1ef94b085c` |
| persona [#2](https://github.com/jeskoky-pixel/amadeus-persona/pull/2) | Merged → `main` | `74597508b456a020c91be0e1274b3cc515458086` |

原有七个 PR 全部合并。QQ #5 先进入 main，#6 从旧发行分支改为 main 后合并；其 provenance journal、可信普通 Session 判定完整保留。Memory #6 先合并，#7 保留全部 ACL/Learn/Borrow 功能并升级 1.3.0 后合并。

新增 QQ #7 / Life #6 是本轮实测发现的兼容性修复；QQ #8 修正浏览器发现的旧版本页脚并增加版本一致性断言。Persona #2 还修正了手动发布工作流引用不存在 build 命令的问题，改用 build:client；没有运行发布工作流。

## 实际发现与修复

独立官方 Host 首次联合运行复现：QQ 预留裸 UUID 被 Host 接受，但 Life 的 Workspace 绑定只接受 session-UUID，因而含 QQ Session 的 Workspace 绑定失败。

- QQ 1.2.1：新预留 ID 使用 session-UUID，journal 仍在创建前原子记录。
- Life 1.2.1：已有裸 UUID 作为严格格式的历史 Session ID 兼容；同步 bootstrap、带 LIMIT 的唤醒 SQL 前置过滤、配置界面校验。可信 Workspace 成员关系、Host 身份和唤醒 authorization_ref 检查不变。
- 不重命名旧 Session，不重建会话，不删除旧 journal 或历史，不改变数据 schema。
- 回归先在旧实现失败，再在修复后通过；额外检查未经授权的 UUID 不产生唤醒。

## Memory 1.3.0

package.json、lockfile 根包版本、隐私打包门禁、版本测试、安全支持版本表、README、CHANGELOG、Release Notes、安装文档均同步为 1.3.0。运行时版本从 package.json 读取；runtime-manifest.json 的 manifestVersion=2 及依赖闭包版本不应跟随插件版本修改。保留包名 @fg-lab/amadeus-memory 和内部 ID dsh-mneme。

保留现有统一 ACL schema 2。新增磁盘重开测试逐表比较所有持久化行，并检查记忆正文、Learn 撤权保留、源记忆撤权拒绝、第三方转分享拒绝；不是只比较行数。已有 schema 1→2 迁移、备份和回滚测试继续通过。没有因版本升级重置 Mneme 数据结构。QQ 仍为可选服务，普通 DSH 独立运行由现有回归覆盖。

## 最终质量门禁

| 插件 | 默认完整质量门禁 | 构建 / 同步、发行包检查 | 合并后检查 |
| --- | --- | --- | --- |
| QQ 1.2.1 | 303 通过、4 跳过、0 失败 | 通过；页脚修正另有定点测试 | main CI + tree 一致性 |
| Memory 1.3.0 | 782 通过、0 跳过、0 失败 | sync、源/包检查通过 | main CI + tree 一致性 |
| Life 1.2.1 | 211 通过、0 跳过、0 失败 | 通过 | main CI + tree 一致性 |
| State 1.2.0 | 141 通过、0 跳过、0 失败 | 通过 | main CI + tree 一致性 |
| Persona 1.2.0 | 49 通过、0 跳过、0 失败 | build:client / 打包通过 | main CI + tree 一致性 |

五仓库最终 main 的 GitHub verify 检查均已成功。

合计 1486 通过、4 跳过。QQ 的四项跳过是本机 Windows 不具备符号链接创建能力的媒体/附件安全场景，不计为通过。Memory 使用仓库 npm test 的 quality:amadeus 全部产品门禁；不把未执行的上游独立发行流水线测试算入。

旧 RC 在合并前也重新执行：QQ #5 为 299 通过 / 4 跳过；Memory #6 为 725 通过；Life #5 为 209 通过。源码未变化时不反复运行完整本机测试；合并后的 main CI 对最终树再次检查。

## 五插件独立运行环境联合验证

本轮使用官方 DSH Desktop 0.2.1-rc.3 附带 CLI / Web Host，新建独立 DSH_HOME 与 Profile，通过官方 dsh plugin add 安装真实本地 tgz；不是直接从开发目录 import 来冒充安装。没有操作当前主 Desktop Profile。

已完成：

1. 五包同时加载，唯一 dsh-mneme，Host 服务无重名注册冲突，构建与 Bundle / Client 资源完整。
2. 创建真实 Host Workspace、三个普通 Session，并建立三个合成群聊 Scene 和一个合成私聊 Scene；实际调用 Host Session 创建和持久化 API。
3. QQ 的旧裸 UUID 和新 session-UUID 同时保留；真实 QQ Host 可信映射被 Memory 接受，Life 成功绑定全部 Session，State 同一 Workspace 可见七个成员。普通 Session 经 QQ provenance 验证为 ordinary。
4. 安装包中的 Memory runtime 使用真实 Host Session / Workspace 身份，在独立合成数据库验证 Learn 撤权保留、Borrow 撤权失效、禁止转分享。另有完整模块测试覆盖普通/QQ 身份配对、来源约束与迁移。
5. Life / State / Persona 绑定同一 Workspace；State、Persona 通过受 Host Owner 鉴权保护的 HTTP 管理 API 保存配置。Life 外发禁用。
6. 保留测试数据从 QQ/Life 1.2.0 升级 1.2.1，重启后旧 Session ID、映射、学习依赖、Workspace 绑定和 Persona 核心配置保留。
7. 真实 Edge 浏览器打开 Memory 原四页加 Amadeus 第五页，以及 QQ、Persona 控制台；无 pageerror。QQ/Persona 匿名访问遭 401/403 拒绝。Life/State 使用宿主原生 Client 页面，并非独立 /console 路由。

此处 QQ 连接为仅本地合成 OneBot HTTP/WS 服务，没有连接真实 NapCat，也没有向真实平台发送。界面的“已连接”只表示该测试连接，不能当作生产 QQ 通过。

Memory 页面截图：[真实隔离页面](docs/screenshots/main-integration-memory-20261031.png)。该页面为无用户数据的空状态，ACL 验证数据在独立契约测试数据库中。

### 未重新验证的边界

- 未在主 DSH 重装、清库、切换模型或操作真实 NapCat；真实入站、连续模型对话、QQ/Life 外发本轮未复验。
- 本轮为 Desktop 自带运行时的独立 Web Profile，未再次操作 Electron 图形安装器。
- 未运行新的模型请求；Persona 最终提示词诊断明确返回 agent_not_live，不能标为本轮真实模型注入已通过。其自动化注入/工具集合回归与历史证据保留。
- Life/State 原生设置页面完整交互、长期 State 演化及真实 Life 自主外发未逐项复验；共同加载、绑定、数据持久性及各插件自动化门禁通过。
- State 的 QQ 场景级规则与 Memory 写入属于已有可选契约边界，未假定为已实现；当前支持可信 Session 级规则。Persona 正确提示无法从 Memory 公共契约确认人格卡开关，不静默修改 Memory 配置。
- 公开下载、npm/Market 安装与发行授权属于下一轮。上述限制不应在后续报告中被改为通过。

## 源码回退与数据安全

| 仓库 | 合并前 main SHA | 备份分支 |
| --- | --- | --- |
| qq | `7832323c0604dc0a78c38d17cd4d8f1fb54db099` | `backup/pre-main-integration-20261031` |
| memory | `218244c1f510bbbf59781c1a0019393f1e76be3b` | `backup/pre-main-integration-20261031` |
| life | `ad8863e7b520410faa2f3c7fad49ad580083b079` | `backup/pre-main-integration-20261031` |
| state | `73c8ded7d0850fd50293ce10f8d4177b69244f40` | `backup/pre-main-integration-20261031` |
| persona | `3319f8623a31c574e7fd2a6e3a73e46486f179e0` | `backup/pre-main-integration-20261031` |

Memory 额外保留 backup/amadeus-memory-pre-unified-session-acl，指向 `d1c1aa405ad12092ac125ee00798476a5069d07b`。既有候选包与数据库回退证据保留。Git 备份只恢复代码；有 schema 变化时必须恢复匹配数据快照，不能假定旧程序可读取新库。本轮仅临时隔离合成数据发生写入，主 Memory、Life、State、Persona、NapCat 和 QQ 登录状态未改变。

## 本地候选安装包

这些文件位于本轮私有验收目录，未作为公开 Release 附件发布。

| 安装包 | SHA-256 |
| --- | --- |
| `fg-lab-amadeus-qq-1.2.1.tgz` | `7aeb527c22dd3e32c148cae622d80a3d1eabf6fcb9cc615595827a04a6379676` |
| `fg-lab-amadeus-memory-1.3.0.tgz` | `320b715342e625bd1b775c58d06e89879d5b8aaac49254e80f39e2992a607215` |
| `fg-lab-amadeus-life-1.2.1.tgz` | `b8ab56d7f913f4d8add5ae0025b94a9abd538a55adc884f67b462e1d1a76faaf` |
| `fg-lab-amadeus-state-1.2.0.tgz` | `f67fee94e861a8f03fe4c7a2e37e137a730682884860267013aa09850a41eab0` |
| `fg-lab-amadeus-persona-1.2.0.tgz` | `1957d64d05a5ff641117195f68cab166dff405b54c3b8a37e5973458a78eb11c` |

Memory 仓库根目录现在就是安装包，不再使用旧 dsh-mneme 子目录安装后缀；内部 ID 仍必须唯一。未来个人安装应使用下一轮核准的固定版本预构建包，先备份 Profile 与独立用户数据，不应盲目跟随 main。五插件可独立安装；联合推荐先配置 QQ，再配置 Memory、State、Life、Persona 的同一 Workspace。
