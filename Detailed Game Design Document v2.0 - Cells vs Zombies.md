# Detailed Game Design Document v2.0: **Cells vs Zombies**

> **Changelog from v1.0:** This revision corrects biological inaccuracies, fills in
> undocumented mechanics (win/loss, waves, placement, controls, pathing, economy
> numbers), reconciles inconsistencies with the README, adds missing GDD-standard
> sections (art, audio, assessment, curriculum, tutorial), and substantially expands
> the accessibility plan to match the stated audience of students with disabilities.
> A detailed errata is appended in Section 14.

---

## 1. Executive Summary

**Cells vs Zombies** (working title; README uses "Cellular Defense") is a
single-player, educational tower-defense game targeting **7th-grade life-science
students, including students with disabilities**. Players manage a three-zone
economy representing the journey from food intake to cellular function, defending a
Cell against waves of pathogens.

The core pedagogical thesis is that survival requires the balanced management of
**Matter** (Proteins / Amino Acids) and **Energy** (Glucose / ATP), mirroring the
real biochemistry of cellular respiration and protein synthesis.

| Attribute | Detail |
|---|---|
| Genre | Tower Defense / Real-Time Strategy |
| Platform | Web (HTML5 via Godot export to GitHub Pages) |
| Engine | Godot 4.x (GDScript) |
| Input | Mouse-only (see Section 10 for accessibility) |
| Session Length | 10-15 minutes per level |
| Players | Single-player |
| Art Style | 2D top-down, 64x64 pixel-art sprites |
| Target Audience | 7th-grade life-science students (ages 12-13), including students with learning disabilities, visual impairments, and motor difficulties |

---

## 2. Curriculum Alignment & Learning Objectives

### NGSS Standards Addressed

| Standard | Coverage |
|---|---|
| MS-LS1-2 | Develop and use a model to describe the function of a cell as a whole and ways the parts of cells contribute to the function. |
| MS-LS1-3 | Use argument supported by evidence for how the body is a system of interacting subsystems composed of groups of cells. |
| MS-LS1-7 | Develop a model to describe how food is rearranged through chemical reactions forming new molecules that support growth and/or release energy. |

### Learning Objectives

By the end of a full playthrough, students will be able to:

1. **Explain** aerobic cellular respiration as Glucose + O2 -> ATP + CO2 + H2O.
2. **Compare** aerobic respiration (~36 ATP per Glucose) with anaerobic fermentation (2 ATP per Glucose + Lactic Acid waste).
3. **Describe** protein synthesis as the assembly of Amino Acids into functional structures, requiring energy (ATP).
4. **Identify** the roles of mitochondria (energy conversion) and ribosomes (protein assembly) within a cell.
5. **Explain** why specialized transport systems (circulatory analogy) are necessary for complex organisms.
6. **Describe** fat/lipid storage as a mechanism for buffering energy supply.

### Assessment Integration

- **End-of-Level Quiz (optional, teacher-configurable):** 2-3 multiple-choice questions tied to that level's mechanic. Correct answers award a small bonus for the next level.
- **Biology Blurbs:** Every plant, structure, and zombie has an in-game info panel with a short biology explanation and a "Test Yourself" prompt.
- **Teacher Dashboard (stretch goal):** JSON export of per-student performance data (levels completed, quiz scores, time-to-complete, fermentation usage frequency).

---

## 3. The Three-Zone System

The game world is divided into three horizontally-arranged zones, scrollable via the minimap or edge-panning. Resources must physically travel between zones via Robots.

```
[BACKYARD]  <-->  [HOUSE]  <-->  [FRONT YARD]
 (Source)       (Processor)      (Cell/Combat)
```

### A. The Backyard (External Environment / Source)

- **Purpose:** Raw resource generation.
- **Layout:** 4x5 planting grid.
- **Structures:**
  - **Starch Melon Vine:** Produces 1 Starch bundle every 8 seconds. Costs 2 ATP to plant.
  - **Protein Pod Bush:** Produces 1 Protein bundle every 12 seconds. Costs 3 ATP to plant.
  - **Harvester (replaces v1.0 "Eaters"):** Placed adjacent to a vine/bush. Automatically collects finished bundles and flags them for robot pickup. 1 Harvester per plant. Costs 1 ATP.
