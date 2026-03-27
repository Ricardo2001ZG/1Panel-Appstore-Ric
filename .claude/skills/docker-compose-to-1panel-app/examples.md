# 示例

## 调用示例

```text
/docker-compose-to-1panel-app ./docker-compose.yml my-app
```

## 预期输出目录

```text
apps/my-app/
├── data.yml
├── README.md
└── 1.0.0/
    ├── data.yml
    ├── docker-compose.yml
    └── data/
```

## 预期转换结果形态

### 输入
一个 docker-compose 项目，包含：
- 一个主 Web 服务
- 一个对外暴露的 HTTP 端口
- 多个环境变量
- 一个持久化数据卷

### 输出
- 根目录 `data.yml`，其元数据风格接近 `apps/dify/data.yml` 与 `apps/lobe-chat-database/data.yml`
- 版本目录 `data.yml`，其中 `formFields` 至少覆盖：
  - 主 HTTP 端口，使用 `PANEL_APP_PORT_HTTP`
  - 敏感信息，使用 `password`
  - 可编辑文本配置，使用 `text`
- 改写后的版本目录 `docker-compose.yml`，要求：
  - 使用 `${CONTAINER_NAME}`
  - 使用 `1panel-network`
  - 使用 `createdBy: "Apps"`
  - 将可配置值替换为 `${ENV_VAR}`

## 最小端口字段示例

```yaml
- default: 8080
  edit: true
  envKey: PANEL_APP_PORT_HTTP
  labelEn: Port
  labelZh: 端口
  required: true
  rule: paramPort
  type: number
```

## 最小 compose 示例

```yaml
services:
  my-app:
    image: example/my-app:1.0.0
    container_name: ${CONTAINER_NAME}
    restart: always
    networks:
      - 1panel-network
    ports:
      - "${PANEL_APP_PORT_HTTP}:8080"
    environment:
      - APP_SECRET=${APP_SECRET}
    labels:
      createdBy: "Apps"

networks:
  1panel-network:
    external: true
```
