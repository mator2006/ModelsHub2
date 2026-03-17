# ModelsHub2

ModelsHub2 是一个基于 Go + Vue 3 的 AI 网关管理台，用于维护 Key、Upstream、Mapping，并提供日志查询与聊天代理转发能力。

## 主要功能

1. Key 管理：新增、删除、测试、复制、按 provider 查看模型列表。
2. Upstream 管理：列表查询、按模型名过滤、编辑、删除。
3. Mapping 管理：本地模型与上游模型映射增改删。
4. Mapping 快捷调用：在映射列表可一键复制 Chat Completions 调用信息。
5. Logs：从 SQLite 读取日志，支持 provider/action/关键词筛选。
6. Chat Proxy：将本地模型名通过映射转发到上游 `/v1/chat/completions`。
7. 多语言：前端默认英文，导航栏按钮可一键切换中文。

## 前端页面

1. `/keys` Key Management
2. `/upstreams` Upstream Management
3. `/mappings` Model Mapping
4. `/keys/:provider/models` Models List
5. `/logs` Logs View

## 技术栈

1. Backend: Go 1.22 + net/http
2. Frontend: Vue 3 + Vue Router + Vue CLI
3. Data: JSON + SQLite (`data/aihub.db`)

## API 概览

1. Key: `GET /keys`, `POST /keys`, `DELETE /keys/:provider`, `POST /keys/test`, `POST /keys/refresh-models`, `GET /keys/models?provider=...`
2. Upstream: `GET /upstreams`, `POST /upstreams`, `PUT /upstreams/:id`, `DELETE /upstreams/:id`
3. Mapping: `GET /mappings`, `POST /mappings`, `PUT /mappings/:id`, `DELETE /mappings/:id`
4. Settings: `GET /settings/local-endpoint`（当前仅只读，返回 host/port/updated_at/external_address）
5. Logs: `GET /logs?limit=...&provider=...&action=...&q=...`
6. Chat Proxy: `POST /v1/chat/completions`（兼容 `POST /chat/completions`）
7. Health: `GET /health`

## 本地运行

```bash
cd server
go mod tidy
go run .
```

```bash
cd web
npm install
npm run serve -- --host 0.0.0.0 --port 5173
```

默认地址：
1. Backend: `http://localhost:8080`
2. Frontend: `http://localhost:5173`（或你自定义端口）

## Docker 热加载运行

```bash
docker compose up -d
```

端口由环境变量控制：
1. Backend: `${BACKEND_PORT}` 映射到容器内 `8080`
2. Frontend: `${WEB_PORT}` 映射到容器内 `${WEB_PORT}`

## 数据文件

1. `data/keys.json`：provider -> key
2. `data/aihub.json`：upstreams/mappings/local_endpoint/key_tested_at
3. `data/aihub.db`：logs 表（自动创建）

数据目录解析顺序：
1. `AIRHUB_DATA_DIR`
2. `data/`
3. `../data/`

## 关键配置

`config/config.yml` 中 `app` 配置支持：
1. `listen_address`: 后端监听地址
2. `backend_port`: 后端端口
3. `external_address`: 对外调用地址（默认 `192.168.10.216`），用于前端“复制调用信息”生成 URL

## 当前已验证

1. `go test ./...` 通过（当前无测试文件，编译通过）。
2. `npm run build` 通过。
3. 核心接口链路可用：health/keys/upstreams/mappings/settings/logs/chat-proxy。

## 文档

1. UI 说明见 `UI.md`
2. 程序设计与逻辑见 `Program_Logic_Detailed.md`





