# LiveCapsule：面向 Windows 的灵动岛桌面工具

> 一款以 Python 和 PySide6 构建的 Windows 桌面悬浮工具。它把时间、天气、系统资源、媒体控制和常用应用入口集中在一枚可展开的“灵动岛”中，并提供通知、日历、AI 助手与高精度计算器等扩展功能。

**LiveCapsule 不是 Apple 官方产品，也不隶属于 Apple。** 本项目是独立的 Windows 桌面软件，视觉和交互灵感来自灵动岛式的紧凑信息呈现。

---

## 项目亮点

- **轻量常驻的信息岛**：收起时显示时间、天气图标和可选系统资源；展开后显示更多信息、媒体控制和应用快捷入口。
- **自绘云母风格**：界面以 Qt `QPainter` 绘制，不依赖 Windows 窗口模糊 API；支持深色/浅色主题、自定义背景和动画效果。
- **围绕真实桌面使用场景设计**：全屏或最大化窗口遮挡时可自动让位；媒体、天气和系统采样在后台处理，尽量避免阻塞界面。
- **Windows 原生能力集成**：托盘菜单、开机自启、系统媒体会话、音量/亮度 HUD、全局快捷键以及桌面/开始菜单快捷方式。
- **可独立使用的高精度计算器**：C++ 数值内核配合 PySide6 界面；内核 DLL 不可用时可回退到 Python 实现。
- **可选 AI 助手**：在悬浮岛中直接与兼容的聊天 API 对话，并可按权限设置使用本地文件和命令工具。

## 功能一览

### 灵动岛与桌面交互

- **收起态**：显示时钟、天气图标，以及可选的内存、CPU、GPU、磁盘或 Wi-Fi 用量；空间不足时逐级收缩显示内容，避免控件相互覆盖。
- **展开态**：显示时间和日期、天气摘要、CPU/GPU/内存指标、媒体控制条和应用快捷键行。
- **媒体控制**：显示曲目与歌手、播放状态和可用的播放进度；提供上一首、播放/暂停、下一首及支持时的进度拖动。
- **应用快捷键**：通过设置界面挑选应用、编辑名称与启动命令、选择图标和调整顺序；变更可即时反映在岛上。
- **自动让位**：按配置在全屏或最大化窗口前收起或降低透明度，并支持鼠标移到屏幕顶部时临时唤回。
- **动画和 HUD**：支持岛体展开/收起过渡、音量与亮度状态提示，以及可配置的粒子效果。
- **托盘和快捷方式**：支持系统托盘操作、可选登录启动，以及创建桌面和开始菜单快捷方式。

### 天气与日历

- **天气数据**：使用 Open-Meteo 获取天气实况、逐小时信息和 7 天预报；无需 API Key。城市可搜索，也可选择基于 IP 的自动定位。
- **天气面板**：显示实况、体感温度、日出日落、降水、风、湿度、气压、能见度、紫外线等信息，以及可用的天气提示。
- **农历日历**：包含公历、农历、二十四节气、常见节日及已内置年份的休假/调休安排。点击时间或日期可打开日历面板。
- **网络状态处理**：天气请求在后台运行；网络请求失败时保留先前数据并显示失败状态，不将旧数据伪装成新结果。

### 通知与 AI 助手

- **通知面板**：可将 Windows 通知纳入灵动岛通知面板；是否启用通知拦截及通知相关联动由设置控制。
- **AI 对话**：可配置 API 地址和 API Key，支持流式回复、图片/文本文件附件、对话导出、自定义模型名和推理强度等选项。
- **本地工具**：AI 可按当前权限调用文件读取、写入、编辑、目录列表、glob、grep 和命令执行工具；工作区设置用于约束允许访问的路径。
- **权限选项**：包含仅查看、工作区内修改、完全权限及实验性的 Auto review 等模式。应根据使用场景选择最小权限；完全权限会放宽本地访问限制。
- **凭据保护**：Windows 上的 API Key 使用 DPAPI 加密保存，并与当前 Windows 用户及设备绑定。

