# Amadeus 开发约束

先阅读 `docs/handoff/NEXT_CODEX_START.md`，按其顺序读取 CURRENT_STATE、DSH_DEVELOPMENT、REPOSITORY_MAP 与本机忽略的 private/handoff（如存在）。不能依赖旧 Codex 聊天记录。

主仓库只维护规格、契约、兼容与集成。plugins 下是独立 Git 克隆。当前只开发 Life：先完成四项 0.1.0 质量缺口，再规划 0.2.0 并持续推进 v1.0，内部验收关卡不要求反复向用户确认。

主要实现交给现有官方 DSH Desktop cordis Creator 持续会话，Codex 阅读源码、精确派单、独立测试与审查；不要用 headless 冒充 Creator，不批量新建一次性 Agent。

禁止 Computer Use、改动生产 SnowLuma/QQ Bridge/生产 DSH、真实平台外发、擅自付款/发布/部署或合并 PR；保留现有数据库、会话和工作成果。只使用后台接口/终端。公开文档用相对路径，不上传用户名绝对路径、认证或原始私人模型日志。插件独立许可尚未确定。
