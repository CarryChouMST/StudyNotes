- Cpp_mock：**一个独立可运行的 C++ 测试桩程序**，目的是让 `agroup`（组队功能）的核心逻辑能在 **macOS 本机**脱离真实 iOS 环境独立运行和测试，无需接入任何真实的外部服务。

- `AGroupTestStub` — 测试桩主体

  **核心角色**：把 Mock 层 + `AgroupServiceImpl` 串联起来，提供一个**命令驱动的测试入口**。

  - **命令文件轮询**：监听 `/tmp/agroup_test.cmd`，解析 `[CMD] command_name|key1=val1|key2=val2` 格式的命令
  - **日志文件输出**：把服务事件写到 `/tmp/agroup_test.log`，供外部（Python UI）读取
  - **内置命令**：`create_team`、`join_team`、`leave_team`、`location_update`、`scene_change`、`focus_member`、`get_state` 等，直接驱动真实的 `AgroupServiceImpl` 执行业务逻辑

- 接口可以分为：操作接口(外部主动调用)、依赖注入接口(外部能力提供)，和事件回调接口(通知外部).
  - 操作接口是接口提供方实现并提供create方法，让外部调用。
  - 依赖注入是外部实现，然后内部主动调用。
  - 事件回调接口，也是外部实现，也是内部主动调用的(但是主要是为了通知外部干事情，而不是向外部要能力)。
- 接口测试/功能测试部分：可以做一个SKILL包括SKILL.md，脚本/，模版/，示例/。
- `cmake -B build ...`：**配置并生成构建文件**；`cmake --build build`：**真正开始编译/链接**，或者用make .

- Flow_chat的mermaid流程图。

## C++SDK运行

- `WebAssembly`是一种**可在浏览器中高性能运行的二进制代码格式**。

- 