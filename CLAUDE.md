# CLAUDE.md

## 项目目标
开发“县城小企业 AI 知识库问答系统”一期，基于 FastAPI + 原生 HTML/JS + Dify。
当前处于开发阶段 1：前端静态页面 + mock 交互。

## 项目结构与命令
- `backend/`   FastAPI 后端（目前是空壳）
- `frontend/`  原生 HTML/CSS/JS（单文件，不引入框架）
- 前端预览：`cd frontend && npx http-server -p 8080`
- Python 环境：没有全局 Python，必须使用 `uv` 管理虚拟环境，用 `uvx` 跑 lint (ruff/mypy)。

## 开发规范
- **前端 lint 适配**：由于高版本 ESLint 不认旧的 `.eslintrc.json`，你在写前端代码时，顺手在 `frontend/` 下创建一个 `eslint.config.mjs`，把 `no-var`、`prefer-const`、`eqeqeq` 等规则用新版 flat config 语法配置好。
- **API 约定**：后端接口统一返回 `{"code": 0, "data": {}, "message": "ok"}`。`/chat` 查不到结果时必须返回“知识库暂无相关信息”。
- **环境避坑**：本机没有 Docker，不需要尝试启动 Docker；暂时不需要管后端环境，全力把前端静态页面跑通。

## 开发顺序（严格遵守）
1. **当前任务：前端静态页面** + mock 交互（发送消息 -> 延迟 800ms -> 返回带来源的假回答）。
2. FastAPI mock 三个接口，跑通前后端联调。
3. 接真实 Dify API。

## Git 规范
每次完成一个可运行的模块后，必须先跑 lint，再进行 git commit（使用 Conventional Commits 格式）。

## IMPORTANT
不要试图尝试运行测试或启动后端，当前阶段只关注前端 UI 和 mock 逻辑。