- **Lesson:** Food exists as complex polymers (Starch, Protein) in the environment before the body can use it.

### B. The House (The Body / Processor)

- **Purpose:** Digestion, gas exchange, and energy storage.
- **Layout:** Interior schematic with fixed structure slots (not a free-placement grid).
- **Key Structures:**
  - **The Digester (Digestive System):** Accepts Starch or Protein bundles. Breaks 1 Starch into 2 Glucose cubes (4s processing time). Breaks 1 Protein into 3 Amino Acid units (6s processing time). Starts with 1 Digester; a second can be built at Level 4 for 10 ATP + 5 Amino Acids.
  - **The Window (Lungs):** O2 passively enters through the Window at a base rate of 1 O2 canister every 5 seconds. CO2 must be carried back to the Window by robots and expelled. If CO2 accumulates past a threshold (5 canisters in the Front Yard), O2 intake rate halves (simulating impaired gas exchange).
  - **The Adipose Silo (Fat Storage):** Converts 3 Glucose into 1 Fat cell (compact, stackable, does not spoil). Can reverse-convert 1 Fat cell back into 2 Glucose (lossy conversion, representing metabolic cost). Capacity: 10 Fat cells. Unlocked at Level 3.
- **Lesson:** The body must digest polymers into monomers (Glucose, Amino Acids) before they can be used by cells.

### C. The Front Yard (The Cell / Combat Zone)

- **Purpose:** ATP production, protein synthesis, and pathogen defense.
- **Layout:** 5x9 tower-defense grid. Zombies advance from the right edge; the Cell Core is on the left.
- **Key Structures (fixed, non-removable):**
  - **Mitochondrion:** Consumes 1 Glucose + 1 O2 -> produces 6 ATP + 1 CO2. Cycle time: 5 seconds. The player starts with 1; a second is unlockable at Level 5 (costs 15 Amino Acids + 10 ATP).
  - **Ribosome:** Consumes Amino Acids + ATP -> produces Protein-based defense units (plants). Each defensive plant has its own Amino Acid and ATP cost (see Section 5). Build time varies per plant.
- **The Cell Core (Base):** Has 10 HP. If any zombie reaches it, the Core takes 1 damage per zombie. Core HP does not regenerate. **Reaching 0 HP = Level Failed.**
- **Lesson:** The cell is where energy is released (mitochondria) and functional structures are built (ribosomes).

> **v1.0 Issue:** The original included an external hotlinked image from Google/Shutterstock with inadequate attribution. Reference images should be stored locally in `/assets/reference/` or described textually. Removed.

---

## 4. Resource Flow

The complete resource chain is:

```
Backyard                    House                          Front Yard
--------                    -----                          ----------
Starch Melon --> [Robot] --> Digester --> 2 Glucose ---+--> Mitochondrion
                                                      |   (+ 1 O2 --> 6 ATP + 1 CO2)
                                                      |
                                                      +--> Adipose Silo
                                                           (3 Glucose --> 1 Fat)
                                                           (1 Fat --> 2 Glucose)

Protein Pod  --> [Robot] --> Digester --> 3 Amino Acids --> Ribosome
                                                           (Amino Acids + ATP --> Plant)

Window: O2 enters passively --> [Robot carries to Mitochondrion]
        CO2 produced by Mitochondrion --> [Robot carries back to Window]
```

### Resource Inventory Limits

Resources left on the ground too long spoil (30 seconds for Glucose, 60 seconds for Amino Acids). This prevents hoarding and forces active logistics management.

| Resource | Storage Limit (ground) | Notes |
|---|---|---|
| Starch / Protein | 5 bundles in Backyard | Harvesters stop if full |
| Glucose | 10 cubes in House, 6 in Front Yard | Excess spoils |
| Amino Acids | 8 units in Front Yard | Excess spoils |
| O2 | 4 canisters in Front Yard | Window output slows if unused O2 accumulates |
| CO2 | Unlimited (but penalizes at 5+) | See Section 3B |
| ATP | 20 max (global pool, no spoilage) | Displayed as main resource counter |
| Fat | 10 cells in Adipose Silo | Stable, no spoilage |

---

## 5. Defensive Plants (Units)

