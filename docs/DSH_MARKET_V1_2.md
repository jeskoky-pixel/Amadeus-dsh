# Amadeus v1.2 市场投稿准备

状态：2026-10-30 准备中。本轮只制作材料，不向第三方市场提交 PR 或 Issue；GitHub Topic、npm 包、自动索引与正式收录是不同状态。

## Awesome DSH Plugin

当前贡献指南要求每个插件单独提交一个 `data/plugins/<owner>__<repo>.yml`，字段为 `url`、`name`、`category`、`description.en`，可附中文简介和 GitHub 托管的位图图标。不能在条目中加入 `npm:` 或 `tarball:` 字段，也不应直接改自动生成的 README。仓库必须公开、创建至少一天、有真实代码、根包声明 `dsh.bundle`、含 `dsh-plugin` Topic。维护者还会核对描述和重复收录情况。

| 插件 | 建议分类 | 投稿草案 |
| --- | --- | --- |
| QQ | `remote` | [QQ 条目](https://github.com/jeskoky-pixel/amadeus-qq/blob/codex/v1.2-release/market/jeskoky-pixel__amadeus-qq.yml) |
| Memory | `memory` | [Memory 条目](https://github.com/jeskoky-pixel/amadeus-memory/blob/codex/v1.2-release/market/jeskoky-pixel__amadeus-memory.yml) |
| Life | `workflow` | [Life 条目](https://github.com/jeskoky-pixel/amadeus-life/blob/codex/v1.2-release/market/jeskoky-pixel__amadeus-life.yml) |
| State | `identity` | [State 条目](https://github.com/jeskoky-pixel/amadeus-state/blob/codex/v1.2-release/market/jeskoky-pixel__amadeus-state.yml) |
| Persona | `identity` | [Persona 条目](https://github.com/jeskoky-pixel/amadeus-persona/blob/codex/v1.2-release/market/jeskoky-pixel__amadeus-persona.yml) |

五仓已公开、超过一天并设置 `dsh-plugin` Topic；`@fg-lab/*` 的最终包、Memory 根目录入口、公开发行与安装结果仍应以正式发行后的验证为准。原 Mneme 已被收录。Memory 是基于 Mneme MIT 许可独立维护的增强发行版，新增 Workspace/QQ Scene 隔离、关系图谱和授权能力；投稿时应准确说明这些差异并由市场维护者判断是否接受独立条目。

## DSH Market

`2BingLing/dsh-market` 会扫描 `dsh-plugin` Topic。Topic 可使仓库进入发现候选，但不证明它已被索引或通过市场安装。公开发行后需实际检查五个条目是否出现在索引、版本和截图是否正确、从市场入口安装与更新是否成功；必要时才按该项目流程另行提交插件 Issue。

正式投稿前还需确认五个 `main` 分支的 Bundle Manifest、Release/npm 安装源、图标及截图路径、各自 README 与许可证，以及 Memory 不再要求 `#path:/dsh-mneme`。没有完成这些检查前，不应声称“已上架”。
