## Telegram 设置机器人报错

- 检查 Telegram Bot Token 是否正确
- 检查 BaseUrl 是否为 HTTPS
- 检查网站 SSL 证书是否有效
- 检查服务器能否正常连接 Telegram API

## 订阅链接在配置 Cloudflare CDN 后无法正常更新

在 Cloudflare Cache Rule 中将以下 URL 设为 Bypass Cache：

```
*.example.com/sub/*
```

## SSPanel-UIM 会添加 xx 协议吗？

SSPanel-UIM 目前支持 Shadowsocks, Shadowsocks2022, Vmess，Trojan 以及 TUIC，其中 Vmesss 与 Trojan 协议支持 V2Ray 传输插件，Shadowsocks 2022 协议支持单端口多用户。

## SSPanel-UIM 支持 VLESS/XTLS/REALITY.. 吗？

我们相信一个良好设计的代理协议是不会需要在其生命周期内进行反复修改底层设计才能有效规避审查，原版 Trojan 协议就是一个很好的正面例子。即使在先进流量审查技术例如主动探测，深度包检测以及基于机器学习的流量分析等等威胁下依然能够有效穿透绝大多数受限网络环境，中国和伊朗等地的专制政权需要使用诸如无差别干扰 TLS 握手，SNI 域名白名单，人工 IP 黑名单甚至是局部切断互联网等极端方式才能真正影响原版 Trojan 协议的运作。

可惜的是，我们在 Xray 项目中并没有看到这种发展，相反的，我们看到的是 VLESS，XTLS 等等“革命性”技术在引进的数年后就被宣布取消支持，并且使用新的“革命性” REALITY 协议取而代之。对于 SSPanel-UIM 这样依赖志愿者贡献时间与精力维护的开源项目来说，支持一个缺少长期稳定性且每次重大更新后都需要反复修改后端代码才能适配的协议是一件得不偿失的举措。
