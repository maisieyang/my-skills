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

# Fork 增量 —— 把"靠重建吃透一个领域"的方法编码成 skill

> 上游 agent-skills（`upstream` = addyosmani/agent-skills）给的是通用 SWE 手艺——spec / plan / TDD / review……它缺的是**一套进入并吃透一个陌生领域的方法**。这个 fork 只补这一件：
>
> **learn-by-rebuilding** —— 选一个领域里最好的对标项目，从零重建它，再把原则蒸馏下来。*What I cannot create, I do not understand.*
>
> 贯穿始终一条纪律：**只编码基石没有的**。上游 24 个通用 skill 原样保留、原地用、随 merge 自动更新；下面是上游没有、我加的那几样。

## 方法长什么样：三步，落进三个 skill

```
 study  ──►  build  ──►  distill
   │           │            │
reverse-spec  interview-me  debrief
→ REFERENCE   + plan → TDD  → 模块认知文档
(一次·冻结)              横切：碰概率行为 → eval
```

- **study** —— `reverse-spec` 把对标项目逆向成一张冻结的认知地图 `REFERENCE`（三镜头 + §5 有序模块拆分），防玩具化的底图。
- **build** —— 每个模块用上游 `interview-me`（想清楚）+ `plan`（拆解）设计，`TDD` 实现。**全是上游手艺，我不重写。**
- **distill** —— `debrief` 把每个做完的模块沉淀成一篇认知文档。
- **横切** —— 改动碰概率行为（prompt / memory）→ `eval` 做回归基线（确定性测试证明不了这层）。

所以这个 fork 真正新增的，只有 study / distill / eval 这三处上游缺的能力；设计与实现全站在上游肩上。

## 我加的 3 个 skill（核心是 reverse-spec）

| skill | 干什么 | 上游为何没有 |
|---|---|---|
| `reverse-spec` | **本 fork 的核心**：逆向对标项目 → 三镜头 REFERENCE（§1-§4 目录树 + 数据流 + 核心要素）+ §5 有序 build 模块拆分 | 上游 spec-driven 是正向需求→代码，方向相反；planning-and-task-breakdown 是任务级，不是项目级 map |
| `eval` ·**draft** | 概率性模型行为的回归基线（test⊥eval）。还在入门，留着用、未打磨 | 上游整套无"概率行为评估"概念 |
| `debrief` ·**draft** | 每做完一个模块沉淀一份认知文档（§回顾）。未打磨 | 上游 documentation-and-adrs 是记录，不是学习提取 |

## 没有自定义命令

模块循环全是可直接 `/名字` 调或自动触发的 skill，不另包编排命令（壳无编排价值即冗余）。

## 范围收敛 & 待办

- **收敛（2026-06-14）**：`align`、`/learn-module` 已删（设计交上游 interview-me + plan、实现交 TDD，不再自定义编排）；`roadmap` 并入 reverse-spec 的 §5。
- **draft**：`eval` / `debrief` 等更多实战再打磨；老模块"回溯补文档"路径见 debrief 的「前提」一节。
