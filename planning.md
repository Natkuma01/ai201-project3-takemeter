# TakeMeter Project Planning: Pokémon Community Discourse Classifier

## 1. Community Selection & Rationale
* **Chosen Community:** Pokémon Online Communities (`r/pokemon` / `r/stunfisk`).
* **Why this community?** Pokémon discourse is highly varied. It contains a passionate mix of casual fans, lore theorists, and hardcore competitive players. A single thread can contain a 5-paragraph mathematical breakdown of a Pokémon's competitive viability, a wild "hot take" claiming a universally loved generation is terrible, and pure emotional hype over catching a shiny or a new game trailer. This makes it an incredibly rich environment for testing a text classifier.

---

## 2. Label Taxonomy & Definitions

### Label: `strategy_lore`
* **Definition:** The post or comment provides a structured, detailed argument backed by specific, verifiable game data (e.g., base stats, movepools, damage calculations, tier placements) or deep in-game lore/textual evidence.
* **Example 1:** *"In Regulation G, Incineroar remains dominant because its access to Intimidate, Fake Out, and Parting Shot allows it to control the board pace. Even with the introduction of new attackers, its 95/90/90 bulk lets it survive a Choice Specs Tera Stellar Astral Barrage from Calyrex-Shadow."*
* **Example 2:** *"The Paradox Pokémon aren't actually from the past or future; they are physical manifestations of the Professor’s imagination created by the Terapagos crystals. Look at the Occulture magazines in the academy—the sketches existed before the time machine was even built."*

### Label: `hot_take`
* **Definition:** A bold, highly subjective, or controversial opinion about Pokémon designs, game quality, generation rankings, or competitive balance stated as a fact without structural or data-backed evidence.
* **Example 1:** *"Generation 5 has the absolute worst designs in the entire franchise. Trubbish and Vanillite prove Game Freak completely ran out of ideas, and Black/White are entirely unplayable because of it."*
* **Example 2:** *"Charizard is completely mid and only gets attention because of nostalgic Gen 1 boomers. If it came out in Gen 9, literally nobody would care about it."*

### Label: `reaction`
* **Definition:** An immediate emotional response, exclamation, or surface-level feeling regarding a game announcement, a personal milestone (like a shiny hatch), or a casual observation without any deeper argumentative backing.
* **Example 1:** *"OH MY GOD AFTER 4,000 RESETS I FINALLY GOT THE SHINY RAYQUAZA!!! I'M CRYING RIGHT NOW LFG!!!"*
* **Example 2:** *"This new Legends trailer looks absolutely incredible!! The graphics look so much better and I cannot wait until next year!"*

---

## 3. Hard Edge Cases & Boundary Rules

### The Ambiguity: The "Decorative Stat" or "Cherry-Picked Lore"
The hardest edge case occurs when a user shares a highly emotional or controversial opinion (`hot_take`) but drops a single base stat, move, or lore fact to make it look like a well-thought-out argument.
* **Edge Case Example:** *"Pikachu is unironically a top-tier competitive threat in modern formats because Light Ball doubles its Attack stats."*

### Explicit Decision Rule
To maintain strict mutual exclusivity, we will apply **The Subtraction Rule**:
* If you strip away the personal bias or aggressive opinion from the text, does a multi-step, objectively sound argument remain? 
* In the Pikachu example above, the claim ignores the reality of Pikachu's terrible base bulk and speed tiers, using one item fact to push a wild opinion. Therefore, it is a **`hot_take`**. If the post went on to calculate specific damage match-ups against meta threats, it would become **`strategy_lore`**.

---

## 4. Data Collection Plan
* **Source:** Public threads on Reddit (`r/pokemon` for casual/lore, `r/stunfisk` for competitive strategy).
* **Target Sample Size:** Minimum 200 total examples, aiming for an even split (~65–70 examples per label).
* **Imbalance Strategy:** If `strategy_lore` is underrepresented, I will intentionally pull from competitive "Rate My Team" threads or lore-specific discussion tags to balance the dataset.

---

## 5. Evaluation Metrics
1. **Overall Accuracy:** For overall pipeline health.
2. **Macro-Averaged F1-Score:** To ensure the classifier performs evenly across all three labels, regardless of data distribution skew.
3. **Per-Class Precision for `strategy_lore`:** If a user uses a tool to filter for deep strategy/lore, they don't want low-effort hot takes ruining their feed. We want precision for this class to be high.

---

## 6. Definition of Success
* **Minimum Viable Accuracy:** $\ge 75\%$ overall accuracy on the test set.
* **Target F1-Score:** Macro F1 $\ge 0.70$.
* **Precision Safeguard:** Precision for `strategy_lore` must be $\ge 80\%$.

---

## 7. AI Tool Plan
* **Label Stress-Testing:** I will ask an LLM to generate borderline Pokémon statements to test my "Subtraction Rule" before manual annotation.
* **Annotation Assistance:** I will completely hand-label all 200+ examples myself to prevent AI bias.
* **Failure Analysis:** I will feed the model's eventual mistakes into an LLM to look for structural confusion patterns (e.g., short posts vs. long posts).