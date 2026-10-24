# AI 编码 Setup｜Unity 6 ClimbingTest 双道具工程

## 1. 目标与技术栈

推荐 **Codex CLI（本机） + Git + 根目录 AGENTS.md + 仓库级 Skill + Unity Editor / Unity Test Framework**。Codex 负责检查代码、修改 C#、生成幂等 Editor 工具、运行已安装的测试并写报告；Unity Editor 负责验证场景、组件资源、实际物理反馈和操作手感。

本套包只提供**开发指令、设计文档和测试命令模板**，不包含已经实现的两件道具 C# 游戏功能。Agent 需在你的 Unity 项目目录内执行主 Prompt 才会产生代码。任何运行和测试结论取决于本机实际环境。

## 2. 本机准备（Windows PowerShell）

前置：Unity Hub + 与 `ProjectSettings/ProjectVersion.txt` 相同的 Unity Editor（目前是 6.3 LTS (6000.3.27f1)，但以仓库文件为准）、Git、Node.js/npm、任选 VS/VS Code/Rider、可用 Codex 登录方式。

```powershell
node --version
git --version
npm --version
npm install -g @openai/codex@latest
codex --version
```

登录方式以当前 CLI 提示为准；不要将 API Key/个人令牌粘贴进仓库、Prompt、AGENTS.md 或共享日志。

先保存并退出 Unity Play Mode，在工程**根目录**（含 `Assets`/`Packages`/`ProjectSettings`）执行：

```powershell
cd '$root\PhysicsPrototype'  # 改成你的真实项目根目录
Get-Content '.\ProjectSettings\ProjectVersion.txt'
git status --short
# 先保护既有未提交改动，必要时手工备份/提交 checkpoint
git switch -c feature/peak-dryer-suction
codex
```

已存在同名分支时换一个分支名；如存在未提交改动，先保存/提交或建立独立工作树，**不要**使用 `git reset --hard` / `git clean -fd`。

## 3. 把交付文件合并到当前项目，而不是覆盖现有内容

把 `Docs` 文件夹复制到项目根目录（与 `Assets` 同级）。在 `.agents/skills/` 中增加 `peak-items-unity/SKILL.md`。与旧的 `unity-climbing-prototype` Skill 可同时存在；不要改动用户现有其他 Skills。

**AGENTS.md**：如果项目已有 AGENTS.md（你之前 v16 应该已有），优先把 `AGENTS_PEAK_ITEMS_APPEND.md` **追加合并**到旧文件，而不是复制本包的 `AGENTS.md` 直接覆盖。包里的完整 `AGENTS.md` 是包含 v16 约束的参考合并稿，不一定与当前本地版本完全相同。

推荐结构：

```text
YourUnityProject/
├── AGENTS.md                     # 用“追加规则”合并，别覆盖旧规则
├── .agents/
│   └── skills/
│       └── peak-items-unity/
│           └── SKILL.md
├── Docs/
│   ├── PEAK_NewProps_GDD_V1.0.md
│   ├── 01_AI_Agent_Prompt.md
│   ├── 02_AI_Setup_Codex_CLI_Skill_AGENTS.md
│   └── 03_AI_AcceptanceTests.md
├── Assets/
│   ├── Scripts/             # Agent 按审计结果增量创建道具代码
│   ├── Editor/              # 幂等场景/测试区创建工具
│   ├── Scenes/              # 保留原场景及用户改动
│   ├── Audio/
│   └── Tests/               # EditMode / PlayMode 测试
├── Packages/
├── ProjectSettings/
└── ...
```

**不要**通过复制 `.unity`/`.prefab` 的旧版本去覆盖手动调整过的资源。`namespace PhysicsPrototype` 是代码命名空间，可保留。

## 4. 发给 Codex 的第一条命令

```text
请使用仓库本地 peak-items-unity Skill，先逐一读取：
1. AGENTS.md
2. PEAK_NewProps_GDD_V1.0.md
3. 01_AI_Agent_Prompt.md
4. 02_AI_Setup_Codex_CLI_Skill_AGENTS.md
5. 03_AI_AcceptanceTests.md
5. .agents/skills/peak-items-unity/SKILL.md

然后严格按照主 Prompt 从 Phase 0 做 Repository 审计，在 Docs/ITEMS_TECH_AUDIT.md 标记已有/缺失的输入、体力、库存、天气、篝火、网络能力；保护所有已手动编辑的 ClimbingTest、Mini Peak、相机和资源引用。
审计后继续按 Phase 1–6 实际修改工程，完成两件道具编码、测试场景、EditMode/PlayMode 测试、回归和交付报告。除非明确属于技术阻碍，不要只输出建议而不修改代码。所有无法实测的项目如实标记 NOT RUN；如果无网络包，多人集成须标为 NOT IMPLEMENTED。
```

如果希望逐阶段可审查，可以将 Phase 0 单独运行、检查审计报告，然后在 Codex 输入“从 Phase 1 开始执行，使用相同 GDD、AGENTS 与 Skill；每阶段结束生成测试报告并作一次 Git commit”。正式需求在 GDD；不要在聊天提示中临时改掉数值。

