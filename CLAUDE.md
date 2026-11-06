# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库当前状态

**这是一个只有骨架的工程，业务代码尚未实现。** 唯一的 commit（`9e298d8 chore: 工程骨架 + lint 基线`）只创建了目录占位和两套 lint 配置，绝大多数文件是 0 字节空文件：

- 空文件占位：`README.md`、`docker-compose.yml`、`backend/requirements.txt`、`frontend/index.html`、`backend/app/main.py`、`backend/app/config.py`
- 空目录：`dify/docker/`
- `backend/app/` 下没有 `__init__.py`，仓库没有 `.gitignore`
- 唯一有实际内容的文件是 [.eslintrc.json](.eslintrc.json) 和 [backend/pyproject.toml](backend/pyproject.toml)

因此**不要试图从这里推断既有架构或复用已有实现——它们不存在**。项目名 `zhishi-qa` 加上 `dify/` 目录指向一个基于 Dify 的知识问答应用，但具体需求要问用户。

## 预期架构（尚未落地）

目录布局表达的是各层职责，不是已实现的代码：

| 位置 | 角色 |
|---|---|
| `backend/app/main.py` | Python API 服务入口（FastAPI 风格的常见布局，但 `requirements.txt` 为空，框架尚未选定） |
| `backend/app/config.py` | 配置加载 |
| `frontend/index.html` | 浏览器端客户端 |
| `dify/docker/` | Dify（开源 LLM 应用平台）的自建部署文件（Dify 官方仓库的 compose 也在 `docker/` 下） |
| 根 `docker-compose.yml` | 编排 backend / frontend / Dify 及其依赖（Postgres、Redis、向量库等） |

`backend/` + `frontend/` + `dify/` 三段式说明这是前后端分离、后端再对接 Dify 作为 LLM 应用层的结构，而不是端到端单体内的。

## 命令

以下命令均在 Windows 10 + Git Bash 下实测通过。

```bash
# 后端 lint（配置在 backend/pyproject.toml）
cd backend && uvx ruff check .
cd backend && uvx ruff format --check .

# 后端类型检查
cd backend && uvx mypy app

# 前端 lint —— 当前无法运行，见「已知缺口」第 1 条
npx eslint frontend/
```

**目前没有 build / test / run 命令**：仓库没有 `package.json`、没有测试框架、`requirements.txt` 是空的、也没有 CI 配置。前端只有一个 `index.html` 占位，后端没有可启动的服务。实现功能时按所选技术栈补上 `package.json`（含 `lint` / `test` 脚本）与依赖清单，再把上面的 `uvx` / `npx` 调用换成项目脚本。

## 代码规范基线

这是仓库中仅存的真实约定，改代码必须遵守。

**Python（ruff + mypy，`backend/pyproject.toml`）**
- `line-length = 100`，`target-version = "py310"`（目标 Python 3.10）
- ruff 规则集 `E, F, I, N, UP`（pycodestyle / pyflakes / isort / pep8-naming / pyupgrade），忽略 `E501`
- `I` 表示 import 必须符合 isort 分组顺序；`UP` 表示要用 3.10+ 的现代语法
- mypy：`disallow_untyped_defs = true`（每个函数都要有完整类型标注）、`warn_return_any = true`

**前端（ESLint，`.eslintrc.json`）**
- 环境 browser + es2021；解析 ecmaVersion latest、sourceType module
- `no-var`、`prefer-const`、`eqeqeq` 判为 error；`no-unused-vars` 判为 warn

没有 TypeScript 配置，也没有 Prettier。

## 已知缺口

动手写代码前先确认这几项，它们会直接影响实现方式：

1. **前端 ESLint 配置当前完全跑不起来。** ESLint ≥9 移除了 `.eslintrc.*` 支持，默认只读 `eslint.config.*`。`npx eslint frontend/` 报 `ESLint couldn't find an eslint.config.* file.` 并以 exit code 2 退出；在 ESLint 10 下连 `ESLINT_USE_FLAT_CONFIG=false` 也无效。修法是新增 `frontend/eslint.config.js`（或 `.mjs`）flat config，把上面 4 条规则等价迁过去；用 `.mjs` 可避免 `MODULE_TYPELESS_PACKAGE_JSON` 警告。
2. **没有测试框架和 CI。** 前后端的测试运行方式（pytest / vitest）都待定。
3. **没有 `.gitignore`。** 首次提交 Python / Node 依赖产物前需补上（`.venv/`、`__pycache__/`、`node_modules/`、`.env`、`dist/`）。
4. **配置加载方式未定。** `backend/app/config.py` 是空的——用 pydantic-settings 还是直接读 `os.environ`，需要与用户确认。
5. **Dify 对接方式未定。** `dify/docker/` 为空，是要引入 Dify 官方 compose 还是只放自己的 override 文件，需确认。

## Git 约定

- Conventional Commits 前缀 + 中文描述，例如 `chore: 工程骨架 + lint 基线`、`feat: ...`、`fix: ...`。
- 当前分支是 `master`，且**没有配置 remote**，首次推送前需要 `git remote add origin`。
- 提交前跑上面的 ruff / mypy；前端 ESLint 修好之前无法作为门禁。

## 环境说明（Windows 10 + Git Bash）

- `uv` / `uvx` 可用（`~/.local/bin/uv`）——用它跑 ruff、mypy，不要往全局 Python 环境装包。
- **没有可用的 Python 解释器**：`python` / `python3` 指向 Windows 应用商店的占位程序，实际不可用。需要时用 `uv python install 3.10` 安装，并创建 `.venv`。
- Node v24.18.0、npm 11.16.0、pnpm 11.17.0 可用；yarn 不可用。
- **Docker 未安装**，`docker-compose.yml` 无法在本机验证。
- ruff、mypy、eslint 均未全局安装，只能通过 `uvx` / `npx` 调用。
