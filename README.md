# Predicting Race Outcome Categories in Formula 1 (2010–2023)
 
**Oluwatomilayo Faloseyi** · University of Prince Edward Island · April 2026
 
---
 
## Overview
 
This project uses machine learning to predict a Formula 1 driver's race outcome using only information available **before the race begins** — no lap times, no race-day data, no hindsight.
 
Rather than a simple podium/non-podium binary split (which treats finishing 4th and finishing 18th as identical), the outcome is framed as a **four-class classification problem** that reflects the real competitive structure of F1:
 
| Class | Definition |
|---|---|
| **Winner** | Finished P1 |
| **Podium** | Finished P2 or P3 |
| **Points** | Finished P4–P10 |
| **Outside Points** | Finished P11 or lower |
 
This framing was chosen because it is both interpretable and meaningful — each tier carries distinct sporting and strategic implications in real F1.

---
 ## Live Demo

🔗 [Try the pre-race outcome predictor](https://layo200.github.io/Predicting-Podium-Finishes-in-Formula-1/)

Input a constructor and grid position to get a predicted race outcome category based on the model's learned patterns.
 
---
 
## Dataset
 
- **Source**: [Formula 1 World Championship Dataset (1950–2023)](https://www.kaggle.com/datasets/suletanmay/formula-1-dataset-1950-2023-cleaned) — Kaggle
- **Scope**: Restricted to **2010–2023** (modern regulatory era) to ensure structural consistency across seasons
- **Final dataset**: 5,757 observations across 14 seasons
### Key Predictors
 
| Feature | Description |
|---|---|
| `grid_position` | Starting position on the race grid (strongest predictor, r = 0.59 with finish) |
| `qualifying_position` | Final qualifying result |
| `made_q2` / `made_q3` | Whether the driver advanced past Q1/Q2 |
| `constructorId` | Team identity — top teams cluster heavily in Winner/Podium |
| `driverId` | Driver identity |
| `circuitId` | Circuit identifier |
| `year` / `round` | Season and race number context |
 
### Class Distribution
 
The dataset is naturally imbalanced — a structural feature of F1, not a data quality issue:
 
| Class | Share |
|---|---|
| Outside Points | 52.9% |
| Points | 33.0% |
| Podium | 9.4% |
| Winner | 4.7% |
 
---
 
## Modelling Approach
 
A standard supervised learning pipeline was used:
 
- **Train / Validation / Test split** for unbiased evaluation
- Categorical features (`constructorId`, `driverId`, `circuitId`) → **one-hot encoded**
- Numeric features → **median imputed + standard scaled**
Three classifiers were compared:
 
| Model | Validation Accuracy | Macro F1 |
|---|---|---|
| Logistic Regression | 66.2% | 0.55 |
| Random Forest | 66.2% | 0.55 |
| Decision Tree | 61.1% | 0.52 |
 
**Logistic Regression was selected as the final model.** It matched Random Forest on every validation metric while being significantly simpler and more interpretable — a straightforward application of Occam's Razor.
 
---
 
## Results
 
Evaluated on the held-out test set:
 
- **Accuracy**: 67.4%
- **Macro F1**: 0.55
- **Weighted F1**: 0.67
### Per-Class Performance
 
| Class | Precision | Recall | F1 |
|---|---|---|---|
| Outside Points | 0.79 | 0.80 | **0.80** |
| Points | 0.59 | 0.59 | **0.59** |
| Winner | 0.42 | 0.52 | **0.47** |
| Podium | 0.41 | 0.31 | **0.35** |
 
The model performs best on **Outside Points** (the largest, most separable class) and struggles most with **Podium** — the smallest class, which shares strong grid positions and constructor profiles with Winners, making it hard to distinguish pre-race.
 
---
 
## Key Findings
 
- **Grid position** is the strongest single predictor (Pearson r = 0.59), but it only explains ~35% of finish variance — the remaining 65% is race-day uncertainty
- **Constructor identity** is a strong pre-race signal: Mercedes, Red Bull, and Ferrari dominate Winner/Podium; backmarkers sit almost exclusively in Outside Points
- **Qualifying-derived features** (`qualifying_position`, `made_q2`, `made_q3`) provided a modest but meaningful improvement over grid position alone
- A simpler model (Logistic Regression) matched a more complex one (Random Forest) — suggesting that **feature quality matters more than model complexity** here
---
 
## Limitations
 
- Only pre-race variables are used — weather, tire strategy, pit stops, reliability, and race incidents are not captured
- Driver and constructor identifiers capture competitive structure but not rolling form (e.g. recent average finish, current-season points)
- Class imbalance makes smaller classes (Winner, Podium) harder to predict accurately
## Future Work
 
- Rolling driver/constructor form features (prior average finish, points entering the race)
- Detailed qualifying sector times
- Weather and circuit surface data
- Seasonal performance trends to capture mid-season changes in team competitiveness
---
 
## Repository Structure
 
```
├── OluwatomilayoFaloseyi_EDA.ipynb   # Full notebook: EDA, modelling, evaluation
├── README.md
```
 
---
 
## References
 
- Suletanmay. (n.d.). *Formula 1 dataset (1950–2023, cleaned)*. Kaggle. https://www.kaggle.com/datasets/suletanmay/formula-1-dataset-1950-2023-cleaned
- Bunker, R. P., & Thabtah, F. (2019). A machine learning framework for sport result prediction. *Applied Computing and Informatics, 15*(1), 27–33.
 


