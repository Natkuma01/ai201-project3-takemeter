# TakeMeter: Pokémon GO Community Take-Evaluator

This is an AI text classifier built to look at posts and comments from Pokémon GO online spaces (like r/TheSilphRoad and r/PokemonGO) and figure out what kind of "take" a player is sharing. 


---

## 1. What Are We Measuring? (The Labels)

Every post is sorted into one of three buckets:

*   **`strategy_lore`**: This is for the hardcore stats and facts. If a post talks about exact IV numbers, PvP movesets, break points, or optimal raid counters, it goes here.
*   **`hot_take`**: This is for bold, spicy opinions stated as absolute facts without any real data or proof to back them up (like claiming the game is rigged against you).
*   **`reaction`**: This is for pure emotion or simple updates. Things like absolute hype over catching a rare shiny Pokémon, or venting because the app crashed.

### Our Golden Rule: The Subtraction Rule
Sometimes players drop a single number or stat just to make their angry opinion sound smart. To handle this, we use the **Subtraction Rule**: Imagine removing all the emotion and anger from the post. Is there still a logical, multi-step argument left over? If not, it's just a `hot_take`.

---

## 2. The Dataset

I collected and hand-labeled 200 real examples from the Pokémon GO community. The notebook automatically split them up like this:
*   **Training Set (140 posts)**: Used to teach our model (`strategy_lore`: 50, `reaction`: 47, `hot_take`: 43).
*   **Validation Set (30 posts)**: Used to check the model's homework while it was learning.
*   **Test Set (30 posts)**: Saved in a locked box until the very end to give both models a final exam (`strategy_lore`: 11, `reaction`: 10, `hot_take`: 9).

### Hard Annotation Border Conflicts (Edge Cases)

During the hand-labeling phase, separating lines became blurry. Here are three genuinely difficult examples encountered and the definitive labeling decisions made:

1. **The Single Fact Rant**
   * *Text:* "The catch rate of Galarian birds is a joke and makes the entire daily incense feature completely unrewarding."
   * *Decision:* `hot_take`
   * *Reasoning:* While "catch rate" is a standard game property, removing the user's frustration leaves no actual data calculation or logical testing behind. Applying the *Subtraction Rule* explicitly marks this as a sweeping personal opinion stated as a definitive rule.

2. **The Sarcastic Achievement**
   * *Text:* "Wow, another 2-star event spawn. Thank you so much for this amazing reward, Niantic."
   * *Decision:* `reaction`
   * *Reasoning:* This uses vocabulary that looks like it could be a programmatic breakdown of spawn rates, but the context is purely a sarcastic emotional expression of disappointment. It is an immediate social reaction with zero multi-step logic.

3. **The Mechanical Inquiry**
   * *Text:* "Does anyone want to trade gifts from different countries? I really need postcards from the ocean region."
   * *Decision:* `reaction`
   * *Reasoning:* This string discusses technical global geography features ("ocean region", "postcards"). However, it contains no tactical evaluation, damage thresholds, or team composition theory. It is a straightforward interactive personal request, placing it safely inside the casual reaction bucket.


---

## 3. The Final Exam Results

Two different AIs on our locked test set: a giant general-knowledge model (`Llama 3.3 70B` via Groq) acting as our zero-shot baseline, and our small, custom fine-tuned `DistilBERT` model.

### Side-by-Side Scoreboard
| Metric / Class | Zero-Shot Baseline (Llama 3.3) | Fine-Tuned DistilBERT |
| :--- | :---: | :---: |
| **Overall Accuracy** | **1.000 (100%)** | **0.567 (56.7%)** |
| `strategy_lore` F1-Score | 1.00 | 0.63 |
| `hot_take` F1-Score | 1.00 | 0.00 |
| `reaction` F1-Score | 1.00 | 0.75 |

### Where the Fine-Tuned Model Guessed (Confusion Matrix)
The rows show what the post *actually* was, and the columns show what the fine-tuned model *guessed*.

| True Category \ Model Guessed | `strategy_lore` | `hot_take` | `reaction` |
| :--- | :---: | :---: | :---: |
| **`strategy_lore`** | 11 | 0 | 0 |
| **`hot_take`** | 9 | 0 | 0 |
| **`reaction`** | 4 | 0 | 6 |

---

## 4. Honest Mistake Analysis

Looking at the table above, a major pattern jumps out: **The fine-tuned model completely failed to learn what a `hot_take` is.** It didn't guess it a single time! Instead, it got confused and dumped all the hot takes—and even some emotional reactions—right into the `strategy_lore` bucket.

### 3 Examples the Model Got Wrong

1.  **The PvP Algorithm Conspiracy Theory**
    *   *Text:* "The Go Battle League algorithm tracks your team composition and purposely pairs you with absolute hard counters the moment you hit a six-game win streak."
    *   *True Label:* `hot_take`
    *   *Model Guessed:* `strategy_lore` (Confidence: 0.42)
    *   *Why it tripped up:* The post uses gaming words like "algorithm," "team composition," and "win streak." Small model associates these vocabulary words with game data, it assumed the post was a strategic breakdown, missing the fact that it's an unproven conspiracy theory.

