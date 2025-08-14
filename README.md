<p align="center">
  <img src="https://raw.githubusercontent.com/Ricardo2001zg/1Panel-Appstore-Ric/main/1Panel-Appstore.png" >
</p>
<h1 align="center">1Panel AppStore (Ricardo2001zg) </h1>
<br>
<p align="center">
  <img src="https://img.shields.io/badge/Release-v1.0-blue.svg" />
  <img src="https://img.shields.io/badge/Platform-Docker-red.svg" />
  <img src="https://img.shields.io/badge/Awesome-List-9cf.svg">
</p>

### ⚠️ Fork

Fork from [https://github.com/1Panel-dev/appstore](https://github.com/1Panel-dev/appstore)

Fork from [https://github.com/arch3rPro/1Panel-Appstore](https://github.com/arch3rPro/1Panel-Appstore)

### 📖 仓库介绍
 - 本仓库包含多个适用于 1Panel 的应用，旨在为用户提供简单、快速的安装与更新体验。应用均为开源项目，支持通过 1Panel 的计划任务功能自动化安装和更新。通过仓库提供的脚本，可以轻松地将应用集成到 1Panel 系统中。

### ⚠️ 仓库申明

- 非官方，第三方应用商店
- 接受开发者贡献版本更新 Pull Request
- 不保证提交的新增应用 Pr 都能通过，无固定审核标准
- 仓库应用为个人常用应用，尽量保证每周更新与稳定性，用于生产用途请自行评估风险
- 不对任何原始镜像的有效性做出任何明示或暗示的保证或声明，安全性和风险自查
- 个人仓库，可以Fork后自行更新，但是严禁未经授权，私自删除个人信息后合并发布 


### 📱 应用列表

以下是当前在本仓库中提供的应用列表及其版本信息，**点击应用名称可查看应用详细介绍文档**

#### 🤖 AI 与智能应用

<table>
<tr>
<td width="33%" align="center">

<a href="./apps/lobe-chat-data/README.md">
<img src="./apps/lobe-chat-data/logo.png" width="60" height="60" alt="LobeChat-Data">
<br><b>LobeChat-Data</b>
</a>

💬 开源现代设计的 ChatGPT/LLMs UI/框架

<kbd>1.98.2</kbd> • [官网链接](https://github.com/lobehub/lobe-chat)

</td>
<td width="33%" align="center">

<a href="./apps/dify/README.md">
<img src="./apps/dify/logo.png" width="60" height="60" alt="Dify">
<br><b>Dify</b>
</a>

🤖 开源LLM应用开发平台，支持AI工作流和RAG管道

<kbd>1.7.1</kbd> • [官网链接](https://github.com/langgenius/dify)

</td>
<td width="33%" align="center">

<a href="./apps/prompt-optimizer/README.md">
<img src="./apps/prompt-optimizer/logo.png" width="60" height="60" alt="Prompt-Optimizer">
<br><b>Prompt-Optimizer</b>
</a>

🚀 强大的AI提示词优化工具，支持多种主流大语言模型

<kbd>1.3.1</kbd> • [官网链接](https://github.com/arch3rPro/Prompt-Optimizer)

</td>
</tr>
</table>

### 🚀 使用方法

#### 📋 添加脚本到 1Panel 计划任务

1. 在 1Panel 控制面板中，进入"计划任务"页面。
2. 点击"新增任务"，选择任务类型为"Shell 脚本"。
3. 在脚本框中粘贴以下代码：

```bash
#!/bin/bash

# 清理旧的临时目录
rm -rf /tmp/appstore_merge

# 克隆 appstore-Ricardo2001zg
git clone --depth=1 https://github.com/Ricardo2001zg/1Panel-Appstore-Ric /tmp/appstore_merge/appstore-Ricardo2001zg

# 复制 数据（完整复制）
cp -rf /tmp/appstore_merge/appstore-Ricardo2001zg/apps/* /opt/1panel/resource/apps/local/

# 清理临时目录
rm -rf /tmp/appstore_merge
echo "应用商店数据已更新"
```
