# Skill 创建与编写规范

本规范适用于本仓库下所有 skill，目标是让 skill 同时具备 4 个特性：

- 能被正确触发
- 能被长期维护
- 能被验证效果
- 能被稳定安装

本文综合了本仓库约束、官方 `skill-creator` 原则、`template-skill` 最小骨架，以及近期关于 evals / benchmark / trigger tuning 的实践。

## 1. 先判断要不要做成 skill

只有满足下面两个前提，才建议沉淀为 skill：

- 任务会重复发生
- “什么算做好”已经有相对稳定的标准

适合做成 skill 的场景：

- 团队会反复执行同一类流程
- 需要统一输出口径、结构或检查顺序
- 已经积累了明确的样例、禁区和边界
- 后续会持续维护，而不是一次性脚手架

不适合做成 skill 的场景：

- 一次性临时任务
- 连作者自己都还没有想清楚通过标准
- 每次都强依赖临场探索，几乎没有稳定流程

建议先判断 skill 类型，再决定写法和评测重点：

- `Capability uplift`：补强基础模型尚不稳定或尚不擅长的能力
- `Encoded preference`：把团队既有流程、顺序、口径和偏好编码进去

两类 skill 的维护重点不同：

- `Capability uplift` 需要经常验证“现在还值不值得存在”
- `Encoded preference` 需要经常验证“是否仍然忠实执行团队流程”

## 2. 仓库级规范

### 2.1 目录约定

所有 skill 必须放在 `skills/<skill-name>` 下，每个 skill 一个目录：

```text
/project-root
├── .agents
│   └── skills -> ../skills
├── .claude
│   └── skills -> ../skills
└── skills
    └── <skill-name>
```

强制要求：

- 只修改 `skills/` 目录
- 不直接修改 `.agents/skills` 和 `.claude/skills`
- skill 名称使用 kebab-case，例如 `git-worktree-helper`

### 2.2 Skill 目录标准结构

推荐结构如下：

```text
skills/<skill-name>/
├── SKILL.md
├── agents/
│   └── openai.yaml            # 可选，UI 元数据
├── references/               # 可选，按需加载的参考资料
├── scripts/                  # 可选，可执行脚本
└── assets/                   # 可选，输出要用到的资源
```

项目内统一采用这套命名，不再额外引入 `instructions/`、`examples/`、`tools/` 等平行目录，避免资料分散和心智负担。

额外约束：

- `SKILL.md` 是唯一必需文件
- 不要在 skill 目录内添加 `README.md`、`CHANGELOG.md`、`INSTALLATION_GUIDE.md`、`QUICK_REFERENCE.md` 等作者文档
- 作者规范、评测说明、维护记录统一放在仓库根目录的 `docs/`、`evals/` 等位置

### 2.3 README 同步要求

仓库根目录 [`README.md`](../README.md) 的 `## 技能列表` 必须与 `skills/` 目录保持同步：

- 每个 skill 都要有条目
- 标题需要带 emoji
- 条目内需要有指向 `./skills/<skill-name>/SKILL.md` 的链接
- 安装命令要保持可用

## 3. SKILL.md 编写标准

### 3.1 Frontmatter

项目标准 frontmatter 只保留最关键字段：

```yaml
---
name: your-skill-name
description: 用一句话说明这个 skill 做什么、何时触发、关键边界是什么。
---
```

要求：

- `name` 必须与目录名一致
- `description` 必须同时回答“做什么”和“什么时候用”
- 默认不要添加未被当前仓库消费的附加字段

说明：

- 某些第三方模板会加入 `visibility` 等字段，但本仓库不把它作为默认规范
- 触发依赖主要来自 `name` 和 `description`，因此描述质量比堆字段更重要

### 3.2 正文结构

`SKILL.md` 正文建议收敛为下面几类信息：

1. skill 的目标和适用范围
2. 明确的触发条件
3. 不应触发或不应执行的场景
4. 标准工作流程
5. 输出要求或结果判定标准
6. 安全边界、失败条件和停止条件
7. 资源导航：什么时候读取 `references/`、调用 `scripts/`、使用 `assets/`

推荐骨架：

```markdown
# Skill Title

一句话说明这个 skill 解决什么问题。

## 适用场景
- 用户提出了什么类型的请求
- 请求中通常会出现哪些关键词、对象或约束

## 不适用场景
- 哪些请求不应该触发
- 哪些情况只能先检查、不能直接执行

## 标准流程
1. 先做什么
2. 再做什么
3. 什么时候停止并暴露问题

## 输出要求
- 必须包含什么
- 必须避免什么

## 资源导航
- 读取 `references/...` 的条件
- 调用 `scripts/...` 的条件
- 使用 `assets/...` 的条件

## 安全规则
- 不允许做什么
- 遇到什么情况必须中止
```

### 3.3 写作要求

好的 `SKILL.md` 应该是“可执行说明”，而不是“面向人类宣传的产品介绍”。

建议：

- 用命令式、判断式语言，不写空泛口号
- 优先写具体约束、顺序、边界，而不是长篇背景介绍
- 对高风险步骤给出清晰 guardrails
- 对多分支流程写明“何时继续、何时停止、何时询问用户”

避免：

- 把第一版模板原样提交
- 用模糊词代替执行条件，例如“适当处理”“必要时优化”
- 把所有背景知识都塞进 `SKILL.md`
- 把它写成 FAQ、培训文档或 README

## 4. description 编写最佳实践

