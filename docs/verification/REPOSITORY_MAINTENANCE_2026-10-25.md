# 仓库维护检查：公开状态与许可证

日期：2026-10-25。范围：全部 9 个项目仓库；不包含 Life 功能实现。

## 检查结果

- GitHub 实际可见性：主仓库与 8 个插件全部 PUBLIC。
- 主仓库、QQ、Life 的默认分支仍含旧的私有说明；此前修改位于待审 M0 分支。本轮已在各自独立维护克隆中修正文档，并以独立维护提交推送默认分支。
- 其余 6 个插件的默认分支 README / HANDOFF 已正确写明公开，无需重复修改。
- 主仓库模块表补充 visibility=public、license_status=pending_owner_decision；不把仓库公开等同于功能可用或已经选择许可证。
- 主仓库有用户此前选择的 MIT LICENSE；8 个插件均未确定独立许可证，不新增 MIT 或非商用许可。待项目拥有者明确授权意图后再处理。

## 默认分支维护提交

| 仓库 | 独立维护提交 |
| --- | --- |
| Amadeus-dsh | a8c607f |
| amadeus-qq | 7522872 |
| amadeus-life | 4eafad2 |

上述提交只改 README / HANDOFF；主仓库另改模块登记表。逐份 staged diff 与空白检查通过，无应用代码、依赖或生产配置变更。

## Life 开发入口

本轮后续唯一主要功能是 amadeus-life。使用独立官方 DSH Desktop、独立 DSH_HOME / Electron userData、独立 Life Git 克隆与 Creator 新会话；不使用生产 QQ 工作空间，不发真实平台消息。启动及功能验收另记报告，不能用本维护提交冒充 Life 完成。
