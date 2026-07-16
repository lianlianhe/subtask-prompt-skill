# subtask-prompt-skill

> 将一次性提示词改写为「委派子任务线程 + 主线程持续看护验证」结构化提示词的 Skill。

[![Release](https://img.shields.io/github/v/release/lianlianhe/subtask-prompt-skill)](https://github.com/lianlianhe/subtask-prompt-skill/releases)

---

## ⚠️ 使用前提

> **本项目是纯提示词（Prompt）层面的 Skill，不包含任何运行时代码。**

使用本 Skill 需要满足以下前提：

1. **目标 Agent 必须原生支持子 Agent（Sub-agent）能力。**
   典型的支持示例：[Codex CLI](https://github.com/openai/codex)、[Claude Code](https://docs.anthropic.com/en/docs/claude-code) 等具备"委派子任务线程并异步执行"的 Agent 框架。
   若所使用的 Agent **不支持**子 Agent，本 Skill 的委派-看护结构将退化为普通串行对话，无法发挥预期效果。

2. **Token 消耗显著高于单次提问。**
   采用"委派 + 持续攻克 + 看护验证"三段式结构后，每个任务至少涉及：
   - 主线程初始规划 + 子任务下发
   - 子线程多轮执行（每轮含上下文回放）
   - 主线程逐步核查并写 Babysitting note

   实际 Token 消耗通常是一次性提问的 **3～10 倍**，复杂任务可更高。
   请根据所用模型的计费标准提前评估成本，**不建议在 Token 预算极度受限的场景下使用**。

---

## 一、产品介绍

**subtask-prompt-skill** 是一个 Prompt 工程 Skill。它把用户常见的"一次性提问式"提示词，自动改写成一种更可靠的三段式结构：

1. **委派 (Delegate)**：主线程为另一个子线程撰写一个清晰的 GOAL；
2. **持续攻克 (Persist)**：子线程主动追求该目标、持续攻克障碍，直到产出"可验证的交付物 (verified deliverable)"；
3. **看护验证 (Babysit & Verify)**：主线程逐步核对子线程产出，写 "Babysitting note"，未达标就驱动其重做，直到全部通过验收。

### 解决什么问题

- 一次性提问往往得到"看起来完成"的敷衍答案，缺乏验证；
- 复杂任务需要多轮试错、规划与质量把关；
- 缺少明确的"完成定义 (Definition of Done)"和数据来源约束。

通过引入子任务委派与主线程看护，为任务加入**内置引导 (built-in steering)** 与**额外一层质量/品味验证 (taste verification)**，显著提升复杂任务的可靠性。

### 适用场景

- 任务链条长、技术障碍多，一次对话难以完成；
- 需要反复验证正确性（如底层解析、数据管线、复杂重构、投研分析）；
- 需要"规划—执行—验证"闭环，而非一次性生成。

### 目录结构

```
subtask-prompt-skill/
├── README.md                         项目说明（本文）
├── SKILL.md                          Skill 定义：用途 / 触发场景 / 核心原则 / 步骤 / 注意事项
├── templates/
│   └── subtask_prompt_template.md    可复用的"委派+看护验证"提示词模板
└── examples/
    └── example_fund_analysis.md      基金持仓分析的改造前/后示例
```

---

## 二、部署方式

本 Skill 是纯文本/Markdown 资产，无需编译或运行时依赖。任选以下一种方式部署。

### 方式 A：下载 Release 压缩包（推荐）

1. 打开 [Releases 页面](https://github.com/lianlianhe/subtask-prompt-skill/releases)；
2. 下载最新版本的 `Source code (zip)`：
   - 直链：`https://github.com/lianlianhe/subtask-prompt-skill/archive/refs/tags/v1.0.0.zip`
3. 解压后，将 `SKILL.md` 提供给支持 Skill 的 Agent，或直接复用 `templates/` 下的模板。

### 方式 B：git clone

```bash
git clone https://github.com/lianlianhe/subtask-prompt-skill.git
cd subtask-prompt-skill
```

### 方式 C：接入支持 Skill 的 Agent

1. 将整个目录放入 Agent 的 skills 目录（具体路径依你使用的 Agent 平台约定）；
2. Agent 读取 `SKILL.md` 的 frontmatter (`name` / `description`) 完成注册；
3. 当用户任务匹配触发条件时，Agent 依据 `SKILL.md` 的步骤，套用 `templates/subtask_prompt_template.md` 生成结构化提示词。

### 快速使用

打开 `templates/subtask_prompt_template.md`，将 `=== GOAL ===` 等占位段替换为你的具体任务即可。可参考 `examples/example_fund_analysis.md` 的改造前/后对照。

---

## 三、卸载方式

### 若通过 Release/zip 部署

直接删除解压出的 `subtask-prompt-skill/` 目录即可，无残留。

### 若通过 git clone 部署

```bash
rm -rf subtask-prompt-skill
```

Windows PowerShell：

```powershell
Remove-Item -Recurse -Force subtask-prompt-skill
```

### 若已接入 Agent

1. 从 Agent 的 skills 目录中移除本 Skill 目录；
2. 若平台有 Skill 注册表/缓存，按平台文档执行注销或刷新，使 `subtask-prompt-rewriter` 不再被加载。

卸载后不会影响系统其他部分——本 Skill 不写入任何全局配置、不安装依赖、不创建后台进程。

---

## 四、版本

- **v1.0.0**：首个版本，包含 Skill 定义、提示词模板、基金分析示例。

## 五、免责声明

本 Skill 用于提升提示词质量。其生成的分析类内容（如投资、医疗等）仅供参考，不构成专业建议；涉及资金、发布或不可逆操作时，请保留人工最终审查。
