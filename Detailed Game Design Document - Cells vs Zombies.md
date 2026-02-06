# Detailed Game Design Document: **Cells vs Zombies**

## 1. Executive Summary

**Cells vs Zombies** is a specialized tower defense game designed for 7th-grade students with disabilities. It translates the biological equations of cellular respiration and protein synthesis into real-time strategy mechanics. The player manages a three-zone economy to defend a "Cell" against pathogens, emphasizing that survival requires the perfect balance of **Matter** (Proteins/Amino Acids) and **Energy** (Glucose/ATP).

---

## 2. The Three-Zone System

The game world is split into three distinct functional areas. Success depends on the **Circulatory System** (Robots) moving resources between them.

### A. The Backyard (External Environment / Source)

- **Purpose:** Resource Generation.
    
- **Mechanics:** Starch Melons and Protein Pods grow here.
    
- **Interaction:** Player must plant "Eaters" (Harvesting units) to collect these.
    
- **Lesson:** Food exists as complex polymers (Starch/Protein) before it enters the body.
    

### B. The House (The Body / Processor)

- **Purpose:** Conversion and Gas Exchange.
    
- **Key Structures:**
    
    - **The Digester:** Breaks Starch into Glucose and Protein into Amino Acids.
        
    - **The Window (Lungs):** The entry point for $O_2$ and the exit point for $CO_2$.
        
    - **The Storehouse:** Contains the **Adipose Silos** (Fat cells) for energy storage.
        
- **Lesson:** The body must process raw materials into monomers (Glucose/Amino Acids) to be useful.
    

### C. The Front Yard (The Cell / Combat)

- **Purpose:** Defense and ATP Production.
    
- **Key Structures:**
    
    - **Mitochondria:** The power plant ($Glucose + O_2 \rightarrow ATP + CO_2$).
        
    - **The Ribosome:** The construction site (Amino Acids + ATP $\rightarrow$ Plants).
        
- **Lesson:** The cell is the site of energy release and structural building.
    

![Image of the structure of an animal cell including mitochondria and ribosomes](https://encrypted-tbn3.gstatic.com/licensed-image?q=tbn:ANd9GcSsaranerOl2JBecfQ7C9luqzREUSVVDjU10LVkVGyvmzqX71ETXSVIJeEkIPgfRdPEbPTFYTGP8uRj7lUaAtFIq0fLmZTdWS0qfUwN09DmMx0yq4Y)

Shutterstock

---

## 3. Movement & Combat (Matter vs. Energy)

### Phase 1: Energy Defense (Levels 1-2)

Early plants are "Energy-Only." They do not require matter to function but are inefficient.

- **Shock-Grass:** Emits a short-range zap. Uses 1 ATP per zap.
    
- **Catchweed:** Entangles a zombie. Uses 1 ATP per second to hold it.
    
- **Strategy:** Students must focus purely on the Mitochondria’s $O_2/Glucose$ balance.
    

### Phase 2: Structural Defense (Level 3+)

Once the player unlocks **Proteins**, they can build physical projectiles.

- **Peashooter:** Requires 5 Amino Acids to build. Fires physical seeds.
    
- **Matter Advantage:** Because the plant has a "body," it is more efficient, using only 0.2 ATP per shot.
    
- **Lesson:** Growth requires matter (Protein), but specialized structures save energy.
    

---

## 4. Pathogen Logic (Zombies)

Zombies act as biological stressors that test the player's system.

- **Health Formula:** $2 + 1 \text{ per level}$ (e.g., a Level 4 Boss has significantly more health than a Level 1 Mook).
    
- **Tier 1: Mooks ($CO_2$ Clouds):** Leave behind empty $O_2$ canisters ($CO_2$) upon death, clogging the yard.
    
- **Tier 2: Shocktroops (Sugar Bandits):** Move fast and steal loose Glucose cubes.
    
- **Tier 3: Minibosses (Hijackers):** Disable Mitochondria or Ribosomes on contact.
    
- **Tier 4: Bosses (Entropy Golems):** Leave trails of Lactic Acid sludge that damage plants and slow robots.
    

---

## 5. The Circular Economy (Robots)

The "Heart" of the game is the transport system.

### The Clear-Bots (Plasma/Generalist)

- **Starts as:** The only robot available.
    
- **Behavior:** Carries 1 item of any type.
    
- **Inefficiency:** It gets distracted by carrying $O_2$ and $CO_2$, often leaving plants without ATP.
    

### The Red-Bots (Red Blood Cells)

- **Unlocked:** Level 2.
    
- **Behavior:** Specialized for Gas Exchange only.
    
- **Advantage:** Can carry multiple canisters at once.
    
- **System Shift:** Freeing up Clear-Bots allows the nutrient economy to flourish.
    

---

## 6. Emergency Systems (Fermentation)

If $O_2$ levels drop (Red-Bot bottleneck), the player can use the **Fermentation Vat**.

- **Input:** 1 Glucose.
    
- **Output:** 2 ATP (Very low) + 1 Lactic Acid (Toxic Waste).
    
- **Trade-off:** Keeps the cell alive, but creates a massive cleanup job for robots, potentially leading to a "Cramp" (system slowdown).
    

---

## 7. User Interface & Accessibility

- **Visual Feedback:** Plants "shiver" and turn grey when $CO_2$ builds up.
    
- **System Overview Minimap:** A simplified real-time view of all three zones. It shows "Traffic Jams" as clusters of robot pixels.
    
- **Information Panels:**
    
    - **Plant/Zombie Details:** Includes a "Biology Blurb" and stats.
        
    - **Text-to-Speech:** A button on every info panel to read the biology content aloud for students with reading disabilities.
        

---

## 8. Development Status (Implementation Plan)

- **Framework:** Godot 4.5.
    
- **Logic Engine:** Claude Opus/Sonnet 4.5.
    
- **Persistence:** JSON-based state saving.
    
- **Next Steps:**
    
    1. Implement the **Mitochondria** state machine ($O_2 + Glucose \rightarrow ATP + CO_2$).
        
    2. Code the **Robot Search Priority** (O2 > Waste > Glucose > ATP).
        
    3. Develop the **Adipose Plant** storage logic (Glucose $\leftrightarrow$ Fat).
        

---

### Final Developer Reflection:

The most critical "fun" element to tune will be the **Robot Travel Time**. If the robots move too fast, the biology is ignored. If they move too slow, the game is frustrating. The goal is to make the student feel that a **well-organized system** (specialized robots and fat storage) makes the defense easy, while a **disorganized system** leads to a crash.