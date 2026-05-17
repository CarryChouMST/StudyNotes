## Openclaw的调试日志

- 总结一下微信的情况：错误日志是 sendWeixinOutbound: contextToken missing，意思是微信插件发消息需要一个 contextToken（微信的会话令牌），但 cron 定时任务运行在 isolated session 里，拿不到这个 token。这是 openclaw-weixin 插件本身的限制——它只能在有用户主动发消息时回复，不支持像钉钉那样主动推送。

  钉钉能支持主动推送是因为它用的是 Stream 长连接协议，有持久化的通道可以随时发消息。

  所以目前喝水提醒只能走钉钉，已经帮你切回去了，每小时触发一次。如果将来 openclaw-weixin 插件更新支持了主动推送，再切回微信就行。

- 任务计划创建成功了！现在全部配置完成，给你总结一下今天做了哪些事：

  **已修复的问题：**

  1. **Dashboard 无法访问** — Gateway 只监听 loopback，改成了 lan 模式，并配置了端口转发（WSL IP → Windows 127.0.0.1:18789）
  2. **Token 上下文显示 16k** — 旧 session 的 contextTokens 写死了 16000，删除后重建恢复为 100k
  3. **钉钉机器人不回复** — 飞书插件路径不存在导致配置无效，移除后修复；同时 V2Ray 代理拦截了钉钉 WebSocket，添加了 NO_PROXY 绕过
  4. **喝水定时任务报错** — delivery 配置缺少 to 和 accountId 字段，参照 sessions.json 的格式修复后正常发送到钉钉
  5. **微信定时推送** — openclaw-weixin 插件不支持主动推送（缺 contextToken），目前只能用钉钉

  **开机自启配置：**

  - **Windows 任务计划 WSL-OpenClaw-PortForward** — 每次登录后自动获取 WSL 新 IP 并更新端口转发
  - **WSL systemd** — OpenClaw Gateway 已配置为 systemd 服务，WSL 启动后自动运行

  **开机后你只需要做一件事：** 打开一次 WSL 终端（或在 cmd 里执行 wsl.exe），剩下的都会自动完成。

- `openclaw dashboard`的 Dashboard URL: http://127.0.0.1:18789/#token=0cd6b091584eff182b3a483cc371ba1844024b62937f8354，要把#换成?，不然浏览器的js可能解析不了。
- F11可以切全屏或者窗口模式，现在wsl代理是走主机的v2ray转发，所以v2ray一定要开着