# Clash Verge Rev / FlClash 极简分流配置

适用于 Clash Verge Rev 和 FlClash 的极简分流与防污染配置模板，代理层基于 WireGuard（可配合 Wstunnel 等隧道）。

**这是一个持续更新的项目**，跟随博客文章的实践进度不断优化和完善配置方案。

---

## 📅 更新日志

| 版本 | 日期 | 主要改进 | 状态 |
|------|------|----------|------|
| **v3** | 2026-06-19 | 将 GEOSITE,google 前置到 private 之前，确保 Google 服务优先走代理 | ✅ 稳定 |
| **v2** | 2026-06-15 | 新增 DNS 最小覆写配置，解决 Google 污染问题 | ✅ 稳定 |
| **v1** | 2026-06-14 | 基于 MetaCubeX 的极简分流规则初版 | ✅ 稳定 |

## 🌟 核心特点

- **极简原则**：回归 MetaCubeX geosite.dat + geoip.dat 模型，用"地理归类"替代"手工规则维护"
- **稳定性优先**：v2 版本新增 DNS 最小覆写配置，解决 Google 污染问题
- **跨平台支持**：适配 Windows + Ubuntu + Android 多端环境
- **可维护性强**：结构最小化，便于排查问题
- **持续更新**：跟随博客实践进度不断优化

## 🔧 技术栈

- **Clash Verge Rev**：代理客户端（TUN 模式）
- **WireGuard**：VPN 隧道协议
- **Wstunnel**：WebSocket 隧道（可选，用于绕过封锁）
- **MetaCubeX**：GEOSITE + GEOIP 数据库

## 🏗️ 架构设计

```
Clash Verge Rev (TUN)
        ↓
WireGuard
        ↓
Wstunnel (可选)
        ↓
VPS
```

## 📋 使用方法

### 1. 配置准备

1. 确保已安装 Clash Verge Rev（推荐使用 Meta 内核）
2. 配置 WireGuard 连接（可配合 Wstunnel 使用）
3. 确保 MetaCubeX 数据库已内置（Clash Verge Rev 默认已包含）

### 2. 启用方式

在 Clash Verge Rev 中：
- ✔️ 开启 TUN 模式（GUI 设置）
- ❌ 不再使用系统代理
- ✔️ 使用内置 MetaCubeX 数据库

## 📝 分流规则说明

### 核心策略

```
中国流量 → DIRECT（直连）
海外流量 → Proxy（代理）
```

### 规则优先级

1. **Wstunnel 服务器直连**：避免回环
2. **本地/私有网络**：GEOSITE,private + GEOIP,private
3. **中国大陆直连**：GEOSITE,cn + GEOIP,CN
4. **非中国大陆走代理**：GEOSITE,geolocation-!cn
5. **默认兜底**：全部走代理

### v2 版本 DNS 最小覆写（核心改进）

v2 版本新增 DNS 配置，解决 Google 等境外域名被污染的问题：

```yaml
dns:
  nameserver:
    - https://dns.alidns.com/dns-query  # 国内域名使用阿里 DoH，直连
  proxy: Proxy  # 关键：让 fallback 查询走代理，避免本地 DNS 污染
  fallback:
    - tls://1.1.1.1:853  # 境外域名使用 Cloudflare DoT
  fallback-filter:
    geoip: true
    geoip-code: CN  # 只有非 CN 域名才触发 fallback
```

**原理说明**：
- `proxy: Proxy` 强制让 fallback 查询通过 WireGuard 隧道发出
- 彻底绕开本地 DNS 污染，获取正确的境外域名 IP
- 保持国内域名解析速度不受影响

### v3 版本 Google 优先代理（Play 下载优化）

v3 版本将 `GEOSITE,google` 规则前置到 `GEOSITE,private` 之前，实测可解决 Google Play 下载问题：

```yaml
rules:
  - GEOSITE,google  # 【关键】前置确保 Google 服务优先走代理
  - GEOSITE,private
  - GEOIP,private
  - GEOSITE,cn
  - GEOIP,CN
  - GEOSITE,geolocation-!cn
  - MATCH,Proxy
```

**原理说明**：
- Google Play 下载需要连接 Google 服务器，原规则中 private 优先导致部分 Google 流量被直连
- 将 google 规则前置，确保所有 Google 服务都走代理
- 配合 v2 的 DNS 配置，彻底解决 Play 下载失败问题

## ⚠️ 注意事项

- 不同地区网络环境、DNS 行为以及 VPS 线路质量，都会影响最终效果
- 建议先稳定运行 1~2 周，再逐步添加精细规则（如 Google、GitHub、AI 服务优化等）
- 本配置属于实验性技术探索，不保证适用于所有网络环境

## 📚 相关资源

- [Clash Verge Rev 官方文档](https://github.com/zzzgydi/clash-verge-rev)
- [FlClash 官方文档](https://github.com/chen08209/FlClash)
- [MetaCubeX 项目](https://github.com/MetaCubeX)
- [WireGuard 官方文档](https://www.wireguard.com/)
- [Wstunnel 项目](https://github.com/erebe/wstunnel)

## 📖 参考博客

本项目配置基于以下博客文章：

- [Clash Verge Rev + WireGuard + Wstunnel 稳定配置实践（一）：极简原则与初版构建](https://www.shuijingwanwq.com/2026/06/14/17084/)
- [Clash Verge Rev + WireGuard + Wstunnel 稳定配置实践（二）：Google 污染的 DNS 最小修正](https://www.shuijingwanwq.com/2026/06/15/17114/)
- [FlClash + WireGuard + Wstunnel 稳定配置实践（三）：Google Play 下载问题解决](https://www.shuijingwanwq.com/2026/06/19/17374/)

## 📄 许可证

MIT License