Plants are placed on the Front Yard grid. Each grid cell holds one plant. Plants are built by the Ribosome (except Phase 1 energy-only plants, which are placed directly for an ATP cost).

### Phase 1: Energy-Only Plants (Levels 1-2)

These require only ATP to deploy. They have no physical structure and cannot be rebuilt once destroyed.

| Plant | ATP Cost | Mechanic | DPS Equivalent | HP |
|---|---|---|---|---|
| Zap-Grass | 3 ATP to place, 1 ATP per zap | Short-range electric burst (1 tile). 1 damage per zap, 1 zap/sec. | 1.0 | 2 |
| Catchweed | 4 ATP to place, 1 ATP/sec while holding | Roots a zombie in place. Cannot damage; only restrains. | 0 (utility) | 3 |

### Phase 2: Protein-Based Plants (Level 3+)

Built at the Ribosome. More durable and energy-efficient because their physical structure does mechanical work.

| Plant | Build Cost | Build Time | Mechanic | DPS | ATP/shot | HP |
|---|---|---|---|---|---|---|
| Peashooter | 5 AA + 3 ATP | 8s | Fires a physical seed projectile. Range: 5 tiles. 1 damage/shot, 1 shot/1.5s. | 0.67 | 0.2 | 5 |
| Wall-Nut | 8 AA + 2 ATP | 10s | No attack. High-HP blocker. | 0 | 0 | 15 |
| Repeater | 10 AA + 5 ATP | 12s | Fires 2 seeds per volley. Range: 5 tiles. | 1.33 | 0.3 | 4 |
| Enzyme Sprayer | 12 AA + 8 ATP | 15s | Area-of-effect acid spray (2x2 tiles). 1 damage/sec to all zombies in area. | ~2-4 | 0.5/sec | 3 |

> **v1.0 Correction:** The Ribosome's output was written as "Amino Acids + ATP -> Plants."
> Ribosomes synthesize **proteins**, which in this game are expressed as defensive
> structures. The corrected framing is: "Amino Acids + ATP -> Protein Structure
> (Defensive Unit)."

### Lesson: Matter vs. Energy

- **Phase 1** plants are pure energy expenditure: cheap to deploy but expensive to operate (1 ATP per action).
- **Phase 2** plants cost matter (Amino Acids) upfront but are far more energy-efficient to operate (0.2-0.5 ATP per action) because their protein-based physical structures perform mechanical work, reducing the need for constant chemical energy input.

---

## 6. Pathogen Logic (Zombies)

Zombies spawn from the right edge of the Front Yard and advance left toward the Cell Core. They do not enter other zones.

### Wave Structure

Each level consists of 3-5 waves with a 20-second break between waves. A wave indicator at the top of the screen shows progress. During breaks, the player can manage logistics and build defenses.

| Level | Waves | Zombie Types | Notable Mechanic Introduced |
|---|---|---|---|
| 1 | 3 | Mooks only | Basic energy economy |
| 2 | 3 | Mooks + Shocktroops | Red-Bots unlock; gas logistics |
| 3 | 4 | Mooks + Shocktroops | Protein economy; Peashooter unlocks |
| 4 | 4 | All Tier 1-3 | Adipose Silo unlocks; Hijacker threat |
| 5 | 5 | All types + Boss wave | Fermentation Vat; second Mitochondrion |

### Zombie Types

**Health Formula:** Base HP = 2 + (Current Level). A Level 1 Mook has 3 HP; a Level 5 Mook has 7 HP. Bosses have 3x the formula.

| Tier | Name | Speed | HP Modifier | Behavior | On-Death Effect |
|---|---|---|---|---|---|
| 1 - Mook | CO2 Cloud | Slow (1 tile/3s) | 1x | Walks toward Core. | Drops 1 CO2 canister on the field (must be cleaned up by robots). |
| 2 - Shocktrooper | Sugar Bandit | Fast (1 tile/1.5s) | 0.75x | Prioritizes stealing loose Glucose cubes from the ground before advancing. If it grabs Glucose, it heals 1 HP. | None. |
| 3 - Miniboss | Hijacker | Medium (1 tile/2s) | 1.5x | On contact with a Mitochondrion or Ribosome, disables it for 10 seconds. Must be destroyed to end the disable. | Disabled structure reactivates. |
| 4 - Boss | Entropy Golem | Slow (1 tile/4s) | 3x | Leaves a trail of damaging sludge (1 tile wide) that deals 1 damage/sec to plants standing in it and slows robot movement by 50%. Trail fades after 15 seconds. | Trail immediately clears. |

