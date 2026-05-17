| 模块                 | 职责                                          | 关键文件/入口                                    |
| -------------------- | --------------------------------------------- | ------------------------------------------------ |
| **LangGraph Server** | 代理运行时与工作流执行                        | `src/agents/lead_agent/agent.py` CLAUDE.md:92-96 |
| **Gateway API**      | REST API（模型、MCP、技能、记忆、上传、产物） | `src/gateway/app.py` app.py:42-68                |
| **中间件链**         | 请求/响应横切关注点（9 个中间件）             | `src/agents/middlewares/` CLAUDE.md:110-122      |
| **工具系统**         | 沙箱/内置/社区/MCP/技能工具加载               | `src/tools/tools.py:get_available_tools`         |
| **记忆系统**         | 长期上下文提取、存储与注入                    | `src/agents/memory/` CLAUDE.md:235-253           |
| **沙箱系统**         | 隔离执行（本地/Docker）与虚拟路径映射         | `src/sandbox/` CLAUDE.md:165-185                 |
| **子代理系统**       | 异步任务委托与并发执行                        | `src/subagents/` CLAUDE.md:186-193               |
| **MCP 集成**         | 动态加载外部 MCP 服务器工具                   | `src/mcp/` CLAUDE.md:212-219                     |
| **技能系统**         | 领域工作流发现、加载与提示注入                | `src/skills/` CLAUDE.md:220-227                  |
| **模型工厂**         | 动态实例化 LLM（思考/视觉支持）               | `src/models/factory.py` CLAUDE.md:228-234        |
| **配置系统**         | 应用与扩展配置加载与解析                      | `src/config/`                                    |

以上是deerflow后面的模块，其中gateway门控层可以下放给chainlit。

- LangGraph Server 是 DeerFlow 的核心代理运行时，负责基于 LangGraph 的多代理工作流编排，包括代理创建与配置、线程状态管理、中间件链执行、工具执行编排以及 SSE 流式响应 ARCHITECTURE.md:54-66 。其入口为 `src/agents/lead_agent/agent.py:make_lead_agent` ARCHITECTURE.md:58-。
- 中间件链是一组按严格顺序执行的横切关注点处理器，在每次代理调用前后依次执行，用于准备环境、处理上下文、管理状态等。DeerFlow 中包含 11 个中间件，从线程目录创建到澄清中断，覆盖文件、上传、沙箱、记忆、图像等各个方面。



# 中间件链

| #    | 中间件                     | 作用                                                         |
| ---- | -------------------------- | ------------------------------------------------------------ |
| 1    | ThreadDataMiddleware       | 创建线程隔离目录（workspace/uploads/outputs） CLAUDE.md:112-113 |
| 2    | UploadsMiddleware          | 注入新上传文件到对话上下文 CLAUDE.md:113-114                 |
| 3    | SandboxMiddleware          | 获取沙箱环境并存入状态 CLAUDE.md:114-115                     |
| 4    | DanglingToolCallMiddleware | 为缺失响应的工具调用注入占位消息 CLAUDE.md:115-116           |
| 5    | SummarizationMiddleware    | 接近令牌限制时压缩上下文（可选） CLAUDE.md:116-117           |
| 6    | TodoListMiddleware         | 计划模式下用 write_todos 跟踪任务（可选） CLAUDE.md:117-118  |
| 7    | TitleMiddleware            | 首轮对话后自动生成线程标题 CLAUDE.md:118-119                 |
| 8    | MemoryMiddleware           | 将对话加入异步记忆更新队列 CLAUDE.md:119-120                 |
| 9    | ViewImageMiddleware        | 视觉模型支持时注入 base64 图像数据 CLAUDE.md:120-121         |
| 10   | SubagentLimitMiddleware    | 强制最大并发子代理数限制（可选） CLAUDE.md:121-122           |
| 11   | ClarificationMiddleware    | 拦截 ask_clarification 并中断执行（必须最后）                |

中间件在 `src/agents/lead_agent/agent.py` 中按顺序注册并执行 

# 一些实现

- summarize中间件会被放到beforemodel的钩子时机上。

# 用户级别

- 线程隔离目录：基于传入的userid，需要修改记忆后端目录