> 使用 AI 功能时，消息和附件会发送到用户配置的 API 服务；本地工具则会在本机执行。请只配置可信服务，并在发送前确认提示内容和附件不含不应外传的信息。

### 高精度计算器

计算器位于 `calc/`，可从灵动岛启动，也可作为独立应用运行。它提供标准、科学、绘图、程序员、日期计算和多类单位转换模式，并包含：

- 精确大整数运算与可调有效位数的任意精度十进制运算；
- 三角、对数、指数、常数、阶乘、模幂等科学运算；
- 多曲线绘图、程序员进制/位运算和日期计算；
- 多种单位转换，以及可手动刷新的货币汇率快照；
- 重型计算的并行执行、CPU 节流、运算取消和长结果导出。

C++ 内核由 `ctypes` 桥接。仓库内提供的 `calc/bin/calc_core.dll` 可直接加载；若内核不可用，程序会提示并回退到 Python 后端。Mandel Supercompute Mode 与 550W MOSS Mode 属于面向高负载/加速器能力的特殊功能；加速硬件、驱动和运行时不同会影响可用后端与性能，界面状态不应被理解为所有设备都具备 NPU 加速。

## 技术栈

- **语言**：Python；计算器核心包含 C++。
- **桌面 UI**：PySide6 / Qt 6、自绘 `QPainter`。
- **系统指标**：`psutil`，以及 Windows 平台相关接口。
- **媒体同步**：Windows SMTC / `winsdk`（Windows 环境）；部分播放器存在接口能力差异。
- **天气**：Open-Meteo Forecast 与 Geocoding 服务；自动定位可使用 IP 地理定位服务。
- **AI**：兼容 OpenAI Chat Completions 风格的流式 API；默认配置指向 DeepSeek API，也可配置接口地址和模型名。
- **计算器**：C++ 大整数/高精度十进制内核、Python 回退实现、PySide6 界面。

## 系统要求

- Windows 桌面环境。项目调用 Windows 特有的媒体、系统和窗口能力，其他操作系统不在当前支持范围内。
- 建议使用官方 CPython 3.12 和独立虚拟环境；依赖详见 `requirements.txt`。
- 部分能力依赖网络、可用的 Windows 媒体会话或兼容硬件。AI 功能需要用户自行提供 API Key；天气功能依赖外部服务。


### AI 助手设置

首次使用时，在 AI 面板中配置 API Key 和接口地址。默认接口为 `https://api.deepseek.com`；请根据服务商要求选择正确的 API 地址、模型和访问权限。Key 使用 Windows DPAPI 保存，不要将真实密钥提交到仓库、截图或公开日志中。

## 配置与数据

