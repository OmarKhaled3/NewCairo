# New Cairo Game: Milestone 1 Spec

Last updated 2026-10-15. Living copy of the spec doc; edit freely.

## Goal
Milestone 1 is done when Omar can drive a car from home in Zizinia to DFN Gym and to The Mind Space, on roads
built from real New Cairo map data, running smoothly on his 2019 MacBook.

It is deliberately small: one car, one map, no people or missions. The point is to prove the whole pipeline
works end to end, from real map data to a playable world, before anything else is built on top of it.

### Definition of done
- [ ] The map covers the drive between all three places, with roads in the right layout
- [ ] Buildings stand where they really are, as simple low-poly shapes
- [ ] The car is fun enough that Omar keeps driving after the test is over
- [ ] A chase camera follows smoothly, with a reset key if you flip
- [ ] Each destination has a marker you can drive to
- [ ] Holds at least 30 FPS on the MacBook (target 60)
- [ ] The project lives in a private Git repo, so every change can be undone

## The map
The first map is the triangle Omar actually drives: home, gym and class. Exact data: data/places.json.

| Place | Role | Coordinates |
| --- | --- | --- |
| Home (Zizinia, beside AUC) | Spawn point | 30.008667, 31.494417 |
| DFN Gym (near Mivida) | Destination 1 | 30.009778, 31.524306 |
| The Mind Space (near Waterway 2) | Destination 2 | 30.034889, 31.485278 |

Straight-line distances: home to gym about 2.9 km, home to class about 3.0 km, gym to class about 4.7 km.

Map box (with about 500 m margin): south 30.0037, west 31.4803, north 30.0399, east 31.5293.
That is roughly 4.0 km by 4.7 km, around 19 square kilometers. Big for this hardware, so tiling and
draw distance matter from day one.

Gated compounds get a simple treatment for now: a wall with a gate you can drive through. Internal compound
streets are included only where the data has them or where Omar's routes need them.

## Tech stack
| Piece | Choice | Why |
| --- | --- | --- |
| Engine | Godot 4 (latest stable), Compatibility renderer | Free, light, plain-text scenes and scripts |
| Language | GDScript | Simple, Python-like |
| Map import | Custom importer script for OSM data | Re-runnable as the map grows |
| Version control | Git + private GitHub repo | Every change can be reviewed and undone |
| AI partner | Claude Code in this folder | Writes and edits code; Omar runs and judges feel |
| Free assets | CC0 packs (Kenney, Quaternius) | Matching low-poly cars, trees, props |

## Map pipeline
1. Download OSM data for the map box using data/overpass_query.txt (Overpass Turbo), save to data/raw/.
2. Parse roads (by highway type) and building footprints.
3. Convert lat/lon to local meters around the map origin.
4. Build road meshes with widths by type: 90th Street wide, compound streets narrow.
5. Extrude building footprints into blocks, with default heights by type when OSM has none.
6. Add ground, sidewalks, and collision.
7. Split the world into tiles so only what's near the car is drawn.
8. Save as Godot scenes in world/generated/.

Hand fixes live in world/overrides/ so re-running the importer never wipes them.

## Car, controls and camera
Arcade feel, not simulation: grippy, quick to turn, fun to slide around a roundabout.
- Car: one hatchback on Godot's VehicleBody3D, tuned by feel over several sessions
- Controls: WASD or arrows, Space handbrake, R reset; gamepad optional
- Camera: chase camera that lags slightly and swings wider at speed
- HUD: speed in km/h, distance to current destination
- Sound: basic engine hum that rises with speed

## Visual style
Clean flat-shaded low-poly, warm desert palette: sand and beige facades, terracotta accents, dusty greens,
golden late-afternoon sun. A few hand-placed landmarks (AUC gate, a mall block, compound gates) do more
than detail everywhere. Palms and roadside trees along 90th Street. Real street names fine; no brand logos.

## Out of scope for Milestone 1
- Walking on foot, entering and leaving cars
- Traffic, pedestrians and police
- Missions, story and characters
- Day and night cycle and weather
- Building interiors
- Radio, music and voice
- Anything outside the map box

## Build plan (about 8 evening sessions of 2 to 3 hours)
| Session | Goal | What Omar sees at the end |
| --- | --- | --- |
| 1 | Setup: Godot project, Git, empty main scene | A test car driving on a flat grey plane |
| 2 | Car feel and chase camera | Driving that already feels like a game |
| 3 | OSM download and importer, roads only | Real road layout as flat lines |
| 4 | Road meshes with widths and collision | Driving down 90th Street |
| 5 | Buildings, ground, basic materials | New Cairo as low-poly blocks |
| 6 | Performance pass on the MacBook | Steady frame rate, tiles loading as you drive |
| 7 | Landmarks and the three destinations | Home, gym and class marked and reachable |
| 8 | Polish: HUD, reset, home-to-gym timer | Milestone 1 done |

Each session ends with Omar playing the build and saying what feels off. That feedback steers the next session.

## Risks
| Risk | Looks like | Fallback |
| --- | --- | --- |
| MacBook performance | Stutter once buildings appear | Smaller tiles, shorter draw distance, simpler shapes |
| Gaps in map data | Missing compound streets, odd shapes | Hand-fix only the routes Omar drives |
| Car feel | Floaty or twitchy | Short tuning pass each session |
| Claude can't see the screen | Visual bugs missed | Omar describes or screenshots; on-screen debug info |
| Scope creep | "Just add traffic" in session 4 | New ideas go to the Backlog first |

## Open questions
- [x] Exact pins for home, gym and class (done 2026-10-15)
- [ ] Keyboard only, or a controller too?
- [ ] A working name for the project

## Backlog
(New ideas land here instead of in the code.)
