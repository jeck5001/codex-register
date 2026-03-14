# OpenAI/Codex CLI 自动注册系统

一个功能完整的 OpenAI/Codex CLI 自动注册系统，支持多种邮箱服务、Web UI 管理界面和命令行操作。

## 功能特性

### 核心功能
- **自动注册流程** - 完整的 OpenAI 账号自动注册，支持 OAuth 2.0 + PKCE 认证
- **多种邮箱服务** - 支持 Tempmail.lol、Outlook IMAP、自定义域名邮箱
- **Web UI 界面** - 现代化的 Web 管理界面，包含控制台日志输出
- **账号管理** - 批量删除、导出（JSON/CSV）、刷新 Token、查看详情
- **后台任务** - 异步执行注册任务，实时查看任务状态和日志
- **SQLite 存储** - 轻量级数据库存储账号和配置信息

### 邮箱服务
| 服务类型 | 描述 | 适用场景 |
|---------|------|---------|
| Tempmail.lol | 临时邮箱服务 | 快速测试、一次性注册 |
| Outlook IMAP | Outlook 邮箱 IMAP 接收 | 长期使用的邮箱 |
| 自定义域名 | 支持自定义域名邮箱 API | 企业级邮箱服务 |

### Web UI 页面
- **注册页面** - 嵌入式控制台，实时显示注册日志
- **账号管理** - 表格展示、批量操作、搜索过滤
- **系统设置** - 代理配置、邮箱服务管理、注册参数、数据库管理

## 安装

### 环境要求
- Python >= 3.10
- pip 或 uv 包管理器

### 安装步骤

```bash
# 克隆项目
git clone <repository-url>
cd codex-register-v2

# 创建虚拟环境
python -m venv .venv

# 激活虚拟环境
# Windows
.venv\Scripts\activate
# Linux/macOS
source .venv/bin/activate

# 安装依赖
pip install -e .

# 或使用 uv（推荐）
uv sync
```

## 使用方法

### 方式一：Web UI（推荐）

```bash
# 启动 Web UI
python webui.py

# 或指定参数
python webui.py --host 0.0.0.0 --port 8000 --debug

# 使用已安装的命令
codex-webui
```

访问 `http://localhost:8000` 即可使用 Web 界面。

### 方式二：命令行

```bash
# 单次注册
python cli.py --once

# 循环注册
python cli.py --sleep-min 5 --sleep-max 30

# 使用代理
python cli.py --proxy http://127.0.0.1:7890

# 使用已安装的命令
codex-register --once
```

### 命令行参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--proxy` | 代理地址 | 无 |
| `--once` | 只运行一次 | False |
| `--sleep-min` | 循环模式最短等待秒数 | 5 |
| `--sleep-max` | 循环模式最长等待秒数 | 30 |
| `--log-level` | 日志级别 | INFO |
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
WEBUI_SECRET_KEY=your-secret-key

# 代理配置
PROXY_ENABLED=false
PROXY_TYPE=http
PROXY_HOST=127.0.0.1
PROXY_PORT=7890

# OpenAI OAuth
OPENAI_CLIENT_ID=app_EMoamEEZ73f0CkXaXp7hrann

# Tempmail 配置
TEMPMAIL_BASE_URL=https://api.tempmail.lol/v2
TEMPMAIL_TIMEOUT=30

