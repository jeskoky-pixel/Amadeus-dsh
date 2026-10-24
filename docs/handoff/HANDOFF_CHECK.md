# 从零接手检查表

2026-10-25，由同一 Codex 仅按磁盘交接入口核查，未启用新 Agent，也未模拟新账号登录。

| 新接手问题 | 文件答案 |
| --- | --- |
| Amadeus 是什么、最终目标？ | NEXT_CODEX_START、CURRENT_STATE 的产品目标与v1.0范围 |
| 主目录和八插件在哪里？ | REPOSITORY_MAP相对目录；本机LOCAL_PATHS绝对目录 |
| 开发哪个插件，代码/测试进度？ | CURRENT_STATE与Life HANDOFF/QUALITY_GAPS；29旧测试与四缺口分别记载 |
| 哪些PR、精确分支和版本？ | REPOSITORY_MAP；最终主提交和全部remote核验见本机FINAL_DELIVERY |
| DSH Workspace/Session在哪里？ | DSH_DEVELOPMENT逻辑目录与ID，DSH_LOCAL_RUNTIME物理目录 |
| 如何调用/恢复Creator？ | DSH_DEVELOPMENT实际RPC/参数示例与LOCAL_RECOVERY |
| 当前任务是否运行？ | 实查seq721、5个completed响应、空inbox；新账号必须再查询，不靠历史idle推断 |
| 下一步是什么？ | 四项失败回归→原Creator修复→独立验收→0.2.0规划→持续v1.0 |
| 哪些生产不能碰？ | AGENTS、CURRENT_STATE、LOCAL_PATHS；后台接口限独立profile |
| 什么未同步？ | UNCOMMITTED_WORK与FINAL_DELIVERY；private运行数据与恢复资料仅本机 |

本轮新增公开Markdown相对链接、用户名绝对路径及常见凭据模式已检查；所有公开变更为文档或元数据，Life src/test/package运行代码未改。所有提交的远端分支另逐一ls-remote核验，不把push调用成功等同最终一致。GitHub历史中旧本机路径不会被本次脱敏抹除，不重写历史；当前证据文件已改为逻辑根。

本机保全不是异地备份；换电脑时必须另行保留原本机数据。账号切换后的权限、Provider账单与首次新模型请求未验证。
