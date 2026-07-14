
uv
ud add --dev black ruff mypy


Toml File
```
[project]
name = "backend"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.14"
dependencies = [
    ...
]

[dependency-groups]
dev = [
    "black",
    "ruff",
    "mypy",
]

[tool.black]
line-length = 88
target-version = ["py314"]

[tool.ruff]
line-length = 88
target-version = "py314"

[tool.ruff.lint]
select = ["E", "F", "I"]

[tool.mypy]
python_version = "3.14"
strict = true
```



```python
from pydantic_settings import BaseSettings, SettingsConfigDict

  
  

class Settings(BaseSettings):

model_config = SettingsConfigDict(env_file=".env")

  

DATABASE_URL: str

DEBUG: bool

SECRET_KEY: str

  
  
  
  
  

settings = Settings()
```



- Mean
- ✅ Median
- ✅ Mode
- ✅ Percentile
- ✅ Quartile (Q1, Q2, Q3)
- ✅ IQR
- ✅ Outlier
- ✅ Standard Deviation
- ✅ Variance
- ✅ Correlation

Ha, juda ko'p atamalar bor. Lekin ularni tartibli o'rgansang, eslab qolish ancha oson bo'ladi.

Sening maqsading **Full Stack AI Engineer** bo'lgani uchun men faqat haqiqatan ham kerak bo'ladigan atamalarni kategoriyalarga ajrataman.

---

# 1. Statistics (eng muhim poydevor)

Bularni Data Scientist, ML Engineer va AI Engineer ham tushunishi kerak.

### Markaziy tendensiya

- Mean
    
- Median
    
- Mode
    

### Tarqalish (Dispersion)

- Variance
    
- Standard Deviation
    
- Range
    
- Interquartile Range (IQR)
    

### Percentiles

- Percentile
    
- Quartile
    
- Q1
    
- Q2
    
- Q3
    

### Outliers

- Outlier
    
- IQR Rule
    
- Z-score
    

### Probability

- Probability
    
- Distribution
    
- Normal Distribution
    
- Gaussian Distribution
    
- Uniform Distribution
    
- Skewness
    
- Kurtosis
    

### Relationships

- Correlation
    
- Covariance
    
- Pearson Correlation
    
- Spearman Correlation
    

---

# 2. Data Analysis (EDA)

Bu yerda datasetni o'rganish boshlanadi.

- Exploratory Data Analysis (EDA)
    
- Missing Values
    
- Null Values
    
- Duplicate Values
    
- Feature Distribution
    
- Histogram
    
- Box Plot
    
- Scatter Plot
    
- Heatmap
    
- Pair Plot
    
- Correlation Matrix
    

---

# 3. Data Cleaning

- Imputation
    
- Mean Imputation
    
- Median Imputation
    
- Mode Imputation
    
- Outlier Removal
    
- Winsorization
    
- Clipping
    
- Scaling
    
- Normalization
    
- Standardization
    
- Encoding
    

---

# 4. Feature Engineering

- Feature
    
- Target
    
- Label
    
- Derived Feature
    
- Feature Selection
    
- Feature Extraction
    
- Polynomial Features
    
- One-Hot Encoding
    
- Label Encoding
    
- Frequency Encoding
    
- Target Encoding
    
- Interaction Features
    

---

# 5. Machine Learning

### Dataset

- Train Set
    
- Validation Set
    
- Test Set
    

### Learning

- Supervised Learning
    
- Unsupervised Learning
    
- Reinforcement Learning
    

### Models

- Linear Regression
    
- Logistic Regression
    
- Decision Tree
    
- Random Forest
    
- XGBoost
    
- LightGBM
    
- CatBoost
    
- SVM
    
- KNN
    
- Naive Bayes
    

---

# 6. Model Evaluation

Regression

- RMSE
    
- MSE
    
- MAE
    
- R²
    

Classification

- Accuracy
    
- Precision
    
- Recall
    
- F1 Score
    
- ROC
    
- ROC-AUC
    
- PR Curve
    
- Confusion Matrix
    

---

# 7. Model Training

- Epoch
    
- Batch
    
- Mini Batch
    
- Gradient Descent
    
- SGD
    
- Learning Rate
    
- Loss Function
    
- Cost Function
    
- Optimization
    

---

# 8. Regularization

- L1
    
- L2
    
- Elastic Net
    
- Overfitting
    
- Underfitting
    
- Bias
    
- Variance
    

---

# 9. Hyperparameter Tuning

- Hyperparameter
    
- Parameter
    
- Grid Search
    
- Random Search
    
- Bayesian Optimization
    
- Cross Validation
    
- K-Fold
    

---

# 10. Deep Learning

- Neuron
    
- Layer
    
- Activation Function
    
- ReLU
    
- Sigmoid
    
- Tanh
    
- Softmax
    
- Backpropagation
    
- Dropout
    
- Batch Normalization
    

---

# 11. LLM / AI

- Token
    
- Embedding
    
- Vector
    
- Vector Database
    
- RAG
    
- Prompt Engineering
    
- Fine-tuning
    
- Context Window
    
- Chunking
    
- Retrieval
    
- Agent
    
- MCP (Model Context Protocol)
    
- Tool Calling
    

---

# 12. MLOps

- MLflow
    
- Experiment Tracking
    
- Model Registry
    
- Model Serving
    
- Inference
    
- Training Pipeline
    
- Feature Store
    
- Drift
    
- Monitoring
    
- Retraining
    

---

# 13. Data Engineering

- ETL
    
- ELT
    
- Data Pipeline
    
- Data Warehouse
    
- Data Lake
    
- Batch Processing
    
- Stream Processing
    

---

# Men senga tavsiya qiladigan ketma-ketlik

Hozir aynan ML Zoomcamp Regression bo'limida ekansan. Men bo'lsam quyidagi tartibda o'rganardim:

### Bosqich 1 (Statistics)

- Mean
    
- Median
    
- Mode
    
- Variance
    
- Standard Deviation
    
- Percentile
    
- Quartile
    
- IQR
    
- Outlier
    
- Correlation
    

⬇️

### Bosqich 2 (EDA)

- Missing Values
    
- Data Cleaning
    
- Histograms
    
- Box Plots
    
- Feature Distribution
    

⬇️

### Bosqich 3 (Machine Learning)

- Train/Validation/Test
    
- RMSE
    
- MAE
    
- MSE
    
- Linear Regression
    
- Regularization
    
- Cross Validation
    

⬇️

### Bosqich 4 (Advanced ML)

- Trees
    
- Random Forest
    
- XGBoost
    
- Hyperparameter Tuning
    

⬇️

### Bosqich 5 (LLM & AI)

- Embeddings
    
- Vector Databases
    
- RAG
    
- Agents
    
- Fine-tuning
    

---

## Muhim tavsiya

Bu ro'yxatdagi 100+ atamani birdaniga yodlashga urinma. Atamalarni **mavzu bilan birga** o'rgan. Masalan, hozir Regression bo'limidasan, shuning uchun aynan shu mavzuga tegishli atamalarni chuqur tushun. Keyingi bo'limga o'tganingda yangi atamalarni qo'shib bor. Shu usul bilan bilimlar bir-biriga bog'lanadi va eslab qolish ham ancha oson bo'ladi.