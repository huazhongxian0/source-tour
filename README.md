# Source Tour

Source Tour 是一个用于精读源码的 Agent Skill。它从真实入口沿调用链梳理指定功能，生成一个**单文件、可离线打开**的 HTML 导读：左侧是可交互的流程图与时序图，右侧显示所选节点对应的真实源码、文件路径和行号。

## 能做什么

- 沿入口追踪实际调用、分支、重试和错误处理，而不是根据文件名推测流程。
- 用 10–30 个节点概括主流程；点击流程节点或时序消息即可查看对应源码。
- 在同一页面切换图表、跳转节点、缩放、复制代码，并通过 URL 片段定位当前节点。
- 把页面所需的样式、脚本和源码快照放进一个 `index.html`，查看时无需启动服务或加载 CDN。

## 安装

仓库根目录就是完整的技能目录，安装时需要保留 `SKILL.md` 和 `assets/template.html`。选择与你使用的工具对应的目录：

### Codex

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/huazhongxian0/source-tour.git ~/.codex/skills/source-tour
```

### Claude Code

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/huazhongxian0/source-tour.git ~/.claude/skills/source-tour
```

已有 GitHub SSH 访问权限时，也可以把上述克隆地址换成 `git@github.com:huazhongxian0/source-tour.git`。

已通过 Git 安装时，可在相应目录执行 `git pull --ff-only` 更新。例如：

```bash
git -C ~/.codex/skills/source-tour pull --ff-only
```

安装或更新后，开启新会话以重新发现技能。Codex 和 Claude Code 均使用带 YAML 元数据的 `SKILL.md` 目录；Claude Code 的个人技能路径见 [Anthropic 文档](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)。

## 使用

在要阅读的项目目录中，请助手使用 `source-tour`，并尽量说明功能和入口。例如：

> 使用 source-tour 精读 `KafkaProducer.send` 到 Broker 追加日志的主流程，从 `send` 方法开始，生成离线导读。

也可以只指定项目或模块。技能会根据代码结构选择一条有代表性的主流程，先说明入口与覆盖范围。大型项目的一份导读聚焦一条流程；需要覆盖另一条链路时，可另生成一份导读。

默认输出位于当前目录的 `<目标名>-guide/index.html`，也可以在请求中指定输出位置。页面包含两种视图：

| 视图 | 用途 |
| --- | --- |
| 流程图 | 看控制流、判断、分支和回边 |
| 时序图 | 看组件或模块间的调用顺序 |
| 源码区 | 看节点绑定的文件、行号、讲解和代码片段 |

## 工作流程与质量检查

技能会确认范围与入口、记录当前提交、扫描项目结构、沿调用链核对源码、绘制两张图、绑定代码引用，然后把数据写入 `assets/template.html` 并检查结果。每个流程节点绑定 1–4 个引用，每个引用通常保留 5–60 行。图的边、可点击的时序消息和源码引用必须能对应到真实节点与文件。嵌入页面前，需要把序列化 JSON 中的 `<` 转义为 `\u003c`。

`files` 数据保存生成时的源码快照。对于超过约 1500 行的文件，可以只保存引用附近的内容，但必须保留原始行号：未嵌入的行用空行占位，并在页面说明中标注。这种情况下，“查看文件快照”展示的是嵌入内容，不是完整源文件。数据结构和完整验收清单见 [SKILL.md](SKILL.md)。

## 使用范围

- 导读解释所选主流程，不等同于整个仓库的完整架构文档；未覆盖的旁路会在页面中说明。
- 输出 HTML 内含真实源码快照。分享生成文件前，请按项目的保密要求和许可证检查其中的内容。
- 若当前工作环境不允许预览本地 HTML，技能仍会交付文件的绝对路径。

## 仓库结构

```text
source-tour/
├── SKILL.md             # 触发条件、阅读流程、数据格式与验收规则
└── assets/
    └── template.html    # 离线导读页面模板
```
