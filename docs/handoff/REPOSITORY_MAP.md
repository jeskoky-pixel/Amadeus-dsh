# 仓库与克隆映射


## 2026-10-25 最终加固增量

Life 活跃开发克隆与 `plugins/amadeus-life/` 均同步到 `codex/life-core` 的 `e9e0764955f7eb5e260e32e2622729463b2f589c`；Life PR #2 开放、未合并。主仓 `codex/life-development` 更新最终加固报告、兼容矩阵和交接；其他插件未修改。下方旧库存仅为历史快照。


## 2026-10-25 Life v1 增量

Life 活跃开发克隆和 `plugins/amadeus-life/` 均为 `codex/life-core` 的 `7855cddbbe376216afd6d093c8846fa71f770094`；Life PR #2 开放。主仓 `codex/life-development` 更新了 v1 验收与交接文档，PR #2 保持开放。下方旧库存为历史快照；其他插件未参与本轮开发。

## 2026-10-25 v4 增量

Life 活跃开发克隆已推送 `codex/life-core` 的 `4f690c1304bd31c1b8772b2afa3bc000983b9d29`，Life PR #2 开放、未合并。普通 `plugins/amadeus-life/` 克隆已快进至 `52d6284`，尚需待下一次安全同步到当前 HEAD；不可 reset 其余工作树。主仓此轮仅更新交接及验收文档；下表是较早库存快照。

## 2026-10-25 Host adapter 增量

Life 活跃开发克隆与 `plugins/amadeus-life/` 已同步至 `codex/life-core` 的 `ce93ff98b3df44490f1305158fa85c64a63db47e`，Life PR #2 未合并。主仓本次文档提交 SHA 以当前 PR #2 / `git rev-parse HEAD` 核对；其余插件没有本次代码改动。下表为早期库存快照。

## 2026-10-25 Life v3 增量

Life 活跃开发克隆与 `plugins/amadeus-life/` 均已同步到 `codex/life-core` 的 `b21ecb299c5fbcc27d5a27edfd41bef3b16a5b8a`；Life PR #2 未合并。其余插件无此次代码更动。主仓 PR #2 的最新 SHA 请以当前 `git rev-parse HEAD` / PR head 核对；下表仍是更早快照。

## 2026-10-25 Life 增量

九仓初始路径、远端与分支映射仍见下表。Life 开发克隆和 `plugins/amadeus-life/` 已同步到 `codex/life-core` 的 `fc977b1`；Life PR #2 未合并。主仓 `codex/life-development` 当前 HEAD 请用本地 `git rev-parse HEAD` 与 PR #2 核对；其余插件未参与本次质量修复。下表的交接 HEAD 为此前快照，不能覆盖当前 Git 检查。

2026-10-25：以下是实际 git fetch/status/remote/worktree/branch 与 GitHub open PR 查询结果。公开路径以项目根为基准；绝对路径只保存在本机 private/handoff。九仓均公开、当前可 ADMIN；初始无未提交修改。本轮所有插件 handoff 已推送。