description 决定 skill 是否会在正确的时候触发。写得太宽，会误触发；写得太窄，会完全不触发。

### 4.1 好的 description 应包含的元素

- 任务动作：做什么
- 操作对象：处理什么
- 使用场景：何时会用到
- 关键限制：哪些前提或边界成立时才适用
- 常见别名：用户可能使用的术语、缩写、同义表达

### 4.2 编写原则

- 先写完整句，不要只写标签
- 直接复用用户会说的话，而不是只用作者自己的术语
- 包含高价值关键词，但不要堆关键词
- 把“不应该触发”的情况留给正文边界规则，不要把 description 写成一段杂乱清单

### 4.3 示例

较差：

```text
处理文档
```

较好：

```text
检查、重写和结构化整理长文档，适用于用户要求提炼章节结构、统一格式、补全缺失段落或按模板改写文档的场景。
```

## 5. 渐进式披露与资源拆分

默认假设模型已经足够聪明。只有“模型不知道、但这个任务必须知道”的信息，才值得放进 skill。

拆分原则：

- 高频核心流程留在 `SKILL.md`
- 大段规则、样例、领域资料放到 `references/`
- 需要稳定执行的动作写成 `scripts/`
- 最终输出要复用的模板、图片、字体等放到 `assets/`

推荐做法：

- `SKILL.md` 控制在 500 行以内
- 参考资料尽量保持“一跳可达”，即都从 `SKILL.md` 直接引用
- 不同领域、框架、提供方的细节拆成独立 reference 文件

示例：

```text
references/
├── trigger-examples.md
├── workflow-rules.md
├── aws.md
└── gcp.md
```

### 5.1 何时写脚本

满足下列任一条件时，优先放进 `scripts/`：

- 同一段代码被反复重写
- 执行顺序脆弱，靠自然语言容易出错
- 结果要求稳定、可重复
- 不希望每次都把大段实现加载进上下文

## 6. 与第三方模板的取舍原则

你给的 `template-skill-starter` 很适合作为“起步提醒”，但本仓库会做以下收敛：

- 保留它的“先有最小骨架、再补规则和资源”的思路
- 不直接采用其 `README / CHANGELOG / instructions / examples / tools` 目录方案
- 统一改为 `SKILL.md + references/ + scripts/ + assets/ (+ agents/)`

这样做的原因：

- 更贴近官方 `skill-creator` 的渐进式披露模型
- skill 安装后更轻，避免把作者文档打包给终端用户
- 目录命名更稳定，不会出现多套分类同时存在

## 7. 评测、benchmark 与回归流程

从 2026 年的 `skill-creator` 新实践开始，skill 不应该“只生成不验证”。

### 7.1 建议的评测资产位置

建议把评测资产放在仓库根目录，而不是 skill 目录内：

```text
evals/<skill-name>/
├── prompts.md
├── fixtures/
└── notes.md
```

这样可以避免把作者侧测试数据一起打包进 skill。

### 7.2 每个 skill 至少要覆盖的 3 类 case

- 正常路径：最典型、最常见的成功案例
- 边界案例：容易出错、容易歧义、资料不完整的案例
- 触发案例：该触发与不该触发的 prompt 样本

### 7.3 评测重点

对每次迭代，至少关注：

- 通过率是否提高
- 耗时是否明显变长
- token 开销是否明显上升
- skill 是否在该触发时触发、在不该触发时保持安静

### 7.4 推荐工作流

1. 先生成或修改 skill 第一版
2. 写一组 evals，明确什么叫通过
3. 跑 benchmark，记录通过率、耗时和 token
4. 对 `skill vs no-skill` 或 `旧版 vs 新版` 做 A/B 对比
5. 模型升级、description 变更、关键流程调整后，复跑同一组 evals

### 7.5 维护判断

当 `Capability uplift` skill 出现下面情况时，应考虑弱化甚至删除：

- 基础模型在不加载 skill 的情况下也能稳定通过 evals
- 加载 skill 后收益不明显，但成本更高

当 `Encoded preference` skill 出现下面情况时，应优先修复：

- 输出仍然“能做事”，但已经偏离团队流程
- 新 prompt 不再稳定触发
- 团队 SOP 已变，但 skill 规则未同步

## 8. 提交前检查清单

- skill 真的值得沉淀，而不是一次性任务
- 目录位于 `skills/<skill-name>/`
- `name`、目录名、README 技能列表条目一致
- `description` 同时说明了做什么和何时触发
- `SKILL.md` 写清楚了适用场景、边界、流程和输出要求
- 详细资料已拆到 `references/`，没有无关作者文档塞进 skill
- 需要稳定执行的逻辑已经脚本化，而不是反复手写
- 至少准备了正常路径、边界路径和触发路径的评测样本
- 修改 skill 后，README 的 `## 技能列表` 仍然准确

## 9. 最小结论

本仓库的标准可以概括成一句话：

> 先确认任务值得沉淀，再用最小骨架写清触发、流程、边界和输出，最后用 evals / benchmark 把“感觉可用”变成“有证据可用”。

## 参考

- <https://skillsclaude.com/templates/template-skill-starter>
- <https://skillsclaude.com/guides/skill-creator>
- <https://skillsclaude.com/guides/skill-creator-evals-benchmark>
- <https://claude.com/blog/improving-skill-creator-test-measure-and-refine-agent-skills>
- <https://github.com/anthropics/skills/tree/main/template>
