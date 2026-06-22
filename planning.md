# TakeMeter Project Planning: Pokémon GO Community Discourse Classifier

## 1. Community Selection & Rationale
* **Chosen Community:** Pokémon GO Online Communities (`r/TheSilphRoad` / `r/PokemonGO`).
* **Why this community?** Pokémon GO discussions have a lot of variety. The community has casual players, people who track news, and hardcore players who study hidden game numbers. A single thread can contain a 3-paragraph breakdown of raid battle damage counters, a wild opinion claiming a new update ruined the game, or pure excitement about catching a rare shiny legendary. This mix makes it a great place to test a text classifier.

---

## 2. Label Taxonomy & Definitions

### Label: `strategy_lore`
* **Definition:** The post or comment makes a clear argument using exact in-game facts, such as IV (Individual Value) stats, PvP movesets, break points, raid counters, or official map mechanics.
* **Example 1:** *"For the upcoming Master League meta, you need a 15/15/14 Dialga because missing even one point in defense drops your breakpoint against Dragon Breath users, causing you to lose the mirror match completely."*
* **Example 2:** *"Primal Kyogre is the absolute best raid counter for primal Groudon raids because its water type damage gets boosted by rainy weather, allowing a group of 3 players to finish the raid easily."*

### Label: `hot_take`
* **Definition:** A bold, strong opinion about Niantic's choices, event ticket prices, shiny encounter odds, or features that is stated as an absolute fact without real proof or game data to back it up.
* **Example 1:** *"The new update is a total scam designed to make remote players quit completely. Niantic is intentionally lowering shiny rates for anyone who doesn't buy the expensive live event tickets."*
* **Example 2:** *"GO Battle League is completely rigged. The game intentionally matches you against an exact hard-counter team the moment you win three games in a row to keep your win rate at 50%."*

### Label: `reaction`
* **Definition:** A quick emotional response or simple feeling about an event, a personal achievement, or a bad connection error with no deep logical argument.
* **Example 1:** *"OH MY GOD! After doing 45 raids, I finally caught the 100% IV Shundo Rayquaza!!! I am shaking so much right now!!!"*
* **Example 2:** *"The game froze right as the raid boss died and I lost my pass! I hate this app so much, it is completely broken!"*

---

## 3. Hard Edge Cases & Boundary Rules

### The Ambiguity: The "Decorative Stat"
The hardest edge case happens when a user shares a strong, angry opinion (`hot_take`) but throws in a single stat or number just to make themselves look right.
* **Edge Case Example:** *"Remote raid passes are a complete waste of coins because the catch rate is only 2 percent."*

### Explicit Decision Rule
To keep our labels clear, we use **The Subtraction Rule**:
* Remove the emotional opinion from the text. Is there still a logical, multi-step argument left over?
* In the example above, the user uses a single base capture rate fact just to push a sweeping angry opinion. Because there is no real logical argument left when you take away the anger, we label it a **`hot_take`**. If the post went on to mathematically compare pass costs to catch rewards, it would be **`strategy_lore`**.

---

## 4. Data Collection Plan
* **Source:** Public posts on Reddit (`r/TheSilphRoad` for strategy, `r/PokemonGO` for opinions and hype).
* **Target Sample Size:** At least 200 examples total, aiming for a balanced mix (~65–70 examples per label).
* **Imbalance Strategy:** If we cannot find enough `strategy_lore` posts, I will look specifically inside PvP team-building threads to find them.

---

## 5. Evaluation Metrics
1. **Overall Accuracy:** To see how many posts the model gets right overall.
2. **Macro-Averaged F1-Score:** To make sure the model performs well on all three labels, even if we have more of one label than the others.
3. **Per-Class Precision for `strategy_lore`:** If a user wants to filter out noise to read deep strategy, they do not want opinions showing up. Precision tracks how often our model is right when it guesses this label.

---

## 6. Definition of Success
* **Minimum Accuracy:** 75% or higher overall accuracy on the test set.
* **Target F1-Score:** 0.70 or higher.
* **Precision Safeguard:** Precision for `strategy_lore` must be 80% or higher.

---

## 7. AI Tool Plan
* **Label Stress-Testing:** I will ask an AI to generate tricky, borderline Pokémon GO posts to test my sorting rules before I start labeling.
* **Annotation Assistance:** I will hand-label all 200+ examples myself to ensure they are accurate.
* **Failure Analysis:** I will give the model's mistakes to an AI to help me find patterns in what it got wrong.