| 仓库/remote | 本地目录 | 当前分支 | 精确文档交付 HEAD | 打开 PR |
| --- | --- | --- | --- | --- |
| [Amadeus-dsh](https://github.com/jeskoky-pixel/Amadeus-dsh) | `.` | `codex/life-development` | 交接前 `073164a2f637fb207b5bd580ad2416d870800765`；交接提交见本文件 git log/PR head | #2 + 草稿 #1 |
| [amadeus-android](https://github.com/jeskoky-pixel/amadeus-android) | `plugins/amadeus-android/` | `main` | `f8aa4e583de3272ac6b3224f84e9953bb0ae2b87` | 无 |
| [amadeus-bilibili](https://github.com/jeskoky-pixel/amadeus-bilibili) | `plugins/amadeus-bilibili/` | `main` | `e3eefdaa7108ee4ff3f794b552ed5fd542b39bb3` | 无 |
| [amadeus-browser](https://github.com/jeskoky-pixel/amadeus-browser) | `plugins/amadeus-browser/` | `main` | `e64379eed3457d7e5dffb24363018646f52b93c8` | 无 |
| [amadeus-games](https://github.com/jeskoky-pixel/amadeus-games) | `plugins/amadeus-games/` | `main` | `85c537f7f946cafd21cc9de0840e23715e6dddee` | 无 |
| [amadeus-life](https://github.com/jeskoky-pixel/amadeus-life) | `plugins/amadeus-life/` | `codex/life-core` | `61663064d9056a932ac4a5c984bea6e43f771b08` | [#2](https://github.com/jeskoky-pixel/amadeus-life/pull/2) / [#1](https://github.com/jeskoky-pixel/amadeus-life/pull/1) |
| [amadeus-memory](https://github.com/jeskoky-pixel/amadeus-memory) | `plugins/amadeus-memory/` | `main` | `01e83bec483a3ca799709423972933d53996a578` | 无 |
| [amadeus-qq](https://github.com/jeskoky-pixel/amadeus-qq) | `plugins/amadeus-qq/` | `codex/m0-baseline` | `a0abc9f534daa0a7a081158b579f9d68f0ac0473` | [#1](https://github.com/jeskoky-pixel/amadeus-qq/pull/1) |
| [amadeus-state](https://github.com/jeskoky-pixel/amadeus-state) | `plugins/amadeus-state/` | `main` | `a7f7fbbbb5e17982fd68007f8a6f62edf80d1d0f` | 无 |

## 独立克隆、worktree 与其他分支

九个常规克隆的 git worktree list 均只有各自单一工作树，没有额外 linked worktree。额外发现四个实际独立克隆（不是 worktree）：

- `private/life-development/workspace/amadeus-life/`：真正安装进独立 Desktop 的开发克隆，codex/life-core；已 fast-forward 到上表 Life 文档 HEAD；常规 plugins 克隆供浏览/独立核查，后续代码优先在开发克隆改，推送后同步另一个。
- `private/life-development/maintenance/Amadeus-dsh/`：main，a8c607fa9871e1b2dde9c7769e1ec5ffb9408772。
- `private/life-development/maintenance/amadeus-life/`：main，4eafad29a22c7a4810d6999602c7bff44f7971bb。
- `private/life-development/maintenance/amadeus-qq/`：main，7522872e43ab9bd80542f890b5e34a1ab6a20040。

以上维护克隆本轮未修改、未作为活跃开发目录。profile/node_modules/@amadeus/life 是开发克隆的 link，不是第四个 Life 实现。扫描项目树排除 node_modules/运行数据后未发现其他仓库；未做全电脑无限搜索。

主仓库保留本地 codex/m0-baseline=099c2663d85c196577b20f98a227d508dc7b6569、main=c191552（落后 origin/main，不得误用）；Life 保留 codex/m0-baseline=73afbb53bafdebe50db9a3a284371e18b5320812、旧本地 main；QQ 保留旧本地 main。完整 branch -vv/worktree 原始安全检查仅在 private/handoff/git-inventory.before.json。其他六仓仅 main，只有文档初始化。

默认 main 与功能分支不同，GitHub 网页审阅必须选 PR/开发分支。主 PR #2 包含原 M0 基线与 Life 总结，Life PR #2 包含实际核心。当前不创建重复 PR，不合并。

## 同步检查

本次已 fetch 所有常规仓库和四个独立克隆；后续推送逐分支以 ls-remote 核验。本机 FINAL_DELIVERY.json 保存最终九仓 HEAD、远端 SHA/同步结果和全部打开 PR。主仓库最终 SHA 无法写进自身同一提交，使用 `git log -1 --format=%H -- docs/handoff/REPOSITORY_MAP.md` 或 PR head 获取；插件表不受此限制。
