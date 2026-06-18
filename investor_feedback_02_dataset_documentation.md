# Investor Acquisition Event — Feedback Dataset Documentation

## Overview

This document describes the synthetic dataset `investor_feedback_1000.csv`, generated to simulate realistic feedback responses from an investor acquisition event. The dataset contains **1,000 entries** across **18 questions**, structured to match the CSV output of the accompanying HTML questionnaire.

---

## Dataset Structure

The CSV file contains one header row and 1,000 data rows. All values are fully quoted. Column names include the question number, label, and measurement scale type, e.g.:

```
"Q1: Age group (Ordinal)"
"Q8: Typical single deal size considered (Ratio)"
"Q16: How would you describe the atmosphere of this event? (Text)"
```

### Measurement scales used

| Scale | Questions | Description |
|---|---|---|
| Nominal | Q2, Q3, Q5, Q14 | Unordered categories (gender, country, industry, format preference) |
| Ordinal | Q1, Q4, Q10–Q13 | Ranked categories (age group, org size, Likert satisfaction items) |
| Ratio | Q6, Q7, Q8, Q9, Q15 | Numeric with true zero (years, events, deal size, meetings, contacts) |
| Text | Q16, Q17, Q18 | Free-text open answers intended for sentiment analysis |

---

## Tendency Distribution

Each response was assigned one of three sentiment tendencies before generation. Tendencies were shuffled randomly across all 1,000 rows to avoid any clustering by position.

| Tendency | Count | Share |
|---|---|---|
| Positive | 400 | 40% |
| Neutral | 350 | 35% |
| Negative | 250 | 25% |

---

## How Tendency Shapes Each Response

### Likert scale items (Q10–Q13)

Satisfaction, relevance, networking quality, and recommendation likelihood were drawn from a Gaussian distribution centred on a tendency-specific mean, clamped to the 1–5 range.

| Tendency | Centre (μ) | Std. dev. (σ) |
|---|---|---|
| Positive | 4.1 | 0.7–0.9 |
| Neutral | 3.1 | 0.7–0.9 |
| Negative | 2.0 | 0.7–0.9 |

Resulting satisfaction distribution across all 1,000 entries:

| Score | Count |
|---|---|
| 1 | 64 |
| 2 | 194 |
| 3 | 310 |
| 4 | 301 |
| 5 | 131 |

Overall satisfaction mean: **3.24**

### Ratio variables driven by tendency

| Variable | Positive (μ) | Neutral (μ) | Negative (μ) | Std. dev. (σ) |
|---|---|---|---|---|
| Q9: Follow-up meetings planned | 3.5 | 1.8 | 0.6 | 0.8–1.2 |
| Q15: Network contacts to invite | 3.2 | 1.5 | 0.4 | 0.7–1.5 |

All ratio values were floored at 0 (no negative counts).

### Free-text answers (Q16–Q18)

Each tendency maps to a dedicated pool of pre-written responses per question. Entries were sampled randomly within the appropriate pool.

| Question | Topic | Pool size per tendency |
|---|---|---|
| Q16 | Event atmosphere | 8 positive / 8 negative / 5 neutral |
| Q17 | Probability of successful acquisitions | 7 positive / 7 negative / 5 neutral |
| Q18 | Reasons to participate again | 7 positive / 7 negative / 5 neutral |

The text pools were written to reflect realistic investor language and vary in specific details (sectors, formats, concerns) to avoid exact repetition across the dataset. They are suitable as labelled training or test data for sentiment analysis models.

---

## Tendency-independent Variables

Demographic and investment profile variables (Q1–Q8 except Q9, plus Q14) were generated independently of tendency, using fixed probability weights intended to reflect a realistic European investor event audience.

| Variable | Distribution notes |
|---|---|
| Q1: Age group | Modal group 40–49; weights 8/25/30/25/12 across age bands |
| Q2: Gender | Approx. 55% male, 30% female, 15% other/prefer not to say |
| Q3: Country | Germany most frequent (25%); broad international spread |
| Q4: Organisation size | Medium (50–249) most common; micro least common |
| Q5: Industry | Technology/Software and Financial Services dominate |
| Q6: Years of experience | Gaussian μ=12, σ=7; floored at 0 |
| Q7: Events attended (2 yrs) | Gaussian μ=4, σ=2; floored at 0 |
| Q8: Typical deal size (€k) | Gaussian μ=750, σ=400; rounded to nearest €50k; minimum €50k |
| Q14: Preferred format | Hybrid most common (40%); fully virtual least common (15%) |

---

## Intended Use

- **Descriptive statistics** on Q1–Q15 (demographics, Likert means, ratio aggregates)
- **Sentiment analysis** on Q16–Q18, using the implicit tendency labels as ground truth or validation reference
- **Correlation analysis** between Likert satisfaction scores and ratio variables (follow-ups, deal size, experience)
- **Segmentation** by industry, country, or organisation size against satisfaction outcomes

---

## Reproducibility

The dataset was generated with Python's `random` module seeded at `42`. Re-running the generation script with the same seed will produce an identical dataset.
