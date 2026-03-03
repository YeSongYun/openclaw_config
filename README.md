# OpenClaw Config

<p align="center">
  <strong>OpenClaw DMXAPI 一键配置工具</strong>
</p>

<p align="center">
  纯 Go 终端 TUI 工具，零 CGO，支持 5 平台一键交叉编译
</p>

---

## 简介

OpenClaw Config 是一个简洁易用的终端配置工具，帮助用户快速配置 OpenClaw 的 DMXAPI 设置，无需手动编辑配置文件。

### 主要功能

- 终端交互式配置 DMXAPI 的 Base URL、API Key 和模型
- 自动检测 API 格式（Claude / Gemini / GPT-5 / 其他 OpenAI 兼容模型）
- 内置多种预设模型快速选择
- 支持自定义模型名称
- 一键保存配置到 OpenClaw 配置文件
- 配置保存后自动重启网关，使配置立即生效

### 支持的预设模型

- Claude 系列：claude-opus-4-6、claude-sonnet-4-6
- MiniMax：MiniMax-M2.5-cc
- GLM：glm-5-cc
- Hunyuan：hunyuan-2.0-thinking-20251109-cc
- Kimi：kimi-k2.5-cc
- GPT-5：gpt-5.3-codex、gpt-5.2
- Gemini：gemini-3.1-pro-preview、gemini-3-flash-preview

---

## 前提条件

使用本工具前，请确保已安装 OpenClaw。本工具会修改 OpenClaw 的配置文件，并在配置完成后自动重启 openclaw 网关。

---

## 下载安装

前往 [Releases](https://cnb.cool/dmxapi/openclaw_config/-/releases) 页面下载对应平台的版本：

| 平台 | 架构 | 文件 |
|------|------|------|
| Linux | x64 | `openclaw-config-linux-amd64` |
| Linux | ARM64 | `openclaw-config-linux-arm64` |
| Windows | x64 | `openclaw-config-windows-amd64.exe` |
| macOS | Intel | `openclaw-config-macos-amd64` |
| macOS | Apple Silicon | `openclaw-config-macos-arm64` |

---

## 使用说明

### Linux

```bash
# x64
chmod +x openclaw-config-linux-amd64
./openclaw-config-linux-amd64

# ARM64
chmod +x openclaw-config-linux-arm64
./openclaw-config-linux-arm64
```

### macOS

```bash
# Intel
chmod +x openclaw-config-macos-amd64
./openclaw-config-macos-amd64

# Apple Silicon
chmod +x openclaw-config-macos-arm64
./openclaw-config-macos-arm64
```

> **注意**：首次运行时 macOS 可能提示"无法验证开发者"。解决方法：
>
> **方法一**：右键点击文件 → 选择"打开" → 在弹出对话框中再次点击"打开"
>
> **方法二**：在终端执行以下命令清除隔离标记，然后再运行：
> ```bash
> xattr -rd com.apple.quarantine ./openclaw-config-macos-arm64
> ```

### Windows

在文件所在目录按住 **Shift** 并右键，选择"在此处打开 PowerShell 窗口"，然后执行：

```powershell
.\openclaw-config-windows-amd64.exe
```

### 版本查看

```bash
./openclaw-config-<平台> --version
# 例：./openclaw-config-macos-arm64 --version
```

---

## 配置字段说明

启动工具后，按顺序填写以下字段：

| 字段 | 说明 |
|------|------|
| **Base URL** | DMXAPI 的 API 接入地址，默认为 `https://www.dmxapi.cn/v1`，通常无需修改 |
| **API Key** | 在 [dmxapi.cn](https://www.dmxapi.cn) 申请的 API 密钥，格式为 `sk-...` |
| **模型** | 从预设列表中选择，或选择"自定义模型..."后输入模型名称 |

---

## 配置完成后

配置保存后，工具会**自动执行**网关重启，使配置立即生效。

仅当工具提示重启失败时（如 openclaw 未安装或未在 PATH 中），才需要手动执行：

```bash
# 仅在工具提示重启失败时才需要手动执行：
openclaw gateway restart
```

### 常用命令

| 命令 | 说明 |
|------|------|
| `openclaw gateway restart` | 重启网关使配置生效 |
| `/model` | 在聊天中查看/切换模型 |
| `openclaw models list` | 列出所有可用模型 |
| `openclaw-cn tui` | 打开终端聊天助手 |

### 配置文件位置

配置保存在以下文件中，可手动查看或备份：

- 主配置：`~/.openclaw/openclaw.json`
- API Key：`~/.openclaw/agents/main/agent/auth-profiles.json`
- 模型列表：`~/.openclaw/agents/main/agent/models.json`

---

## 本地开发

### 环境要求

- Go 1.23+

### 克隆项目

```bash
git clone https://github.com/YeSongYun/openclaw_config.git
cd openclaw_config
```

### 开发模式（直接运行）

```bash
go run .
```

### 构建

```bash
# 构建当前平台
go build -o openclaw-config .

# 交叉编译（5 平台）
GOOS=linux   GOARCH=amd64 CGO_ENABLED=0 go build -o dist/openclaw-config-linux-amd64 .
GOOS=linux   GOARCH=arm64 CGO_ENABLED=0 go build -o dist/openclaw-config-linux-arm64 .
GOOS=windows GOARCH=amd64 CGO_ENABLED=0 go build -o dist/openclaw-config-windows-amd64.exe .
GOOS=darwin  GOARCH=amd64 CGO_ENABLED=0 go build -o dist/openclaw-config-macos-amd64 .
GOOS=darwin  GOARCH=arm64 CGO_ENABLED=0 go build -o dist/openclaw-config-macos-arm64 .
```

---

## 项目结构

```
openclaw_config/
├── app.go                    # TUI 表单逻辑
├── main.go                   # 程序入口（参数解析）
├── internal/
│   ├── config/
│   │   ├── manager.go        # 配置管理器
│   │   └── types.go          # 配置类型定义
│   └── models/
│       └── presets.go        # 预设模型列表
├── .github/workflows/        # GitHub Actions CI/CD
└── .cnb.yml                  # CNB 云原生构建配置
```

---

## CI/CD

项目配置了自动化构建和发布：

- **CNB**：推送 Tag 时自动构建 5 个平台并创建 Release
- **GitHub Actions**：推送 Tag 时自动构建 5 个平台并发布

---

## 相关链接

- [OpenClaw 项目主页](https://cnb.cool/dmxapi/openclaw_config)

---

## 作者

**yesongyun** - yesongyun@foxmail.com

---

## License

MIT
