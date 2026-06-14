# agent-skills

This is the agent-skills project — a collection of production-grade engineering skills for AI coding agents.

## Project Structure

```
skills/       → Core skills (SKILL.md per directory)
agents/       → Reusable agent personas (code-reviewer, test-engineer, security-auditor, web-performance-auditor)
hooks/        → Session lifecycle hooks
.claude/commands/ → Slash commands (/spec, /plan, /build, /test, /review, /code-simplify, /ship; plus /webperf specialist audit)
references/   → Supplementary checklists (testing, performance, security, accessibility)
docs/         → Setup guides for different tools
```

## Skills by Phase

**Define:** interview-me, idea-refine, spec-driven-development
**Plan:** planning-and-task-breakdown
**Build:** incremental-implementation, test-driven-development, context-engineering, source-driven-development, doubt-driven-development, frontend-ui-engineering, api-and-interface-design
**Verify:** browser-testing-with-devtools, debugging-and-error-recovery
**Review:** code-review-and-quality, code-simplification, security-and-hardening, performance-optimization
**Ship:** git-workflow-and-versioning, ci-cd-and-automation, deprecation-and-migration, documentation-and-adrs, observability-and-instrumentation, shipping-and-launch

## Conventions

- Every skill lives in `skills/<name>/SKILL.md`
- YAML frontmatter with `name` and `description` fields
- Description starts with what the skill does (third person), followed by trigger conditions ("Use when...")
- Every skill has: Overview, When to Use, Process, Common Rationalizations, Red Flags, Verification
- References are in `references/`, not inside skill directories
- Supporting files only created when content exceeds 100 lines

## Commands

- `npm test` — Not applicable (this is a documentation project)
- Validate: Check that all SKILL.md files have valid YAML frontmatter with name and description

## Boundaries

- Always: Follow the skill-anatomy.md format for new skills
- Never: Add skills that are vague advice instead of actionable processes
- Never: Duplicate content between skills — reference other skills instead

---

# Fork 增量 — learn-by-rebuilding 方法论（maisieyang）

> 这是本 fork 在上游 agent-skills 之上的私有扩展。上游(`upstream` = addyosmani/agent-skills)的 24 个通用 skill 原样保留、原地使用、随 merge 自动更新；下面是我加的、上游没有的东西。一条纪律：**只编码基石没有的**——上游有的能力一律装配、不重写。

**一句话**：通过重建一个优秀的对标开源项目，快速成为某领域专家。*What I cannot create, I do not understand.*

## 我加的 3 个新 skill（核心是 reverse-spec）

| skill | 干什么 | 上游为何没有 |
|---|---|---|
| `reverse-spec` | **本 fork 的核心**：逆向对标项目 → 三镜头 REFERENCE（§1-§4 目录树 + 数据流 + 核心要素）+ §5 build 路线图 | 上游 spec-driven 是正向需求→代码，方向相反；planning-and-task-breakdown 是任务级，不是项目级 map |
| `eval` ·**draft** | 概率性模型行为的回归基线（test⊥eval）。还在入门，留着用、未打磨 | 上游整套无"概率行为评估"概念 |
| `debrief` ·**draft** | 每做完一个模块沉淀一份认知文档（§回顾）。未打磨 | 上游 documentation-and-adrs 是记录，不是学习提取 |

> **范围收敛（2026-06-14）**：原 `align`（模块设计对齐）和 `/learn-module`（编排命令）已删——模块**设计**交给上游 interview-me（想清楚）+ plan（拆解），**实现**交给 TDD 等上游手艺，不再自定义编排。`roadmap` 早先已并入 reverse-spec（§5：首次排布在 reverse-spec，反复维护在每模块回顾后手动更新）。

## 没有自定义命令

模块循环 = `/reverse-spec`（一次，建 REFERENCE + §5）→ 每模块：interview-me + plan（设计）→ TDD（实现）→ eval（碰概率行为时，draft）→ debrief（沉淀文档，draft）。全是可直接 `/名字` 调或自动触发的 skill，不另包编排命令（壳无编排价值即冗余）。

## 已知待办

- eval / debrief 标为 draft，等更多实战经历再打磨。
- 老模块"回溯补文档"路径（debrief 假设有设计阶段判断可证伪；样例见 `build-my-own-harness/docs/modules/agent-loop.md`）。

## 消费项目要提供的绑定

skill 是通用的，项目特定值（测试命令、质量门、对标 spec 路径、eval substrate 入口）由消费项目的 CLAUDE.md 提供一张绑定表。样板见 `build-my-own-harness/CLAUDE.md`。

## 设计推理出处

[../five-position-model.md](../five-position-model.md)（五位置统一模型）· [../anthropic-anaysize.md](../anthropic-anaysize.md) · [finance-skills/TWO-LAYERS.md](../finance-skills/TWO-LAYERS.md)
