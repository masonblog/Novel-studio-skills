# Novel Studio Skills 开发计划

> 面向 Claude Code、Codex、Hermes Agent 等 AI Agent 工具的多 Agent 协作网络小说创作工作流技能集。

## 1. 目标

用户只需提供一个大致的梗概创意，技能集即可驱动多个角色 Agent 协作，完成从立项、设定、大纲、正文到美术物料的网络小说批量创作，并在长篇创作中**分阶段交付、分段汇报**，避免单次执行过于冗长。

## 2. 角色分工

| 角色 | 技能名 | 职责 |
|------|--------|------|
| 总编 | `novel-chief-editor` | 编制实施计划、推进创作流程、成品验收与交付、分阶段向用户汇报 |
| 架构师 | `novel-architect` | 世界观设定、故事梗概编写、章节大纲制定 |
| 设定师 | `novel-lore-master` | 人物/任务、场景、关键道具、关键情节的设定编写 |
| 写手 | `novel-writer` | 分阶段编写小说正文 |
| 编辑 | `novel-editor` | 润色加工正文，并对照梗概与设定做一致性对齐 |
| 设计师 | `novel-designer` | 小说封面、角色立绘、关键情节插画（出图提示词与排版规格） |

另设入口技能 `novel-studio`：定义统一的流水线、项目目录规范、状态机与质量门禁，是所有角色共享的"宪法"。

## 3. 架构设计

### 3.1 跨工具兼容策略

- 技能采用开放的 **Agent Skills 规范**（`SKILL.md` + YAML frontmatter + 支撑文件），Claude Code 原生支持。
- 提供 `AGENTS.md` 作为 Codex / Hermes 等工具的入口说明，指引它们按需读取各 `SKILL.md`。
- 为 Claude Code 额外提供 `.claude/agents/` 子代理定义，使各角色可作为独立 subagent 并行/隔离运行；其他工具则按"单 Agent 顺序换帽"模式执行同一流程，产物与质量门禁完全一致。
- 所有协作通过**文件系统交接**（项目工作区目录），不依赖任何特定工具的进程间通信能力。

### 3.2 项目工作区规范

每部小说一个工作区 `novel-projects/<slug>/`：

```
novel-projects/<slug>/
├── project.yaml              # 项目元信息 + 流程状态机（唯一事实来源）
├── 01-planning/plan.md       # 总编：实施计划
├── 02-architecture/          # 架构师产物
│   ├── world.md              # 世界观设定
│   ├── synopsis.md           # 故事梗概
│   └── outline.md            # 章节大纲
├── 03-lore/                  # 设定师产物
│   ├── characters/           # 人物卡
│   ├── quests/               # 任务/主线支线
│   ├── scenes/               # 场景设定
│   ├── items/                # 关键道具
│   └── plot-points/          # 关键情节
├── 04-drafts/                # 写手初稿（按批次/卷分目录）
├── 05-final/                 # 编辑定稿
├── 06-art/                   # 设计师产物
│   ├── cover/
│   ├── portraits/
│   └── illustrations/
└── reports/                  # 总编分阶段汇报
```

### 3.3 流水线与状态机

```
init → planning → architecture → lore → [drafting ⇄ editing]×N批 → art → acceptance → delivered
```

- 每个阶段有明确的**进入条件、产物、质量门禁**。
- 长篇小说强制分批：每批默认 5 章（可配置），批内 写手→编辑 流水作业，批末由总编生成阶段报告并向用户汇报、征求是否继续/调整。
- `project.yaml` 记录当前阶段、批次进度，任何 Agent 中断后均可凭此恢复。

## 4. 开发里程碑

| 阶段 | 内容 | 产物 |
|------|------|------|
| M1 骨架 | 仓库结构、入口文档、开发计划 | `README.md`、`AGENTS.md`、`docs/DEVELOPMENT_PLAN.md` |
| M2 核心规范 | 入口技能：流水线、目录规范、状态机、模板 | `skills/novel-studio/`（含 references 与 templates） |
| M3 角色技能 | 六个角色 SKILL.md | `skills/novel-*/SKILL.md` |
| M4 Claude Code 适配 | 子代理定义 | `.claude/agents/*.md` |
| M5 验收 | 自检：技能间引用一致、模板可用、流程闭环 | 本次提交 |

## 5. 质量门禁（验收标准）

1. 每个 SKILL.md 含合法 frontmatter（name、description），description 说明触发时机。
2. 所有角色技能引用同一套工作区规范与模板，路径一致。
3. 流程对长篇强制分批，总编汇报节点明确，不允许一次性写完全书。
4. 编辑环节有可执行的一致性核对清单（对照梗概、大纲、人物卡、道具、伏笔）。
5. 任何工具（含无图像能力的）执行设计师技能都能产出可交付物（提示词 + 规格文档）。
6. 中断可恢复：仅凭 `project.yaml` + 工作区文件即可继续。

## 6. 后续迭代方向（非本期范围）

- 多语言（英文网文）模板变体。
- 接入图像生成 MCP 工具时设计师直接出图。
- 番茄/起点等平台投稿格式导出脚本。
- 多写手并行分卷创作的冲突合并策略。
