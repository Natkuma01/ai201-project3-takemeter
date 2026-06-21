# TakeMeter Project Planning: r/nba Discourse Classifier

## 1. Community Description
The `r/nba` subreddit is a massive online community dedicated to National Basketball Association basketball. Discourse ranges from deeply analytical breakdowns of team strategies to reactionary memes and emotional outbursts during live games. Distinguishing high-substance insights from low-effort emotional reactions or unbacked declarations is vital for users navigating the sub to find genuine basketball discussion rather than pure noise.

## 2. Label Taxonomy

### Label 1: `analysis`
* **Definition:** The post or comment makes a structured argument backed by specific, verifiable evidence such as player statistics, salary cap numbers, injury history, or tactical film observations.
* **Example 1:** *"The Celtics' offensive rating drops by 6.4 points when Tatum sits because their bench units lack a secondary playmaker who can collapse the paint. Look at yesterday's game film where Pritchard struggled to run the high pick-and-roll."*
* **Example 2:** *"Trading for Grant makes zero financial sense. He has 3 years left on a $90M deal, which triggers the second apron hard cap for us next offseason under the new CBA rules."*

### Label 2: `hot_take`
* **Definition:** A bold, confident, or provocative opinion regarding player/team quality or future outcomes stated without systematic supporting evidence. 
* **Example 1:** *"Embiid will never win a championship. He completely lacks the mental toughness required to carry a team past the second round of the playoffs."*
* **Example 2:** *"SGA is already a significantly better all-around player than peak James Harden ever was, and it's honestly not even close."*

### Label 3: `reaction`
* **Definition:** An immediate emotional outburst, exclamation, or surface-level response to a specific, real-time event (e.g., a trade, an injury, a spectacular play) expressing a feeling without an overarching argument.
* **Example 1:** *"OH MY GOD TATEUM JUST POSTERIZED HIM!!! INSANE DUNKKKK LFG!!!"*
* **Example 2:** *"I can't believe our front office just gave up three first-round picks for a backup center. I am absolutely sick to my stomach. Fire everyone."*

---

## 3. Boundary Definition & Hardest Anticipated Edge Case

### The Borderline Scenario: The "Decorative Stat"
The hardest edge case occurs when a post expresses a highly subjective, emotionally charged statement or an aggressive opinion (`hot_take`), but pulls a single, cherry-picked statistic to look credible (`analysis`).

* **Edge Case Example:** *"LeBron is completely overrated as a clutch player—his playoff win rate against top-seeded opponents when trailing in the 4th quarter is below .500."*

### Explicit Decision Rule
To maintain **mutual exclusivity**, we apply the following rule:
* If a post includes data or a statistic, look at its context. If the statistic is used as a standalone tool to punch up a sweeping, emotional statement (a "decorative stat"), classify it as **`hot_take`**.
* If the post uses the statistic as part of an objective, multi-step logical argument that would remain coherent even if the opinionated framing was removed, classify it as **`analysis`**.