> **v1.0 Correction:** Mooks were described as leaving "empty O2 canisters (CO2)."
> This is chemically misleading. CO2 (carbon dioxide) is a distinct waste molecule, not
> a "used-up" oxygen molecule. In v2.0, Mooks drop CO2 waste canisters that must be
> removed -- framed as metabolic waste from the cell's exertion during combat, not as
> transformed O2 containers.

> **v1.0 Correction:** Boss "Lactic Acid trails" were attributed to an external
> pathogen. Biologically, lactic acid is an *internal* metabolic byproduct of
> fermentation, not something a pathogen produces. In v2.0, the Boss trail is renamed
> to generic "damaging sludge" (representing cellular damage/inflammation caused by the
> pathogen). Lactic Acid remains exclusively tied to the Fermentation Vat mechanic
> (Section 8) where it is biologically accurate.

### Zombie Pathing

Zombies use simple lane-based pathing (like PvZ). The Front Yard grid has 5 rows; zombies spawn randomly across rows and advance left. They do not switch lanes. They attack the first plant they contact (1 damage/sec to the plant) until it is destroyed, then resume advancing.

---

## 7. The Circular Economy (Robots)

Robots are the circulatory system of the game. They autonomously carry resources between zones on predefined paths (Backyard <-> House <-> Front Yard). The player does not directly control robot movement but can influence their behavior through upgrades and priority settings.

### Robot Types

| Robot | Unlocked | Carries | Capacity | Speed | Behavior |
|---|---|---|---|---|---|
| Clear-Bot (Plasma) | Level 1 | Any resource type | 1 item | Medium (1 zone transit = 4s) | Generalist. Picks up the nearest available item. |
| Red-Bot (RBC) | Level 2 | O2 and CO2 only | 3 gas canisters | Medium (4s) | Specialist. Shuttles O2 from Window to Front Yard and CO2 back. |

### Robot Priority System

Clear-Bots follow this pickup priority when multiple resources are available:

1. **ATP delivery to plants** (highest -- plants die without energy)
2. **Glucose to Mitochondria** (ATP production is critical)
3. **Amino Acids to Ribosome** (building queue)
4. **Starch/Protein from Backyard** (raw materials)
5. **CO2 cleanup** (lowest -- only if Red-Bots are overwhelmed)

> **v1.0 Correction:** The original priority was "O2 > Waste > Glucose > ATP."
> This would mean robots prioritize carrying O2 (which enters passively at the Window)
> over delivering ATP to starving plants. The corrected priority above reflects actual
> urgency: plants without ATP shut down, and Glucose-to-Mitochondria fuels ATP
> production. O2 transport is handled by specialized Red-Bots and should not dominate
> Clear-Bot behavior.

### Robot Counts & Upgrades

- **Starting robots:** 2 Clear-Bots.
- **Level 2:** +2 Red-Bots (free, narrative unlock).
- **Building more:** The player can spend 5 ATP + 3 Amino Acids to build 1 additional robot of either type at the House. Maximum: 6 Clear-Bots, 4 Red-Bots.
- **Speed Upgrade (stretch goal):** Spend 10 ATP + 5 AA to reduce a robot's transit time by 25%.

### The Bottleneck Design

The intentional constraint is robot travel time. A well-optimized system (specialized robots, fat storage to buffer glucose shortfalls, minimal waste) runs smoothly. A poorly-managed system creates visible "traffic jams" on the minimap -- robots queued up carrying the wrong things while plants starve and CO2 accumulates.

---

## 8. Emergency Systems (Fermentation)

If O2 supply fails (Red-Bot bottleneck, CO2 clogging the Window), the player can activate the **Fermentation Vat** (unlocked Level 5, or available earlier on Easy difficulty).

