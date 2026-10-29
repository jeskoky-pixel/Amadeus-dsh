# Amadeus v1.2.0 五插件兼容矩阵

状态：内部发行候选。下表的安装与升级实验使用官方 DSH Desktop `0.2.1-rc.3` 附带的 DSH CLI / Web Host，在独立 `DSH_HOME` 中运行。Desktop 图形插件管理器、公开 Market 下载源、真实 NapCat 和生产数据尚未在本轮验证。

| 插件 | 上一基线 → 候选 | 内部 Bundle ID | 数据格式 | 用户数据位置 |
| --- | --- | --- | --- | --- |
| QQ | `1.1.1 → 1.2.0` | `amadeus-qq` | JSON `schemaVersion=1` | Profile 配置、显式 `statePath`、DSH Session 日志 |
| Memory | `1.0.1 → 1.2.0` | `dsh-mneme` | SQLite `user_version=0 → 1` | `memoryDir` 内的 Workspace 数据库和备份 |
| Life | `1.1.0 → 1.2.0` | `amadeus-life` | SQLite schema 10 | `$DSH_HOME/amadeus-life` 或显式路径 |
| State | `1.1.0 → 1.2.0` | `amadeus-state` | SQLite schema 2 | `$DSH_HOME/amadeus-state` 或显式路径 |
| Persona | `0.1.0 → 1.2.0` | `amadeus-persona` | JSON `schemaVersion=1` | `$DSH_HOME/amadeus-persona` 或显式目录 |

五个包可分别安装，也可在同一 Profile 中同时加载。组合测试验证了 Bundle ID 不冲突、旧包替换安装后数据文件保持不变、重启后合成数据可读取、卸载后数据目录仍在，以及降级代码读取本轮数据。Memory 0→1 的实际升级另产生迁移前 SQLite 一致性备份。测试只覆盖列出的合成内容与 Host 接口，不代表真实 QQ、Life 外发、模型推理或大规模向量索引已通过。

## 稳定协作契约

- QQ 是可信 QQ Scene / Session 映射和平台执行权限的权威；没有 QQ 时其他插件安全降级。
- Persona 只注入独立的核心与场景 System Prompt Section；不替换 Preset、Memory ACL 或工具权限。
- Memory 保留单一 `dsh-mneme` 身份；场景记忆与授权由 Memory 和 QQ 的可信 Host 身份判定。
- Life 管理长期活动和自主唤醒；QQ 管理当前会话的连续交流，不重复争抢消息游标。
- State 管理状态及兴趣，不覆盖 Persona 人格。Life、Memory、State、Persona 均可在其他插件缺席时独立运行。

后续单独升级某个插件时，应先运行该仓库的单元及旧版数据升级测试，再在隔离 Profile 验证上述接口；不兼容更改须增加适配或限制组合版本。v1.0 与 v1.1 的历史证据继续保留在 [四插件矩阵](FOUR_PLUGIN_COMPATIBILITY.md)与[五插件历史基线](FIVE_PLUGIN_COMPATIBILITY.md)。
