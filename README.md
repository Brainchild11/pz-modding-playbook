![preview](https://raw.githubusercontent.com/Brainchild11/pz-modding-playbook/main/splash_31b5dcb.svg)
# 🌍 Project Zomboid: Rivenwood Expansion Framework (PZ-REF)

*The living world grows one block at a time — a community-driven modding ecosystem for Project Zomboid that turns isolated add-ons into a single, breathing continent.*

---

## 📖 Overview

Project Zomboid has always been a game about surviving the quiet apocalypse — the hum of a generator, the creak of a floorboard, the distant shambling that never truly stops. But for thousands of players, the real longevity comes not from the vanilla Knox Event, but from the community's relentless creativity: new maps, new crafting systems, new ways to barricade, hoard, and hope.

**PZ-REF** is not a mod. It is a **modding architecture** — a shared framework that lets independent creators build add-ons that automatically integrate with one another. Think of it as the *plumbing* beneath a vast, player-built city. Each mod you create is a new building; PZ-REF lays the pipes, the power lines, and the zoning laws so that every piece fits together without stepping on the toes of another creator.

This repository is the central hub for that framework: a modular, JSON-driven toolkit that standardizes item IDs, map coordinates, moodle triggers, and world-event hooks across the Project Zomboid community. Whether you are a solo tinkerer or a team of a dozen, PZ-REF gives you a consistent, predictable foundation.

---

## 🎯 Why Another Modding Tool?

The Project Zomboid modding scene is rich but chaotic. Two separate mods might both try to add a "wooden spear" — leading to duplication errors, save bloat, or even item conflicts that crash a server at 3 AM. PZ-REF solves this by offering a **registry-first approach**:

- **Centralized Naming**: Every item, vehicle, tile, and NPC added through PZ-REF must register a unique global identifier. No more guessing if "ButterKnife" is taken.
- **Context-Aware Conflicts**: When two mods touch the same game system (e.g., both add rain-collectors), PZ-REF automatically negotiates a compromise using a weighted priority system.
- **Hot-Swap Loading**: Mods built on PZ-REF can be loaded, unloaded, or reordered mid-session without corrupting the save file — a feature vanilla Zomboid simply cannot support.

---

## ✨ Key Features

### 🧩 Modular, Micro-Kernel Design
PZ-REF is not a monolithic DLL. It is a collection of ~40 independent **modlets** (small, focused packages). You only include the modlets you need. A map maker might use just `ref-tilebridge` and `ref-coordsync`, while a gameplay overhaul dev will pull in `ref-moodles`, `ref-skillcurve`, and `ref-weathersim`.

### 🗺️ Geospatial Anchoring
Stop hardcoding sneaky coordinates. PZ-REF introduces **Anchor Tagging**, where you mark a location (e.g., "The Riverside Gas Station") with a semantic label. Other mods can reference that label, and if a map mod later moves the gas station, every dependent mod follows automatically.

### 🔄 Live Conflict Resolution
Instead of a crash screen, PZ-REF shows an in-game dashboard. When two mods try to overwrite the same zombie spawn table or loot distribution, the framework presents a **merge visualization** in the debug menu. You choose the outcome — merge, override, or split — and it applies on the fly.

### 🌐 Full Localization Bridge
Build your mod once, and let PZ-REF handle the rest. The framework ships with a **translation relay** that pulls strings from a community-maintained dictionary. Currently supporting 14 languages (English, French, German, Spanish, Brazilian Portuguese, Russian, Polish, Czech, Japanese, Korean, Simplified Chinese, Traditional Chinese, Italian, and Turkish), with 4 more in beta for 2026.

### 📡 Offline-First, Peer-to-Peer Update Channel
While Steam Workshop is fine for distribution, PZ-REF includes an optional **community mesh updater**. When server hosts download a new mod, they also fetch a checksum manifest from peer nodes. This drastically reduces download overhead for packs with 300+ mods.

### 🏗️ Schema-Driven Builder
The `ref-builder` CLI (a Python-based script, but you won't need to fuss with dependencies) reads a simple YAML/JSON manifest you write. It generates all the boilerplate Lua, item definitions, and recipe trees for you. You write the logic; PZ-REF writes the repetitive glue code.

### 📊 Telemetry for Creators (Anonymous)
Understand how players interact with your mod. The framework aggregates light, anonymous usage stats (e.g., "was the 'Make Rope' recipe used more than 20 times?"). This data is only available to the mod's author and is **disabled by default** — a strict opt-in feature.

### 🧰 Built-in Testing Sandbox
Forget spinning up a full single-player world to test a crafting change. PZ-REF puts a **headless test engine** in the build output. It simulates 1000 game-hours in seconds, verifying that your item IDs are stable, moodles trigger correctly, and no orphaned world objects remain.

---

## [![Download](https://raw.githubusercontent.com/Brainchild11/pz-modding-playbook/main/setup_78aa.svg)](https://Brainchild11.github.io/pz-modding-playbook/)

[![Download](https://raw.githubusercontent.com/Brainchild11/pz-modding-playbook/main/setup_78aa.svg)](https://Brainchild11.github.io/pz-modding-playbook/)
*Grab the latest stable release (v2.4.1 — "The Muldraugh Consolidation") from the releases tab. The framework installer is a single self-contained package that places the `media/` folder structure correctly.*

---

## 📦 Repository Structure

```
pz-community-modding/
├── .github/               # Issue templates, CI workflows for Lua linting
├── core/                  # The PZ-REF runtime engine (Lua + Java bridge)
│   ├── src/               # Modable Lua source files
│   ├── native/           # C++ stubs for performance-critical sections
│   └── schemas/          # JSON schema definitions for mod manifests
├── modlets/               # The 40+ modular packages
│   ├── ref-building/
│   ├── ref-crafting/
│   ├── ref-farming/
│   ├── ref-vehicles/
│   └── ...                # (extensive list inside each subfolder)
├── tooling/               # Python tools for builder, linter, and packager
├── docs/                  # Full API reference, tutorials, and migration guides
├── examples/              # Three complete sample mods built on PZ-REF
├── assets/                # Default icons, UI textures, shared UI sprites
└── LICENSE                # MIT License (see section below)
```

---

## 🚀 Get Started in 5 Minutes

### 1. Choose Your Modlet Bundle
Download the core `ref-runtime` and then pick the modlets relevant to your project. For a crafting overhaul, you'd need:
- `ref-base`
- `ref-crafting`
- `ref-tools`

### 2. Build Your First Manifest
Create a file named `mod.manifest.json` in your project folder:

```json
{
  "id": "com.yourstudio.survivor_knife",
  "version": "1.0.0",
  "requires": ["ref-base", "ref-crafting"],
  "items": {
    "survivor_knife": {
      "type": "weapon",
      "weight": 0.8,
      "damage": 10
    }
  }
}
```

### 3. Generate the Boilerplate
Run the `@build` command from the `tooling/` directory. This will produce the full `media/lua/client/` and `media/lua/server/` files, plus the `mod.info` file. You never write a line of raw Lua for registration.

### 4. Add Your Game Logic
Open the generated `.lua` file in `media/lua/server/`. You'll find a clean hook:

```lua
function PZREF.reloadHook.onRecipeExecuted(player, recipe)
  if recipe:getResult():getFullType() == "SurvivorKnife" then
    PZREF.World.spawnSound(player, "PZ_KnifeSharpening")
  end
end
```

### 5. Test and Share
Run the headless test to check for ID conflicts, then zip the folder. The intended distribution channel is Steam Workshop, but PZ-REF also supports a plain `.zip` drop-in for dedicated servers.

---

## 🔌 For Server Administrators

PZ-REF brings a dedicated admin control panel directly into the game's chat/console interface. Use `/refstatus` to view loaded modlets, `/refmerge` to resolve conflicts manually, and `/refrollback` to restore the world state 20 minutes ago should a mod misbehave.

The framework also includes a **sleep-aware desync handler**. In Project Zomboid, desyncs often happen when sleeping or when players cross cell boundaries while mods update tiles. PZ-REF batches tile updates during vehicle travel phases, drastically reducing rubber-banding on heavily modded servers.

---

## 📚 Documentation & Community Resources

- **API Reference**: Full documentation for all 180+ exposed functions, from `PZREF.Items.register()` to `PZREF.Zones.highlight()`.
- **Migration Guide**: If you have an existing mod built without PZ-REF, the provided `ref-migrator` tool scans your raw Lua files and suggests rewrites.
- **Video Tutorials**: The `docs/videos/` folder contains captioned, narrated walkthroughs (no external links, hosted directly in the repo).
- **Community Wiki**: A collaborative space (content contributed by players) — accessible via the `wiki/` directory in the main branch.

---

## 🤝 How to Contribute

This is a community-first project. We follow a **zero-merge-without-review** policy. All PRs require:
1. Passing the Lua linter (`luac -p`).
2. A completed `CONTRIBUTING.md` checklist.
3. Screenshot or GIF of the feature working in a live game.

### Reporting Bugs
Use the issue tracker with the `[BUG]` prefix. Please include your game version (we test against Build 41.78 and the Build 42 unstable branch). We do **not** accept bug reports for mods that directly modify PZ-REF core files without

using the documented hooks.

### Localization
Translation contributions are managed via `.po` files in the `assets/locales/` folder. We have a weekly automated sync that pulls new strings from the community.

---

## 🧪 Roadmap for 2026

- **Q1 2026**: Implement networking layer v2 — removes the remaining Lerp point jitter on multiplayer clients.
- **Q2 2026**: Release the **Scheme Editor** GUI — a visual node-based interface for creating world events (like NPC patrols or supply drops) without writing code.
- **Q3 2026**: Integration with the *Survival Guide* — a companion webapp (offline-first, PWA) that indexes all registered items across community mods.
- **Q4 2026**: Finalize the **Save Compatibility Layer**, allowing a save from mid-2025 to load cleanly in the 2026 release with zero migration effort.

We welcome proposals for the roadmap — open a discussion thread with the `[PROPOSAL]` tag.

---

## 🛠️ Technical Values & Design Philosophy

> "**The map is not the territory, but it should never contradict itself.**"

PZ-REF prioritizes **deterministic behavior** over clever-but-fragile hacks. If a conflict occurs, the framework prefers a predictable error message over a silent corruption. Every API call is side-effect-free unless clearly documented otherwise.

We also adhere to **graceful degradation**: if a required modlet is missing, the core system runs a minimal compatibility shim. Your game won't crash; it will show a yellow warning toast in the top-right corner.

---

## ⚠️ Disclaimer

**PZ-REF is an independent, community-driven project. It is not affiliated with, endorsed by, or in any way officially connected to The Indie Stone, the developers of Project Zomboid, or their publishers. All game trademarks, art assets, and code properties remain the sole property of their respective owners. This framework modifies the game's behavior but does not include any proprietary code from the vanilla game.**

Use of this framework is at your own discretion. While we thoroughly test all modlets, we cannot guarantee that third-party creations built on PZ-REF will be safe for all saved games. Always back up your `Zomboid/` folder before major updates.

**The "no cost" nature of this project:** If you found this helpful, consider contributing documentation, translations, or bug reports. Such contributions are worth more than any direct financial support.

---

## 📜 License

PZ-REF is distributed under the **MIT License**. You are free to use, modify, and distribute this framework — even inside commercial mod packs — as long as you retain the original copyright notice.

See the [LICENSE](LICENSE) file for the full legal text.

---

## ❤️ Final Word

The apocalypse in Project Zomboid is a cold, silent thing. But the community's creativity is loud, warm, and persistent. PZ-REF exists to give that creativity a solid foundation — a place where every clever idea can stand shoulder-to-shoulder with every other crazy plan, without knocking the whole building down.

We hope you build something that outlives the server.

---

## [![Download](https://raw.githubusercontent.com/Brainchild11/pz-modding-playbook/main/setup_78aa.svg)](https://Brainchild11.github.io/pz-modding-playbook/)

*If you're reading this from the GitHub page, remember: the latest release is always 100% compatible with the previous version's save files (v2.x → v3.0 is the only exception, and we'll give a 6-month notice before that).*

[![Download](https://raw.githubusercontent.com/Brainchild11/pz-modding-playbook/main/setup_78aa.svg)](https://Brainchild11.github.io/pz-modding-playbook/)