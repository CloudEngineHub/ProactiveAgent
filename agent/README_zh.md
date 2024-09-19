<div align = "center">
    <h1> 🤖 主动智能体 </h1>
</div>

# 总览

主动智能体是一个监听用户行为和外部环境的智能体，它将尽可能做到主动和帮助用户。

当前的展示将会监听用户的键鼠事件，用户的浏览器和 `vscode` 并通过弹出桌面通知帮助用户。

## 数据处理
在我们的展示中，我们采用 ActivityWatcher 来监听用户的行为和环境。技术上而言，
- 通过调用 `pynput` 库，智能体将能够获取你的键鼠事件，这些收集的数据将会视为你的行为，该脚本于 `ActionListener` 中实现。我们也通过 `paperclip` 库来确保最终结果将会被写入你的粘贴板。
- 通过使用浏览器插件，智能体将能够获知你的浏览器活动，包括标签个数，你正在浏览的原 HTML 代码等内容。
- 通过使用 vscode 插件，智能体将能够观察到你的工作空间，这包括当前项目名称，编程语言和当前文件名等。

对于用户行为，我们将拼接键鼠事件以获得键盘输入与鼠标的移动，点击等行为。

对于环境信息，
- 通过检查 `afk` 桶，智能体能够了解用户是忙碌或是空闲。
- 通过检查 `windows` 桶，智能体能够知道用户的聚焦处，我们的展示将会忽略除了浏览器和 vscode 之外的其他软件，并主要在这两个软件上提供帮助。
- 通过检查 `vscode` 桶和 `web` 桶，智能体将会知道你的工作内容和重心，并提供更合适的结果。

对于每段时期，`ActionListener` 将会从四个桶中获得信息，并且筛去用户重心不在浏览器或 vscode 上的时间。选择最后一个有效时刻的必要信息，将整合后的事件发送给智能体，而智能体将依赖此向用户提供主动回应。

## 运行主动智能体
为了完整体验主动智能体，你需要先下载额外依赖。

0. 下载依赖。查询 [此处](../README.md#install-activity-watcher) 以获得详细的下载 ActivityWatcher 插件的指导。

1. 为智能体安装依赖.
  ```bash
  pip install -r requirements.txt
  ```
  对于 Mac 用户，使用
  ```bash
  pip install -r requirements_mac.txt
  ```

2. 配置必要信息
  配置信息包括大语言模型的 api_key, 以及对于 ActivityWatcher 的必要设置，要编辑并被我们的脚本读入，你需要 **复制** 模板文件 `./gym/example_config.toml`, **重命名**为 `./gym/private.toml` 并 **编辑** 其中与 LLM 调用有关的参数。

3. 运行服务器。
   运行指令
   ```bash
    python main.py
    ```
    如果成功，控制台将输出信息。
    - 对于 windows 用户，该服务器将会为我们的智能体注册一个 AUMID, 这对于我们的桌面通知是必须的。脚本将会创建一个 `appid.txt` 文件，其中包含我们的 AUMID. **请勿删除此文件，除非你想要生成一个新的 AUMID**。

4. 保留之前的控制台，打开新的控制台并运行指令
   ```bash
    python ragent.py --web [the web bucket name] --apps [the chromes you want to monitor]
    # python ragent.py --web aw-watcher-web-edge --apps explorer.exe,msedge.exe
    ```
    以下一些参数你值得留意：
    - `--web`: 这是 ActivityWatcher 的 Web 桶名称，它可能会根据你使用的浏览器类型不同而改变。
    - `--apps`: ActivityWatcher 所显示的浏览器名。由于操作平台和浏览器的不同，app 名称也会不同，我们需要你去通过 ActivityWatcher 的 window 桶找到 app 名称，并且通过半角符号 `,` 分割并传入。
    - `--interval`: 智能体尝试提供帮助的频率. 默认为 15，单位为秒。
    - `--host`: 我们的智能体会尝试使用从 ActivityWatcher 客户端处获取的主机名作为默认主机名，如果这不是你的主机名，则在这里传入。
      一个主机名是默认桶的后缀，如 `aw-watcher-windows_<client_hostname>`。
    - `--activity_port`: ActivityWatcher所持有和监听的端口，默认为 `5600`, 除非你修改了 ActivityWatcher 的端口，否则无需传入内容。
    如果智能体运行成功，控制台将会展示配置信息以及信息 `Demo running Started.`

在这之后，你就可以打开你的浏览器 和/或 vscode， 我们的智能体将会开始工作。