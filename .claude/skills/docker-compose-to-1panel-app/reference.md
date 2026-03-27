# 参考说明

## 1Panel App 目录结构

依据 `docs/appstore.wiki/How-to-submit-your-own-application.md`、`docs/appstore.wiki/如何提交自己想要的应用.md` 以及当前仓库中的实际示例，目标目录结构应为：

```text
apps/<app-key>/
├── data.yml
├── README.md
├── logo.png                 # 可选，仅在已有可用 logo 时创建
└── <version>/
    ├── data.yml
    ├── docker-compose.yml
    ├── data/               # 仅在需要时创建
    └── scripts/            # 仅在需要时创建
```

版本目录不能以 `v` 开头。

## 应用根目录 data.yml 模式

优先参考仓库中的真实结构：
- `apps/dify/data.yml`
- `apps/lobe-chat-database/data.yml`

观察到的根级结构模式如下：

```yaml
name: ExampleApp
tags:
  - AI
title: 简短标题
description: 简短描述
additionalProperties:
  key: example-app
  name: ExampleApp
  tags:
    - AI
  shortDescZh: 简短中文描述
  shortDescEn: short english description
  type: website
  crossVersionUpdate: true
  limit: 0
  recommend: 0
  website: https://example.com
  github: https://github.com/example/example
  document: https://docs.example.com
  architectures:
    - amd64
```

当仓库实际示例与较早的 wiki 示例不一致时，优先以仓库现状为准。

## 版本目录 data.yml 模式

参考以下文件中的 `additionalProperties.formFields`：
- `apps/dify/1.8.1/data.yml`
- `apps/lobe-chat-database/1.143.3/data.yml`

常见字段包括：
- `default`
- `edit`
- `envKey`
- `labelEn`
- `labelZh`
- 可选的多语言 `label`
- `required`
- `rule`
- `type`
- 可选的 `random`
- 可选的 `child`
- 可选的 `values`

常见类型映射：
- `number`：端口
- `password`：密码、密钥等敏感信息
- `text`：普通可编辑文本
- `service`：直接依赖的服务
- `apps`：可选择的依赖应用类型
- `select`：枚举值

来自 wiki 的常用校验规则：
- `paramPort`
- `paramExtUrl`
- `paramCommon`
- `paramComplexity`

## docker-compose 改写规则

参考以下文件的写法：
- `apps/dify/1.8.1/docker-compose.yml`
- `apps/lobe-chat-database/1.143.3/docker-compose.yml`

必须遵守的约定：
- 主应用容器名使用 `${CONTAINER_NAME}`。
- 服务接入 `1panel-network`。
- 添加标签 `createdBy: "Apps"`。
- 主 Web 端口优先使用 `${PANEL_APP_PORT_HTTP}`。
- 其他可配置的外部端口使用 `${PANEL_APP_PORT_*}`。
- 将用户需要配置的硬编码值替换为版本 `data.yml` 中已声明的 `${ENV_VAR}`。

示例模式：

```yaml
services:
  app:
    image: example/app:1.0.0
    container_name: ${CONTAINER_NAME}
    restart: always
    networks:
      - 1panel-network
    ports:
      - "${PANEL_APP_PORT_HTTP}:8080"
    environment:
      - EXAMPLE_KEY=${EXAMPLE_KEY}
    labels:
      createdBy: "Apps"

networks:
  1panel-network:
    external: true
```

## README 编写建议

参考以下文件的风格：
- `apps/dify/README.md`
- `apps/lobe-chat-database/README.md`

README 应包含简短的产品介绍和精炼的特性列表，不要过度展开。

## 决策规则

当不确定时：
1. 优先参考当前仓库中的真实示例，而不是通用假设。
2. 生成结果保持最小化。
3. 只为安装时确实需要配置的值暴露表单字段。
4. 仅当源项目明确需要时，才创建 `data/` 与 `scripts/`。
