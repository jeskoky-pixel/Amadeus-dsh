# 当前候选兼容矩阵

| 插件 | 包名与版本 | main SHA |
| --- | --- | --- |
| qq | `@fg-lab/amadeus-qq@1.2.1` | `5aba529aff84ef4eb43b1c1d12aec32286fc06b8` |
| memory | `@fg-lab/amadeus-memory@1.3.0` | `c2fea2f2c28cb43c1eedda85c9ab1dfa8076213b` |
| life | `@fg-lab/amadeus-life@1.2.1` | `9e973b5c2da53ff8a1ccb17176a8c7c22199b7d3` |
| state | `@fg-lab/amadeus-state@1.2.0` | `5ca357ea0d43735532433656742b6c1ef94b085c` |
| persona | `@fg-lab/amadeus-persona@1.2.0` | `74597508b456a020c91be0e1274b3cc515458086` |

上表为 2026-10-31 main，未正式公开发行。宿主基线为官方 Desktop 0.2.1-rc.3。

QQ 1.2.1 + Memory 1.3.0 + Life 1.2.1 + State/Persona 1.2.0 在隔离官方 Host 同装并重启；QQ 使用合成 OneBot。QQ 1.2.0 的裸 UUID 已由 Life 1.2.1 向后兼容，无需重建 Session。Memory 保留 schema 2，QQ 不是必选依赖。

详细结果与未验证边界见[联合报告](../AMADEUS_MAIN_INTEGRATION_2026-10-31.md)。正式发布须继续执行各仓库 UPGRADE_POLICY、DATA_MIGRATION、RELEASE_CHECKLIST。
