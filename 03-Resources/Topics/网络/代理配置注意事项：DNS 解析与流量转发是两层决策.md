---
tags:
  - network
  - dns
  - proxy
type: resource
status: active
created: 2026-04-14
updated: 2026-04-14
reuse_value: high
---

# 代理配置注意事项：DNS 解析与流量转发是两层决策

## Summary

很多代理配置问题，不是规则写错了，而是把 DNS 解析和流量转发混成了一件事。

## 两阶段模型

```text
【DNS 阶段】
      ↓
nameserver-policy 决定
用哪个 DNS 解析域名
      ↓
【得到 IP / fake-ip】
      ↓
【流量阶段】
      ↓
rules 决定
→ PROXY
→ DIRECT
```

## 核心结论

### 1. DNS 和代理不是同一层

- DNS 负责回答“这个域名对应什么地址”
- 代理规则负责回答“这次连接要不要交给代理服务器转发”

即使 DNS 解析对了，后续如果把内网流量交给代理服务器，依然可能失败。

### 2. 内网访问最重要的是：内网域名必须用内网 DNS，且流量直连

标准链路应该是：

```text
intranet.corp.local
   ↓
DNS → 内网 DNS（10.0.0.2）
   ↓
得到 10.x.x.x
   ↓
连接 → DIRECT
```

这里只满足其中一个条件都不够。内网域名要交给内网 DNS，内网流量还必须 `DIRECT`。

## 为什么内网域名不能走代理

### 情况 1：DNS 阶段就失败

```text
intranet.corp.local
   ↓
DNS → 外部 DNS
   ↓
找不到内网域名
```

### 情况 2：DNS 侥幸成功，但代理阶段失败

```text
intranet.corp.local
   ↓
DNS → 外部 DNS
   ↓
得到 10.x.x.x
   ↓
PROXY → 代理服务器去连接 10.x.x.x
   ↓
连不上
```

## 最容易混淆的误区

### 误区 1：把 DNS 解析策略当成代理规则

`nameserver-policy` 只决定“问谁解析”，真正决定连不连代理的是 `rules`。

### 误区 2：把代理规则当成 DNS 解析策略

即使给 `corp.local` 配了 `DIRECT`，如果 DNS 还是走外部解析，也可能在拿到 IP 之前就失败。

### 误区 3：以为 fake-ip 会破坏内网访问

`fake-ip` 本身不是问题，问题在于：

- 内网域名有没有命中正确 DNS 策略
- 内网流量有没有绕过代理

## 推荐的决策模型

### 1. 内网域名

- DNS：强制走内网 DNS
- 流量：`DIRECT`

### 2. 国内公网域名

- DNS：国内 DNS 优先
- 流量：通常 `DIRECT`

### 3. 国外公网域名

- DNS：优先 DoH / 外部可信 DNS
- 流量：通常 `PROXY`

## Related

- 暂无内部关联页

## Open Questions

- 什么时候应该把“代理排障”上升成一张更通用的网络分层判断图，而不是继续堆具体配置片段？
