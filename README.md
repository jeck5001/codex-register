# OpenAI/Codex CLI 自动注册系统

一个功能完整的 OpenAI/Codex CLI 自动注册系统，支持多种邮箱服务、Web UI 管理界面和命令行操作。

## 功能特性

### 核心功能
- **自动注册流程** - 完整的 OpenAI 账号自动注册，支持 OAuth 2.0 + PKCE 认证
- **多种邮箱服务** - 支持 Tempmail.lol、Outlook（IMAP/Graph API）、自定义域名邮箱
- **Web UI 界面** - 现代化的 Web 管理界面，包含实时控制台日志输出
- **账号管理** - 批量删除、导出（JSON/CSV/CPA格式）、刷新 Token、验证 Token
- **后台任务** - 异步执行注册任务，实时查看任务状态和日志
- **代理管理** - 支持代理列表管理，注册时自动轮换代理
- **CPA 上传** - 支持将账号上传到 CPA (Codex Protocol API) 管理平台
- **SQLite 存储** - 轻量级数据库存储账号和配置信息

### 邮箱服务
| 服务类型 | 描述 | 适用场景 |
|---------|------|---------|
| Tempmail.lol | 临时邮箱服务 | 快速测试、一次性注册 |
| Outlook | Outlook 邮箱（IMAP / Graph API） | 长期使用，支持批量导入 |
| 自定义域名 | 支持自定义域名邮箱 API | 企业级邮箱服务 |

### Web UI 页面
- **注册页面** - 支持单次/批量注册，嵌入式控制台实时显示日志
- **Outlook 批量注册** - 选择 Outlook 账号批量注册，自动跳过已注册邮箱
- **账号管理** - 表格展示、批量操作、搜索过滤、Token 刷新/验证、CPA 上传
- **系统设置** - 代理配置、邮箱服务管理、CPA 平台配置

## 安装

### 方式一：源码运行

#### 环境要求
- Python >= 3.10
- pip 或 uv 包管理器

```bash
# 克隆项目
git clone <repository-url>
cd codex-register-v2

# 创建虚拟环境（推荐）
python -m venv .venv

# 激活虚拟环境
# Windows
.venv\Scripts\activate
# Linux/macOS
source .venv/bin/activate

# 安装依赖
pip install -r requirements.txt

# 或使用 uv（推荐）
uv sync
```

### 方式二：打包可执行文件（Windows）

```bash
# 安装打包依赖
pip install pyinstaller

# 执行打包
build.bat
```

打包完成后在 `dist/` 目录生成 `codex-register-windows-<架构>.exe`，可直接分发运行，无需安装 Python 环境。

## 使用方法

### Web UI 模式（推荐）

**源码运行：**
```bash
# 默认启动（0.0.0.0:8000）
python webui.py

# 指定主机和端口
python webui.py --host 127.0.0.1 --port 9000

# 启用调试模式
python webui.py --debug
```

**打包可执行文件：**
```bash
# 默认启动
codex-register.exe

# 指定端口
codex-register.exe --port 9000

# 指定主机和端口
codex-register.exe --host 0.0.0.0 --port 9000
```

访问 `http://localhost:8000` 即可使用 Web 界面。

#### Web UI 参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--host` | 监听主机 | `0.0.0.0` |
| `--port` | 监听端口 | `8000` |
| `--debug` | 启用调试模式 | `false` |
| `--reload` | 启用热重载 | `false` |
| `--log-level` | 日志级别 | `INFO` |

### 命令行模式

```bash
# 单次注册
python cli.py --once

# 循环注册
python cli.py --sleep-min 5 --sleep-max 30

# 使用代理
python cli.py --proxy http://127.0.0.1:7890 --once
```

#### CLI 参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--proxy` | 代理地址 | 无 |
| `--once` | 只运行一次 | `false` |
| `--sleep-min` | 循环模式最短等待秒数 | `5` |
| `--sleep-max` | 循环模式最长等待秒数 | `30` |
| `--log-level` | 日志级别 | `INFO` |
| `--log-file` | 日志文件路径 | 无 |

