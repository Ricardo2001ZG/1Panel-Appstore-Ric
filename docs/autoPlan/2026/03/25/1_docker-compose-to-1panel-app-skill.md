# docker-compose-to-1panel-app skill 执行计划

## 任务信息
- 任务名称：docker-compose-to-1panel-app
- 今日任务编号：1
- 日期：_2026-03-25-15-57-06-UTC+8
- Context：本次任务需要在项目内新增一个 Claude Code skill，用于将现有的 docker-compose 项目转换为符合 1Panel Apps 规范的应用目录。该 skill 必须遵循本仓库中已有应用的实际组织方式，并参考 `docs/appstore.wiki` 中的说明文档以及 `docs/claudeCodeDocs/zh-CN/skills.md` 中的 skill 规范。最终目标是得到一个项目级可复用的转换 skill，并补齐对应的执行计划文档，便于后续维护和复用。

## 原始 Prompt
请在 .claude/skills 中创建一个用于转换 docker-compose 文件到 1Panel Apps 的 skill，name 字段使用英文描述，App 的文件夹组织格式请读取 docs/appstore.wiki 中的 md 文件与参考 apps 文件夹中的应用目录组织形式。skills 格式请参考 docs/claudeCodeDocs/zh-CN/skills.md。执行计划写入 docs/autoPlan 文件夹中，计划文件名为 {任务命名}_{今日任务编号：1}_{日期}.md。其中，{任务命名}中使用"-"连接，{日期}中使用"-"连接，如 2026-03-25-15-20-00-UTC+8，今日任务编号为 1，并将该段 Prompt 完整写入计划任务中，建立“原始 Prompt”章节。

## 目标产物
1. 项目级 skill 目录：`.claude/skills/docker-compose-to-1panel-app/`
2. skill 主文件：`.claude/skills/docker-compose-to-1panel-app/SKILL.md`
3. 参考说明：`.claude/skills/docker-compose-to-1panel-app/reference.md`
4. 示例文件：`.claude/skills/docker-compose-to-1panel-app/examples.md`
5. 执行计划文档：`docs/autoPlan/docker-compose-to-1panel-app-skill_2026-03-25-15-57-06-UTC+8_1.md`

## 关键参考
- `docs/appstore.wiki/How-to-submit-your-own-application.md`
- `docs/appstore.wiki/如何提交自己想要的应用.md`
- `docs/claudeCodeDocs/zh-CN/skills.md`
- `apps/dify/data.yml`
- `apps/dify/1.8.1/data.yml`
- `apps/dify/1.8.1/docker-compose.yml`
- `apps/lobe-chat-database/data.yml`
- `apps/lobe-chat-database/1.143.3/data.yml`
- `apps/lobe-chat-database/1.143.3/docker-compose.yml`

## 实施步骤
1. 阅读 appstore wiki，确认 1Panel App 的标准目录结构、根目录 `data.yml` 与版本目录 `data.yml` 的职责。
2. 阅读 `docs/claudeCodeDocs/zh-CN/skills.md`，确认 skill 的目录结构、frontmatter 字段、supporting files 用法与 `disable-model-invocation: true` 配置。
3. 对照 `apps/dify` 与 `apps/lobe-chat-database`，提炼当前仓库中真实使用的字段模式，而不是只照搬 wiki 示例。
4. 在 `.claude/skills/docker-compose-to-1panel-app/` 下创建 `SKILL.md`、`reference.md`、`examples.md`。
5. 将 skill 文件内容统一写为中文，但保留 frontmatter 中要求使用英文命名的 `name` 字段。
6. 在 `SKILL.md` 中明确转换流程：解析 compose、推断 app 元数据、生成 `data.yml`、改写 `docker-compose.yml`、编写 README、自检结果。
7. 在 `reference.md` 中总结仓库实际使用的 1Panel App 目录结构、字段模式、compose 改写规则与 README 写法。
8. 在 `examples.md` 中提供最小调用示例、预期输出目录与最小化字段示例。
9. 在 `docs/autoPlan/` 中按命名规则写入本计划文件，并保留“原始 Prompt”章节。

## 验证方式
1. 检查 `.claude/skills/docker-compose-to-1panel-app/` 目录下是否存在 `SKILL.md`、`reference.md`、`examples.md`。
2. 检查 `SKILL.md` frontmatter：
   - `name` 为英文小写连字符风格
   - `disable-model-invocation: true` 已配置
   - `description` 和正文已改为中文
3. 检查 `reference.md` 与 `examples.md` 是否均为中文内容。
4. 检查 skill 正文是否引用 `reference.md` 与 `examples.md`。
5. 检查计划文件命名是否符合 `{任务命名}_{日期}_{今日任务编号}.md` 规则，并包含“原始 Prompt”章节。

## 当前结果
- 已创建并写入 `.claude/skills/docker-compose-to-1panel-app/SKILL.md`
- 已创建并写入 `.claude/skills/docker-compose-to-1panel-app/reference.md`
- 已创建并写入 `.claude/skills/docker-compose-to-1panel-app/examples.md`
- 已将以上 skill 文件内容统一调整为中文
- 当前已补写本执行计划文档
