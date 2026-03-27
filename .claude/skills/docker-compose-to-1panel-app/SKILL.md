---
name: docker-compose-to-1panel-app
description: 将 docker-compose 项目转换为当前仓库中的 1Panel App 目录结构。用于把现有 docker-compose 文件或目录转换为 apps/ 下的应用文件。
disable-model-invocation: true
argument-hint: [source-compose-file-or-dir] [app-key]
---

# 将 Docker Compose 转换为 1Panel App

将 `$ARGUMENTS` 转换为符合当前仓库目录结构与元数据约定的 1Panel App。

## 目标

在 `apps/<app-key>/` 下生成一个最小可用、结构合法的应用目录，并遵循本仓库已有应用与 appstore wiki 的组织方式。

## 必须遵循的流程

1. 解析输入源。
   - 接受单个 `docker-compose.yml` 文件，或包含 compose 相关文件的目录。
   - 识别服务、镜像、端口、环境变量、卷、网络、重启策略以及服务之间的依赖关系。

2. 推断目标应用元数据。
   - 如果参数中传入了 `app-key` 和 `version`，优先使用它们。
   - 如果未提供，则根据项目名或服务名生成安全的小写 kebab-case 形式的 app key。
   - 版本号如果无法从输入中推断，从 docker-compose 的 image 字段中获取，如无法获取，使用 `latest` 作为默认值.
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
     - 用户名、密码、数据库名等配置项添加 `random: true` 以启用随机生成。
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
