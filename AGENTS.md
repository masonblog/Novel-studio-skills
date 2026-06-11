# Agent 入口说明（Codex / Hermes / 其他 AI Agent 工具）

本仓库是一套多角色协作的网络小说创作工作流技能集。当用户提供小说创意并要求创作时，按以下方式工作：

## 启动协议

1. **先读宪法**：完整阅读 `skills/novel-studio/SKILL.md`，以及其引用的 `references/pipeline.md`（阶段流程与门禁）和 `references/workspace.md`（目录规范）。
2. **确定模式**：
   - 你的运行环境支持子代理/并行任务 → 主线程扮演总编，向子代理派发其他角色（角色提示词可参考 `.claude/agents/*.md`）。
   - 单 Agent 环境 → 按流水线顺序"换帽"：每次切换角色前，读取对应 `skills/novel-<role>/SKILL.md` 并严格按该角色职责边界工作。
3. **从总编开始**：阅读 `skills/novel-chief-editor/SKILL.md`，立项建工作区，编制实施计划。

## 角色技能索引

| 顺序 | 角色 | 技能文件 |
|------|------|----------|
| 0（贯穿） | 总编 | `skills/novel-chief-editor/SKILL.md` |
| 1 | 架构师 | `skills/novel-architect/SKILL.md` |
| 2 | 设定师 | `skills/novel-lore-master/SKILL.md` |
| 3（分批循环） | 写手 | `skills/novel-writer/SKILL.md` |
| 4（分批循环） | 编辑 | `skills/novel-editor/SKILL.md` |
| 5 | 设计师 | `skills/novel-designer/SKILL.md` |

## 必须遵守的三条纪律

1. **分批铁律**：长篇正文按批次推进（默认每批 5 章），每批完成后以总编身份向用户汇报并等待确认，**绝不连跑多批**。
2. **文件即接口**：所有产物写入 `novel-projects/<slug>/` 工作区，使用 `skills/novel-studio/references/templates/` 模板；角色只写自己的目录。
3. **状态先行**：每次启动先读工作区 `project.yaml` 判断当前阶段与批次，从断点继续；每次阶段变更后立即更新它。

## 恢复会话

用户说"继续写《某书》"时：定位 `novel-projects/<slug>/project.yaml` → 读 stage 与 progress → 按 `references/pipeline.md` 的"中断恢复"一节从断点继续。
