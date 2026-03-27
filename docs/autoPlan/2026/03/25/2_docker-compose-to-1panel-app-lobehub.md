# 执行计划

## 任务信息
- 任务名称：docker-compose-to-1panel-app-lobehub
- 今日任务编号：2
- 日期：2026-03-25-15-20-00-UTC+8
- 目标：将 `./apps/lobehub/workspace lobehub` 转换为符合当前仓库目录结构与元数据约定的 1Panel App。

## 背景与上下文
需要将 `apps/lobehub/workspace` 中的 Docker Compose 资源整理为仓库规范的 1Panel App。当前输入只包含主服务 `lobe` 的 compose，依赖了 PostgreSQL、Redis、RustFS、SearXNG 等外部服务，但这些依赖没有在输入 compose 内完整定义，因此本次转换采用“主应用 + 外部依赖”的方式，而不是扩展成多服务打包应用。

用户已确认：
- 依赖服务使用外部依赖，通过 1Panel 面板表单/环境变量接入。
- `SEARXNG_URL` 作为可选文本字段暴露。
- `env.zh-CN.example` 中的敏感样例值（如 `JWKS_KEY`）保留为空表单项，不迁移示例值。

## 执行步骤
1. 参考仓库现有应用模式，复用 `apps/lobe-chat-database` 与 `apps/dify` 的元数据、formFields、docker-compose 结构。
2. 生成根级应用文件：
   - `apps/lobehub/data.yml`
   - `apps/lobehub/README.md`
3. 生成版本级文件：
   - `apps/lobehub/2.1.44/data.yml`
   - `apps/lobehub/2.1.44/docker-compose.yml`
4. 将 compose 改写为 1Panel 形式：
   - 使用 `${CONTAINER_NAME}`
   - 接入 `1panel-network`
   - 添加 `createdBy: "Apps"`
   - 使用 `${PANEL_APP_PORT_HTTP}:3210`
   - 去除 `depends_on`、`env_file`、原有 `lobe-network`
5. 通过 formFields 暴露最小必要配置：
   - Web 端口
   - PostgreSQL 外部依赖
   - Redis 外部依赖
   - S3 外部对象存储
   - 应用密钥与认证参数
   - 可选 `SEARXNG_URL`
6. 清理 `apps/lobehub/workspace/`。
7. 检查目录结构与文件可读性，确认输出最小且合法。

## 关键文件
- `apps/lobehub/data.yml`
- `apps/lobehub/README.md`
- `apps/lobehub/2.1.44/data.yml`
- `apps/lobehub/2.1.44/docker-compose.yml`

## 验证方式
1. 检查目标目录结构是否完整。
2. 检查根 data.yml 是否包含标准 `additionalProperties` 字段。
3. 检查版本 data.yml 是否包含 `PANEL_APP_PORT_HTTP`、数据库依赖、Redis、S3 与密钥字段。
4. 检查 compose 是否符合 1Panel 规范。
5. 确认 `workspace` 目录已移除。

## 原始 Prompt
```text
<command-message>docker-compose-to-1panel-app</command-message>
<command-name>/docker-compose-to-1panel-app</command-name>
<command-args>./apps/lobehub/workspace lobehub</command-args>
Base directory for this skill: /Users/ricardo2001zg/Projects/LingMiaoTech/1Panel-WorkSpace/1Panel-Appstore-Ric/.claude/skills/docker-compose-to-1panel-app

# 将 Docker Compose 转换为 1Panel App

将 `./apps/lobehub/workspace lobehub` 转换为符合当前仓库目录结构与元数据约定的 1Panel App。

## 目标

在 `apps/<app-key>/` 下生成一个最小可用、结构合法的应用目录，并遵循本仓库已有应用与 appstore wiki 的组织方式。

## 必须遵循的流程

1. 解析输入源。
   - 接受单个 `docker-compose.yml` 文件，或包含 compose 相关文件的目录。
   - 识别服务、镜像、端口、环境变量、卷、网络、重启策略以及服务之间的依赖关系。

2. 推断目标应用元数据。
   - 如果参数中传入了 `app-key` 和 `version`，优先使用它们。
   - 如果未提供，则根据项目名或服务名生成安全的小写 kebab-case 形式的 app key。
   - 版本号如果无法从输入中推断，从 docker-compose 的 image 字段中获取，如无法获取，使用 `latest` 作为默认值。
   - 根据项目内容推断可读的应用名称。
   - 根据仓库现有约定谨慎推断应用类型：Web 应用优先使用 `website`，否则选择最接近的合法 1Panel 类型。

3. 创建 1Panel App 目录结构。
   - 创建 `apps/<app-key>/data.yml`
   - 创建 `apps/<app-key>/README.md`
   - 创建 `apps/<app-key>/<version>/data.yml`
   - 创建 `apps/<app-key>/<version>/docker-compose.yml`
   - 仅当存在值得暴露的持久化挂载时创建 `apps/<app-key>/<version>/data/`
   - 仅当明确需要安装、升级、卸载钩子时创建 `apps/<app-key>/<version>/scripts/`

4. 生成应用根目录元数据。
   - 参考 `apps/` 下现有应用的实际格式。
   - 使用 `additionalProperties`，并在可确定时包含本仓库中稳定存在的字段，如 `key`、`name`、`tags`、`type`、`crossVersionUpdate`、`limit`、`recommend`、`website`、`github`、`document`、`architectures`。
   - 描述保持简洁。

5. 生成版本级表单字段。
   - 从需要用户配置的值中构建 `additionalProperties.formFields`。
   - 复用本仓库中的 1Panel 约定：
     - 主 Web 外部端口优先使用 `PANEL_APP_PORT_HTTP`
     - 其他暴露的外部端口使用 `PANEL_APP_PORT_*`
     - 密钥类字段使用 `type: password`
     - 端口类字段使用 `type: number`
     - 数据库、Redis 等依赖可按需要使用 `type: service` 或 `type: apps`
   - 不要为仅内部固定值创建不必要的表单字段。

6. 将 docker-compose 改写为 1Panel 形式。
   - 当只有一个主应用容器时，确保使用 `container_name: ${CONTAINER_NAME}`。
   - 将服务接入 `1panel-network`。
   - 增加 `labels:`，其中包含 `createdBy: "Apps"`。
   - 将硬编码的可配置值替换为版本 `data.yml` 中定义的 `${ENV_VAR}` 引用。
   - 如果原项目确实需要多服务结构，保留合法的多服务组合。

7. 编写实用的 README。
   - 简要介绍应用。
   - 如果能从源码或 compose 中明显看出主要特性，则做简短总结。
   - 风格保持精炼，并尽量接近本仓库现有应用的 README 写法。

8. 完成前自检。
   - 确认生成的目录结构符合 1Panel 文档要求。
   - 确认生成字段与本仓库现有应用模式一致。
   - 如果通用推断与仓库真实示例冲突，优先以仓库中的真实示例为准。
   - 若目标 apps 目录下存在 workspace 文件夹，移除该文件夹。
   - 完成后移除传入的 docker-compose 文件。

## 仓库内参考资料

在生成文件前先加载：

- [reference.md](reference.md)：仓库内 1Panel App 规则与字段模式参考。
- [examples.md](examples.md)：预期输出形态示例。

## 输出规则

- 结果保持最小化且合法。
- 优先复用仓库现有约定，不要发明新的结构。
- 不要创建应用所需范围之外的额外说明文件。
- 如果关键元数据无法准确推断，使用最小且合理的占位值，并确保目录结构仍然合法。
```
