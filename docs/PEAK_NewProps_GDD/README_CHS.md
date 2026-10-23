# 《PEAK》双道具 Unity AI Agent 开发交付包

**内容类型：需求基线 + Agent Prompt + CLI/Skill/AGENTS 配置 + 测试工具；不包含已完成的道具代码。**

## 包内文件

| 文件 | 用途 |
|---|---|
| `PEAK_NewProps_GDD_V1.0.md` | 已确认的玩法详细设计案（唯一设计数字与规则基线） |
| `01_AI_Agent_Prompt.md` | 可以直接交给 Codex 的编码主任务（分 Phase 0–6） |
| `02_AI_Setup_Codex_CLI_Skill_AGENTS.md` | Windows/CLI 安装、工程集成、安全操作、Unity 测试命令 |
| `03_AI_AcceptanceTests.md` | QA-D/S/I/M/U 测试分类与缺失依赖清单 |
| `AGENTS.md` | 与旧 v16 AGENTS 合并的**参考完整稿**，不推荐直接覆盖已有 AGENTS |
| `AGENTS_PEAK_ITEMS_APPEND.md` | **推荐**追加到现有项目 `AGENTS.md` 的道具任务补充规则 |
| `.agents/skills/peak-items-unity/SKILL.md` | Repository 内可复用的 Unity 双道具研发工作流 |
| `Scripts/Run-UnityTests.ps1` | 从 PowerShell 调用目标 Unity Editor 运行 EditMode/PlayMode 测试 |

## 对你现有工程的安全安装步骤

1. 打开 Unity 保存 `ClimbingTest`，确认 `git status` 和已有修改已经妥善备份，退出 Play Mode。
2. 从此包中**仅合并** `.agents/skills/peak-items-unity/` 至 Unity 根目录，**不要复制覆盖**任何现有 `Assets/Scenes` 或 `Assets/Scripts`。
3. 将 `AGENTS_PEAK_ITEMS_APPEND.md` 的内容合并到现有的 `AGENTS.md`，保留旧场景/物理/镜头保护规则；完整 `AGENTS.md` 仅作为参考合并示例。
4. 可选地复制 `Scripts/Run-UnityTests.ps1` 到根目录 `Scripts/`。这是开发工具目录，**不是** Unity 的 `Assets/Scripts/`。
5. 安装或升级 Codex CLI：`npm install -g @openai/codex@latest`。进入 Unity 工程根目录运行 `codex`，按提示登录。
6. 打开 `Docs/02_AI_Setup_Codex_CLI_Skill_AGENTS.md`，将其中“第一条命令”粘贴到 Codex。**Phase 0 一定先审计当前 Repository**，因为旧项目可能没有完整的 Backpack、篝火、天气和多人代码。

## 这次任务的三条红线

- 不能因为新道具而破坏用户已调整的 ClimbingTest 场景、Mini Peak、镜头、现有抓附与体力逻辑。
- 不能把缺少的真实 PEAK 内部系统“猜”出来；单机替身与实际多人/完整天气必须分别标记。
- 不能把“C# 已输出”当作“功能已通过 Unity Editor 实测”。

**正确开发目标**：在当前 Unity Prototype 中完成两件道具的可玩闭环、配置与 QA 测试；为真实 PEAK 风格完整游戏可能存在的网络和天气系统留下规范接口，而不是冒充在官方游戏中已上线。
