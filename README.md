# Predicting Seismic Building Drift with Support Vector Regression

**Hayden Willen** | DS 4021 Machine Learning II | University of Virginia School of Data Science

This project predicts how much a building moves during an earthquake using real sensor recordings from instrumented buildings in the US and Japan. It follows the approach in [Tao et al. (2024)](https://doi.org/10.1038/s41598-024-81705-3), which used Support Vector Regression (SVR) to predict a building's maximum drift ratio from the NDE1.0 earthquake database.

Fast drift predictions matter because right after an earthquake, emergency teams need to know which buildings are most likely damaged, and most buildings in a city have no sensors and incomplete design information.

## What's in the notebook

1. **Background and SVR theory.** How SVR relates to SVM for classification, with the intuition behind each equation in the paper.
2. **Exploratory data analysis.** The distribution of Drift and how it relates to all 41 input features.
3. **SVR vs. Ridge regression.** Both models tuned with cross-validation and compared on a held-out test set.
4. **Feature selection.** An SVR using only the 10 features most correlated with Drift, compared with Lasso regression.
5. **Robustness.** How much each model's predictions change when we remove 10 random training points.

## Results

| Model | Features | Test R² | Test MSE |
|---|---|---|---|
| SVR (RBF kernel) | 41 | 0.554 | 0.351 |
| Ridge | 41 | 0.477 | 0.411 |
| SVR (reduced) | 10 most correlated | 0.399 | 0.472 |
| Lasso | 8 kept out of 41 | **0.613** | **0.304** |

## Key findings

- **SVR beat Ridge** because the RBF kernel can fit nonlinear relationships between the features and Drift, which a linear model can't.
- **Picking features by correlation backfired.** The 10 most correlated features all measured nearly the same thing: how hard the ground shook, so the reduced SVR overfit and lost accuracy. Two of them also depend on sensors inside the building, which defeats the purpose of a smaller, more practical model.
- **Lasso did best overall.** It judges features together instead of one at a time, so it kept one strong shaking measure and added features with different information.
- **Ridge was much more stable than SVR.** When 10 training points were removed, SVR's predictions changed about 40 times more than Ridge's. Over half the training points were support vectors, so removing a few reshaped the SVR fit.

## Data

The dataset comes from the NDE1.0 database of earthquake recordings in instrumented buildings, the same data used in [Tao et al. (2024)](https://doi.org/10.1038/s41598-024-81705-3), the paper this project is based on. NDE1.0 was originally compiled by [Astorga et al. (2020)](https://doi.org/10.1007/s10518-019-00746-6) and is provided by the URBASIS program at ISTerre, Université Grenoble Alpes, through the [NDE1.0 flatfile page](https://www.isterre.fr/annuaire/pages-web-du-personnel/philippe-gueguen/new-earthquake-data-recorded-in-buildings-nde1-0/article/acces-to-the-flatfile.html).

The version used here is `data/data_lab02.xlsx`, which was provided for the course. All credit for the data goes to the original authors.

## How to run

```bash
git clone https://github.com/haydenwillen/seismic-drift-svr.git
cd seismic-drift-svr
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook seismic_drift_svr.ipynb
```

## Project structure

```
seismic-drift-svr/
├── seismic_drift_svr.ipynb   # full analysis
├── requirements.txt          # Python packages
├── .gitignore
├── README.md
└── data/
    └── data_lab02.xlsx       # NDE1.0 dataset
```

## Limitations

- Drift in this dataset was already standardized, so the paper's relative error metrics (MARD and D10%) aren't meaningful here. R² and MSE are used instead.
- Some Magnitude values go up to 500, which isn't physically possible and likely reflects data errors.
- The data mixes all building types, while the paper trained on one type only and found that type-specific models perform better.
- The Section 3 SVR was capped at 10,000 solver iterations, so some fits stopped early.
- Sections 3 and 4 use different preprocessing and cross-validation settings, so their scores aren't perfectly comparable.

## Credits

This started as a group lab for DS 4021 (Machine Learning II) at the University of Virginia. My classmates wrote the original code for Sections 2 and 3, and I wrote Sections 4 and 5. For this version, I reviewed the full notebook, edited their code and findings, and completed the remaining analysis. I used Claude Code to help with coding.
