# Grasshopper MCP Bridge

Grasshopper MCP Bridge 是一个面向 AI Agent 的 MCP 桥接服务，用于把 Rhino/Grasshopper 与支持 Model Context Protocol (MCP) 的桌面客户端连接起来。通过它，AI Agent 可以用自然语言向 Grasshopper 发送建模指令，例如创建组件、连接参数、生成几何体或执行常见建模流程。

## 功能特性

- 通过 MCP 协议连接 Grasshopper 与 AI Agent / Claude Desktop。
- 提供直观的工具接口，用于创建、查找和连接 Grasshopper 组件。
- 支持高层意图识别，可以根据简单描述自动组合复杂组件模式。
- 内置组件知识库，了解常见组件的参数、输入输出和连接规则。
- 提供组件连接指导资源，帮助 AI Agent 更准确地生成 Grasshopper 定义。

## 系统架构

项目由三部分组成：

1. **Grasshopper MCP 组件 (`GH_MCP.gha`)**：安装到 Grasshopper 中的插件，负责启动 TCP 服务并接收外部命令。
2. **Python MCP Bridge Server**：Python 编写的 MCP 桥接服务器，负责连接 MCP 客户端与 Grasshopper 插件。
3. **组件知识库**：JSON 文件，保存组件信息、常见模式和意图识别数据。

## 安装说明

### 环境要求

- Rhino 7 或更高版本
- Grasshopper
- Python 3.8 或更高版本
- 支持 MCP 的客户端，例如 Claude Desktop 或其他 AI Agent 客户端

### 安装步骤

1. **安装 Grasshopper MCP 组件**

   **方式一：下载预编译的 `GH_MCP.gha` 文件（推荐）**
   
   从 GitHub 下载 [GH_MCP.gha](https://github.com/alfredatnycu/grasshopper-mcp/raw/master/releases/GH_MCP.gha)，并复制到 Grasshopper 组件库目录：
   ```
   %APPDATA%\Grasshopper\Libraries\
   ```

   然后重启 Rhino 和 Grasshopper。

   **方式二：从源码编译**
   
   如果需要自行修改 C# 插件，可以克隆仓库后使用 Visual Studio 打开并编译 `GH_MCP/GH_MCP.sln`。

2. **安装 Python MCP 桥接服务**

   **方式一：从 PyPI 安装（推荐）**
   
   使用 pip 直接安装：
   ```
   pip install grasshopper-mcp
   ```
   
   **方式二：从 GitHub 安装最新版**
   
   也可以从 GitHub 安装最新版本：
   ```
   pip install git+https://github.com/alfredatnycu/grasshopper-mcp.git
   ```
   
   **方式三：从源码安装**
   
   如果需要修改代码或进行二次开发，可以使用：
   ```
   git clone https://github.com/alfredatnycu/grasshopper-mcp.git
   cd grasshopper-mcp
   pip install -e .
   ```

   **安装指定版本**
   
   从 PyPI 安装指定版本：
   ```
   pip install grasshopper-mcp==0.1.0
   ```
   或从 GitHub 指定标签安装：
   ```
   pip install git+https://github.com/alfredatnycu/grasshopper-mcp.git@v0.1.0
   ```

## 使用方法

1. **启动 Rhino 和 Grasshopper**

   打开 Rhino，然后启动 Grasshopper。

2. **将 `GH_MCP` 组件放到 Grasshopper 画布上**

   在 Grasshopper 组件面板中找到 `GH_MCP`，并把它拖入画布。

3. **启动 Python MCP 桥接服务**

   打开终端，运行：
   ```
   python -m grasshopper_mcp.bridge
   ```
   
   > **说明**：由于不同系统的 Python 脚本路径可能不同，`grasshopper-mcp` 命令不一定能直接运行。更推荐使用 `python -m grasshopper_mcp.bridge`。

4. **在 MCP 客户端中连接桥接服务**

   **方式一：手动连接**
   
   在 Claude Desktop 或其他 MCP 客户端中使用以下设置连接：
   - 协议：MCP
   - 主机：`localhost`
   - 端口：`8080`

   **方式二：配置 Claude Desktop 自动启动桥接服务**
   
   可以修改 Claude Desktop 配置，让它自动启动 MCP 桥接服务：
   
   ```json
   "grasshopper": {
     "command": "python",
     "args": ["-m", "grasshopper_mcp.bridge"]
   }
   ```
   
   这段配置会让 Claude Desktop 通过 `python -m grasshopper_mcp.bridge` 启动 MCP 服务。

5. **开始用自然语言控制 Grasshopper**

   当 Grasshopper 画布上已经放置 `GH_MCP` 组件，并且 Python 桥接服务正常运行后，即可让 AI Agent 通过 MCP 与 Grasshopper 交互。

## 示例指令

你可以向 AI Agent 输入类似下面的指令：

- 创建一个半径为 5、圆心在 `(0,0,0)` 的圆。
- 将这个圆连接到 Extrude 组件，并设置挤出高度为 10。
- 创建一个 5 行 5 列的点阵。
- 对所有选中的对象应用随机旋转。

## 常见问题

如果遇到问题，可以按下面的方向检查：

1. **`GH_MCP` 组件无法加载**
   - 确认 `GH_MCP.gha` 已放入正确目录：`%APPDATA%\Grasshopper\Libraries\`。
   - 在 Grasshopper 中进入 `File > Preferences > Libraries`，对新组件执行 `Unblock`。
   - 重启 Rhino 和 Grasshopper。

2. **Python 桥接服务无法启动**
   - 如果 `grasshopper-mcp` 命令不可用，请改用 `python -m grasshopper_mcp.bridge`。
   - 确认已经安装所需 Python 依赖。
   - 检查 `8080` 端口是否已被其他程序占用。

3. **MCP 客户端无法连接**
   - 确认 Python 桥接服务正在运行。
   - 确认连接地址为 `localhost:8080`。
   - 查看桥接服务终端输出，检查是否有错误信息。

4. **指令没有在 Grasshopper 中执行**
   - 确认 `GH_MCP` 组件已经放在 Grasshopper 画布上。
   - 检查 Python 桥接服务终端是否输出错误。
   - 确认 MCP 客户端已成功连接到桥接服务。

## 项目结构

```text
grasshopper-mcp/
|-- grasshopper_mcp/       # Python 桥接服务
|   |-- __init__.py
|   `-- bridge.py          # 桥接服务主入口
|-- GH_MCP/                # Grasshopper 组件源码，C# 项目
|   `-- ...
|-- releases/              # 预编译文件
|   `-- GH_MCP.gha         # 已编译的 Grasshopper 组件
|-- setup.py               # Python 包配置
`-- README.md              # 项目说明
```


## 开发与贡献

欢迎提交 Issue 或 Pull Request 来改进该项目。二次开发时，建议分别检查 Python 桥接服务和 C# Grasshopper 插件的运行环境。

## 许可证

本项目使用 MIT License，详情见 `LICENSE` 文件。

## 致谢

- 感谢 Rhino 与 Grasshopper 社区提供优秀的建模工具生态。
- 感谢 Anthropic 推动 Claude Desktop 与 MCP 协议的发展。

## 联系与支持

如果遇到问题，请在 GitHub 仓库中提交 Issue，并附上 Rhino、Grasshopper、Python 版本，以及桥接服务终端输出。
