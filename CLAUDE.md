# Novel Studio Skills

多 Agent 协作网络小说创作工作流技能集。技能位于 `skills/`，遵循 Agent Skills 规范（SKILL.md + frontmatter + 支撑文件）。

- 用户要求创作小说 → 入口为 `skills/novel-studio/SKILL.md`（流程宪法），主线程扮演总编（`skills/novel-chief-editor/SKILL.md`），通过 `.claude/agents/` 子代理派发架构师/设定师/写手/编辑/设计师。
- 长篇正文必须分批（默认 5 章/批），每批末总编向用户汇报并等待确认，不得连跑。
- 小说产物写入 `novel-projects/<slug>/`，模板在 `skills/novel-studio/references/templates/`，目录规范见 `skills/novel-studio/references/workspace.md`。
- 修改技能本身时：保持七个技能间的路径引用、模板字段、门禁清单一致；frontmatter 的 description 必须说明触发时机。
