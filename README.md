# 100 Days of AI/ML

Daily practice, notebooks, and exercises from my public **100 Days** learning series — machine learning fundamentals worked through from scratch.

📌 Daily posts: [LinkedIn](https://www.linkedin.com/in/prince-patel0911/)
🎓 AI/ML Engineering @ Parul University

> **Structure:** folders are organized **by topic**, not by day number, so the repo stays navigable as it grows. The day-by-day timeline lives on LinkedIn and in the commit history. Days 1–66 were committed as a single batch when this repo was created; from Day 67 onward, commits follow the `Day NN: <topic>` convention.

---

## Index

| Days | Topic | Folder | What's inside |
|---|---|---|---|
| 1–XX | NumPy & Pandas foundations | [`numpy-pandas-basics/`](./numpy-pandas-basics) | Array operations, broadcasting, DataFrames, cleaning and reshaping real datasets |
| XX–XX | Supervised learning | [`supervised-learning/`](./supervised-learning) | Regression, classification, tree models, and ensemble methods |
| XX–XX | Unsupervised learning | [`unsupervised-learning/`](./unsupervised-learning) | Clustering, dimensionality reduction, and anomaly detection |

<!-- Fill the day ranges from your LinkedIn post history. -->

---

### `numpy-pandas-basics/`

| Notebook | Focus |
|---|---|
| `numpy.ipynb` | Arrays, indexing, broadcasting, vectorized operations |
| `pandas.ipynb` | DataFrames, selection, groupby, merging, missing-value handling |

Datasets: `globalAirQuality.csv`, `raw_data.csv`

---

### `supervised-learning/`

**Regression & classification**

| Notebook | Algorithm |
|---|---|
| `linear_reg.ipynb` | Linear regression |
| `lasso_reg.ipynb` | Lasso / L1 regularization |
| `logistic_reg.ipynb`, `logistic_regression.ipynb` | Logistic regression |
| `knn.ipynb` | K-Nearest Neighbors |
| `decision_tree_classifier.ipynb` | Decision tree — classification |
| `decision_tree_regressor.ipynb` | Decision tree — regression |

**Ensemble methods**

| Notebook | Algorithm |
|---|---|
| `random_forest.ipynb` | Random Forest |
| `adaboost.ipynb` | AdaBoost |
| `gradient_boosting.ipynb` | Gradient Boosting |
| `xgboost_classification.ipynb` | XGBoost |
| `ensemble_heterogenous.ipynb` | Voting / stacking across model types |
| `svr_implementation.ipynb` | Support Vector Regression |

**Practice projects**

| Notebook | Problem |
|---|---|
| `project_1.ipynb` | House price prediction |
| `project2_employee.ipynb` | Employee turnover prediction |
| `shop_smart.ipynb` | Retail / purchase behaviour |

Datasets: `HousePricePrediction.csv`, `Iris.csv`, `heart.csv`, `insurance.csv`, `employee_data.csv`, `employee_turnover.csv`

---

### `unsupervised-learning/`

| Notebook | Technique |
|---|---|
| `KMeans.ipynb`, `KMeansForIris.ipynb` | K-Means clustering |
| `Hierarchical.ipynb`, `hierarchical_clustering.ipynb` | Agglomerative clustering, dendrograms |
| `DBSCAN.ipynb` | Density-based clustering |
| `PCA.ipynb` | Principal Component Analysis |
| `isolation_forest.ipynb` | Anomaly detection — Isolation Forest |
| `lof.ipynb` | Anomaly detection — Local Outlier Factor |

Dataset: `thyroid_dataset.csv`

---

## Standalone projects

Larger builds live in their own repositories so they read as projects rather than practice:

| Project | Description | Repo |
|---|---|---|
| **SmartCart** | Customer segmentation with clustering — full pipeline from EDA through segment profiling | [→ repo](#) |
| **Exoplanet Detection** | ISRO Bhartiya Antariksh Hackathon 2026 — TESS light-curve classification using BLS periodogram + Random Forest, 84% test accuracy | [→ repo](#) |

<!-- Replace # with the real URLs. -->

---

## Running the notebooks

```bash
git clone https://github.com/prince0911-tech/100-days-of-AiML.git
cd 100-days-of-AiML
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook
```

**requirements.txt**

```
numpy
pandas
matplotlib
seaborn
scikit-learn
xgboost
jupyter
```

---

## What's next

Deep Learning begins at **Day 67** — PyTorch, neural network foundations, and beyond.

---

## Commit convention

```
Day 67: Tensors and neuron in PyTorch
```

One commit per day of the public series.