# ⚾ MLB Bullpen Forecaster — Predicting Reliever Performance

A machine learning project that predicts whether a Blue Jays bullpen pitcher will have a "good outing" based on recent performance trends, workload, and arsenal data.

## Key Question

> Given a reliever's recent velocity trends, whiff rates, run value, rest days, and workload history — can we predict whether their next appearance will be effective?

## Results Summary

| Model | Accuracy | Baseline | AUC | 5-Fold CV |
|-------|----------|----------|-----|-----------|
| **Gradient Boosting** | **73.0%** | 69.6% | **0.713** | 60.3% (±7.8%) |
| Logistic Regression | 73.0% | 69.6% | 0.692 | 68.9% (±7.4%) |
| Random Forest | 62.2% | 69.6% | 0.643 | 66.3% (±5.2%) |

**Best Model: Gradient Boosting** — 73.0% accuracy with 0.713 AUC, outperforming the naive baseline of always predicting "good outing."

## Good Outing Definition

An appearance is classified as a "good outing" when all of the following are true:
- Hits allowed ≤ 1
- Home runs allowed = 0
- Walks allowed ≤ 1
- Total Run Value ≤ 0.5

**Dataset: 184 appearances — 128 good (69.6%), 56 bad (30.4%)**

## Key Insights

### Feature Importance

The most predictive features for bullpen outing quality:

1. **Rolling 3-game whiff rate** — Recent swing-and-miss trends are the strongest signal
2. **Previous outing run value** — Momentum/carry-over from last appearance
3. **Rolling 3-game velocity** — Velocity trends indicate arm freshness
4. **Rest days** — Recovery time between appearances
5. **Rolling 3-game pitch count** — Cumulative workload fatigue indicator

### Bullpen Confidence Report

The model generates a real-time "confidence score" for each reliever based on their latest performance data:

| Tier | Pitchers | Description |
|------|----------|-------------|
| 🟢 HIGH (70%+) | Fluharty, Varland, Fisher, Mantiply, Nance, Rodríguez, Lee | Recent trends predict a good outing |
| 🟡 MED (50-70%) | Hoffman | Mixed recent signals |
| 🔴 LOW (<50%) | Miles, Macko, Little, Rogers | Recent trends suggest risk |

### Practical Application

In a front office or coaching context, this model could:
- Inform bullpen deployment decisions in close games
- Identify relievers trending toward fatigue before performance collapses
- Quantify the impact of rest days on expected performance
- Flag relievers whose recent trend data diverges from their season averages

## Features Used

| Feature | Type | Description |
|---------|------|-------------|
| total_pitches | Current | Pitch count for this appearance |
| rest_days | Current | Days since last appearance |
| appearance_num | Cumulative | Season appearance count |
| prev_pitches | Lag-1 | Pitch count from previous outing |
| prev_whiff_rate | Lag-1 | Whiff% from previous outing |
| prev_velo | Lag-1 | Average velocity from previous outing |
| prev_rv | Lag-1 | Run value from previous outing |
| prev_good | Lag-1 | Was previous outing good? (binary) |
| prev_k_rate | Lag-1 | Strikeout rate from previous outing |
| rolling3_whiff | Rolling | 3-game average whiff rate |
| rolling3_rv | Rolling | 3-game average run value |
| rolling3_velo | Rolling | 3-game average velocity |
| rolling3_pitches | Rolling | 3-game average pitch count |
| rolling3_good_pct | Rolling | 3-game good outing percentage |
| n_pitch_types | Arsenal | Number of distinct pitch types used |

## Models Evaluated

- **Gradient Boosting Classifier** (best) — 150 trees, max_depth=5
- **Logistic Regression** — Baseline linear model, max_iter=1000
- **Random Forest Classifier** — 200 trees, max_depth=8

## Visualizations

| Chart | Description |
|-------|-------------|
| `model_comparison.png` | Accuracy, AUC, and CV comparison across 3 models |
| `feature_importance_bullpen.png` | Feature importance from the best model |
| `bullpen_confidence.png` | Current reliever confidence scores |

## Tech Stack

- **Python**: pandas, numpy, matplotlib
- **ML**: scikit-learn (RandomForest, GradientBoosting, LogisticRegression)
- **Data**: SQLite database from [statcast-database](https://github.com/seongji-park/statcast-database) project
- **Source**: Baseball Savant Statcast via pybaseball

## Project Structure

```
mlb-bullpen-forecaster/
├── README.md
├── notebooks/
│   └── 01_bullpen_forecaster.ipynb
└── visualizations/
    ├── model_comparison.png
    ├── feature_importance_bullpen.png
    └── bullpen_confidence.png
```

## Related Projects

- [statcast-database](https://github.com/seongji-park/statcast-database) — SQLite pipeline for Statcast data (data source)
- [mlb-pitch-prediction](https://github.com/seongji-park/mlb-pitch-prediction) — Pitch type prediction using game-state features
- [bluejays-daily-reports](https://github.com/seongji-park/bluejays-daily-reports) — Daily Statcast game reports with pitch arsenal analysis

---

*Built with Statcast data via pybaseball and scikit-learn. All pitch metrics sourced from Baseball Savant.*
