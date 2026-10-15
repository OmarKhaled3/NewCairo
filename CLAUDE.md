# New Cairo Game: briefing for Claude Code

Read this file and SPEC.md at the start of every session.

## What this is
A GTA-style open-world driving game set in New Cairo, Egypt, built from real OpenStreetMap data.
Owner: Omar. He lives in the map, knows every road, and is the final judge of how things look and feel.
We are on **Milestone 1**: drive a car around a low-poly, geographically accurate slice of Fifth Settlement
between three real places (see data/places.json). Full scope and session plan: SPEC.md.

## Working with Omar
- Omar is a project manager and UI/UX designer, not a game programmer. Explain what you changed and why in
  plain language, using analogies when a concept is new. Skip jargon or define it once.
- After every change, tell him exactly how to test it: what to open, which key to press, what he should see.
- You cannot see the screen. When something is visual or about feel, ask him to describe it or send a
  screenshot. Add on-screen debug info (FPS, speed, position) rather than guessing.
- He is direct; be direct back. If an idea is a bad fit for the hardware or scope, say so and offer the better option.

## Hard constraints
- **Engine:** Godot 4 (latest stable), GDScript only. No C#, no GDExtension, no paid plugins.
- **Renderer:** Compatibility (OpenGL). Do not switch to Forward+ or Mobile.
- **Hardware:** 2019 13-inch MacBook Pro, Intel quad-core i5 2.4 GHz, Intel Iris Plus 655 integrated
  graphics, 16 GB RAM, macOS Sequoia (cannot upgrade further). Runs in clamshell mode with two external monitors.
- **Performance budget:** must hold 30 FPS minimum while driving at full speed; aim for 60.
  Prefer merged meshes per tile, MultiMesh for repeated props (trees, lamps), simple materials,
  no real-time shadows from more than one light, conservative draw distance with fog to hide the edge.
- **Art:** clean flat-shaded low-poly, warm desert palette (sand, beige, terracotta, dusty green, golden sun).
  Free assets must be CC0 (Kenney, Quaternius). Record every third-party asset and its license in CREDITS.md.
- **Legal:** real street and area names are fine. No real brand logos, company names on buildings, or the word "GTA" in the game.

## Scope discipline
- Work only on the current session's goal from the SPEC.md build plan.
- New ideas go into the "Backlog" section at the bottom of SPEC.md, not into the code.
- If a task balloons, stop, explain the tradeoff, and let Omar decide.

## Map and coordinates
- Source data: OpenStreetMap via the Overpass query in data/overpass_query.txt. Raw downloads live in
  data/raw/ (git-ignored).
- Map origin and bounding box: data/places.json. World (0,0,0) = map_origin.
- Axes: +X east, -Z north, +Y up, 1 unit = 1 meter. Convert lat/lon with an equirectangular projection
  around the origin (meters per degree lat = 111320; meters per degree lon = 111320 * cos(origin_lat)).
- Generated content goes in world/generated/ and is fully rebuilt by the importer.
  Hand fixes go in world/overrides/ and must never be overwritten by the importer.
- Default building heights when OSM has none: villas 8 m, apartment blocks 15 m, commercial/malls 20 m.
  Use `height` or `building:levels` (x 3.2 m) from OSM when present.

## Project layout (create as needed)
```
project.godot
scenes/        main scenes (main.tscn, car.tscn)
scripts/       GDScript
tools/         importer and other editor/CLI scripts
world/generated/   importer output (rebuildable)
world/overrides/   hand-placed fixes and landmarks
assets/        models, textures, audio (CC0 only)
data/          places.json, overpass query, raw/ downloads
```

## Git
- Commit after each working step with a clear message (what changed, in plain words).
- Never force-push, never rewrite history, never commit data/raw/.
- If something breaks, prefer reverting to the last good commit over piling on fixes.

## Running things
- Godot on macOS: `/Applications/Godot.app/Contents/MacOS/Godot`
- Open the editor on this project: `/Applications/Godot.app/Contents/MacOS/Godot -e --path .`
- Run the game: `/Applications/Godot.app/Contents/MacOS/Godot --path .`
- Use headless runs (`--headless`) to check that scripts load without errors before asking Omar to test.