## 5. Codex 权限、编辑策略与 Unity 风险

- 尽可能使用需要用户批准的命令/编辑权限模式，不采用危险的 unrestricted/full-access 模式处理重要 Unity 仓库。
- 用户的 Scene Transform/Materials/Prefab Inspector 参数优先于此前版本的历史默认参数。
- 改动前后都执行 `git diff --stat`、`git diff --check`、`git status --short`；检查 `Assets/**/*.meta` 未被无意删除或批量重建。
- 从 C# 修改后先解决编译问题，再安装或修改场景；不要让 Unity Editor 场景编译错误长期存在。
- 创建新增测试场景或向现有场景**新增一个独立根对象组**时，必须用幂等 Editor 脚本。再次运行不能复制多份、不能重建用户已有场景。优先遵守 Unity 序列化字段，改名时迁移旧字段引用。
- 外部工具不能直接打开 Play Mode 时，Agent 要输出可复现的手动验证步骤/自动测试脚本，而不是假装执行了。

## 6. Unity Test Framework 测试命令

首先核实 `Packages/manifest.json` 是否已有相应 Test Framework；不存在时先按项目依赖规范启用。关闭占用**同一工程**的 Unity Editor 后运行。下面是 Windows PowerShell 示例，**路径须替换为实际 Editor 和项目路径**：

```powershell
$UnityExe = 'C:\Program Files\Unity\Hub\Editor\6000.3.0f1\Editor\Unity.exe' # 示例，以项目实际情况为准
$Project = 'D:\UnityProjects\PhysicsPrototype'                            # 示例，以项目实际情况为准
$Results = Join-Path $Project 'TestArtifacts'
New-Item -ItemType Directory -Force $Results | Out-Null

& $UnityExe -batchmode -projectPath $Project -runTests `
  -testPlatform EditMode `
  -testResults (Join-Path $Results 'EditMode.xml') `
  -logFile (Join-Path $Results 'EditMode.log')

& $UnityExe -batchmode -projectPath $Project -runTests `
  -testPlatform PlayMode `
  -testResults (Join-Path $Results 'PlayMode.xml') `
  -logFile (Join-Path $Results 'PlayMode.log')
```

注意：这里不主动添加 `-quit`，因为运行 `-runTests` 的 Editor 通常在测试结束后自行退出；`TestArtifacts/*.xml` 和 `.log` 应实际存在并由 Agent 解析失败项。不能仅凭进程退出码断言测试通过。包内还有 `Scripts/Run-UnityTests.ps1` 供在正确路径配置后快速运行单类测试。

**实际需执行的人工回归**：手持吹风机瞄准干/湿岩、连续 LMB、雨天 20s 倒计时、内外角与薄墙背面、充能后交换物品、吸盘竖墙/倒悬/转角、15→0 最后会话、E/Q/Tab/Backpack、普通攀爬与既有 Q/T 道具、Start/Pause/Resume、镜头不翻转。

## 7. 推荐交付和记录格式

Agent 最终必须留下：

- `Docs/ITEMS_TECH_AUDIT.md`：代码/Scene/网络/天气/库存状况与差距；
- `Docs/ITEMS_IMPLEMENTATION_STATUS.md`：按 P0/P1、QA-ID 对齐的实现状态；
- `Docs/ITEMS_TEST_REPORT.md`：Unity 版本、运行命令、测试数/通过/失败、XML/日志路径、人工验证与未运行项；
- `Docs/ITEMS_CHANGELOG.md`：每阶段变更文件、数据迁移、Scene 影响和回滚方式；
- 完整的 C#、asmdef（如果需要）、两个道具 Prefab、测试场景/增量测试区、基础原创占位美术与音效、配套 README。

## 8. 常见误区

- **没有官方 PEAK 游戏源码**：只能做 PEAK 风格 Unity 原型，不能承诺把道具集成进官方客户端。
- **项目 v16 没有雨水/篝火/Backpack**：这些需通过适配层或最小可用替身完成原型功能；确实缺的完整游戏系统要写明，不能当作已集成。
- **复杂 Mesh 的 7x7 连通面积**：项目可先在合法的规则面片模型上验证，再对不规则 Mesh 扩展精度。未经真实几何测试不能宣称任意地形都通用。
- **15 次吸附 ≠ 15 次帧更新**：成功创建一次会话才扣一次，并保留恢复预算。最后一次有效会话完成后才销毁。
- **补丁和测试结果**：生成代码不代表已经通过 Unity 编译与 Play Mode。界面、镜头、声音和物理手感必须在目标 Unity 环境验证。

## 参考文档（用于确认工具设置，不是玩法需求）

- Codex CLI：https://help.openai.com/en/articles/11096431
- Codex AGENTS + repository skills：https://developers.openai.com/blog/skills-agents-sdk
- Unity 6.3 命令行运行测试：https://docs.unity.com/en-us/engine/6000.3/manual/programming-environment/test-framework-introduction/running-tests/run-tests-from-command-line
- Unity Test Framework 参数：https://docs.unity.com/en-us/engine/6000.7/manual/programming-environment/test-framework-introduction/reference-command-line（使用时以项目包版本为准）
