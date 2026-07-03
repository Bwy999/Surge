<div align="center">

# 🍎 Surge Apple Ruleset

**为 Surge 精心调优的 Apple 分流规则集**

*推送直达 · 受限服务代理 · 下载 CDN 直连*

<br>

![Surge](https://img.shields.io/badge/Surge-5.x-FF6B00?style=flat-square&logo=apple&logoColor=white)
![Format](https://img.shields.io/badge/Format-RULE--SET-4A90D9?style=flat-square)
![Rules](https://img.shields.io/badge/Rules-4_Lists-9B59B6?style=flat-square)
![Maintained](https://img.shields.io/badge/Maintained-yes-2ECC71?style=flat-square)

</div>

---

## 📖 简介

本规则集把 Apple 系服务按用途拆成四个独立的 `.list` 文件，交给不同策略处理，兼顾**推送及时性**、**受限服务可用性**与**下载速度**。

> Apple 的域名体系庞杂，一刀切要么拖慢下载，要么让推送/认证走了不该走的路。本规则集的核心思路是 **按功能分层，各走各路**。

---

## 📦 文件清单

| 文件 | 用途 | 建议策略 | 说明 |
|:---|:---|:---:|:---|
| 🔔 `ApplePush.list` | APNs 推送 | `Apple Push` | 域名 + IP 段双保险,含 IPv6 |
| 🌐 `AppleService.list` | 国内受限服务 | `代理` | Siri 建议、News、gateway 等 |
| 🛒 `AppStore.list` | 账户与认证 | `代理` | Apple ID / idmsa / TestFlight |
| 🚀 `AppleDirect.list` | 下载与 CDN | `DIRECT` | mzstatic / iCloud,直连最快 |

---

## ⚙️ 引用方式

在主配置 `[Rule]` 段中按 **固定顺序** 引用（顺序不可乱）：

```ini
# ① 推送优先,保证及时性
RULE-SET,https://your.host/ApplePush.list,Apple Push

# ② 受限服务走代理
RULE-SET,https://your.host/AppleService.list,PROXY
RULE-SET,https://your.host/AppStore.list,PROXY

# ③ 兜底直连(范围最大,必须放最后)
RULE-SET,https://your.host/AppleDirect.list,DIRECT
RULE-SET,SYSTEM,DIRECT
```

> ⚠️ **顺序即优先级。** `AppleDirect.list` 含 `apple.com` 这类宽泛后缀,一旦提前引用,会把推送、认证的子域提前拦成直连。务必排在最后。

---

## 🧠 设计要点

<table>
<tr>
<td width="50%" valign="top">

### 🔔 推送双保险
`DOMAIN-SUFFIX,push.apple.com` 负责域名匹配,配合 `17.x` 段 IP-CIDR 兜底。所有 IP 规则均带 `no-resolve`,避免 DNS 解析拖慢首包。

</td>
<td width="50%" valign="top">

### 🌐 Siri 用 SUFFIX
`smoot.apple.com` 的实际请求是 `api.smoot.apple.com` 等子域,必须用 `DOMAIN-SUFFIX` 才能命中,精确 `DOMAIN` 会漏。

</td>
</tr>
<tr>
<td width="50%" valign="top">

### 🚀 下载走直连
`mzstatic` / `aaplimg` / iCloud 内容分发在国内本就有节点,直连比代理更快更稳。

</td>
<td width="50%" valign="top">

### 🧹 无死规则
移除了 akadns / edgekey 等靠 CNAME 匹配的无效条目——Surge 只匹配原始 hostname,这类规则永不命中。

</td>
</tr>
</table>

---

## 🔄 更新规则集

订阅未及时刷新时:

- **手动刷新** — Surge → 首页 → 长按配置 → 更新外部资源
- **强制拉新** — 在 URL 后追加版本参数,如 `?v=2`

---

<div align="center">

**📚 参考**

[Surge 官网](https://nssurge.com) · [配置规范](https://manual.nssurge.com) · [App Store](https://apps.apple.com/us/app/surge-4/id1442620678)

<br>

*Crafted for a faster, cleaner Apple experience* 🍏

</div>