## 配置

### 配置方式

配置优先级：环境变量 > `.env` 文件 > 默认值

创建 `.env` 文件：

```env
# 应用配置
DEBUG=false
LOG_LEVEL=INFO

# 数据库
DATABASE_URL=sqlite:///data/database.db

# Web UI
WEBUI_HOST=0.0.0.0
WEBUI_PORT=8000

# 代理配置
PROXY_ENABLED=false
PROXY_URL=http://127.0.0.1:7890

# Tempmail 配置
TEMPMAIL_BASE_URL=https://api.tempmail.lol/v2
TEMPMAIL_TIMEOUT=30
TEMPMAIL_MAX_RETRIES=3

# 自定义域名邮箱
CUSTOM_DOMAIN_BASE_URL=https://mail.example.com/api
CUSTOM_DOMAIN_API_KEY=your-api-key

# CPA 平台
CPA_ENABLED=false
CPA_API_URL=https://cpa.example.com/api
CPA_API_TOKEN=your-token
```

### 主要配置项

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `webui_host` | Web UI 监听地址 | `0.0.0.0` |
| `webui_port` | Web UI 监听端口 | `8000` |
| `proxy_enabled` | 是否启用代理 | `false` |
| `proxy_url` | 全局代理地址 | - |
| `registration_max_retries` | 注册最大重试次数 | `3` |
| `registration_timeout` | 注册超时时间（秒） | `120` |
| `cpa_enabled` | 是否启用 CPA 上传 | `false` |
| `cpa_api_url` | CPA API 地址 | - |
| `cpa_api_token` | CPA API Token | - |

## 项目结构

```
codex-register-v2/
├── cli.py                 # 命令行入口
├── webui.py               # Web UI 入口
├── codex_register.spec    # PyInstaller 打包配置
├── build.bat              # Windows 打包脚本
├── pyproject.toml         # 项目配置
├── requirements.txt       # 依赖列表
├── src/
│   ├── config/            # 配置模块
│   │   ├── settings.py    # Pydantic 设置模型
│   │   └── constants.py   # 常量定义（含随机用户信息生成）
│   ├── core/              # 核心功能
│   │   ├── oauth.py       # OAuth 认证
│   │   ├── register.py    # 注册引擎
│   │   ├── http_client.py # HTTP 客户端（curl_cffi）
│   │   ├── cpa_upload.py  # CPA 上传
│   │   ├── token_refresh.py # Token 刷新
│   │   └── utils.py       # 工具函数
│   ├── database/          # 数据库模块
│   │   ├── models.py      # 数据模型
│   │   ├── crud.py        # CRUD 操作
│   │   ├── session.py     # 会话管理（含自动迁移）
│   │   └── init_db.py     # 数据库初始化
│   ├── services/          # 邮箱服务
│   │   ├── base.py        # 基类和工厂
│   │   ├── tempmail.py    # Tempmail.lol 服务
│   │   ├── custom_domain.py # 自定义域名服务
│   │   └── outlook/       # Outlook 服务（多 Provider）
│   │       ├── service.py
│   │       ├── providers/
│   │       │   ├── graph_api.py  # Graph API Provider
│   │       │   ├── imap_new.py   # 新版 IMAP
│   │       │   └── imap_old.py   # 旧版 IMAP
│   │       └── ...
│   └── web/               # Web 模块
│       ├── app.py         # FastAPI 应用
│       ├── task_manager.py # 任务管理器
│       └── routes/        # API 路由
│           ├── accounts.py       # 账号管理
│           ├── registration.py   # 注册任务
│           ├── settings.py       # 系统设置
│           ├── email_services.py # 邮箱服务
│           └── websocket.py      # WebSocket
├── templates/             # Jinja2 模板
└── static/                # 静态文件
    ├── css/
    └── js/
```