# 自定义域名邮箱
CUSTOM_DOMAIN_BASE_URL=https://mail.example.com/api
CUSTOM_DOMAIN_API_KEY=your-api-key
```

### 主要配置项

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `database_url` | 数据库连接 URL | `data/database.db` |
| `webui_host` | Web UI 监听地址 | `0.0.0.0` |
| `webui_port` | Web UI 监听端口 | `8000` |
| `proxy_enabled` | 是否启用代理 | `false` |
| `proxy_url` | 代理地址 | - |
| `registration_max_retries` | 注册最大重试次数 | `3` |
| `registration_timeout` | 注册超时时间（秒） | `120` |

## 项目结构

```
codex-register-v2/
├── cli.py                 # 命令行入口
├── webui.py               # Web UI 入口
├── pyproject.toml         # 项目配置
├── requirements.txt       # 依赖列表
├── src/
│   ├── __init__.py
│   ├── config/            # 配置模块
│   │   ├── settings.py    # Pydantic 设置模型
│   │   └── constants.py   # 常量定义
│   ├── core/              # 核心功能
│   │   ├── oauth.py       # OAuth 认证
│   │   ├── register.py    # 注册引擎
│   │   ├── http_client.py # HTTP 客户端
│   │   └── utils.py       # 工具函数
│   ├── database/          # 数据库模块
│   │   ├── models.py      # 数据模型
│   │   ├── crud.py        # CRUD 操作
│   │   ├── session.py     # 会话管理
│   │   └── init_db.py     # 数据库初始化
│   ├── services/          # 邮箱服务
│   │   ├── base.py        # 基类和工厂
│   │   ├── tempmail.py    # Tempmail.lol 服务
│   │   ├── outlook.py     # Outlook IMAP 服务
│   │   └── custom_domain.py # 自定义域名服务
│   └── web/               # Web 模块
│       ├── app.py         # FastAPI 应用
│       └── routes/        # API 路由
│           ├── accounts.py    # 账号管理
│           ├── registration.py # 注册任务
│           ├── settings.py    # 系统设置
│           └── email_services.py # 邮箱服务
├── templates/             # Jinja2 模板
│   ├── index.html         # 注册页面
│   ├── accounts.html      # 账号管理
│   └── settings.html      # 系统设置
├── static/                # 静态文件
│   ├── css/style.css
│   └── js/
│       ├── app.js
│       ├── accounts.js
│       └── settings.js
└── data/                  # 数据目录
    └── database.db        # SQLite 数据库
```

## API 文档

### 账号管理 API

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/accounts` | 获取账号列表 |
| GET | `/api/accounts/{id}` | 获取单个账号 |
| DELETE | `/api/accounts/{id}` | 删除账号 |
| POST | `/api/accounts/batch-delete` | 批量删除 |
| GET | `/api/accounts/export` | 导出账号 |
| POST | `/api/accounts/{id}/refresh` | 刷新 Token |

### 注册任务 API

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/registration/start` | 开始注册 |
| GET | `/api/registration/status/{uuid}` | 任务状态 |
| GET | `/api/registration/logs/{uuid}` | 任务日志 |
| DELETE | `/api/registration/cancel/{uuid}` | 取消任务 |

### 设置 API

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/settings` | 获取设置 |
| PUT | `/api/settings` | 更新设置 |
| GET | `/api/settings/email-services` | 邮箱服务列表 |
| POST | `/api/settings/email-services` | 创建邮箱服务 |

## 开发

### 运行测试

```bash
# 安装开发依赖
pip install -e ".[dev]"

# 运行测试
pytest
```

### 代码风格

项目使用 Python 3.10+ 特性，包括类型注解、dataclass、Pydantic 模型等。

### 添加新的邮箱服务

1. 继承 `BaseEmailService` 类
2. 实现必需的方法：`get_email()`, `wait_for_code()`, `get_status()`
3. 在 `EmailServiceFactory` 中注册服务

```python
from src.services import BaseEmailService, EmailServiceFactory, EmailServiceType

class MyEmailService(BaseEmailService):
    async def get_email(self) -> str:
        # 实现获取邮箱地址
        pass

    async def wait_for_code(self, email: str, timeout: int = 300) -> str:
        # 实现等待验证码
        pass

# 注册服务
EmailServiceFactory.register(EmailServiceType.CUSTOM, MyEmailService)
```

## 技术栈

- **Web 框架**: FastAPI + Uvicorn
- **模板引擎**: Jinja2
- **数据库**: SQLAlchemy + SQLite
- **HTTP 客户端**: curl_cffi（支持浏览器指纹模拟）
- **数据验证**: Pydantic
- **认证**: OAuth 2.0 + PKCE

## 许可证

MIT License

## 作者

Yasal

---

**注意**: 本工具仅供学习和研究目的，请勿用于违反 OpenAI 服务条款的活动。
