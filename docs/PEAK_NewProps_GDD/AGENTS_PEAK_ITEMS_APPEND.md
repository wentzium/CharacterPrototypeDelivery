# Append to the existing repository AGENTS.md — PEAK-style item development

> 合并说明：本段是针对已批准的「便携烘干机与吸盘」新增任务的补充，不应删除原 AGENTS.md 对 ClimbingTest、镜头、Rigidbody、场景保留和 Unity 测试的规则。如果原文件有“不得添加新道具”条款，则以本次用户批准的**仅新增这两件**为明确例外；其他道具仍不得增改。

## Item feature scope and contract

- Before coding, read `Docs/PEAK_NewProps_GDD_V1.0.md` and `Docs/01_AI_Agent_Prompt.md` and use `.agents/skills/peak-items-unity/SKILL.md`.
- Only implement the approved PortableRockDryer and SuctionGrip in the current Unity ClimbingTest. Do not implement the abandoned trampoline, invent extra mechanics, or assume direct access to PEAK proprietary source code.
- Fixed design values: Dryer weight 5, one slot, max/initial 3, area 7m x 7m across connected rock surfaces, dry duration 20 sec, campfire recharge +2 capped at 3 only at 1–2 uses and once per item-ID x campfire-ID. Suction weight 3, one slot, max/initial 15 successful sessions, cumulative stamina recovery cap +20 per session while stationary; movement continues to use regular climbing stamina costs.
- Dryer surface propagation must traverse actual connected surface patches, not pass through thin walls or air gaps; max effective area about 49m². Timed dry overrides must hand control back to weather. No invented permanent wetness changes.
- LMB is context-sensitive: barehand = ordinary climbing; dryer held = one valid shot per press; suction held = initiate/maintain one valid continuous attachment session. An invalid action never consumes an item use. Suction's last session survives its final charge until release.
- E pickup, Tab regular-slot storage, Backpack via existing UI, Q tap drop / hold throw. Preserve current input bindings of preexisting items; centralize priority so one press cannot trigger two actions. Preserve E pickup/other item interactions and pause behavior.
- Preserve the existing Rigidbody motion, both-sided corner geometry, camera and visuals; suction must allow legal vertical-to-overhead transitions without physical capsule teleportation or camera roll inversion.
- Item instance runtime state (charges, world/held/stored owner, campfire history) must persist through inventory moves; ScriptableObject item definitions hold immutable design data, not mutable per-instance charges.
- If weather, backpack, campfire or networking is missing from repository, implement minimal **clearly labeled local test adapters** to make the single-player prototype runnable; record missing real integration, do not silently claim real PEAK multiplayer/weather/campfire support.
- No hidden non-serialized authoritative state; track unique stable instance IDs, guard repeated commands, keep local authority replaceable by project network code when available.
- Editor scripts must be idempotent, non-destructive, and must not hardcode `Assets/PhysicsPrototype/` or regenerate user's changed ClimbingTest scene.

## QA / changes

- Tests must cover design QA-D01–D18, QA-S01–S20 and I/M/U criteria where supported; report unsupported integration tests explicitly as NOT IMPLEMENTED / NOT RUN.
- Write `Docs/ITEMS_TECH_AUDIT.md` before changing files and report each phase's modified files, tests, residual risks and rollback plan.
- Never claim Unity tests, audio timing, collision safety or multiplayer sync passed without execution and artifacts.