| Attribute | Value |
|---|---|
| Input | 1 Glucose |
| Output | 2 ATP + 1 Lactic Acid waste |
| Comparison | Aerobic respiration produces **6 ATP** per Glucose (in-game value; real biology is ~36 ATP, simplified for gameplay). Fermentation produces only **2 ATP**. |
| Trade-off | Lactic Acid waste is toxic. Each unit on the field slows all robots in that zone by 10% (stacking). At 5+ Lactic Acid units, a "Cramp" event triggers: all robots freeze for 3 seconds, then resume at 50% speed for 10 seconds. |
| Cleanup | Robots can carry Lactic Acid to the Window for disposal (same as CO2). |

**Lesson:** Fermentation is a real biological emergency pathway. It keeps the cell alive during oxygen debt but is vastly less efficient and produces harmful waste. Students should experience the desperation of switching to fermentation and the relief of restoring aerobic respiration.

---

## 9. Level Design & Progression

### Level Structure

The game contains 5 core levels (expandable). Each level introduces one major biological concept.

| Level | Title | New Mechanic | New Unlock | Objective |
|---|---|---|---|---|
| 1 | "First Breath" | Core loop: Glucose + O2 -> ATP. Place Zap-Grass. | Zap-Grass, Catchweed | Survive 3 waves. |
| 2 | "Red Means Go" | Gas logistics. Red-Bots free Clear-Bots for nutrients. | Red-Bots | Survive 3 waves without CO2 exceeding threshold. |
| 3 | "Building Blocks" | Protein economy. Build Peashooters at the Ribosome. | Protein Pods, Ribosome, Peashooter, Adipose Silo | Survive 4 waves. Build at least 2 Peashooters. |
| 4 | "Under Siege" | Hijacker threat. Protect key structures. | Wall-Nut, Repeater, 2nd Digester | Survive 4 waves. Keep Mitochondrion online for 90%+ of the level. |
| 5 | "Oxygen Crisis" | Fermentation. O2 supply is restricted mid-level. | Fermentation Vat, Enzyme Sprayer, 2nd Mitochondrion | Survive 5 waves including a Boss wave. |

### Win Condition

- The Cell Core survives the final wave with at least 1 HP remaining.

### Loss Condition

- The Cell Core reaches 0 HP.

### Between-Level Progression

- New plants and structures unlock as described above.
- **Star Rating (1-3 stars):** Based on Core HP remaining (3 stars = no damage), encouraging replay.
- A brief **Biology Recap** screen appears after each level summarizing the concept learned, reinforcing the educational content.

---

## 10. User Interface & Accessibility

### HUD Layout

```
+-------------------------------------------------------------------+
| [Wave 2/4]  [ATP: 12/20]  [Glucose: 3]  [AA: 5]  [O2: 2] [CO2:1]|
+-------------------------------------------------------------------+
|                                                                    |
|                        GAME VIEWPORT                               |
|                     (Current Zone View)                            |
|                                                                    |
+-------------------------------------------------------------------+
| [Minimap: 3 zones]          [Plant Palette]        [Speed] [Pause]|
+-------------------------------------------------------------------+
```

- **Resource Bar (top):** Displays all key resources with color-coded icons and numeric values.
- **Minimap (bottom-left):** Shows all three zones. Active zone is highlighted. Click to switch zones. Robot traffic is visualized as moving dots; congestion appears as clusters.
- **Plant Palette (bottom-center):** Available plants for the current zone. Greyed out if insufficient resources. Tooltip shows cost and biology blurb.
- **Speed / Pause (bottom-right):** 1x, 2x speed toggle. Pause button freezes the game and opens the full info overlay.

### Resource Icons (High-Contrast, Colorblind-Safe)

