# Brain Skill ｜ 长任务恢复与证据纪律

> 一个给 AI 编码助手用的「大脑」技能：跨会话、跨模型、跨上下文压缩之后，仍能恢复目标、核对证据，不把计划写成现状。
>
> **English**: `brain` is an evidence-first recovery skill for long-running AI coding sessions (DeepSeek Harness / Claude Code / Codex). It keeps a small always-loaded entry, a self-contained authority with 29 rules, and a generated discipline index with incident provenance.

---

## 它解决什么问题

长任务真正的断点往往不是模型能力，而是三件事同时发生：

- 上下文被压缩或换了模型，主线、失败路线、当前状态丢了；
- agent 声称「已完成」，但交付物、来源、校验、索引对不上；
- 纪律只写在对话里，下一次会话读不到，于是同一个错误反复出现。

`brain` 把这三件事固化成可执行条款：入口层负责每轮抓手，权威层定义规则，纪律层记录每条规则的来源事故。规则不依赖会话记忆成立。

## 三层结构

| 层 | 文件 | 作用 |
|---|---|---|
| 入口层 | `brain/SKILL.md`（约 4 KB） | 宿主实际加载的最小抓手：有界恢复、不可省略条款、按触发读参考 |
| 权威层 | `brain/BRAIN_PROTOCOL.md`（约 42 KB） | 29 条 U 规则 + §9 变更程序 + §10 自检入口 + §11 门控口径 |
| 纪律层 | `brain/references/DISCIPLINES_CORE.md`（约 896 KB） | 生成的纪律索引：规则出处、事故记录、各项目原文附录 |
| 条件参考 | `brain/references/OPERATIONS.md`（约 39 KB） | 长任务、子代理、校验、算法排查等条件细则 |
| 条件参考 | `brain/references/ASSET_RECOVERY.md`（约 5 KB） | 资产驱动恢复与收尾的 U29 操作契约 |

入口只放指针与最优先条款；大文件按触发读相关 ID，不默认全文灌入。

## 安装

### DeepSeek Harness

```powershell
git clone https://github.com/yjhd91/brain-skill.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.dsh\skills" | Out-Null
Copy-Item -Recurse -Force .\brain-skill\brain "$env:USERPROFILE\.dsh\skills\brain"
Test-Path "$env:USERPROFILE\.dsh\skills\brain\SKILL.md"   # 应为 True
```

### Claude Code

```bash
git clone https://github.com/yjhd91/brain-skill.git
mkdir -p ~/.claude/skills
cp -R brain-skill/brain ~/.claude/skills/brain
ls ~/.claude/skills/brain/SKILL.md
```

Linux / macOS 上的 DeepSeek Harness 把目标目录换成 `~/.dsh/skills/brain` 即可。

已经装过旧版时，先备份或手工合并，不要直接覆盖；`SKILL.md` 与 `BRAIN_PROTOCOL.md` 必须放在同一目录，入口里的权威指针是相对路径 `./BRAIN_PROTOCOL.md`。

## 什么时候触发

- 多轮实质工作，任务会跨上下文压缩、换模型或换会话；
- 需要核对某个结论、文件、数字是否真的落盘、可追溯；
- 要修改纪律本身（新增规则、修订条款、处理旧条款退役）；
- 长跑批、子代理、无人值守任务需要状态载体与验收纪律。

普通概念问答不触发扫描，也不写盘。

## 使用方式

装好技能后，在会话开始时让 agent 加载 `brain`。入口会要求先完成四步：

1. 声明目标绝对 root、当前 cwd、记忆绑定，三者不得互相推定；
2. 有界读取活跃目录的主线、状态、缺口与失败线索（热读总计 ≤ 6000 字符）；
3. 区分 `OFFLINE` / `MISSING` / `STALE`，证据不足时拒绝依赖该资产的后续动作；
4. 有变更时走完整链路：原件与 `produced_by`/hash/校验 → 既有扫描 → 台账 → 检索索引 → 逐项核对显式 changed paths → brief 与门控。

核心判据包括：没有反例的检验不算检验；零个被测对象必须 FAIL；图形交付必须打开看并量数；不许为通过判据而毁掉交付物；计划不得写成现状。

## 公开导出版说明

本仓由私有 `brain_core` 仓导出。为了让内容可公开、可移植，做了三类改动：

1. 本机绝对路径替换为占位符（见下表）；
2. `SKILL.md` 的权威指针改为 `./BRAIN_PROTOCOL.md`，§3 的同步/部署说明改为公开版口径；
3. 未包含 `sync_config.json`、`code/86`、`code/1300`、`_inherited/`、`outputs/audit/` 等本机运行件。

因此：本仓可以独立阅读、安装、复用规则；但私有源仓的哈希门 / `PROTOCOL_GATE: PASS` 结论不自动适用于本公开导出版。

### 占位符

| 占位符 | 含义 |
|---|---|
| `$BRAIN_CORE` | 权威协议仓根目录（本仓对应 `brain/`） |
| `$AORTA_PROJECT` | 规则来源项目（私有，不公开） |
| `$PROJECTS` | 项目根目录 |
| `$DATA_ROOT` / `$DATA_DRIVE` | 大数据工作盘及其根目录 |
| `$TMP_ROOT` / `$TEMP` | 临时目录 |
| `$TOOLS_ROOT` / `$TOOLS` | 工具安装目录 |
| `$VENV` | Python 虚拟环境 |
| `$DEID_ROOT` / `$PHI_QUARANTINE` | 脱敏输出与 PHI 隔离目录（示例） |
| `~` | 用户主目录 |

这些是示例占位符，不是环境变量；阅读时按自己的机器替换即可。

## 来源

- 规则来自作者在主动脉数字孪生（aorta digital twin）项目中的长期无人值守运行，由 `ULTIMATE_PROTOCOL.md` 去项目化而来，固化为 29 条 U 规则。
- `DISCIPLINES_CORE.md` 同时收录 U 层规则与 K/A/E/L/P/D/S/W 事故索引，每条规则可回溯到具体事故。
- 本仓只保留方法与规则，不包含患者影像或标识数据，也不包含项目源代码与数据文件。

## 边界

- 它不能保证模型遵守规则；只能保证「宿主真的加载时，规则与证据链在场」。
- 它不是记忆系统，不替代项目内的权威原件；项目事实仍以项目原件为准。
- 它不是论文方法学，也不提供医学结论；请勿把纪律条款当作临床或统计判据。

## License

MIT，见 [LICENSE](LICENSE)。
