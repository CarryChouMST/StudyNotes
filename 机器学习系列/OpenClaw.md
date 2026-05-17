- 消息渠道层
- 网关层
- 客户端层
- Agent运行时层
- 模型提供商层
- gateway是整个系统唯一真相来源，单一长期存活进程。Chaneel由ChannelManager管理生命周期，路由引擎负责将来自各渠道的消息映射到具体的Agent和SessionKey，支持多种路由维度
- 客户端链接，所有的客户端都通过WebSocket连接同一个Gateway。
- Agent运行时层内嵌了基于pi-mono的Agent运行时，但是会话管理、工具调用注册、发现机制都由OpenClaw自身掌控。核心入口是runEmbeddedPiAgent(src/agents/pi-embedded-runner/run.js)，按Session Lane串行化运行，解析模型与AuthProfile，订阅pi-agent-core事件并流式输出assistant/tool delta
- 会话管理(Session Management)，将会话以JSONL格式持久化存储在Gateway主机上，格式为agent:\<agentId\>:\<mainKey\>，gateway是会话状态的唯一真想来源，UI客户端不直接读取JSONL文件。
- 多Agent路由，支持在同一Gateway中运行多个完全隔离的Agent, 每个Agent拥有独立的: Workspace(文件、AGENTS.md, SOUL.md等)， agentDir(Auth Profile、模型注册表)， Session Store， 
- 插件系统(Plugin System)、
- 内存系统(Memory)，以Markdown文件为基础存储在workspace中，通过向量嵌入提供语义检索，会话接近压缩阈值时，会自动触发静默的内存刷新，让模型将重要信息写入磁盘再进行上下文压缩。

- 将底层事件转换为三类标准流：tool、assistant和lifecycle
- **事件流是通信层**：标准化数据传输，与执行环境解耦
- **沙箱是执行层**：控制工具运行位置，不影响事件格式
- **策略是控制层**：决定哪些工具可用，沙箱无法绕过策略限制

- openClaw采用开源的pi-mono作为引擎驱动，agent自身处理会话循环、编码工具、抽象接口和内部事件。而openclaw处理服务器会话的复杂性，包括队列话、超时、多Agent隔离、工具策略、异常处理和事件流标准化(转化为tool, assistant和lifecycle展示进度、流式文本和lifecycle状态)。记忆也由openClaw提供分为长期记忆和日期记忆

# 部署使用

- 在部署时，如果发现大模型api始终超时，检查容器内环境是否没有与宿主机共享网络(宿主机可能用了vpn)。可以使用lume的net-bridge模式，或者让宿主机单独为vm开一个监听端口来转发流量。

  - 共享网络之后，还需要处理网关的代理`~/Library/LaunchAgents/ai.openclaw.gateway.plist`在这个plist文件中处理。

  - gateway有前台模式和服务模式，前台模式就是手动在shell中启动gateway run，好处是自动继承当前shell环境变量的代理设置；服务模式，需要手动在plist中设置代理，好处是开机自启和后台运行

  -   停掉服务：
       openclaw gateway stop
       openclaw gateway uninstall

    ✦ 启动服务：
       openclaw gateway install  # 服务模式，开机自启

       source ~/.zshrc && openclaw gateway run  # 前台模式，需要保持终端

  -   怎么看服务是否正在运行：

    ```
    │ openclaw gateway status │ 详细状态（推荐）       │
      │ `ps aux \               │ grep openclaw-gateway` │
      │ lsof -i :18789          │ 检查端口占用
    ```

- 宿主机在打开浏览器访问时，被拒绝访问了。因为网关启动了token认证，访问web应用时需要在链接后面带上token。

  `http://192.168.64.6:18789/?token=9a6139d38effd2049712efc60d9a9379f81a07e9a6a06cc8`

- 钉钉破配置好后没有梵音，是因为虚拟机无法访问钉钉服务器。

  ```
  ✦ 问题解决了！总结一下处理过程：
  
    根本原因：
     1. 虚拟机被阿里郎阻止访问钉钉服务器
     2. 需要通过宿主机的 mitmproxy 代理才能访问
     3. dingtalk-stream 库硬编码了 rejectUnauthorized: true，不读取环境变量中的代理设置
  
    解决方案：
    修改了 dingtalk-stream 库的源码：
  
     1. 禁用 TLS 证书验证：
        sslopts = { rejectUnauthorized: true } → { rejectUnauthorized: false }
  
     2. 注入代理 Agent：
        import { HttpsProxyAgent } from "https-proxy-agent";
        const agent = new HttpsProxyAgent("http://192.168.64.1:8889");
        this.socket = new WebSocket(this.dw_url, { ...this.sslopts, agent });
  
    修改的文件：
     - ~/.openclaw/extensions/dingtalk/node_modules/dingtalk-stream/dist/client.mjs
  
    注意事项：
     - 这是临时方案，如果重新安装插件需要再次修改
     - 建议向 @soimy/dingtalk 或 dingtalk-stream 提 Issue/PR 添加代理配置支持
  ```

  

