---
name: eval
description: 改动触碰 prompt / memory / 概率性模型行为时，在决策面 map 上定位改动、检查或新建带三声明头的 eval、用 pass bar 做 gate。用于"改了 prompt 验证一下"、"行为有没有裂化"、"建一个 eval"。（draft·未实测打磨）
---

# /eval — 概率行为的回归基线

> ⚠️ **draft**：还没经过 reverse-spec 那样的实测打磨。我对 eval 还在入门——先用着，等积累了实战经历再迭代。

> 适用条件：项目包含概率性模型行为（LLM 调用、prompt、memory 等）。纯确定性项目没有这一层。

TDD 管确定性代码，eval 管"具体代码 × 概率模型"组合出的涌现行为。prompt 改一行、memory 换架构、模型升版本——所有测试照样 GREEN，但系统行为可能已经劣化。dogfooding 拦不住这个，eval 是回归基线——它守住已经 work 的东西，不是预言新东西好不好的 oracle。

**本 skill 的终点信号：定位到的 eval 达到 pass bar，或显式 ratify 的例外记录。**

## 流程

1. **定位决策面**：打开项目的决策面 map（位置见项目 CLAUDE.md 绑定），回答"这次改动让模型在哪个决策面上的行为可能变化"。决策面 = 系统把一类判断交给模型的地方（例：一次性结构化输出、工具选择、主循环推进、何时执行隐式 side effect、端到端任务完成）。
   - 项目还没有 map → 在模块设计阶段（interview-me + plan）把决策面枚举出来，不在 eval 内即兴构建（枚举是设计阶段的活，不是 eval 的职责）。
   - 改动对不上任何已有面 → 这可能是新决策面：**先开 decision doc 把它加进 map 并 ratify 优先级**，不许默默归入现有面。
2. **查优先级**：该面在 map 上是高优先级才动手建；推测性的面**不预建**——pre-emptive eval 是 over-engineering，等触发条件到了再回 map。
3. **已有 eval 的面**：先读该 eval dataset card 的 capability claim——确认这份 eval 的声明覆盖你要回答的问题。**声明对不上就不许借用**：被误用的 eval 给出的信号是失真的，比没有更糟。
4. **新建 consumer**（缺 eval 的面）：
   - **必须复用项目的 eval substrate**（runner / 录制回放 / scorer 协议 / 结果持久化——入口见项目 CLAUDE.md 绑定）。不允许为新决策面 vendor 一套独立 runner——substrate 漂移的维护成本是指数的。
   - dataset card 开头必须是**三声明头**，no exceptions：
     1. **Capability claim**——"这份 eval 声明系统在 [决策面 X] 上达到 [Y 水平]"，必须引用 map 中的具体面，并写明"不为之设计"的用途
     2. **Input spec**——输入形态、样本数 N、population 来源
     3. **Judgment spec**——success criterion 是 binary / scalar / LLM-judge，每个 scorer 的输出语义
   - Scorer 优先确定性（categorical / programmatic baseline），LLM-judge 只用在确定性打分够不到的语义维度
5. **跑基线 → 改动 → 重跑**：录制回放控制成本（录一次，回放免费）。改动前后各一次结果落盘，对比。
6. **Gate 用法**：架构级 pivot 必须先在模块 §对齐 或 decision doc 里 ratify pass bar（具体阈值见项目绑定 / decision doc，不在 skill 里写死），过不了 bar 不许走新架构——回去修，或显式 ratify 降级方案。

## 失败模式永久化

Case 有两个来源，第二个比第一个更有价值：

1. **预判**——对齐阶段对决策面行为的合成用例
2. **真实失败**——dogfood / 实际使用中系统在某决策面上的每次真实失败，**必须复现为该面 eval 的永久 case**：定位决策面 → 把失败的输入最小化为 dataset 样本 → 录制 → 在样本 notes 里记原始失败的日期和现象

**永久的含义：case 只增不删。** 修好之后这个 case 就是防它回归的唯一防线。例外只有一种：该 eval 的 capability claim 本身变更，且经 decision doc ratify。

预判 case 测的是"我担心它会错的地方"，失败 case 测的是"它真的错过的地方"——后者的回归概率有实证，覆盖增长应当由它驱动。

## 反模式清单（review 时逐条检查）

1. ❌ monolithic eval——多个决策面塞一个 dataset
2. ❌ 扩现有 eval 去 cover 别的决策面——拆新 consumer
3. ❌ 先建 framework 再想 dataset——substrate 建一次，之后缺的永远是 consumer
4. ❌ 按模块/服务名组织 eval——按决策面组织（同一个服务可能横跨多个面，混用必失真）
5. ❌ 一份 eval 同时回答多个 capability claim——拆分

## 特殊情形

- **跨模型比较**：不是"加几个 case"能解决的——judge 需第三方化、断言需与单一模型的语言风格解耦、N 需够大。单独开 decision doc 再做。
- **结果可疑时先怀疑 scorer**：字符串断言的 brittleness、judge 的 self-preference 都是已知噪声源。读 transcript 再下结论，不看原始输出不判 pass/fail。
