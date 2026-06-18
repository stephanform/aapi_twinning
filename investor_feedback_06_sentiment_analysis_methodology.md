# Sentiment Analysis — Methodological Note

## Context

This note documents the sentiment analysis approach applied to the free-text responses in the Investor Acquisition Feedback survey (Q16–Q18). It is intended as a methodological reference for readers of the dashboard and the broader research documentation.

---

## Questions Analysed

| Question | Type | Content |
|----------|------|---------|
| Q16 | Open text | "How would you describe the atmosphere of this event?" |
| Q17 | Open text | "How do you assess the probability of successful acquisitions resulting from this event?" |
| Q18 | Open text | "What would motivate you to participate in a future edition of this event?" |

Each question captures a different emotional register: Q16 is experiential and impressionistic, Q17 is forward-looking and outcome-oriented, Q18 is conditional and constructive. Sentiment therefore carries a different practical meaning across the three questions and results should not be directly compared without this context in mind.

---

## Approach: LLM-Based Sentiment Scoring

Rather than a rule-based lexicon approach (e.g. VADER), sentiment scores were generated using a large language model (Claude Sonnet, Anthropic). This approach was chosen for the following reasons:

- **Domain sensitivity.** Investor language is specialised. Terms such as "conservative valuation", "cautious outlook", or "early stage" carry connotations that differ substantially from their general-language meanings. A word-list approach would systematically misclassify such responses.
- **Contextual understanding.** The model processes entire sentences as units, handling negation ("not bad"), hedged language ("too early to say"), and compound sentiment ("inspiring atmosphere, but poorly paced sessions") in a way that word-counting cannot.
- **Efficiency.** The survey dataset contained 1,000 responses across three questions, but only 21 unique Q16 responses, 19 unique Q17 responses, and 19 unique Q18 responses — 59 unique texts in total. Scoring was performed once per unique text, with scores then assigned to all matching records via lookup.

---

## Scoring Process

Each unique response was submitted to the model with the following structured prompt:

> *"Score each investor survey response for sentiment in the context of an investor acquisition event. Return a sentiment score from −1.0 (very negative) to +1.0 (very positive), a label (positive / neutral / negative), and 2–3 keywords capturing the main themes."*

The model returned three outputs per response:

1. **Sentiment score** — a continuous value on the interval [−1.0, +1.0]
2. **Sentiment label** — one of: `positive`, `neutral`, `negative`
3. **Keywords** — 2–3 short phrases summarising the dominant themes

Label thresholds applied:

| Label | Score range |
|-------|-------------|
| Negative | score < −0.10 |
| Neutral | −0.10 ≤ score ≤ +0.10 |
| Positive | score > +0.10 |

These thresholds are a design choice, not a mathematical law. A stricter threshold (e.g. ±0.30) would reduce the size of the positive and negative groups and expand the neutral category. The current thresholds were chosen to reflect the relatively direct and unambiguous language typical of investor feedback.

---

## Validation

As an internal validity check, sentiment scores were correlated with the corresponding structured ordinal ratings:

- **Q16 sentiment × Q10 Overall Satisfaction**: Pearson r = **0.747**
- **Q17 sentiment × Q13 Likelihood to Recommend**: see dashboard

The strong correlation between Q16 text sentiment and Q10 satisfaction ratings — two independently collected measures of the same underlying experience — suggests the scoring is capturing genuine signal rather than noise. Investors who wrote positively about the atmosphere also rated overall satisfaction significantly higher on the structured scale, and vice versa.

---

## Limitations

- **Fixed response set.** The survey used a limited number of pre-authored template responses rather than fully open free text. This means the sentiment scores represent the range of those templates, not the full diversity of investor language that would appear in a genuinely open-ended survey. The 59 unique texts cover a reasonable spectrum from strongly negative to strongly positive, but intermediate nuances may be underrepresented.
- **Single scorer.** Each response was scored once by a single model without cross-validation against human judges. For publication-grade research, inter-rater reliability testing against human annotators would be recommended.
- **Label discretisation.** Collapsing the continuous score into three categories (positive / neutral / negative) involves information loss. The continuous score is available in the underlying dataset for more granular analysis.
- **Model versioning.** Scores were generated using Claude Sonnet 4.6 (Anthropic, 2025). Results may differ if regenerated with a different model version.

---

## Software and Tools

| Component | Tool |
|-----------|------|
| Data processing | Python 3.12, pandas |
| Sentiment scoring | Claude Sonnet 4.6 via Anthropic API |
| Dashboard visualisation | HTML/JavaScript, Chart.js 4.4.1 |

---

*Prepared as part of the Investor Acquisition Event Feedback Analysis project, Hochschule Bremen, 2025.*
