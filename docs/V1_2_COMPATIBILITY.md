> 历史 v1.2 发行准备记录。当前 main 版本与本轮验收以[2026-10-31 整合报告](../AMADEUS_MAIN_INTEGRATION_2026-10-31.md)为准；Memory 已完成根目录整理。

# Amadeus v1.2.0 五插件兼容矩阵

状态：`@fg-lab/*@1.2.0` 发行验收中。下表升级实验已使用五个新包、官方 DSH Desktop `0.2.1-rc.3` 附带的 CLI / Web Host 和独立 `DSH_HOME`；Desktop 图形插件管理器也完成本地包安装、启用与重启。公开 Registry 下载源、真实 NapCat 联动和生产数据迁移尚未通过。

| 插件 | 上一基线 → 候选 | 内部 Bundle ID | 数据格式 | 用户数据位置 |
| --- | --- | --- | --- | --- |
| QQ | `1.1.1 → 1.2.0` | `amadeus-qq` | JSON `schemaVersion=1` | Profile 配置、显式 `statePath`、DSH Session 日志 |
| Memory | `1.0.1 → 1.2.0` | `dsh-mneme` | SQLite `user_version=0 → 1` | `memoryDir` 内的 Workspace 数据库和备份 |
| Life | `1.1.0 → 1.2.0` | `amadeus-life` | SQLite schema 10 | `$DSH_HOME/amadeus-life` 或显式路径 |
| State | `1.1.0 → 1.2.0` | `amadeus-state` | SQLite schema 2 | `$DSH_HOME/amadeus-state` 或显式路径 |
| Persona | `0.1.0 → 1.2.0` | `amadeus-persona` | JSON `schemaVersion=1` | `$DSH_HOME/amadeus-persona` 或显式目录 |

五个新包可分别安装，也可在同一 Profile 中同时加载。旧名→新名演练先移除旧包，再安装新包；合成 QQ Scene/Session、Memory 记忆、Life 意图、State 事件与兴趣、Persona 核心/风格/绑定均可读取。新包名不能与旧包并装；Memory 对外包名虽改变，内部 ID、默认数据目录和 Schema 保持。测试只覆盖列出的合成内容与 Host 接口，不代表真实 QQ、Life 外发、模型推理或大规模向量索引已通过。

## 稳定协作契约

- QQ 是可信 QQ Scene / Session 映射和平台执行权限的权威；没有 QQ 时其他插件安全降级。
- Persona 只注入独立的核心与场景 System Prompt Section；不替换 Preset、Memory ACL 或工具权限。
- Memory 保留单一 `dsh-mneme` 身份；场景记忆与授权由 Memory 和 QQ 的可信 Host 身份判定。
- Life 管理长期活动和自主唤醒；QQ 管理当前会话的连续交流，不重复争抢消息游标。
- State 管理状态及兴趣，不覆盖 Persona 人格。Life、Memory、State、Persona 均可在其他插件缺席时独立运行。

后续单独升级某个插件时，应先运行该仓库的单元及旧版数据升级测试，再在隔离 Profile 验证上述接口；不兼容更改须增加适配或限制组合版本。v1.0 与 v1.1 的历史证据继续保留在 [四插件矩阵](FOUR_PLUGIN_COMPATIBILITY.md)与[五插件历史基线](FIVE_PLUGIN_COMPATIBILITY.md)。