用户配置保存在当前 Windows 用户的本地应用数据目录（通常为 `%LOCALAPPDATA%\DynamicIsland\`），例如快捷键清单、天气选项和 AI 配置。此目录与 Git 仓库分开；删除或重装仓库不等于删除这些配置。

网络访问概况：

| 功能 | 可能访问的服务 | 用途 |
| --- | --- | --- |
| 天气/城市搜索 | Open-Meteo | 天气预报和地理编码 |
| 自动定位 | IP 地理定位服务 | 根据公网出口 IP 推断城市 |
| 货币汇率刷新 | `open.er-api.com` | 刷新计算器中的汇率快照 |
| AI 对话 | 用户配置的 API 地址 | 发送提示、对话上下文及用户添加的附件 |

具体请求内容取决于用户启用的功能、配置的城市和提交的内容。无需使用天气、AI 或在线汇率时，可以不启用相应功能或不执行手动刷新。

## 项目结构

```text
LiveCapsule/
├── main.py                 # 灵动岛主窗口、托盘、系统指标、媒体与 HUD
├── ui.py                   # 共享主题、常量与玻璃浮窗基础组件
├── settings.py             # 设置控制台及应用快捷键编辑器
├── weather.py              # 天气数据采样、解析与天气面板
├── lunar_calendar.py       # 农历、节气与日历面板
├── ai_assistant.py         # AI 对话面板与工具循环
├── ai_credentials.py       # API 凭据与 AI 配置管理
├── ai_tools.py             # AI 本地文件/命令工具及权限控制
├── calc/                   # 高精度计算器（Python UI + C++ 核心）
├── assets/                 # 界面使用的资源
├── locales/                # 多语言资源
├── docs/                   # 接口契约、验证报告及其他项目文档
├── tests/                  # 项目测试与诊断
├── tools/                  # 工具链准备脚本等
├── requirements.txt        # Python 依赖
└── run.bat                 # Windows 启动脚本
```



## 已知限制

- 本项目依赖 Windows API 和 Windows 桌面交互，不是跨平台桌面应用。
- 媒体信息和进度取决于播放器是否公开 SMTC 会话与进度；部分播放器可能只能提供曲目/播放状态或有限控制能力。
- 天气与城市查询依赖网络；自动定位根据公网 IP 推断，准确度受网络出口影响。
- 内置节假日安排只覆盖仓库说明中列出的年份，其他年份可能使用周末和法定节日的回退逻辑；货币汇率在离线时使用现有快照。
- 计算器特殊加速模式能否使用 NPU/GPU 取决于硬件、驱动及运行时；缺少相应设备时会退回其他后端或不可用。
- AI 工具可能读取、修改工作区文件或运行命令。权限模式和工作区限制应谨慎配置，尤其不要对不可信提示启用完全权限。
- 当前仓库未发现 LICENSE 文件；在发布前请作者为项目选择并添加合适的开源许可证。没有许可证时，公开仓库并不自动授予他人复用、修改或分发代码的许可。

## English

# LiveCapsule: A Dynamic-Island-Style Desktop Tool for Windows

> A Windows desktop floating tool built with Python and PySide6. It gathers the clock, weather, system resources, media controls, and shortcuts to frequently used apps into a single expandable "Dynamic Island," with extended features including notifications, a calendar, an AI assistant, and a high-precision calculator.

**LiveCapsule is not an official Apple product and is not affiliated with Apple.** This project is an independent Windows desktop application whose visual style and interactions are inspired by the compact, Dynamic-Island-style presentation of information.

---

## Project Highlights

- **A lightweight, always-on information island**: When collapsed, it shows the time, a weather icon, and optional system resource readings; when expanded, it reveals more information, media controls, and app shortcuts.
- **Hand-drawn Mica-style visuals**: The interface is painted with Qt `QPainter` and does not rely on the Windows window blur API; it supports dark/light themes, custom backgrounds, and animations.
- **Designed around real desktop usage**: It automatically yields when a full-screen or maximized window would obscure it; media, weather, and system sampling run in the background to avoid blocking the UI.
- **Windows-native capability integration**: Tray menu, launch at login, system media sessions, volume/brightness HUD, global hotkeys, and desktop/Start menu shortcuts.
- **A standalone high-precision calculator**: A C++ numerical core paired with a PySide6 interface; falls back to a Python implementation when the core DLL is unavailable.
- **An optional AI assistant**: Chat directly with a compatible chat API from within the floating island, and use local file and command tools according to permission settings.

## Feature Overview

### The Dynamic Island and Desktop Interaction

- **Collapsed state**: Shows the clock, a weather icon, and optional memory, CPU, GPU, disk, or Wi-Fi usage; as space runs out, displayed content shrinks progressively to keep widgets from overlapping.
- **Expanded state**: Shows the time and date, a weather summary, CPU/GPU/memory metrics, a media control bar, and a row of app shortcuts.
- **Media controls**: Displays track and artist, playback state, and available playback progress; provides previous, play/pause, and next buttons, plus progress seeking where supported.
- **App shortcuts**: Pick apps, edit names and launch commands, choose icons, and adjust ordering through the settings UI; changes are reflected on the island immediately.
- **Automatic yielding**: Collapses or lowers opacity in front of full-screen or maximized windows according to configuration, and supports temporarily summoning it back when the mouse moves to the top of the screen.
- **Animations and HUD**: Supports island expand/collapse transitions, volume and brightness status indicators, and configurable particle effects.
- **Tray and shortcuts**: Supports system tray operations, optional launch at login, and creation of desktop and Start menu shortcuts.

### Weather and Calendar

- **Weather data**: Uses Open-Meteo for current conditions, hourly information, and a 7-day forecast; no API key required. Cities can be searched, or IP-based automatic location can be selected.
- **Weather panel**: Displays current conditions, feels-like temperature, sunrise and sunset, precipitation, wind, humidity, pressure, visibility, UV index, and more, along with available weather tips.
- **Lunar calendar**: Includes the Gregorian calendar, the lunar calendar, the 24 solar terms, common holidays, and built-in holiday/make-up workday arrangements for the years covered. Clicking the time or date opens the calendar panel.
- **Network state handling**: Weather requests run in the background; when a request fails, previously fetched data is retained and a failure state is shown, rather than passing off stale data as a fresh result.

### Notifications and the AI Assistant

- **Notification panel**: Windows notifications can be brought into the Dynamic Island notification panel; whether notification interception and related notification integrations are enabled is controlled by settings.
- **AI chat**: Configurable API endpoint and API key, with support for streaming replies, image/text file attachments, conversation export, custom model names, and reasoning effort options.
- **Local tools**: Depending on current permissions, the AI can call tools for file reading, writing, editing, directory listing, glob, grep, and command execution; workspace settings constrain the paths that may be accessed.
- **Permission options**: Includes view-only, modify-within-workspace, full permissions, and an experimental Auto review mode, among others. Choose the least privilege appropriate to your use case; full permissions loosen local access restrictions.
- **Credential protection**: On Windows, API keys are stored encrypted with DPAPI and are bound to the current Windows user and device.

> When using AI features, messages and attachments are sent to the API service configured by the user; local tools, in contrast, execute on the local machine. Only configure trusted services, and before sending, confirm that the prompt content and attachments do not contain information that should not be shared externally.

### High-Precision Calculator

The calculator lives in `calc/` and can be launched from the Dynamic Island or run as a standalone application. It offers standard, scientific, graphing, programmer, date calculation, and multiple unit-conversion modes, and includes:

- Exact large-integer arithmetic and arbitrary-precision decimal arithmetic with adjustable significant digits;
- Scientific operations including trigonometric, logarithmic, exponential, constants, factorial, and modular exponentiation;
- Multi-curve plotting, programmer radix/bitwise operations, and date calculations;
- Multiple unit conversions, plus a currency exchange-rate snapshot that can be refreshed manually;
- Parallel execution of heavy computations, CPU throttling, operation cancellation, and export of long results.

The C++ core is bridged via `ctypes`. The `calc/bin/calc_core.dll` provided in the repository can be loaded directly; if the core is unavailable, the program notifies the user and falls back to the Python backend. Mandel Supercompute Mode and 550W MOSS Mode are special features aimed at high-load/accelerator capabilities; differences in acceleration hardware, drivers, and runtimes affect which backends are available and their performance, and the interface state should not be interpreted as meaning that all devices have NPU acceleration.

## Tech Stack

- **Languages**: Python; the calculator core includes C++.
- **Desktop UI**: PySide6 / Qt 6, hand-drawn with `QPainter`.
- **System metrics**: `psutil`, plus Windows platform-specific interfaces.
- **Media sync**: Windows SMTC / `winsdk` (Windows environment); some players differ in interface capabilities.
- **Weather**: Open-Meteo Forecast and Geocoding services; automatic location may use an IP geolocation service.
- **AI**: OpenAI Chat Completions-style streaming APIs; the default configuration points to the DeepSeek API, and the endpoint and model name can also be configured.
- **Calculator**: A C++ large-integer/high-precision decimal core, a Python fallback implementation, and a PySide6 interface.

## System Requirements

- A Windows desktop environment. The project calls Windows-specific media, system, and window capabilities; other operating systems are outside the current support scope.
- Official CPython 3.12 with an isolated virtual environment is recommended; see `requirements.txt` for dependencies.
- Some capabilities depend on the network, an available Windows media session, or compatible hardware. AI features require the user to provide their own API key; weather features depend on external services.


### AI Assistant Setup

On first use, configure the API key and endpoint in the AI panel. The default endpoint is `https://api.deepseek.com`; choose the correct API address, model, and access permissions according to your provider's requirements. The key is stored using Windows DPAPI — do not commit real keys to the repository, screenshots, or public logs.

