# Amadeus 产品化与发行验收 — 2026-10-31

**状态：产品化代码与资料已进入五个 main；正式发行尚未完成。** npm 登录返回 E401，组织发布权限与 Trusted Publisher 尚未验证。未创建正式版本 Tag、GitHub Release 或 npm 发行；未向 DSH Market 投稿。不得将本报告当作公开发行完成证明。

## 版本、来源与 PR

| 插件包 | 版本 | 最终 main SHA | 产品化 PR | npm / Release |
| --- | --- | --- | --- | --- |
| `@fg-lab/amadeus-qq` | 1.2.1 | `9496bc8b6b38d43962c17b7776eb70b1383e2f64` | [#9](https://github.com/jeskoky-pixel/amadeus-qq/pull/9)（已合并）、[#10](https://github.com/jeskoky-pixel/amadeus-qq/pull/10)（已合并） | 未发布 |
| `@fg-lab/amadeus-memory` | 1.3.0 | `c7f50e6437c99ff4274557d388b434f2119189da` | [#8](https://github.com/jeskoky-pixel/amadeus-memory/pull/8)（已合并）、[#9](https://github.com/jeskoky-pixel/amadeus-memory/pull/9)（已合并） | 未发布 |
| `@fg-lab/amadeus-life` | 1.2.1 | `72cff86ca0d3fa2c81134d201f7d729501552024` | [#7](https://github.com/jeskoky-pixel/amadeus-life/pull/7)（已合并）、[#8](https://github.com/jeskoky-pixel/amadeus-life/pull/8)（已合并）、[#9](https://github.com/jeskoky-pixel/amadeus-life/pull/9)（已合并） | 未发布 |
| `@fg-lab/amadeus-state` | 1.2.0 | `b4fbf2bff17efd79d86d598899b27f8fd0a196fc` | [#5](https://github.com/jeskoky-pixel/amadeus-state/pull/5)（已合并）、[#6](https://github.com/jeskoky-pixel/amadeus-state/pull/6)（已合并）、[#7](https://github.com/jeskoky-pixel/amadeus-state/pull/7)（已合并） | 未发布 |
| `@fg-lab/amadeus-persona` | 1.2.0 | `0099f3f6c3153e72ea8022dd62f6961e10714a1a` | [#3](https://github.com/jeskoky-pixel/amadeus-persona/pull/3)（已合并）、[#4](https://github.com/jeskoky-pixel/amadeus-persona/pull/4)（已合并） | 未发布 |

以上 main 的 Git tree 与构建候选树逐一相等。普通 Merge Commit 仅改变提交历史，没有替换已验收代码。没有强推、移动已有 Tag 或绕过必需 CI。Memory 内部 ID 始终为 `dsh-mneme`；五包均在仓库根目录安装，不使用旧子目录后缀。

## 产品化资料

五仓库均提供英文 README、中文 README.zh-CN.md、真实界面截图、现有产品图标、完整配置参考 REFERENCE.md、安装和升级/迁移/回滚指南、PUBLISHING.md。首页说明各功能、默认开关、真实依赖与限制。About 已更新为中英文简介，Homepage 指向主项目，增加 dsh-plugin 与 amadeus Topics。五包沿用 MIT，Memory 保留 Mneme 署名与版权。

Memory 首页明确 Hot Memory、近期动态、Core Memory、BM25/Vector/Hybrid、图谱与实体、Dream/Sleep、Learn/Borrow 和来源约束，并保留宿主模型可能出站的隐私说明。QQ 不打包 NapCat，不默认开放危险权限。Life/State 使用独立原生设置面板；Persona 使用独立 Owner 控制台。

## 当前自动化与安装证据

| 项目 | 结果 |
| --- | --- |
| QQ Windows 完整测试 | 303 通过；4 项因 Windows 符号链接环境跳过 |
| QQ Linux 补测 | 同四项安全测试全部通过，0 跳过；不是修改 skip 为 pass |
| Memory 完整测试 | 782 通过，0 失败/跳过 |
| Life 完整测试 | 213 通过（按全部子进程 TAP 汇总） |
| State / Persona | 143 / 49 通过 |
| 构建、Bundle 与 pack 审计 | 五包通过；Host、Client、Patch 和既有 GUI 资源在包内 |
| 独立下载与安装 | 五包经本地 HTTP 地址和官方 DSH 插件管理 CLI 安装；每个独立 Host 的真实浏览器返回 200、无页面脚本错误 |
| 联合安装、升级 | 同一个隔离 Profile 安装五包并重新启动；使用官方 Desktop 0.2.1-rc.3 附带 Host |
| 卸载/重装 | 官方安装器卸载与重装后，全部复制的合成外部数据文件字节保持；新代码能读取关键内容与绑定 |
| GUI | QQ/Memory/Persona 真实页面加载无错误；State 七页、Life 八页逐页打开无脚本错误；State 经 GUI 成功选择并绑定工作区 |
| 公开 npm / Release 下载复验 | 未执行：发行尚不存在，不能用本地 HTTP 测试冒充 |

本轮产品化未修改核心业务算法，复用上一阶段数据迁移失败、备份恢复及禁用/启用的自动回归；不将这些回归描述成五个全新独立 Profile 中逐项点击完成的 GUI 录像。Life/State 另完成真实 GUI 数值修改、保存、重新打开与恢复原值；回归发现并修复共享表单生命周期和数值 Schema 两项实际缺陷。公开发行前仍需公开源下载/安装与完整恢复清单，不将本地安装替代公开源验证。

## 真实 NapCat 与跨插件门禁

使用已经授权的私聊与测试群，未更改 NapCat 登录、连接配置或主 DSH Profile。测试正文与身份不进入公开包。

- 私聊和群聊各收到一条用户真实发送的验收消息；来自实际 NapCat，不是合成 OneBot 传输。
- 两个真实 QQ Session 的可信 provenance、Scene 配对与 Life 绑定通过。
- 在隔离历史夹具中，用真实 Host 创建裸 UUID 会话并绑定测试 Scene；最终 QQ/Memory/Life 均识别正确，另一个 Scene 仍使用标准 session-UUID。该夹具不是修改生产 Session。
- 使用真实可信 Session 身份与独立合成数据库，验证 Learn 合法成果保留、Borrow 撤权、源内容撤权；Host 重启后保留/拒绝结果仍正确。
- 另一个无入站订阅的受控 QQCore 使用实际 Host Session 和实际 NapCat HTTP 发送一条授权私聊测试消息。投递成功、重复 actionId 抑制、撤权后发送拒绝均通过；没有向群外发。
- 这些是实际传输加 Host 集成测试，不代表本轮重新完成模型自主持续聊天或 Life 模型自主外发。Life 外发保持禁用，没有让两个实例竞争自动回复。

## 发行包与来源校验

| 文件 | 字节 | SHA-256 |
| --- | ---: | --- |
| `fg-lab-amadeus-qq-1.2.1.tgz` | 952374 | `0c21e6e0618bf8739420ea0dc54befd6d41587cc5828a128a5fbbcf2437e6d50` |
| `fg-lab-amadeus-memory-1.3.0.tgz` | 2701840 | `1afd4652f06705bd42c01f753bf56e19a75010bf9afdb278336c84e255defb00` |
| `fg-lab-amadeus-life-1.2.1.tgz` | 591339 | `6df72bdfc8c4e8e12e97cc289247841e7f9f820c5571ece3fda4129da3f03cb3` |
| `fg-lab-amadeus-state-1.2.0.tgz` | 539142 | `06164c6249dc00a0a92af554ccd5686500e292c92517c3bd532827b46f15ecf7` |
| `fg-lab-amadeus-persona-1.2.0.tgz` | 2102818 | `ce3aabad789101e33908dfc6cd83dc4b82aa444c12def089532b7fe587052b87` |

安装包暂存于本地发行工作目录，尚无可验证的公共下载 URL。不得把预计 URL 宣传为已可下载。构建记录保留 sourceCommit、mainCommit、Git tree 和文件清单；五包同一版本没有公开覆盖。

文件审计及现有敏感文件守卫通过；没有打入用户数据库、WAL/SHM、私聊记录、账户凭证、真实测试身份或本机个人绝对路径。公开截图使用历史合成界面或已遮蔽测试身份的真实界面。

## 数据与长期恢复

| 插件 | 数据形式 | 当前 Schema | 保留范围 |
| --- | --- | --- | --- |
| QQ | 独立 JSON 状态与 provenance journal；Profile 配置 | 状态 1，provenance 1 | Scene/Session、权限、去重与可靠投递 |
| Memory | memoryDir 下 SQLite/Workspace 目录及索引 | 基础 1；统一 ACL 2 | 记忆、图谱、权重、授权、Learn/Borrow 与来源依赖 |
| Life | 独立 SQLite 与 Profile 配置 | 10 | 意图、活动、唤醒、预算、工作区 |
| State | 独立 SQLite | 2 | 状态、兴趣、事件与规则 |
| Persona | 独立 persona.json、SOUL/STYLE 投影与备份 | 1 | 核心、风格、绑定、历史与配置 |

本轮重放已有旧版合成数据：读取并核对 QQ 权限/消息/会话、Memory 正文、Life 意图、State 事件/兴趣、Persona 核心/风格/绑定。基础 Memory 旧夹具仍可用 schema 1，不会因包版本变更强制清空；统一 ACL schema 2 另行验证持久性。此重放不等于将所有旧版本全部重新安装一遍。

安装、升级、禁用和卸载均不得清空独立数据目录。恢复旧代码前先核对数据格式；不兼容时使用匹配的升级前完整备份。主 DSH 用户数据、NapCat 状态和已有冻结基线未触碰。

## 发布权限与仍需完成的门禁

1. 五仓库 main 已要求 verify，QQ 另要求 symlink-security；管理员也受保护，禁止 force push 和删除。
2. v* Tag 有更新/删除保护。Actions 默认 contents read。正式 npm workflow 仅 workflow_dispatch，要求版本 Tag、main 祖先、用户给定最终 SHA 和 npm-release 环境审核；普通 push 不触发发布。
3. npm-release 环境只允许 v* Tag，审核人是仓库所有者。npm 侧须绑定精确 owner/repo/workflow/environment，当前不能假称已设置。
4. npm Registry 匿名检查五个目标版本均不存在；新 guard 遇到已存在版本或不确定响应会失败退出。npm whoami 与组织查询返回 E401，Scope 权限未知。
5. 正式公开发布、npm provenance、下载后内容/哈希、固定 URL 安装与公共源恢复尚未通过。不得仅凭 CI 绿色标记完成。

## Market 投稿材料

官方当前 readEntries 解析器与 check-submission.mjs 均已在本地运行。批次一 Memory/QQ/Persona、批次二 Life/State，五仓库检查全通过。每仓库 market/ 下保留独立 YAML；本报告旁 market/product-release-20261031/ 提供副本。没有修改市场生成 README，也没有创建外部投稿 PR。固定 tarball 字段暂未添加，因为公开 Release 资产尚不存在。

预计标题：第一批 “Add Amadeus Memory, QQ and Persona”；第二批 “Add Amadeus Life and State”。正文应只说明真实功能、固定安装源、已测试宿主与限制；目录收录不代表安全审计。

## 账户侧操作与后续发行

需要所有者在自己的终端执行 npm login 并完成浏览器/2FA 登录，然后确认账户对 @fg-lab 有发布权限；不要发送 Token、密码或验证码。包首次创建与 Trusted Publisher 绑定须按 npm 当前能力完成。之后才能继续验证正式发布权限与 OIDC。

后续每次发行：独立 PR → CI 与数据兼容回归 → main → 最终预构建包及哈希 → 独立/联合安装和恢复 → 受保护的版本 Tag → 经审核的发布 → 公共源复验 → 更新市场固定源。若需要修复已公开版本，递增补丁版本，不覆盖旧版本或移动 Tag。

## 测试中发现并处理的问题

- Memory 首页初版整理遗漏隐私说明及模型工具表，守卫发现后补回；最终完整测试通过，没有削弱测试。
- 一次联测脚本复用先前 Borrow 的历史证据导致断言失败。测试用例分离数据库后通过，原失败证据保留；随后单独重启读取原库，确认合法 Learn 保留与 Borrow 撤权。未为通过测试放松产品来源规则。
- Git HTTPS 连接重置，改用 GitHub Git API 创建等价 Git tree 并只做 fast-forward 更新；本地补齐经 SHA 校验的远程提交对象。
- Life/State 设置页修复：组件不再销毁 Host 共享表单；UI 步长不再序列化为接受值约束，保留已有合法数值。真实官方 Schemastery 与组件重挂载回归均通过，State 两项新回归加入默认测试入口。
- 当前安装/迁移文档中的旧版本引用已校正；历史 v1.0/v1.1/v1.2 记录保持原样。

## 最终修复后的复验

Life #9、State #7 已通过必需 CI 并正常合并。最终发行包通过官方 HTTP 安装入口重新安装，已安装的 Host Schema 与 Client 文件逐字节匹配源码。重启隔离 Host 后可信 Scene、Life 绑定、Learn 保留与 Borrow 撤权仍通过；Life 设置关闭重开后可修改保存，State 修改、刷新读取与恢复原值通过。共享配置没有重置。

可公开的脱敏结果与安装包校验见 [验收证据目录](docs/verification/product-release-20261031/)。未收录凭证、Owner URL、真实聊天正文、私有配置或用户数据库。
