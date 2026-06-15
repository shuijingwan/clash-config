# Clash Verge Rev 极简分流配置

适用于 Clash Verge Rev 的极简分流与防污染配置模板，代理层基于 WireGuard（可配合 Wstunnel 等隧道）。

## 🌟 核心特点

- **极简原则**：回归 MetaCubeX geosite.dat + geoip.dat 模型，用"地理归类"替代"手工规则维护"
- **稳定性优先**：简化 DNS 配置，避免 fallback DNS 和复杂规则冲突
- **跨平台支持**：适配 Windows + Ubuntu + Android 多端环境
- **可维护性强**：结构最小化，便于排查问题

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

## ⚠️ 注意事项

- 不同地区网络环境、DNS 行为以及 VPS 线路质量，都会影响最终效果
- 建议先稳定运行 1~2 周，再逐步添加精细规则（如 Google、GitHub、AI 服务优化等）
- 本配置属于实验性技术探索，不保证适用于所有网络环境

## 📚 相关资源

- [Clash Verge Rev 官方文档](https://github.com/zzzgydi/clash-verge-rev)
- [MetaCubeX 项目](https://github.com/MetaCubeX)
- [WireGuard 官方文档](https://www.wireguard.com/)
- [Wstunnel 项目](https://github.com/erebe/wstunnel)

## 📖 参考博客

本项目配置基于以下博客文章：

- [Clash Verge Rev + WireGuard + Wstunnel 稳定配置实践（一）：极简原则与初版构建](https://www.shuijingwanwq.com/2026/06/14/17084/)
- [Clash Verge Rev + WireGuard + Wstunnel 稳定配置实践（二）：Google 污染的 DNS 最小修正](https://www.shuijingwanwq.com/2026/06/15/17114/)

## 📄 许可证

MIT License
