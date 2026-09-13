# Quantumult X 自定义规则

个人用的 Quantumult X 规则集合。规则从公开仓库整理、验证后拆分成本地可用的格式，方便自己按需订阅和改。

## 目录

```
.
├── QiMaoNovel.conf              # 七猫小说 · 三段合一，手动粘贴用
├── filter/
│   └── QiMaoNovel.list          # 七猫小说 · 分流规则，[filter_remote] 用
└── rewrite/
    └── QiMaoNovel.conf          # 七猫小说 · 重写规则（含 hostname），[rewrite_remote] 用
```

## 七猫小说去广告

### 原理

七猫主域是 `wtzw.com`，**内容接口和广告接口在同一个域下**（`api-cfg.wtzw.com`、`api-bc.wtzw.com`、`api-gw.wtzw.com`）。所以不能整域 reject，只能：

1. 对纯广告域名直接 reject（不需要 MITM）；
2. 对同域下的广告接口做 URL 路径级 reject（需要 MITM 解密）。

规则按风险分三组：

| 组 | 内容 | 副作用 |
| --- | --- | --- |
| A | 纯广告域名，`filter/` 里 | 无 |
| B | 第三方广告 SDK 接口（穿山甲、快手联盟、京东联盟、adukwai） | 极小 |
| C | 七猫自家广告与运营接口（开屏、书城推广位、阅读页段落推广） | 可能误伤金币/红包等福利页 |

### 用法一：订阅（推荐）

在 QX 配置文件的对应 section 里加入：

```
[filter_remote]
https://raw.githubusercontent.com/OWNER/REPO/BRANCH/filter/QiMaoNovel.list, tag=七猫小说-分流, enabled=true

[rewrite_remote]
https://raw.githubusercontent.com/OWNER/REPO/BRANCH/rewrite/QiMaoNovel.conf, tag=七猫小说-重写, enabled=true
```

### 用法二：手动粘贴

把 `QiMaoNovel.conf` 里的三块分别追加到自己配置的同名 section 下，**不要整份覆盖**，否则会丢掉你自己的分流和节点。

### 必做的前置步骤

1. 「重写」页打开 **重写** 总开关；
2. 「MITM」页打开 **MITM** 总开关；
3. 证书没装的话：MITM 页生成并安装证书 → 设置 → 通用 → VPN与设备管理 → 安装描述文件 → 设置 → 通用 → 关于本机 → 证书信任设置 → 给 QX 证书打开完全信任；
4. **七猫 App 清一次缓存或重装**，规则才会稳定生效。

### 已知问题

- 字节系 CDN 域名有时无法 MITM（`lf-cdn-tos.bytescm.com` 这一类），这不是配置错误。
- 七猫升级版本后接口路径可能变，届时规则需要更新。
- QX 的 `[filter_local]` / `[filter_remote]` 规则是**全局生效**的，没有按 App 匹配的语法。A 组里的 `gdt.qq.com` 会影响其他 App 的腾讯广告位（通常正是你想要的）。
- 想自己补规则：开着 MITM 和日志，重现「启动 App / 翻页 / 进书城」，在 QX 请求日志里按 Host 排序，找带 `ad` / `adv` / `splash` / `reward` / `operation` 特征的路径，照 C 组的写法加一条 `url reject-dict`。

## 来源

- [fmz200/wool_scripts](https://github.com/fmz200/wool_scripts) — `QuantumultX/rewrite/split/partQ/QiMaoNovel.snippet`
- [GrandpaNiuu/GrandpaNiu](https://github.com/GrandpaNiuu/GrandpaNiu) — `Rewrite/Sources/Apps/seven-cat.conf`（转写自 kelee.one 的 Loon 插件）

## 说明

- 只做**去广告**，不含任何会员解锁 / 破解类脚本。
- 规则仅供个人使用，请支持正版。

_整理日期：2026-09-14_
