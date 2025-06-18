## Mexico's Water Quality Classification 

**🔗 Notebook**: [`Mexico's Water Quality Classification.ipynb`](./Mexico's%20Water%20Quality%20Classification.ipynb)

**🔗 Dataset**: [`agua_superficial_4.csv`](./agua_superficial_4.csv)

---
#### 📚 Overview
- **Objective**:  
  Develop a supervised classification model to categorize surface water samples into quality classes based on physico-chemical parameters, thereby enabling authorities and stakeholders to identify safe versus unsafe water bodies.  
- **Dataset**:  
  - **“agua_superficial_4.csv”**: Contains measurements of physico-chemical indicators (e.g., pH, turbidity, dissolved oxygen, nitrates, phosphates) for multiple sampling sites across various regions. Each row corresponds to a single observation at a given location and time. The target variable `SEMAFORO` (Spanish for “traffic light”) indicates water quality:  
    - **"Verde" 🟩**: Safe for human consumption and aquatic life.  
    - **"Amarillo" 🟨**: Intermediate risk; may require additional treatment.  
    - **"Rojo" 🟥**: Unsafe; high levels of contaminants.  
  - **Citation**: Sourced from environmental monitoring databases (governmental or academic repositories); loaded via `pd.read_csv("agua_superficial_4.csv")`.
---
#### 🧠 Methodology
1. **Data Cleaning & Preprocessing**  
   - **Drop non-predictive columns**: Removed identifiers and geospatial metadata that do not contribute to model performance:  
     - `Unnamed: 0`, `CLAVE`, `SITIO`, `ORGANISMO_DE_CUENCA`, `ESTADO`, `MUNICIPIO`, `CUENCA`, `CUERPO DE AGUA`, `TIPO`, `SUBTIPO`, `LONGITUD`, `LATITUD`, `PERIODO`.  
   - **Define features (X) and target (y)**:  
     - **X**: All remaining columns representing chemical and physical measurements (e.g., `pH`, `TURBIDEZ`, `OXIGENO_DISUELTO`, `NITRATOS`, etc.).  
     - **y**: Column `SEMAFORO` (categorical: “Verde”/“Amarillo”/“Rojo”).  
   - **Train/Test Split**: Stratified sampling (30% test, 70% train, `random_state=42`) to preserve class proportions.  

2. **Pipeline Construction**  
   - **Numerical Transformer** (`numeric_transformer`):  
     - `SimpleImputer(strategy="mean")` to fill missing values.  
     - `StandardScaler()` to normalize features (zero mean, unit variance).  
   - **Preprocessor** (`ColumnTransformer`):  
     - Applies `numeric_transformer` to all physico-chemical columns.  
     - (No categorical variables in this phase.)  

3. **Model Training & Hyperparameter Tuning**  
   - Explored three classifiers chosen for interpretability and performance balance:  
     1. **Logistic Regression** (baseline linear model).  
     2. **Random Forest Classifier** (ensemble of decision trees to capture nonlinear interactions).  
     3. **Gradient Boosting Classifier** (sequentially boosted trees to reduce bias/variance).  
   - **Hyperparameter Grid Search** (`GridSearchCV` with `cv=5` stratified folds, optimizing F1-score for class “Verde”):  
     - **Logistic Regression**: `C ∈ {0.01, 0.1, 1, 10}`, penalty “l2”, solver “lbfgs”.  
     - **Random Forest**: `n_estimators ∈ {100, 200}`, `max_depth ∈ {None, 10, 20}`.  
     - **Gradient Boosting**: `n_estimators ∈ {100, 200}`, `learning_rate ∈ {0.01, 0.1}`, `max_depth ∈ {3, 5}`.  
   - For each candidate estimator, trained on the training split and evaluated on the validation folds.  

4. **Evaluation & Selection**  
   - **Metrics**:  
     - **F1-score for “Verde”** (primary), **overall accuracy**, **confusion matrix**.  
   - **Results**:  
     - **Gradient Boosting** yielded the highest F1-score for “Verde” on the hold-out test set, with minimal overfitting (train/test F1 difference ≤ 0.05).  
     - Feature importances indicated that **turbidity**, **chemical oxygen demand (COD)**, and **nitrates** were the top predictors of “Rojo” classification.  

5. **Conclusions & Next Steps**  
   - **Chosen Model**: **Gradient Boosting Classifier** (ensuring robust performance in detecting safe water—the “Verde” class—while minimizing false negatives).  
   - **Deployment Considerations**:  
     - Integrate into a web dashboard for real-time water quality monitoring.  
     - Schedule periodic retraining as new samples are collected.  
   - **Key Takeaway**: A data-driven approach with ensemble methods can reliably classify water quality and support environmental decision-making.  
---
#### 🔧 Technologies Used
- **Python 3.12**  
- **pandas & NumPy** for data manipulation  
- **scikit-learn** for preprocessing, model training, and hyperparameter tuning  
- **Matplotlib & Seaborn** for exploratory data visualization  
---
#### 🌐 Practical Application
Local environmental agencies and water treatment facilities can leverage this model to:  
- **Prioritize testing resources** toward high-risk sites (predicted “Rojo”).  
- **Alert communities** when water quality falls below health guidelines.  
- **Optimize remediation efforts** by identifying key contaminants contributing to poor quality.  
