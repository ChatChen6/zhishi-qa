# 制度问答一期

## 项目结构
- backend/    FastAPI，提供 /upload、/knowledge/status、/chat
- frontend/   原生 HTML/CSS/JS，不引入框架
- docker-compose.yml   编排 Dify + backend

## 启动与测试
- 后端开发：（待定，目前优先做前端）
- 前端预览：`cd frontend && npx http-server -p 8080` （或者直接用浏览器打开 index.html）
- 后端 lint：`cd backend && uvx ruff check . && uvx mypy app`
- 前端 lint：`npx eslint frontend/`
- 每次改完必须跑 lint，报错清零再 commit

## 接口约定（必须遵守）
- 所有 API 返回统一格式：`{"code": 0, "data": {}, "message": "ok"}`
- 出错时 `code` 非 0，`message` 写人话，不暴露堆栈
- /chat 如果检索结果为空，必须返回包含“知识库暂无相关信息”的回答，禁止模型自由发挥

## 开发顺序（严格按此推进，不许跳）
1. 前端静态页面 + mock 交互
2. FastAPI mock 三个接口，跑通前后端联调
3. 接真实 Dify API：先上传+入库，再检索，最后生成

## 禁止事项
- 不要在前端引入 React/Vue/任何构建工具
- 不要把 API Key 硬编码，一律读环境变量
- 不要跳过冒烟测试直接 commit

## IMPORTANT
每次完成一个可运行的模块后，先跑冒烟测试，再 git commit。