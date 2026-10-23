# AGENTS.md — Unity ClimbingTest Project Rules

## Project and source of truth

- The current task targets the **existing ClimbingTest Scene** in a Unity 6 project; do not rebuild it from scratch.
- Read `PEAK_NewProps_GDD_V1.0.md` for product requirements and acceptance criteria; read `01_AI_Agent_Prompt` for execution phases.
- Read `ProjectSettings/ProjectVersion.txt`, inspect actual assets, and report discrepancies before editing. Unity version and scene implementation must never be guessed.
- The current user's repository places code/audio directly under `Assets/Scripts/`, `Assets/Editor/`, `Assets/Audio/`, etc. There is no required `Assets/PhysicsPrototype/` directory. The `PhysicsPrototype` C# namespace may remain unchanged.

## Safety and preservation

- Always inspect `git status` first. Preserve uncommitted work and the user's manually moved walls, custom ClimbingTest geometry, Mini Peak, 8m/18m fall test areas, materials, prefabs, and scene settings.
- Never run old "Build Both Demo Scenes" or destructive upgrade menus without an explicit user instruction and a backup.
- Never delete/recreate `.meta` GUIDs or overwrite scenes/prefabs with hand-authored serialization when Unity Editor APIs can safely apply changes.
- Use idempotent Editor setup routines that detect and reuse existing scene objects. Scene updates must not reset user-authored Transform values.
- Avoid global `git reset --hard`, `git clean -fd`, destructive folder moves, or deletions without explicit approval.

## Gameplay non-negotiables

- Player is a Dynamic Rigidbody with CapsuleCollider. Physical movement/climbing uses bounded force, acceleration, constraint-aware contact and collision checks. No Transform teleport to fake climbing.
- Separate physical capsule yaw and collision from visual body/head aim and third-person over-shoulder camera rotation. Do not let two scripts fight over the same Transform.
- In-ground camera-relative WASD; wall-local WASD while attached. A/D sign must stay consistent through corners; no forced wall-facing rotation of physics capsule.
- Holding LMB attempts to grab; stamina is drained only after actual successful attachment and while still attached, even without movement. Failed grab attempts consume no climbing stamina. Releasing LMB immediately detaches; pressing LMB again while near a valid surface can reattach.
- Use the current game's stamina values/Inspector data; do not silently reset tunables. Exhaustion detaches and requires correct reset conditions.
- General climb surfaces support both sides of valid BoxCollider wall segments. Inner/outer corners depend on the approached side, discovered from geometry at runtime, not hard-coded pivot markers.
- Distinguish initial grab contact gap from sustained wall offset. Keep existing mantle, stamina, slopes, Q/T items, E pickup/drop, mouse-orbit camera, dynamic reticle, feedback, start/pause menu and test mountain.
- Only visual model/presentation responds with landing bounce, limited tilt or bob; never modify player physics for pure aesthetics. No cumulative per-frame rotation on visual root.
- No unapproved additional mechanics, items, enemies, saves, network logic, or third-party packages.

## Code organization and Unity APIs

- Runtime MonoBehaviours belong under `Assets/Scripts/`; Editor-only classes under `Assets/Editor/`.
- Preserve existing namespaces and serialized field names where possible. New names must migrate serialized scene references safely.
- Use `Update` for input decisions, `FixedUpdate` for Rigidbody forces, and `LateUpdate` for camera/visual presentation as appropriate. Manage event timing explicitly, especially grab/release/landing audio.
- Do not hardcode `Assets/PhysicsPrototype/`. For Editor resource discovery, prefer serialized references and `AssetDatabase.FindAssets` with validation for ambiguities.
- Current project input may use legacy `Input.*` with Active Input Handling `Both`; do not migrate wholesale without tests.
- Pause should block all gameplay input, release the pointer, freeze time/physics, preserve player/item state, and restore normal timescale on exit/disable. In Editor, Exit stops Play Mode; in standalone build, Exit quits the player app.

## Quality gate and output

- Change only what's needed, then compile/run relevant tests. Use Unity Test Framework EditMode/PlayMode and explicit manual scene smoke tests.
- Verify all states, including both sides of two intersecting walls, jump/mantle, close-range grab, exhaustion, camera limits, pickup, 8m/18m landing, and Start/Paused/Resume/Exit.
- Do not claim tests passed without logs/evidence. Use `PASS`, `FAIL` or `NOT RUN`, and clearly identify Unity Editor/runtime checks not performed.
- Every phase must conclude with changed files, scene impact, test evidence, residual risks, and rollback instructions.
- Keep or create `Docs/TECH_AUDIT.md`, `Docs/IMPLEMENTATION_STATUS.md`, `Docs/TEST_REPORT.md` and `Docs/CHANGELOG.md` as appropriate.


---

# Append to the existing repository AGENTS.md — PEAK-style item development

> 合并说明：本段是针对已批准的「便携烘干机与吸盘」新增任务的补充，不应删除原 AGENTS.md 对 ClimbingTest、镜头、Rigidbody、场景保留和 Unity 测试的规则。如果原文件有“不得添加新道具”条款，则以本次用户批准的**仅新增这两件**为明确例外；其他道具仍不得增改。

## Item feature scope and contract

- Before coding, read `Docs/00_PEAK_便携烘干机与吸盘_道具详细设计案_V1.0.md` and `Docs/01_AI_Agent_完整开发Prompt.md` and use `.agents/skills/peak-items-unity/SKILL.md`.
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
