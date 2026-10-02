# 数据契约（脱敏）

本文档只描述**字段与语义**，不含真实域名、key、内部配置，也不含渲染实现。

## 输入

```
query: string   # 玩家名（不区分大小写），或 UUID
```

## 上游字段 → 卡片字段

战绩来源为 Hypixel 玩家接口中的 `player.stats.Bedwars`，等级取自
`player.achievements.bedwars_level`。段位相关字段直接沿用原值，
渲染层自行决定配色。

| 卡片字段 | 上游来源 | 说明 |
|---|---|---|
| `kills` / `deaths` | `kills_bedwars` / `deaths_bedwars` | 普通击杀与死亡 |
| `final_kills` / `final_deaths` | `final_kills_bedwars` / `final_deaths_bedwars` | 终杀与终死 |
| `wins` / `losses` | `wins_bedwars` / `losses_bedwars` | 胜场与负场 |
| `beds_broken` / `beds_lost` | `beds_broken_bedwars` / `beds_lost_bedwars` | 破坏与丢失床数 |
| `winstreak` | `winstreak` | 当前连胜（若有） |
| `level` | `achievements.bedwars_level` | 起床战争等级 |
| `coins` | `coins` | 硬币 |
| `iron` / `gold` / `diamond` / `emerald` | `*_resources_collected_bedwars` | 四种资源采集量 |
| `exp` | `Experience_bedwars`（兼容 `Experience`） | 经验 |
| `tickets` | 箱子类字段 | 票券，可能缺失 |
| `modes[]` | `eight_one` / `eight_two` / `four_three` / `four_four` / `two_four` 的终杀终死 | 各模式 FKDR |

## 派生字段

| 字段 | 算法 | 边界 |
|---|---|---|
| `kdr` | `kills / deaths` | 分母 0 时返回 `kills`，两者都 0 时为 `0` |
| `fkdr` | `final_kills / final_deaths` | 同上 |
| `bbl` | `beds_broken / beds_lost` | 同上 |
| `win_rate` | `wins / (wins + losses) * 100` | 无对局时为 `0` |
| `fkdr_drop_on_one_fd` | `fkdr - final_kills / (final_deaths + 1)` | 终死为 0 时返回 `0` |

## 约束

- **缺失字段一律补 0**，绝不编造或估算展示值；缺失与真实的 0 在卡片上不作区分。
- 字段名保持与上游一致，避免下游再次映射。
- 段位相关字段保留原始枚举值（如 `rank`、`packageRank`、`newPackageRank`、
  `monthlyPackageRank` 及其颜色字段），配色由渲染层决定。

## 脱敏示例

```json
{
  "kills": 12345,
  "deaths": 6789,
  "final_kills": 4321,
  "final_deaths": 987,
  "wins": 654,
  "losses": 700,
  "beds_broken": 2100,
  "beds_lost": 1100,
  "winstreak": 3,
  "level": 250,
  "kdr": 1.8183,
  "fkdr": 4.3779,
  "win_rate": 48.29,
  "bbl": 1.9091,
  "coins": 1000000,
  "iron": 800000,
  "gold": 120000,
  "diamond": 20000,
  "emerald": 8000,
  "exp": 500000,
  "has_resources": true,
  "modes": [
    { "label": "单人", "fk": 1200, "fd": 300, "fkdr": 4.0 },
    { "label": "双人", "fk": 900, "fd": 200, "fkdr": 4.5 }
  ],
  "rank": null,
  "packageRank": "MVP_PLUS",
  "monthlyPackageRank": null
}
```

派生值只做展示用，最终显示精度由渲染层决定。
