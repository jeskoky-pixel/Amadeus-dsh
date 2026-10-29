# Amadeus v1.2.0 发行准备报告

日期：2026-10-30。状态：**内部候选与隔离 DSH 运行时验收中；未公开发行**。本报告记录实际完成的测试和仍待完成的 Desktop/Market 验收，不构成生产部署许可。

## 候选版本与安装包

| 插件 | 来源基线 | 候选版本 | 内部插件 ID | GitHub CI 预构建包 SHA-256 | 候选 Commit / PR |
| --- | --- | --- | --- | --- | --- |
| QQ | `1.1.1` | `@amadeus/qq@1.2.0` | `amadeus-qq` | `e0aec10b5b21286a52ea822f5f992c61bfab7ae510f32def20586e58e4ac5766` | `5e44e88` · [PR #5](https://github.com/jeskoky-pixel/amadeus-qq/pull/5) |
| Memory | `1.0.1` | `@amadeus/memory@1.2.0` | `dsh-mneme` | `a903863e549d3ad481b4d4fd737fe369449a221cb4570b4c24bf6cf0a426e1fa` | `d1cebdb` · [PR #6](https://github.com/jeskoky-pixel/amadeus-memory/pull/6) |
| Life | `1.1.0` | `@amadeus/life@1.2.0` | `amadeus-life` | `d1fc3851a805f29948cff044a9a8af9b66681ec6547a1d00768d503639f6e1e9` | `464ce19` · [PR #5](https://github.com/jeskoky-pixel/amadeus-life/pull/5) |
| State | `1.1.0` | `@amadeus/state@1.2.0` | `amadeus-state` | `5e467f26a254a669530fae30e6d90f7ba3f729bbc491dd2ac9fec0564792e24b` | `a70628c` · [PR #4](https://github.com/jeskoky-pixel/amadeus-state/pull/4) |
| Persona | `0.1.0` | `@amadeus/persona@1.2.0` | `amadeus-persona` | `bc8ccff91f3169b820b25ba34f5f994411724db619bdbd4597ead7e80bb952b9` | `e25824a` · [PR #2](https://github.com/jeskoky-pixel/amadeus-persona/pull/2) |

表中 SHA-256 对应五个 PR 的 GitHub Actions 候选 `.tgz` artifact。它们与本地 `npm pack --ignore-scripts` 产物的文件名和文件内容一致；少数文本文件的 CRLF/LF 差异导致压缩包 SHA 不同，因此以可下载的 CI artifact 校验值为准。所有包均通过 `verify:release` 的 Manifest、Host/Client 入口、静态资源、截图清单、安装脚本与敏感路径/高置信密钥扫描。扫描不能替代人工隐私审查。

五仓库已补齐 v1.2 发行说明，并为市场草案加入未来 `v1.2.0` GitHub Release 的固定 `.tgz` 地址。表中是最终推送的候选 Commit；五个新 CI 检查均通过，SHA-256 已按重新下载的 CI artifact 计算。GitHub Release 资产仍未创建，市场草案的固定地址目前不可安装。

## 测试过的宿主与功能

- 官方 DSH Desktop `0.2.1-rc.3` 附带的 `dsh` CLI、Profile 管理器和 Web Host；Node `22.23.2`。未从当前证据推断其他 DSH 版本兼容。
- 五个独立隔离 `DSH_HOME` 分别用官方 `dsh plugin --profile web add <tgz>` 安装一个候选包。五次 Host 启动及认证后的 HTTP 首页均返回 200。
- 联合隔离 Profile 依序安装 QQ、Memory、State、Life、Persona；合成后的 Bundle ID 各出现一次，Memory 保留 `dsh-mneme`。联合 Host 启动成功；QQ 保持未配置/暂停，未连接 NapCat 或向外发送。
- 从五个 PR 的首轮 GitHub Actions 下载 `.tgz` artifact，在另一个全新隔离 Profile 联合安装，Host 与 QQ/Persona Owner Console 均通过认证后的 HTTP 200。此项浏览器验证对应补发行说明前的 CI 包，不能冒充最终哈希所对应包的页面验收。
- 将最终五个 CI artifact 在第三个全新隔离 `DSH_HOME` 中经官方 `dsh plugin --profile web add` 联合安装，五个包均解析为 `1.2.0`，随后 Web Host 成功启动。最终提交仅变更发行说明、市场草案及 Persona 的文档打包清单；不重复把先前功能验收写成新一轮真实 QQ 验收。
- QQ 和 Persona Owner Web Console 在真实 Edge 浏览器中加载；QQ 六页、Persona 五页及主题切换通过，无页面脚本异常。首次安装 Persona 为空卡，QQ 显示待配置状态。
- 完整自动化回归：QQ 303 项（299 通过、4 跳过）；Memory 717/717；Life 209/209；State 141/141；Persona 49/49。QQ 的 4 项跳过原因以其测试报告为准，不能计为通过。

## 长期升级与数据兼容性专项验收

| 插件 | 数据目录与格式 | 本轮演练 |
| --- | --- | --- |
| QQ | DSH Profile 配置、绝对 `statePath` 的 JSON schema 1、DSH Session 日志 | 旧包写入合成 Scene 权限、Session 和消息；1.2 后内容与绑定保留 |
| Memory | `memoryDir` 的 Workspace/未绑定 SQLite，旧版 user_version 0 → 1 | 旧包写入记忆；升级生成迁移前一致性备份，正文保留；备份失败与未来版本拒绝测试通过 |
| Life | `$DSH_HOME/amadeus-life/life.sqlite` 或显式路径，schema 10 | 旧包写入意图；升级后内容保留；既有 v9→v10 备份/失败恢复测试通过 |
| State | `$DSH_HOME/amadeus-state/state.sqlite` 或显式路径，schema 2 | 旧包写入事件与兴趣；升级后内容保留；既有 v1→v2 迁移/恢复测试通过 |
| Persona | `$DSH_HOME/amadeus-persona/persona.json` 或显式目录，schema 1 | 旧包写入核心、风格、Workspace 与 Session 绑定；升级后内容和有效绑定保留 |

升级演练使用上一基线从 GitHub main 源码打成的旧 `.tgz`，先由旧代码生成真实格式的**合成**用户数据，再由官方 `dsh plugin` 将包替换为 1.2.0。替换安装包后、首次运行新代码前，五个数据主文件的 SHA-256 均完全不变。升级后新代码读取关键字段、关系和授权；联合 Host 重启后再次读取通过。Memory 迁移备份只生成一次。随后在隔离 Profile 卸载全部五包，数据目录仍存在；重新安装 1.2 后内容可读。将代码回退到上述旧版也可读取这组数据，最后恢复 1.2。上述回退仅证明本轮 schema 组合，不保证未来不可逆迁移可以仅回退程序。

配置与代码分离：QQ 的 Bot/Owner/白名单在 DSH Profile，Scene 状态在 `statePath`；Memory 的 `memoryDir` 不位于包内；Life/State/Persona 默认在 `$DSH_HOME` 下。每仓库已有 `UPGRADE_POLICY.md`、`DATA_MIGRATION.md`、`RELEASE_CHECKLIST.md`，CI 包含测试、构建、pack 内容与 SHA 检查。未来 v1.2.1 / v1.3.0 仍须按“上一稳定版安装 → 合成旧格式数据 → 升级 → 重启 → 内容/关联/权限验证 → 失败恢复”执行；不允许仅比较条数或覆盖 main。

## 发行许可、npm Scope 与 Market

- `npm whoami` 返回未登录；`@amadeus` Scope 的发布权限尚未证实。不得以当前候选包进行 npm 公开发布。
- Memory 包保留上游许可记录与 MIT；QQ、Life、State、Persona 当前为 `private:true`，许可缺失或 `UNLICENSED`。公开发行许可证需由仓库所有者确认，不能擅自改成 MIT。
- 五个仓库均已设置 `dsh-plugin` GitHub Topic；`market/*.yml` 草案使用官方分类、双语简介、GitHub 托管图标，以及未来固定 `v1.2.0` Release 包地址。Memory 采用 `tree/main/dsh-mneme` 和 `owner/repo#dsh-mneme`。Release 资产目前尚不存在，条目不能提交外部目录或视为已上架/安全审计通过。
- 按 Market 实际使用的 `github:` 源格式，QQ 的固定 Commit 已从 GitHub 安装成功。Memory 的 `#path:/dsh-mneme` 源安装多次失败；Life 的固定 Commit 安装也因 GitHub `HEAD` 超时及 Git fetch 无法连接 443 端口而失败。对应失败发生在下载阶段，未得到包内容或运行时错误证据；其他三个插件的 GitHub 源路径尚未验收。安装包本身的本地与 CI 路径均通过。
- `Amadeus Suite` 元 Bundle 暂缓；当前优先五个独立包。尚未验证间接依赖、加载顺序与卸载语义，不使用 postinstall 脚本修改用户 Profile。

## 尚未通过的发行门禁

1. 独立 DSH Desktop 图形插件管理器的点击安装、首次 GUI 配置、升级、禁用和卸载全流程；本轮使用的是同版 Desktop 附带的 CLI 与 Web Host。
2. 外部 DSH Market 对 GitHub 候选源的真实下载、安装和更新流程；未公开发行前没有可指向的正式版本。
3. 五插件在真实 NapCat、实际 Workspace / Session、模型推理、Life 自主唤醒和长期记忆向量检索中的联合实机运行；没有触碰生产 Profile、QQ 或记忆数据库。
4. npm Scope 权限、四个私有包的公开发行许可证、正式 `.tgz` 与 Release 版本绑定，均需发行前确认。

这些项目完成并复核最终包 SHA 后，才能请求用户的一次性公开发行确认；该确认之前不执行 npm publish、公开 GitHub Release 或外部市场收录 PR。
