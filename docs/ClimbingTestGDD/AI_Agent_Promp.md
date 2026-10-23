# 给 AI Agent 的增量任务说明

现有 Unity 6 ClimbingTest 工程已有 v16 系统。现在正式使用以下策划案作为唯一玩法需求基线：

`Docs/01_ClimbingTest_3C物理攀爬系统_详细设计案_V1.0.md`

请执行：

1. 阅读 `AGENTS.md`、相关 Skill、当前场景与脚本，再完整阅读上述详细设计案。
2. 检查旧 GDD/Prompt 与此文档的冲突，整理 `Docs/需求差异清单.md`，注明已定、建议、待评审、待实机验证，**先报告冲突，不直接更改玩法**。
3. 按策划案 AC-01 至 AC-17 检查功能。针对每条，提供当前实现位置（脚本、函数、场景对象）、运行记录/截图路径、结果（通过/失败/未验证）。
4. 将需求拆为 DEV-01 至 DEV-09 工作任务，P0 优先。所有方案需先确认对 Scene 及已保存 Inspector 参数的影响。
5. 不添加新道具、机制或玩法；不得重新生成或覆写现有 ClimbingTest、Mini Peak、已移动墙体、Inspector 参数及资源 GUID；保留 ActiveRagdollTest。
6. 所有资源路径以工程实际结构为准，禁止硬编码 `Assets/PhysicsPrototype/`；音频位于 `Assets/Audio`。
7. 只有实际执行 Unity 编译、Play Mode、手工/自动测试之后才写“已通过”；单靠源码分析写“未验证”。
8. 每轮提交输出：改动文件、设计依据、操作及测试证据、已知问题和 Git 回滚提交。