## API 文档

### 账号管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/accounts` | 获取账号列表 |
| GET | `/api/accounts/{id}` | 获取单个账号 |
| DELETE | `/api/accounts/{id}` | 删除账号 |
| POST | `/api/accounts/batch-delete` | 批量删除 |
| POST | `/api/accounts/export/json` | 导出为 JSON |
| POST | `/api/accounts/export/csv` | 导出为 CSV |
| POST | `/api/accounts/export/cpa` | 导出为 CPA Token 格式 |
| POST | `/api/accounts/{id}/refresh` | 刷新 Token |
| POST | `/api/accounts/{id}/validate` | 验证 Token |
| POST | `/api/accounts/batch-refresh` | 批量刷新 Token |
| POST | `/api/accounts/batch-validate` | 批量验证 Token |
| POST | `/api/accounts/{id}/upload-cpa` | 上传到 CPA |
| POST | `/api/accounts/batch-upload-cpa` | 批量上传到 CPA |

### 注册任务

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/registration/start` | 启动单次注册 |
| POST | `/api/registration/batch` | 启动批量注册 |
| GET | `/api/registration/tasks` | 任务列表 |
| GET | `/api/registration/tasks/{uuid}` | 任务详情 |
| GET | `/api/registration/tasks/{uuid}/logs` | 任务日志 |
| POST | `/api/registration/tasks/{uuid}/cancel` | 取消任务 |
| DELETE | `/api/registration/tasks/{uuid}` | 删除任务 |
| GET | `/api/registration/stats` | 注册统计 |
| GET | `/api/registration/outlook-accounts` | Outlook 账号列表（含注册状态） |
| POST | `/api/registration/outlook-batch` | 启动 Outlook 批量注册 |
| GET | `/api/registration/outlook-batch/{id}` | Outlook 批量任务状态 |
| POST | `/api/registration/outlook-batch/{id}/cancel` | 取消 Outlook 批量任务 |

### 邮箱服务

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/email-services` | 服务列表（支持 `service_type` 筛选） |
| POST | `/api/email-services` | 添加服务 |
| GET | `/api/email-services/{id}` | 获取服务详情 |
| PATCH | `/api/email-services/{id}` | 更新服务 |
| DELETE | `/api/email-services/{id}` | 删除服务 |
| POST | `/api/email-services/{id}/test` | 测试服务 |
| POST | `/api/email-services/{id}/enable` | 启用服务 |
| POST | `/api/email-services/{id}/disable` | 禁用服务 |
| POST | `/api/email-services/outlook/batch-import` | 批量导入 Outlook 账号 |
| DELETE | `/api/email-services/outlook/batch` | 批量删除 Outlook |
| GET | `/api/email-services/stats` | 服务统计 |

### 系统设置

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/settings` | 获取所有设置 |
| POST | `/api/settings/proxy` | 更新代理设置 |
| GET | `/api/settings/tempmail` | 获取临时邮箱设置 |
| POST | `/api/settings/tempmail` | 更新临时邮箱设置 |
| GET | `/api/settings/cpa` | 获取 CPA 设置 |
| POST | `/api/settings/cpa` | 更新 CPA 设置 |
| POST | `/api/settings/cpa/test` | 测试 CPA 连接 |
| GET | `/api/settings/database` | 数据库信息 |

## 技术栈

- **Web 框架**: FastAPI + Uvicorn
- **模板引擎**: Jinja2
- **数据库**: SQLAlchemy + SQLite（含自动迁移）
- **HTTP 客户端**: curl_cffi（支持浏览器指纹模拟）
- **数据验证**: Pydantic v2
- **认证**: OAuth 2.0 + PKCE
- **打包**: PyInstaller（单文件 exe）

## 许可证

MIT License

## 作者

Yasal

---

**注意**: 本工具仅供学习和研究目的，请勿用于违反 OpenAI 服务条款的活动。
