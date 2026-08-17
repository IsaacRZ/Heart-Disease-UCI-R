# Heart Disease UCI — EDA, PCA & Clustering

Exploratory data analysis, dimensionality reduction, and unsupervised clustering of patients evaluated for heart disease, using the [Heart Disease UCI dataset](https://archive.ics.uci.edu/dataset/45/heart+disease). The project covers data cleaning, exploratory analysis, Principal Component Analysis (PCA), and K-Means clustering, with a focused sub-analysis on the Cleveland cohort using additional clinical variables (`ca`, `thal`).

## Dataset

- **Source:** UCI Machine Learning Repository, combined records from four medical centers (Cleveland, Hungary, Switzerland, VA Long Beach).
- **Size:** 920 patients, 16 original variables (demographic, clinical, and diagnostic test results).
- **Target variable:** `num` (severity, 0–4), also recoded as a binary `enfermedad` (Ausencia/Presencia) for interpretability.

The raw file (`heart_disease_uci.csv`) is expected at `data/raw/heart_disease_uci.csv` relative to the project root.

## Project structure

```
Proyecto_HeartDisease_EDA_Cluster.Rmd   # Main document, knits all child Rmds below
├── 01-limpieza.Rmd                     # Data loading, cleaning, missing-value strategy
├── 02-eda.Rmd                          # Exploratory data analysis
├── 03-pca.Rmd                          # PCA (main, sensitivity, and Cleveland-extended)
├── 04-clustering.Rmd                   # K-Means (elbow, silhouette, main + Cleveland)
├── 05-resultados.Rmd                   # Cluster profiling and validation
└── 06-conclusiones.Rmd                 # Conclusions, limitations, references
```

Each stage is a separate `.Rmd` included via `child=` in the main document, so it can also be knitted independently for debugging (provided the objects from prior stages are available in the environment).

## Requirements

- R ≥ 4.3
- Packages: `readr`, `dplyr`, `tidyr`, `ggplot2`, `corrplot`, `FactoMineR`, `factoextra`, `cluster`, `knitr`

```r
install.packages(c("readr", "dplyr", "tidyr", "ggplot2", "corrplot",
                    "FactoMineR", "factoextra", "cluster", "knitr"))
```

## How to run

1. Place `heart_disease_uci.csv` in `data/raw/`.
2. Open `Proyecto_HeartDisease_EDA_Cluster.Rmd` in RStudio.
3. Knit to HTML. The document runs the six stages in order; each stage depends on objects created in the previous one (e.g., `data`, `data_CLE`, `resultado_pca`, `datos_cluster`), so the child files must be knitted in sequence rather than out of order.

Expected runtime: a few seconds to knit the full document (no heavy computation involved).

## Methodology summary

1. **Cleaning:** explicit `NA` handling, correction of clinically impossible zero values (`chol`, `trestbps`), and a per-center missing-data strategy — variables missing at random are imputed by center-wise median; variables missing by center (`ca`, `thal`, `slope`, and `chol` in Switzerland) are left unimputed and handled separately.
2. **EDA:** univariate and bivariate analysis, correlation structure (overall and per center), and a dedicated analysis of `ca`/`thal` restricted to Cleveland, the only center where these were consistently recorded.
3. **PCA:** applied to five numeric variables (`age`, `trestbps`, `chol`, `thalch`, `oldpeak`), validated with a complete-case sensitivity analysis, and extended with `ca` for Cleveland.
4. **Clustering:** K-Means on PCA coordinates, with elbow and silhouette methods guiding — but not solely determining — the choice of *k*. The silhouette method favored `k = 2` in both the main analysis (0.260) and Cleveland (0.272); `k = 5` (main) and `k = 3` (Cleveland) were adopted instead for their clinical interpretability, a trade-off documented explicitly in the conclusions rather than presented as the statistically optimal choice.
5. **Validation:** cluster profiles were cross-checked against diagnosed severity (`enfermedad`), medical center, and — for Cleveland — `thal`, a variable that did not participate in the clustering itself.

## Key limitations

- The main clustering analysis uses complete cases across five numeric variables. Because `chol` is missing for 100% of Switzerland's patients, this center is effectively excluded from the main clustering results.
- Categorical variables (`cp`, `thal`, `slope`) were not used as clustering inputs; they were only used afterward for interpretation.
- The adopted number of clusters (`k = 5` main, `k = 3` Cleveland) was chosen for interpretability, not because it maximized the silhouette score — see `06-conclusiones.Rmd` for the full discussion.

## Deliverables

- `Proyecto_HeartDisease_EDA_Cluster.html` — full knitted report.
- `Proyecto_HeartDisease_Condensado.pptx` — 29-slide summary presentation 

## Team & role

| Member | GitHub | Contribution |
|---|---|---|
| **Isaac Rodríguez Zúñiga** | [@IsaacRZ](https://github.com/IsaacRZ) | **Team/Tech Lead.** Coordinated the team's workflow and task distribution, reviewed and merged 7 of the repo's 8 pull requests, and contributed code across most stages of the pipeline (data cleaning, PCA, clustering, and results). |
| Roberto González Gómez | [@robegonza13-pixel](https://github.com/robegonza13-pixel) | PCA analysis and K-Means application. |
| Gabriel Aaron Solano Molina | [@GaboSM1507](https://github.com/GaboSM1507) | Clustering data preparation, results interpretation, and general dataset overview. |
| Jeriel Fonseca Ramírez | [@jeriel169017-a11y](https://github.com/jeriel169017-a11y) | Silhouette method, outlier/categorical analysis, conclusions, limitations, and final HTML report. |

Pull request review and merge history is visible in this repo's [closed PRs](https://github.com/IsaacRZ/Heart-Disease-UCI-R/pulls?q=is%3Apr+is%3Aclosed), and per-author commit activity in [Insights → Contributors](https://github.com/IsaacRZ/Heart-Disease-UCI-R/graphs/contributors?from=16%2F5%2F2026).

## References

- Janosi, A., Steinbrunn, W., Pfisterer, M., & Detrano, R. (1989). *Heart Disease* [Dataset](https://www.kaggle.com/datasets/redwankarimsony/heart-disease-data). UCI Machine Learning Repository.
- Kassambara, A., & Mundt, F. (2020). *factoextra* [R package]. CRAN.
- Wickham, H., et al. (2023). *dplyr: A grammar of data manipulation* [R package]. CRAN.  