# SearXNG

## 简介

SearXNG 是一个开源的元搜索引擎，旨在保护用户隐私并提供无广告、无追踪的搜索体验。它能够聚合来自多个主流搜索引擎（如 Google、Bing、DuckDuckGo、Baidu 等）的结果，支持自建部署，用户完全掌控自己的搜索数据。SearXNG 是 SearX 项目的现代化分支，拥有更活跃的社区和持续的功能更新。

![](https://cdn.jsdelivr.net/gh/xiaoY233/PicList@main/public/assets/Searxng.png)

![](https://img.shields.io/badge/Copyright-arch3rPro-ff9800?style=flat&logo=github&logoColor=white)

## 主要特性

- **隐私保护**：不记录用户搜索历史，不追踪用户行为，支持匿名搜索。
- **多引擎聚合**：支持数十种主流和垂直搜索引擎，结果可自定义排序和过滤。
- **自定义与扩展**：支持插件、主题、API 扩展，界面和功能高度可定制。
- **开源自托管**：基于 Python，支持 Docker 部署，适合个人和企业自建。
- **无广告体验**：默认无广告，界面简洁，专注于搜索本身。
- **多语言支持**：内置多种语言界面，适合全球用户。
- **API 支持**：提供 RESTful API，便于集成到其他应用或自动化流程。
- **安全性**：支持 HTTPS、反爬虫、速率限制等安全机制。

## MCP 接入

SearXNG 支持通过 API 与 MCP（Model Context Protocol）等智能应用集成，实现搜索能力的智能化扩展。典型接入方式如下：

1. **启用 SearXNG API**  
   在 `settings.yml` 中开启 API 支持，确保外部应用可通过 HTTP 请求访问搜索结果。
   ```yaml
   server:
     bind_address: "0.0.0.0"
     port: 8080
     secret_key: "your_secret"
     api:
       enabled: true
   ```

2. **MCP 侧配置 SearXNG 作为搜索插件**  
   在 MCP 平台中添加 SearXNG 搜索插件，配置 API 地址（如 `http://your-searxng-instance:8080/search?q={query}&format=json`），并设置好参数映射。

3. **请求与响应示例**  
   MCP 通过 HTTP GET 请求 SearXNG API，获取 JSON 格式的搜索结果，并将其用于上下文补全、智能问答等场景。
   ```http
   GET /search?q=OpenAI&format=json HTTP/1.1
   Host: your-searxng-instance:8080
   ```

4. **安全与权限**  
   建议为 SearXNG API 设置访问控制（如 IP 白名单、API 密钥等），防止未授权访问。

5. **高级集成**  
   可结合 SearXNG 的自定义过滤器、插件机制，实现更丰富的搜索能力，如自动摘要、结果去重、内容分类等，提升 MCP 智能应用的搜索体验。

---
如需详细部署与集成文档，可参考 [SearXNG 官方文档](https://docs.searxng.org/) 及 MCP 平台相关说明。