2.  **The App Crash Rage-Vent**
    *   *Text:* "The app just crashed right as I threw the ball at a shiny legendary and now it is completely gone."
    *   *True Label:* `reaction`
    *   *Model Guessed:* `strategy_lore` (Confidence: 0.36)
    *   *Why it tripped up:* Words like "threw the ball" and "shiny legendary" are strong gaming terms. The model focused too much on these items and didn't notice the emotional tone of the user losing their catch.

3.  **Asking for Friends to Trade Gifts**
    *   *Text:* "Does anyone want to trade gifts from different countries? I really need postcards from the ocean region."
    *   *True Label:* `reaction`
    *   *Model Guessed:* `strategy_lore` (Confidence: 0.36)
    *   *Why it tripped up:* The comment mentions game features ("trade gifts," "postcards," "ocean region"). The model didn't realize this was just a casual social favor, marking it as a strategic gameplay post instead.

### Why Did This Happen and How Do We Fix It?
Because the training dataset was small (140 examples), the model looked for easy shortcuts. Since `strategy_lore` was the biggest class in training, the model learned that if a post contains standard Pokémon GO words, the safest bet to lower its mathematical error was to just guess `strategy_lore`. 

To fix this, we would need to:
*   Collect way more data (at least 1,000 examples instead of 200).
*   Include a lot more spicy, jargon-heavy complaints in the training set so the model learns that technical words don't automatically mean a post is educational.
*   Train for more than 3 epochs so the model can look past individual words and read the actual sentence structure.

---

## 5. Sample Run Examples

Here is how the fine-tuned model handles a few posts during a live test run:

*   **Example 1 (Correct Guess):**
    *   *Text:* "Wow the sky graphics during this twilight event look absolutely beautiful shoutout to the visual designers."
    *   *Model Predicted:* `reaction` (Confidence: 0.37)
    *   *Why it makes sense:* The words "beautiful" and "shoutout" are purely emotional and have nothing to do with stats or mechanics, so the model easily got this one right.
*   **Example 2 (Incorrect Guess):**
    *   *Text:* "XL candies are an artificial barrier meant to force casual players out of competitive Master League formats entirely."
    *   *Model Predicted:* `strategy_lore` (Confidence: 0.42)
    *   *True Category:* `hot_take`

---

## 6. Reflections

### What the Model Actually Learned vs. What We Wanted
We wanted the model to understand the *vibe* and *intent* of a post—separating cold hard facts from angry rumors. Instead, the model just learned a basic word-matching game: if game mechanics are mentioned, call it strategy. This shows that while a massive model like Llama 3.3 understands nuance instantly without training, a tiny model like DistilBERT needs a lot more diverse examples to grasp complex human behavior like sarcasm or complaining.

### How the Project Guidelines Helped or Changed
*   **The Help:** Forcing us to write clear definitions and an explicit "Subtraction Rule" early on is the exact reason our baseline Llama model got a perfect 100% score—the logic was perfectly clear.
*   **The Change:** Instead of using a standard CSV format which easily breaks when internet comments have commas or weird spacing, we changed our dataset format to split text using `|||`. This kept our code running smoothly without data corruption errors.

---

## 7. AI Tool Disclosures

Use AI assistance in two specific ways during this project:
1.  **Prompt Blueprinting (Milestone 4)**: We gave our custom definitions and taxonomy to an LLM to help build our Groq system prompt. We manually tweaked the output to make sure our "Subtraction Rule" was highlighted word-for-word.
2.  **Pattern Diagnostics (Milestone 6)**: We pasted our 13 wrong predictions into an LLM and asked it to find common threads. It helped point out that the model was completely ignoring user sentiment and acting as a simple keyword-spotter. We verified this by re-reading the text samples ourselves.


## 8. Confidence Calibration Analysis

A calibration review was conducted to see if the model's inner probability alignment matched its true accuracy:
* **Average Confidence when Correct:** 0.385
* **Average Confidence when Wrong:** 0.398

**Analysis:** The model is completely uncalibrated. A well-calibrated model should exhibit high confidence (~0.80+) when correct, and low confidence (~0.40) when making an error. Because our DistilBERT model's confidence scores remain completely flat (hovering around the random 0.33 guess baseline across all outcomes), it shows the model is just guessing down the middle and cannot reliably flag its own uncertainty.


## 9. Systematic Error Pattern Analysis

Our evaluation exposed a stark, systematic failure pattern involving the **`hot_take` ➔ `strategy_lore`** label pair. Out of 9 total hot takes in the test set, the model misclassified **100% of them** as strategy breakdowns. 

#### The Shortcut Pattern: "Jargon-Blindness"
The error data reveals that the model operates entirely as an un-contextual keyword spotter. It consistently matches surface-level gaming vocabulary to structural intent while ignoring the semantic sentiment surrounding them:
* When a post mentions programmatic structural nouns like *"algorithm"*, *"composition"*, or *"win streak"*, the model ignores the underlying unproven conspiracy theory framing (e.g., "GBL is rigged").
* Because genuine strategy posts use these exact same feature words, the model exhibits a severe systemic blind spot: it treats any text describing game architecture properties as a factual calculation. 

To break this pattern, future iterations require training adjustments to penalize structural vocabulary when bound directly to high-sentiment toxicity markers or subjective hyperbole.