## Configuration and Data

User configuration is stored in the local application data directory of the current Windows user (typically `%LOCALAPPDATA%\LiveCapsule\`), such as the shortcut list, weather options, and AI configuration. This directory is separate from the Git repository; deleting or reinstalling the repository does not delete these settings.

Network access overview:

| Feature | Services that may be accessed | Purpose |
| --- | --- | --- |
| Weather / city search | Open-Meteo | Weather forecasts and geocoding |
| Automatic location | IP geolocation service | Inferring the city from the public egress IP |
| Currency rate refresh | `open.er-api.com` | Refreshing the exchange-rate snapshot in the calculator |
| AI chat | The API endpoint configured by the user | Sending prompts, conversation context, and user-added attachments |

The specific request content depends on the features the user enables, the configured city, and the content submitted. If weather, AI, or online exchange rates are not needed, the corresponding features can be left disabled or the manual refresh simply not performed.

## Project Structure

```text
LiveCapsule/
├── main.py                 # Main island window, tray, system metrics, media, and HUD
├── ui.py                   # Shared themes, constants, and glass floating-window base components
├── settings.py             # Settings console and app shortcut editor
├── weather.py              # Weather data sampling, parsing, and weather panel
├── lunar_calendar.py       # Lunar calendar, solar terms, and calendar panel
├── ai_assistant.py         # AI chat panel and tool loop
├── ai_credentials.py       # API credentials and AI configuration management
├── ai_tools.py             # AI local file/command tools and permission control
├── calc/                   # High-precision calculator (Python UI + C++ core)
├── assets/                 # Resources used by the interface
├── locales/                # Localization resources
├── docs/                   # Interface contracts, verification reports, and other project docs
├── tests/                  # Project tests and diagnostics
├── tools/                  # Toolchain preparation scripts and the like
├── requirements.txt        # Python dependencies
└── run.bat                 # Windows launch script
```



## Known Limitations

- This project depends on Windows APIs and Windows desktop interaction; it is not a cross-platform desktop application.
- Media information and progress depend on whether the player exposes an SMTC session and progress; some players may only provide track/playback state or limited control capability.
- Weather and city lookups depend on the network; automatic location is inferred from the public IP, so accuracy is affected by the network egress.
- The built-in holiday arrangements cover only the years listed in the repository documentation; for other years, a fallback based on weekends and statutory holidays may be used. Currency exchange rates use the existing snapshot when offline.
- Whether the calculator's special acceleration modes can use an NPU/GPU depends on hardware, drivers, and runtime; when the corresponding device is absent, it falls back to other backends or becomes unavailable.
- AI tools may read or modify workspace files or run commands. Permission modes and workspace restrictions should be configured carefully — in particular, do not enable full permissions for untrusted prompts.
- No LICENSE file was found in the current repository; before publishing, the author should choose and add an appropriate open-source license. Without a license, a public repository does not automatically grant others permission to reuse, modify, or distribute the code.

## License

**Not yet declared.**

## Acknowledgements and Data Sources

- Weather forecasts and city geocoding: [Open-Meteo](https://open-meteo.com/)
- Qt desktop interface: [Qt for Python / PySide6](https://doc.qt.io/qtforpython-6/)
- System media controls are based on the media session capabilities provided by Windows.

- 天气预报和城市地理编码： [Open-Meteo](https://open-meteo.com/)
- Qt 桌面界面： [Qt for Python / PySide6](https://doc.qt.io/qtforpython-6/)
- 系统媒体控制基于 Windows 提供的媒体会话能力。
