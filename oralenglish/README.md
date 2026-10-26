# oralenglish — TEM-4 口语思辨陪练 Skill

一个开源的 AI Skill（纯指令、零依赖），为英语专业四级（TEM-4）口语考试提供**一轮一轮**的英文思辨训练。加载到支持 Skills 的 AI Agent 后，它就是一个随时在线的口语思辨陪练伙伴。

> 界面内容全英文（话题、参考句型、转写、优化句、点评），仅结构标签使用固定中文格式（①【我的原句】②【优化句】③【简短点评】及选项【继续】【重新回答】）。

## 目录结构

```
oralenglish/
├── SKILL.md            # Skill 主文件：工作流、输出格式、改写规则、选项处理
└── agents/
    └── openai.yaml     # OpenAI Agents 界面元数据（display_name / short_description / default_prompt）
README.md               # 本文件（项目说明）
```

---

## 1. 项目解决什么问题

TEM-4 口语考试要求学生就一个英文话题即时**口头论述**，考查的不仅是词汇语法，更是**思辨能力和自然的口语表达**。中国考生常见痛点：

- **中式英语顽固**：中文直译语序、中式搭配（如 "learn knowledge"、"do contribution to"），自己往往意识不到，或即使察觉也不知道怎么改。
- **缺人陪练**：口语需要即时对练和反馈，多数人没有稳定的英语对话伙伴，自己练习没有针对性反馈。
- **反馈不落地**：背模板、查字典只能得到"看起来对"的句子，缺少针对逻辑、句式、用词地道度的逐项诊断。
- **练习没有延续性**：只练单一话题、没有深入追问，难以形成完整的论述能力。

本项目解决的问题：提供一个**可加载到任意支持 Skills 的 Agent 上的口语陪练工作流**——它自动出话题、给句型引导、转写你的口头作答、产出一版去掉中式英语的优化句、点评本次问题，并支持同话题深度追问或重新作答，让每一次练习都有明确、可改进的反馈。

## 2. 主要功能

- **逐轮训练**：每轮先给出一个可辩论的英文 **Topic**，再给出**两句参考句型**（一句肯定立场、一句否定立场，纯英文），随后等待你口头作答。
- **原样转写**：把你的口述英文**原样转写**（①【我的原句】），保留原始表达与错误，仅纠正明显的听写错漏，不改语法措辞。
- **中式英语优化**：产出一版适配专四口语的**优化句**（②【优化句】）。优先修正中式英语——中文直译语序、中式搭配、逻辑断层、母语负迁移；**拒绝浅层同义词替换**；优化句式逻辑；**核心改动加粗**；适配口头表达、不过度书面化。
- **简短点评**：点明本次的中式英语问题，并简述逻辑、句式、用词地道度情况（③【简短点评】）。
- **两种继续方式**：
  - 【继续】——基于当前话题**深度追问**（质疑前提、要反例、深挖理由、延伸后果），开启本话题下新一轮作答，并再给两句新参考句型。
  - 【重新回答】——**不换新话题**，等待你再次作答，并再次对新回答做转写、优化、点评，循环直至你输入【继续】。

## 3. 安装方法

本项目是**纯指令型 Skill**，不包含任何脚本，因此**无需安装 Python 依赖**，只需将 `oralenglish/` 文件夹放到目标 Agent 能识别到的 Skill 根目录即可。

**方式一：复制文件夹（通用）**

把整个 `oralenglish` 文件夹复制到所用 Agent 的 Skill 目录，保证最终路径为：

```
<skill-root>/oralenglish/SKILL.md
<skill-root>/oralenglish/agents/openai.yaml
```

常见 Skill 根目录示例：

- **OpenAI Agents SDK / Codex**：`<项目>/.agents/skills/` 或 SDK 的 `skills/` 目录；
- **豆包（Doubao）等桌面工作区**：`workspace/.user_skills/` 目录。

**方式二：克隆仓库（发布后）**

将来若发布到 GitHub，可执行 `git clone <repo-url>` 后，把仓库中的 `oralenglish/` 目录软链接或复制到上述 Skill 根目录。

> 验证是否装好：检查 `oralenglish/SKILL.md` 是否存在，并用下列命令校验结构（需 Python 与 PyYAML）：

```
python scripts/quick_validate.py oralenglish
```

## 4. 使用方法

加载 skill 后，在对话里直接发起练习，例如：

> 用 oralenglish 开始一轮专四口语思辨训练。

之后按以下循环进行：

1. **等 AI 出题**：AI 给出英文话题 + 两句正反参考句型。
2. **口头作答**：参考句型，就话题用英文口头作答（在对话中把说的话以文字形式录入）。
3. **看反馈**：AI 输出 ①【我的原句】②【优化句】③【简短点评】。
4. **选择方向**：
   - 输入 **【继续】** → AI 基于当前话题深度追问，开启新一轮作答；
   - 输入 **【重新回答】** → AI 保持话题与句型不变，等你再次作答并重新反馈，可反复循环。
5. 重复 1–4，持续训练。

## 5. 输入输出示例

**输入（AI 出题 + 用户口头作答，模拟）：**

> 话题：Online learning is less effective than classroom learning.
> 参考句型（示例，AI 给出）：Pro: "While it is true that ___, I would argue that ___ because ___."　Con: "Admittedly ___, but in my view the ___ outweighs the ___."
> 我的回答：In my opinion, online learning is not less effective. Because I can learn knowledge by myself at home, and the teacher can give me many good advices in the internet.

**输出（AI 反馈）：**

```
①【我的原句】
In my opinion, online learning is not less effective. Because I can learn knowledge by myself
at home, and the teacher can give me many good advices in the internet.

②【优化句】
I'd argue online learning can be just as effective as classroom learning: I can **pick things up
on my own** at home, and teachers can give me **a lot of useful advice** **online**.

③【简短点评】
Main issue this time: Chinese-style collocations — "learn knowledge", "many good advices", and
"in the internet". Logic is clear (one concession + two reasons) but "Because..." starts a fragment;
sentence structure would read better if the first two ideas were linked. Word choice is a bit
translated and needs more natural, spoken verbs.
```

**输出（选项）：**

```
【继续】  【重新回答】
```

---

## 暂不支持

- 网页抓取 / 爬虫
- PDF 及文件解析
- 图形界面 / 浏览器自动化
- 本 Skill 仅做文字对话

## License

MIT
