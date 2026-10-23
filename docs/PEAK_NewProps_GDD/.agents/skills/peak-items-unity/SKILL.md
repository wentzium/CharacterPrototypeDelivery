---
name: peak-items-unity
description: Incrementally implement, integrate and test the approved PEAK-style Portable Rock Dryer and Suction Grip items inside an existing Unity 6 physics-climbing repository. Use when implementing item usage, wet rock surfaces, campfire charging, ceiling suction, inventory, or regression tests for these two items.
---

# Unity ClimbingTest: approved two-item implementation workflow

Use this skill for the user's **existing** Unity project only. Do not create an independent demo game as a substitute for integration, or assume official PEAK APIs.

1. **Read source-of-truth:** `AGENTS.md`, `Docs/00_PEAK_便携烘干机与吸盘_道具详细设计案_V1.0.md`, `Docs/01_AI_Agent_完整开发Prompt.md`, and `Docs/03_验收矩阵与缺口分类.md`. If file names differ, discover rather than invent paths.
2. **Repository audit:** `git status`, Unity version, assembly definitions, packages, item controls, E/Q/Tab/LMB conflicts, original climbing state graph, Rigidbody/Collider and existing weather/campfire/inventory/network systems. Produce `Docs/ITEMS_TECH_AUDIT.md`.
3. **Make safe changes:** smallest changes in `Assets/Scripts/`, additive and idempotent `Assets/Editor/` setup, GUID-preserving .meta workflow; never regenerate edited scenes or modify Rigidbody physics as a visual workaround.
4. **One authoritative item state:** instance GUID, charges, owner, inventory location, campfire history, active suction session, dry patch expiration. All usable actions return explicit success/failure; no charges deducted on failed requests.
5. **Dryer checks:** connected surface graph, patch area and 7x7 extent, no backside-through-wall, 20s local override, wetness handback, repeated use refresh, campfire per-instance uniqueness and charge cap.
6. **Suction checks:** only successful new attached session consumes one use, continuous LMB across corners/ceiling consumes no extra; one-session +20 cumulative recovery; movement consumes normal climbing stamina; last charge consumed only after current session finishes; no movement through solids.
7. **Regression:** old camera/third person aiming, E pickup, Q/T tools, menus and Pause, old stamina, both sides of corners, mantling, sound/landing, art transforms. Protect all manual scene edits.
8. **Testing:** EditMode for pure geometry, item transactions, budgets/expiry; PlayMode for physics, input priority, ceiling transitions, scene interactions; visually check Inspector/HUD/audio. Log `PASS`, `FAIL`, `NOT RUN`, `NOT IMPLEMENTED` explicitly; report real test XML/log/video filenames.
9. **Phase gate:** document changed files, state/data migrations, undo steps, known blockers. Do not claim multi-user networking if the repository has none; isolate mock command gateway for future integration.

Keep SKILL instructions procedural. The GDD owns rules and numbers; do not duplicate larger specs into this file.
