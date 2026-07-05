# Surge Ruleset

**按场景分层的 Surge 分流规则集**

*Apple 服务分层 · 加密货币 · 港美股券商 · 离岸银行*

![Surge](https://img.shields.io/badge/Surge-5.x-F26B3A) ![Format](https://img.shields.io/badge/Format-RULE--SET-4A90D9) ![Rules](https://img.shields.io/badge/Rules-8_Lists-9B59B6) ![Maintained](https://img.shields.io/badge/Maintained-yes-2ECC71)

---

## 设计原则

一个域名只属于一个列表，一个列表只解决一类问题。

- **按功能分层**——推送、下载、受限服务各走各路，互不拖累
- **按地域归口**——金融类按业务属地（HK / SG）拆分，出口 IP 与账户属地一致，降低风控误判
- **精确优先**——`DOMAIN` > `DOMAIN-SUFFIX`，不使用 `DOMAIN-KEYWORD`；`IP-CIDR` 一律 `no-resolve`
- **抓包驱动**——所有条目来自真实连接记录或官方文档，不收录来源不明的域名

---

## 文件总览

| 文件 | 用途 | 建议策略 |
|---|---|---|
| `ApplePush.list` | APNs 推送长连接 | 代理 |
| `AppleDirect.list` | 系统更新 / App 下载 / CDN | `DIRECT` |
| `AppleService.list` | 受限服务（TV+ / 地区限定内容） | 代理 |
| `AppStore.list` | App Store / 商店前端 | 按需 |
| `Cryptocurrency.list` | CEX / DEX / 钱包 / 行情 / 链上 | 专用节点 |
| `Broker.list` | moomoo/富途 · uSMART SG · 复星财富 | 香港节点 |
| `HKBank.list` | BOCHK · HSBC HK · iFAST · Fusion Bank · AlipayHK · ZA Bank | 香港节点 |
| `OCBC.list` | OCBC 新加坡 | 新加坡节点 |

---

## 使用方式

在主配置 `[Rule]` 段引用，策略在引用处指定：

```ini
[Rule]
# Apple — 顺序敏感，Push 必须在最前
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/ApplePush.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/AppleService.list,Proxy
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/AppStore.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/AppleDirect.list,DIRECT

# 金融
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/Cryptocurrency.list,Crypto
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/Broker.list,HK
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/HKBank.list,HK
RULE-SET,https://raw.githubusercontent.com/<user>/<repo>/main/OCBC.list,SG
```

> 规则集文件内部不含策略。Egern 用户可直接以远程 ruleset 方式引用，格式兼容。

---

## 引用顺序

Surge 按声明顺序自上而下匹配，存在交叉覆盖时**先声明者生效**：

1. `ApplePush.list` 置于所有 Apple 规则之前——推送域被其他列表劫持会导致通知延迟
2. 金融类列表之间无交叉域名，顺序不敏感
3. 共享风控 / SDK 域（如 ThreatMetrix、Sardine）已归入使用它的业务列表，如多个列表策略不同，将你希望优先的列表前置

---

## 维护

- 条目来源：iOS App 隐私报告 + Surge/Egern 连接面板抓包
- 影子域 / 备用域随官方轮换持续更新
- 发现流量未命中：提 Issue 附域名与所属 App 即可

---

## License

MIT
