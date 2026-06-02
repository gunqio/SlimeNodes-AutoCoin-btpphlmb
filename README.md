# SlimeNodes Auto Coin + Renew 🟢

自动刷币 + 自动续期 [SlimeNodes](https://dash.slimenodes.com) 服务器。

## 功能

- 🪙 **自动刷币** — 通过 Linkvertise 广告赚取金币（每次 +240 币）
- 🔄 **自动续期** — 服务器到期前 <24 小时自动续期
- 📱 **TG 通知** — 每次运行后发送详细通知到 Telegram

## 工作原理

纯 HTTP 方式，无需浏览器：

1. 使用 Session Cookie 登录
2. 调用 `/lv/gen` 获取 Linkvertise 链接
3. 解码 `r` 参数（Base64）得到 redeem URL
4. 等待 16-20 秒（服务端计时验证）
5. Redeem URL → +12 币/次
6. 每次运行 20 次广告 = +240 币

## 定时任务

GitHub Actions 每天自动运行两次：

- ⏰ **00:30 UTC**（北京时间 08:30）
- ⏰ **12:30 UTC**（北京时间 20:30）

也可手动触发 `workflow_dispatch`。

## 环境变量

| 变量 | 说明 | 必填 |
|------|------|------|
| `SLIME_SESSION` | SlimeNodes session cookie (`connect.sid`) | ✅ |
| `TG_BOT_TOKEN` | Telegram Bot Token | ❌ |
| `TG_CHAT_ID` | Telegram Chat ID | ❌ |
| `SERVER_ID` | 服务器 ID（默认 10102） | ❌ |
| `MAX_ADS` | 最大广告数（默认 20） | ❌ |
| `RENEW_HOURS` | 续期阈值小时数（默认 24） | ❌ |
| `RENEW_THRESHOLD` | 续期最低余额（默认 50 币） | ❌ |

## GitHub Secrets 设置

```json
{
  "SLIME_SESSION": "s%3A...",
  "TG_BOT_TOKEN": "7935239797:AAH...",
  "TG_CHAT_ID": "644320820"
}
```

## TG 通知格式

```
🟢 SlimeNodes 刷币通知
━━━━━━━━━━━━━━━━
👤 账号: btpphlmb@outlook.com
━━━━━━━━━━━━━━━━
💰 刷币: ✅ +240币
🏦 余额: 1400币
⏰ 到期: 161小时后 (6.7天)
🔄 续期: ⏭️ 暂不需要 (>24h)
━━━━━━━━━━━━━━━━
```

## 续期逻辑

- 通过 `/lastrenew?id={SERVER_ID}` API 获取精确到期时间
- 仅当剩余 < `RENEW_HOURS`（默认 24h）且余额 ≥ `RENEW_THRESHOLD`（默认 50 币）时自动续期
- 续期费用：约 115 币/次

## 参考

- [jpus/SlimeNodes-AutoCoin](https://github.com/jpus/SlimeNodes-AutoCoin) — 原始参考脚本
