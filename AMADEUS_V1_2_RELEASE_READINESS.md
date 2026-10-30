# Amadeus v1.2.0 正式发行验收报告

日期：2026-10-30。状态：**发行候选已推送到五个开发 PR；尚未合并、打 Tag、创建 GitHub Release 或发布 npm。** 本报告以 `@fg-lab/*` 新包和 Memory 根目录改造为准。此前 `@amadeus/*` 候选报告保留在 Git 历史中，不能代替新包验收。

| 插件 | 候选 Commit / PR | npm 包 | 本地候选包 SHA-256 | 自动化 |
| --- | --- | --- | --- | --- |
| QQ | `5378adc` · [PR #5](https://github.com/jeskoky-pixel/amadeus-qq/pull/5) | `@fg-lab/amadeus-qq@1.2.0` | `adaddec438a457ba25a0a9fc697cf051cdc14b7d5c6aefea7864c353114e1443` | 299 通过、4 跳过 |
| Memory | `7fccd83` · [PR #6](https://github.com/jeskoky-pixel/amadeus-memory/pull/6) | `@fg-lab/amadeus-memory@1.2.0` | `2fca0e0d4cfc11e878f443e87f7268434d66e0d073318a6c926cc7d089d21ff3` | 产品门禁 717/717 |
| Life | `4044e41` · [PR #5](https://github.com/jeskoky-pixel/amadeus-life/pull/5) | `@fg-lab/amadeus-life@1.2.0` | `7f2d126010dbc4c9d4701a846461c03b1a8ed2018ec63a1a22218ac894f28499` | 209/209 |
| State | `d1c29c9` · [PR #4](https://github.com/jeskoky-pixel/amadeus-state/pull/4) | `@fg-lab/amadeus-state@1.2.0` | `f67fee94e861a8f03fe4c7a2e37e137a730682884860267013aa09850a41eab0` | 141/141 |
| Persona | `9a0b377` · [PR #2](https://github.com/jeskoky-pixel/amadeus-persona/pull/2) | `@fg-lab/amadeus-persona@1.2.0` | `1957d64d05a5ff641117195f68cab166dff405b54c3b8a37e5973458a78eb11c` | 49/49 |

上述 SHA 是提交前工作树的本地 `.tgz`，不代表 GitHub Release 或 npm Registry 上已有这些资产。正式发行时将从最终 Tag 重打包并核对哈希与内容。五仓 `verify:release`、`npm publish --dry-run --access public` 通过；包清单包含 Host、Client、静态资源和许可证，不含已识别的数据库、密钥或个人日志。Memory 包含原作者的 MIT `LICENSE` 和 `UPSTREAM.md`。公开 Registry 尚无这五个 1.2.0 版本。

## Memory 根目录改造

- 493 个跟踪文件从 `dsh-mneme/` 移到仓库根；旧根 README 与上游 README 分别归档，根目录直接具备 `package.json`、`cordis.patch.yml`、`src/`、`lib/`、`assets/`、`locale/`、`scripts/`、`test/`、`docs/`、`LICENSE` 与 `UPSTREAM.md`。
- 移动前后 183 个 `src/`、`lib/` 文件的字节 SHA-256 一致；少量后续编辑仅为包名、路径、测试假设和发行元数据。内部 Bundle ID `dsh-mneme`、默认 `memoryDir=~/.dsh/memory`、SQLite schema、模型工具与现有数据协议保持。
- `npm test` 717/717。扩展 `test:all` 迁移前 2617 通过、36 失败、1 跳过；迁移后 2618 通过、35 失败、1 跳过，剩余 35 项均为迁移前已有的旧上游套件/环境失败，分类见 [移动专项记录](https://github.com/jeskoky-pixel/amadeus-memory/blob/codex/v1.2-release/docs/verification/MEMORY_V12_FLATTEN_MOVE.md)。
- 同一 10k 合成数据基准的 P95，移动前→移动后：关键词服务 53.951→52.146 ms，向量服务 89.075→84.673 ms，SQL 11.180→11.045 ms，本地向量 23.818→24.022 ms。短时合成基准有运行波动，不能当作生产负载性能保证。
- GitHub 根目录和 npm 包不再需要 `#path:/dsh-mneme`。旧 `@amadeus/memory` 与新包必须先移除旧包再装新包，避免两个 `dsh-mneme` 实例同时打开数据库。迁移前必须做一致性备份，之后核对工作区绑定、记忆正文、图谱和 Scene 权限。

## 安装、升级与联合运行

使用官方 DSH Desktop `0.2.1-rc.3`、独立 `DSH_HOME` 和合成数据。官方 `dsh plugin` CLI 从五个旧包建立 QQ Scene/Session、Memory 记忆、Life 意图、State 事件/兴趣、Persona 核心与风格绑定；逐个移除旧 `@amadeus/*` 包再安装对应 `@fg-lab/*` 包。新代码读取所有关键内容和关联通过，Memory 数据库升级到 schema 1 且 `quick_check=ok`。同一隔离 Profile 只有五个新包和五个唯一内部 Bundle；Web Host 启动后认证 HTTP 首页为 200。

同版 **Desktop 图形插件管理器** 已从本地 `.tgz` 安装并启用五个新包；原生设置菜单出现五个独立入口，QQ 首次设置、Memory 多页管理、Life 健康/活动页、State 状态页、Persona 健康/Workspace 页均实际渲染。一次完整 Desktop 重启后五包仍在已安装列表，Persona 保持未配置空状态，Life 自主唤醒默认关闭，QQ 未配置且未外发。本项证实本地包的图形安装与基础加载，不代表公共 Registry 或 Market 下载安装已经通过。

**数据隔离复核：** 一次 Desktop 预检在设置测试专用 `memoryDir` 之前启动了 Memory，触及已有用户记忆目录的 SQLite 共享内存文件；随即停止实例。现有数据库主文件与 WAL 修改时间早于该预检，复制出的只读快照 `quick_check=ok`，但没有事前哈希，不能据此断言完全未受影响。随后 Desktop 测试改为明确的隔离目录，并核实原目录不再更新。正式部署前应再次核对目标 `memoryDir`，不能把默认路径直接用于破坏性升级演练。

## 发行与市场状态

- npm 登录用户为 `kyjesko`，`fg-lab` 组织中拥有 Owner 权限，CLI 12.0.2；用户已选择 QQ、Life、State、Persona MIT，Memory 保留 Mneme MIT 原作者版权。首次 npm 正式发布可能需要用户本人完成 2FA；未索取或存储 Token。
- 五仓新增只允许手动从版本 Tag 触发的 `npm-publish.yml`，核验 Tag 与包版本、Commit 属于 `main`，重跑测试、打包，并以 OIDC provenance 发布。首次包发布后仍需由用户在 npm 网站为各包配置精确的 GitHub Trusted Publisher；工作流尚未触发。
- GitHub About 已设置准确简介、项目主页和 `dsh-plugin` / `deepseek-harness` / `amadeus` Topic。五个市场条目草案遵循 Awesome DSH Plugin 的 YAML 字段，Memory 指向仓库根；详情见[市场准备](docs/DSH_MARKET_V1_2.md)。**未向第三方市场提交 PR/Issue，未确认被 DSH Market 自动索引。**
- 尚未创建 `v1.2.0` Tag、GitHub Release、公开 npm 包；没有对外安装结果，不能把 dry-run 或本地 `.tgz` 当作已发行。

## 尚需完成的发行门禁

1. 五个 PR 的新 CI 检查通过，审查最终 Diff、包哈希与 Tag 一致。
2. 使用授权 NapCat 完成真实 QQ/Workspace/Scene 联合收发，并核实 Life 不重复外发；不改 NapCat 配置或登录状态。
3. 公共 npm 发布和固定 GitHub Release 后，从真实 Registry/Release 源重复独立安装、联合安装、重启、禁用/卸载与恢复检查。
4. 对旧包名切换提供普通用户可操作的备份、移除旧包、安装新包步骤；不能声称包名改变可原位自动升级。

这些门禁未完成前，五个 PR 保持开发候选，不把合并、Release 或 npm 发布标记为完成。
