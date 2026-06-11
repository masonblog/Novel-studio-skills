# Novel Studio Skills

面向 **Claude Code、Codex、Hermes Agent** 等 AI Agent 工具的多 Agent 协作网络小说创作工作流技能集。

用户只需提供一个大致的梗概创意，六个角色 Agent 即可各司其职，完成从立项、世界观、大纲、设定、正文到封面插画的全流程批量创作。长篇小说**强制分批实施**，总编分段向用户汇报成果，避免单次执行过于冗长。

## 角色与技能

| 角色 | 技能 | 职责 |
|------|------|------|
| 🎯 总编 | [`novel-chief-editor`](skills/novel-chief-editor/SKILL.md) | 编制实施计划、推进创作流程、成品验收与交付、分阶段汇报 |
| 🏛️ 架构师 | [`novel-architect`](skills/novel-architect/SKILL.md) | 世界观设定、故事梗概编写、章节大纲制定 |
| 📇 设定师 | [`novel-lore-master`](skills/novel-lore-master/SKILL.md) | 人物、任务、场景、关键道具、关键情节的设定卡 |
| ✍️ 写手 | [`novel-writer`](skills/novel-writer/SKILL.md) | 分批编写小说正文初稿 |
| 🔍 编辑 | [`novel-editor`](skills/novel-editor/SKILL.md) | 润色加工正文，对照梗概与设定做一致性对齐 |
| 🎨 设计师 | [`novel-designer`](skills/novel-designer/SKILL.md) | 封面、角色立绘、关键情节插画（文生图提示词+规格） |

总控技能 [`novel-studio`](skills/novel-studio/SKILL.md) 是所有角色共享的"宪法"：定义流水线状态机、项目目录规范、分批铁律与质量门禁，并附全套产物[模板](skills/novel-studio/references/templates/)。

## 工作流程

```
用户创意
  └→ 总编：立项 + 实施计划（用户确认）
       └→ 架构师：世界观 → 梗概 → 章节大纲
            └→ 设定师：人物/任务/场景/道具/情节卡
                 └→ ┌─ 批次循环（每批默认 5 章）──────────┐
                    │ 写手起草 → 编辑定稿 → 总编汇报 → 用户确认 │
                    └──────────────────────────────────┘
                      └→ 设计师：封面/立绘/插画
                           └→ 总编：终验交付
```

所有协作通过项目工作区 `novel-projects/<slug>/` 的文件交接，`project.yaml` 记录状态，**中断随时可恢复**。

## 在各工具中使用

### Claude Code

将本仓库克隆到项目中（或把 `skills/` 复制到 `.claude/skills/`），`.claude/agents/` 已提供五个角色子代理。直接对 Claude 说：

> 用 novel-studio 工作流帮我写一部小说：一个外卖骑手意外获得时间回溯能力……长篇，约 60 章。

主线程将以总编身份立项，并通过子代理派发各角色任务。

### Codex / Hermes 等单 Agent 工具

在会话中让 Agent 先阅读 `AGENTS.md` 与 `skills/novel-studio/SKILL.md`，随后按流水线顺序"换帽"扮演各角色（换帽前读取对应角色 SKILL.md）。产物、目录与质量门禁与多代理模式完全一致。

## 关键设计

- **分批铁律**：长篇任何情况下不得跳过批末汇报；每批"写→编→报→确认"四步缺一不可。
- **文件即接口**：角色间不靠对话上下文传设定，全部经工作区文件交接，工具无关。
- **一致性三道闸**：设定卡冻结命名 → 写手章末自检 → 编辑两遍法核对（命名/人设/时间线/伏笔/规则）。
- **无图也能交付**：设计师产出提示词+规格文档；环境有出图能力时再生成成品。

## 仓库结构

```
skills/               # 7 个技能（Agent Skills 规范：SKILL.md + 支撑文件）
  novel-studio/       # 总控：流水线、工作区规范、模板
  novel-chief-editor/ novel-architect/ novel-lore-master/
  novel-writer/ novel-editor/ novel-designer/
.claude/agents/       # Claude Code 子代理定义
AGENTS.md             # Codex / Hermes 等工具的入口说明
docs/DEVELOPMENT_PLAN.md  # 开发计划
```

## License

MIT