| Resource | Icon Color | Shape | Label |
|---|---|---|---|
| Glucose | Yellow (#FFD700) | Cube | "GLU" |
| ATP | Blue (#4488FF) | Lightning bolt | "ATP" |
| Amino Acids | Green (#44BB44) | Triangle/wedge | "AA" |
| O2 | White (#FFFFFF) | Circle | "O2" |
| CO2 | Grey (#888888) | Circle with X | "CO2" |
| Fat | Orange (#FF8844) | Rounded square | "FAT" |
| Lactic Acid | Red (#FF4444) | Drip/blob | "LA" |

All icons include both a distinct shape and a text label, ensuring they are distinguishable without relying solely on color.

### Accessibility Features

This game is designed for a classroom that includes students with diverse disabilities. Accessibility is not optional.

| Feature | Implementation |
|---|---|
| **Mouse-Only Play** | All interactions are point-and-click. No keyboard required. No drag precision needed -- click to select, click to place. |
| **Keyboard Alternative** | Arrow keys navigate the grid; Enter/Space to place. Tab cycles through UI elements. Full keyboard-only play is supported. |
| **Game Speed Control** | 0.5x, 1x, 2x speed. 0.5x gives students with processing difficulties more time. |
| **Pause Anytime** | Spacebar or Pause button. The game pauses completely; students can read info panels at their own pace. |
| **Text-to-Speech** | Every info panel, tooltip, and biology blurb has a speaker icon. Clicking it reads the content aloud using the OS TTS engine. |
| **Font Scaling** | Settings menu allows Small / Medium / Large text. Default is Medium (16px base). |
| **High-Contrast Mode** | Optional setting that increases outline thickness and background contrast for all game elements. |
| **Colorblind-Safe Design** | Icons use shape + label + color (triple encoding). No information is conveyed by color alone. |
| **Screen Reader Hints** | Key game events are announced via the Godot Accessibility addon or OS accessibility API (stretch goal). |
| **Reduced Motion** | Option to disable particle effects and reduce animation speed for students sensitive to visual motion. |
| **Auto-Save** | Game state saves every 30 seconds. Students can close the browser and resume later. |
| **Difficulty Settings** | Easy (slower zombies, higher ATP cap, more starting robots), Normal, Hard. Teacher can lock difficulty per student. |

### Visual Feedback Systems

| Event | Visual Cue | Audio Cue |
|---|---|---|
| CO2 buildup (>3) | Plants turn grey and "shiver" | Low rumbling hum |
| O2 starvation | Mitochondrion flickers and dims | Warning chime |
| Lactic Acid cramp | Screen edges pulse red briefly | Muscle-cramp sound effect |
| Robot traffic jam | Minimap cluster glows yellow | Soft alert ping |
| Plant destroyed | Brief flash, debris particles | Crunch sound |
| Wave incoming | Right edge of screen glows red | Horn/alarm |
| Level complete | Celebratory border glow | Victory jingle |

---

## 11. Tutorial & Onboarding

The tutorial is integrated into Level 1 as a guided experience. A narrator character ("The Textbook," a friendly floating book) provides step-by-step instructions.

### Tutorial Sequence (Level 1)

1. **"Your Cell is Hungry"** -- Narrator explains the Cell Core and that it needs ATP to survive.
2. **"Plant a Starch Melon"** -- Player is guided to plant a Starch Melon in the Backyard. Arrow indicator highlights the grid slot.
3. **"Place a Harvester"** -- Player places a Harvester next to the Melon.
4. **"Meet Your Robots"** -- A Clear-Bot picks up the Starch and carries it to the House. Narrator explains the zone system.
5. **"The Digester"** -- Starch is processed into Glucose. Narrator: "Your body breaks big food molecules into small ones it can use."
6. **"Power Up"** -- Robot delivers Glucose + O2 to the Mitochondrion. ATP is produced. Counter fills up.
7. **"Here They Come!"** -- First wave (2 slow Mooks). Player places Zap-Grass using ATP. Narrator explains the cost.
8. **"Clean Up"** -- Mooks die and leave CO2. Narrator: "CO2 is waste from your cell working hard. Your robots need to carry it to the Window so you can keep breathing."
9. **"You Survived!"** -- Wave complete. Brief biology recap.

Subsequent levels have lighter guidance: a tooltip appears when a new mechanic is first available, but the player is not hand-held.

---

## 12. Art & Audio Direction

### Art Style

- **Aesthetic:** Friendly, colorful, slightly cartoonish. Approachable for 12-13-year-olds. Not overly "babyish."
- **Sprite Resolution:** 64x64 pixels for all game entities. 32x32 for small items (resources, projectiles).
- **Tileset:** Each zone has a distinct tilemap palette:
  - Backyard: Earthy greens and browns (garden).
  - House: Warm interior tones (pinks/reds for body-interior metaphor).
  - Front Yard: Cool cellular blues and purples (cytoplasm).
- **Animation:** Simple 2-4 frame loops for idle, active, and damaged states.
- **Reference:** The art should evoke the visual style of *Plants vs. Zombies* (bright, readable silhouettes) adapted with a biological/cellular theme.

### Audio

- **Music:** Light, upbeat loop per zone. Tempo increases slightly during waves. Mutable.
- **SFX:** Distinct sounds for: plant placement, resource pickup/delivery, zap attack, seed projectile, zombie groan, zombie death, structure disable, fermentation bubble, cramp event, wave start/end, level complete/fail.
- **Volume Controls:** Separate sliders for Music, SFX, and TTS. All default to 70%.
- **No Audio Dependency:** All information conveyed by sound is also conveyed visually. Audio enhances but is never required.

---

## 13. Technical Architecture

### Framework & Export

- **Engine:** Godot 4.x (GDScript).
- **Export Target:** HTML5 (WebAssembly) for deployment to GitHub Pages.
- **Development Assistance:** AI tools (Claude Opus/Sonnet) used for code generation, debugging, and documentation. These are development aids, not runtime components.

### Data Architecture

- **Level Definitions:** JSON files in `/data/levels/`. Define wave composition, timing, unlock triggers.
- **Entity Stats:** JSON files in `/data/entities/`. Plant stats, zombie stats, robot stats. Easily tunable without code changes.
- **Save System:** JSON-based. Auto-saves to browser LocalStorage every 30 seconds. Stores: current level, star ratings, unlocked entities, in-progress level state (resources, plant positions, wave progress).

### Project Structure

```
/scenes/         -- Godot .tscn scene files
  main_menu.tscn
  game.tscn
  backyard.tscn
  house.tscn
  front_yard.tscn
  hud.tscn
/scripts/        -- GDScript files
  /entities/     -- Plant, Zombie, Robot, Structure scripts
  /systems/      -- ResourceManager, WaveManager, RobotAI, SaveSystem
  /ui/           -- HUD, Minimap, InfoPanel, TTS
/assets/
  /sprites/      -- 64x64 and 32x32 PNGs
  /audio/        -- .ogg music and SFX
  /reference/    -- Reference images (local, not hotlinked)
/data/
  /levels/       -- Level definition JSONs
  /entities/     -- Entity stat JSONs
  /saves/        -- (runtime, browser LocalStorage)
```

### Key Systems

1. **ResourceManager (Singleton):** Tracks global ATP pool and per-zone resource inventories. Enforces limits and spoilage timers.
2. **WaveManager:** Reads level JSON, spawns zombie waves on timers, manages inter-wave breaks, triggers win/loss.
3. **RobotAI:** State machine per robot. States: Idle -> SeekPickup -> Carry -> Deliver -> Return. Priority system determines SeekPickup target.
4. **MitochondrionStateMachine:** States: Idle (waiting for input), Active (processing Glucose+O2), OutputReady (ATP+CO2 available for pickup), Disabled (Hijacker effect, 10s timer).
5. **RibosomeBuildQueue:** Queue-based. Player adds build orders; Ribosome processes them sequentially, consuming resources as each build starts.

---

## 14. Errata: Issues Corrected from v1.0

### Biological Errors

| # | Section | v1.0 Text | Issue | v2.0 Correction |
|---|---|---|---|---|
| 1 | 4 (Pathogens) | Mooks "Leave behind empty O2 canisters (CO2)" | CO2 is not "empty O2." They are chemically distinct molecules (O2 vs CO2). Teaching students that CO2 is "used-up oxygen" would create a misconception. | Mooks drop "CO2 waste canisters" -- framed as metabolic waste from combat exertion, not as transformed oxygen. |
| 2 | 4 (Pathogens) | Bosses "Leave trails of Lactic Acid sludge" | Lactic acid is an *internal* byproduct of anaerobic fermentation. Pathogens do not produce lactic acid. Associating it with external enemies conflates two unrelated concepts. | Boss trails renamed to generic "damaging sludge" (representing infection/inflammation). Lactic acid exclusively tied to the Fermentation Vat. |
| 3 | 2C (Front Yard) | Ribosome: "Amino Acids + ATP -> Plants" | Ribosomes produce *proteins*, not plants. The output is protein-based structures that happen to function as defensive units in the game. | "Amino Acids + ATP -> Protein Structure (Defensive Unit)." |
| 4 | 4 (Pathogens) | Health Formula: "2 + 1 per level" with "(e.g., a Level 4 Boss has significantly more health than a Level 1 Mook)" | Ambiguous. "Per level" could mean zombie tier or game level. The example compares across tiers (Boss vs Mook), making it even more confusing. | Clarified: Base HP = 2 + (Current Game Level). Tier modifiers are multipliers (Mook 1x, Boss 3x). |

### Naming Inconsistencies

| # | Issue | Resolution |
|---|---|---|
| 5 | GDD title "Cells vs Zombies" vs README title "Cellular Defense" | Noted in Section 1. Recommend settling on one title before release. |
| 6 | "Shock-Grass" (GDD v1.0) vs "Zap-Grass" (README) | Standardized to "Zap-Grass" throughout v2.0 to match README. |
| 7 | "Eaters" introduced in Section 2A, never mentioned again | Renamed to "Harvester" with full mechanic description. |

### Missing Mechanics (not flagged as omissions in v1.0)

| # | Omission | v2.0 Section |
|---|---|---|
| 8 | No win or loss conditions | Section 9 |
| 9 | No wave structure or spawn timing | Section 6 |
| 10 | No placement system (grid, zones, rules) | Sections 3A, 3C |
| 11 | No control scheme / input method | Section 10 |
| 12 | No zombie pathing or combat interaction | Section 6 |
| 13 | No plant HP or destruction mechanic | Section 5 |
| 14 | No robot count, building, or upgrade path | Section 7 |
| 15 | No resource quantities, production rates, or limits | Section 4 |
| 16 | No level count or progression structure | Section 9 |
| 17 | No tutorial or onboarding | Section 11 |
| 18 | No art or audio direction | Section 12 |
| 19 | No scoring or assessment system | Sections 2, 9 |
| 20 | No curriculum/standards alignment | Section 2 |
| 21 | No Fat/Adipose storage mechanics (mentioned but undefined) | Section 3B |
| 22 | No Fermentation "Cramp" mechanic definition (mentioned but undefined) | Section 8 |
| 23 | No difficulty settings | Section 10 |

### Non-Optimal Practices

| # | Issue | Resolution |
|---|---|---|
| 24 | External hotlinked image (Google encrypted URL / Shutterstock) -- link will rot, attribution insufficient, possibly unlicensed | Removed. Reference images should be stored locally. |
| 25 | LaTeX notation ($O_2$, $\rightarrow$) used throughout -- does not render in most markdown viewers, Godot, or browsers | Replaced with plain text (O2, CO2, ->) in v2.0. LaTeX is acceptable only in documents viewed in LaTeX-aware renderers. |
| 26 | "Logic Engine: Claude Opus/Sonnet 4.5" listed as a technical component alongside Godot | Clarified in Section 13: AI tools are development aids, not runtime components. |
| 27 | Robot priority "O2 > Waste > Glucose > ATP" | Inverted to prioritize ATP delivery and glucose transport. See Section 7. |
| 28 | Aerobic respiration ATP yield never stated (only fermentation's 2 ATP given) | Both values now explicit in Section 8 for direct comparison. |
| 29 | Accessibility section minimal despite target audience being students with disabilities | Expanded to comprehensive table in Section 10 covering motor, visual, cognitive, and auditory accessibility. |

---

## 15. Open Questions & Future Work

The following items are intentionally deferred and flagged as open:

- **Title Decision:** "Cells vs Zombies" or "Cellular Defense"? Settle before public release.
- **Teacher Dashboard:** Export per-student data for grade integration. Requires server-side component or downloadable JSON.
- **Additional Levels (6-10):** Could cover photosynthesis, DNA replication, immune response.
- **White-Bot (Immune Cell):** A third robot type that actively attacks zombies. Would reinforce immune system concepts. Deferred to avoid scope creep.
- **Multiplayer / Co-op:** Two students managing different zones. Interesting pedagogically (collaboration) but significant technical lift.
- **Mobile Support:** Touch input. Godot HTML5 export supports touch but UI would need adaptation.
- **Localization:** English-only for v1. Spanish and other languages for classroom diversity.
- **Robot Speed Tuning:** As noted in v1.0's developer reflection, robot travel time is the single most important balance lever. Playtesting with actual students is required before finalizing values.
