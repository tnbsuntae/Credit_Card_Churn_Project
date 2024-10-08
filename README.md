# Introduction
Customer retention is crucial for credit card companies, as keeping existing customers is more cost-effective than acquiring new ones. This project uses a Kaggle dataset to predict customer churn, helping banks identify at-risk customers and offer them better services or promotions to enhance loyalty. 💳

We'll start with Exploratory Data Analysis (EDA) to uncover key trends and patterns. Next, we'll preprocess the data to prepare it for modeling, including encoding categorical variables, scaling numerical variables, and balancing the classes. ⚙️ We'll then select and tune machine learning models to find the most effective one for predicting churn. Finally, we'll evaluate the models and provide insights to help banks reduce customer attrition and improve their services. 📊

🔍 Check the full notebook file here: [Project Notebook](/project.ipynb)

🔍 Check the dataset from Kaggle here: [Dataset](https://www.kaggle.com/datasets/sakshigoyal7/credit-card-customers/data)

# Background
Customer churn is a major concern for credit card companies, as retaining existing customers is generally more cost-effective than acquiring new ones. High churn rates can impact profitability and growth, making churn prediction a valuable tool for improving customer retention strategies. 📉

The dataset used in this project, sourced from Kaggle, includes features related to credit card usage and account details. By analyzing this data, we aim to build predictive models to identify at-risk customers and help credit card companies implement targeted interventions, such as personalized offers and enhanced services. 🏦

This background section provides an overview of customer churn issues in the credit card industry and the significance of using machine learning techniques to predict and reduce churn.

# Exploratory Data Analysis
The initial investigation focused on the percentage of customer attrition to assess the dataset's balance. It was found that the dataset is imbalanced, with existing customers representing a substantial 83.93%, compared to just 16.07% of attrited customers.

![alt text](asset/attrition_percentage.png)
_Bar graph visualizing customer's attrition rate_

To identify correlations between variables, heatmaps were generated using both Spearman and Pearson correlation methods. This approach was chosen to account for potential linear or monotonic relationships between the variables. The goal is to determine which variables are associated with Attrited_Status.

![alt text](asset/pearson_heatmap.png)
_Pearson Correlation Heatmap_

![alt text](asset/spearman_heatmap.png)
_Spearman Correlation Heatmap_

Based on these heatmaps, the following questions need to be examined:
1. From all cancellations what are the percentage for these categories?
   1. Gender
   2. Education Level
   3. Marital Status
   4. Annual Salary
   5. Card Type
2. What are the distribution of revovling balance in both existing and attrited customers?
3. What are the distribution of average utilization ratio in both existing and attrited customers?
4. Is it possible to classified customer status by using all time transaction count and amount?
5. Is it possible to classified customer status by using the change in transaction count and amount between Q4 and Q1?

### **1.1 From all cancellations what are the percentage for Gender?**
Based on the bar graph below, female customers are more likely to churn than male customers, with a churn rate of 57.16% compared to 42.84% for males.

![alt text](asset/gender_percentage.png)
_Bar graph visualizing gender percentage on attrition customer_

### **1.2 From all cancellations what are the percentage for Education Level?**
According to the bar graph below, customers with a graduate education are the most likely to churn, followed by those with a high school education, unknown education status, and uneducated customers.

![alt text](asset/education_percentage.png)
_Bar graph visualizing education level percentage on attrition customer_

### **1.3 From all cancellations what are the percentage for Marital Status?**
Married and single customers have nearly identical churn rates, at 43.58% and 41.06%, respectively, while other categories have churn rates of approximately 7-8%.

![alt text](asset/marital_percentage.png)
_Bar graph visualizing marital status percentage on attrition customer_

### **1.4 From all cancellations what are the percentage for Annual Salary?**
Customers with annual earnings below $40,000 have the highest churn rate, at 37.68%. In contrast, those earning over $120,000 per year have the lowest churn rate, at 7.74%. Other income groups have churn rates ranging from approximately 11% to 17%.

![alt text](asset/salaray_percentage.png)
_Bar graph visualizing annual salary percentage on attrition customer_

### **1.5 From all cancellations what are the percentage for Card type?**
It is evident that nearly all churned customers hold a company blue card, with an exceptionally high percentage of 93.36%.

![alt text](asset/cardtype_percentage.png)
_Bar graph visualizing card type percentage on attrition customer_

### **2. What are the distribution of revovling balance in both existing and attrited customers?**
The boxplot below clearly indicates that existing customers have a higher median revolving balance of $1,364, while churned customers have a median of $0. This suggests that half of the churned customers have not utilized their revolving balance. 

![alt text](asset/rev_box.png)
_Box plot visualizing revolving balance distribution by attrition status_

### **3. What are the distribution of average utilization ratio in both existing and attrited customers?**

Since half of the attrited customers have not used their revolving balance, it's unsurprising that the boxplot distribution for their utilization ratio is extremely low. Conversely, most existing customers appear to have used their credit cards up to a utilization ratio of 0.53, or around half of their available credit.

![alt text](asset/uti_box.png)
_Box plot visualizing average utilization ratio distribution by attrition status_

### **4. Is it possible to classified customer status by using all time transaction count and amount?**
According to the scatter plot below, most attrited customers have transaction counts ranging from 0 to 60 and transaction amounts between $0 and $5,000.

![alt text](asset/total_amt_ct_scat.png)
_Scatter plot visualizing the relation of total transaction amount and count by attrition status_

### **5. Is it possible to classified customer status by using the change in transaction count and amount between Q4 and Q1?**
It is evident that in Q4, nearly all churned customers made fewer transactions compared to Q1, and the total amount per transaction was also lower than in Q1.

![alt text](asset/q4q1_amt_ct_scat.png)
_Scatter plot visualizing the relation of the change transaction amount and count from Q4 to Q1 by attrition status_

# Data Preprocessing
The initial step in data preprocessing involves selecting only the correlated variables and disregarding the rest. This is achieved by one-hot encoding all categorical variables into binary format and then plotting a correlation heatmap using all the variables.

![alt text](asset/pearson_hm_encoded.png)
_Pearson Correlation Heatmap with all the variables_

![alt text](asset/spearman_hm_encoded.png)
_Spearman Correlation Heatmap with all the variables_

According to these heatmaps only 9 variables listed below will be chosen in the model training process since they show high correlation to the Attrited_Status variable
- Avg_Utilization_Ratio
- Total_Ct_Chng_Q4_Q1
- Total_Trans_Ct
- Total_Trans_Amt
- Total_Amt_Chng_Q4_Q1
- Total_Revolving_Bal
- Contacts_Count_12_mon
- Months_Inactive_12_mon
- Total_Relationship_Count

A pipeline has been established to preprocess the selected variables. This pipeline will standardize the numerical variables and apply one-hot encoding to the categorical data.

Since no categorical variables were selected, the categorical_columns parameter contains an empty list.

```python
numerical_transformer = Pipeline(steps=[
    ('scaler', StandardScaler())
])

categorical_transformer = Pipeline(steps=[
    ('onehot', OneHotEncoder(drop='first',handle_unknown='ignore', sparse_output = False))
])

categorical_columns = []
numerical_columns = ['Avg_Utilization_Ratio', 'Total_Ct_Chng_Q4_Q1', 'Total_Trans_Ct', 'Total_Trans_Amt', 
                     'Total_Amt_Chng_Q4_Q1', 'Total_Revolving_Bal', 'Contacts_Count_12_mon', 'Months_Inactive_12_mon', 'Total_Relationship_Count']

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numerical_transformer, numerical_columns),
        ('cat', categorical_transformer, categorical_columns)
    ],remainder = 'drop')

pipeline = Pipeline(steps=[
    ('preprocessor', preprocessor)])
```
_Python code of the pipeline_

The DataFrame is first divided into independent variables (X) and the dependent variable (y), and then further split into training and testing sets. Both X_train and X_test are subsequently fitted and transformed.

As the dataset is imbalanced, with only 16.07% of customers classified as attrited, oversampling is applied to balance the classes. The Synthetic Minority Over-sampling Technique (SMOTE) is used in this process to create synthetic data points for the minority class, increasing the number of attrited customers to match that of the existing customers.

```python
X = df.drop(columns=['Attrition_Flag'])
y = df['Attrition_Flag'].apply(lambda x : 1 if x == 'Attrited Customer' else 0)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

X_train_preprocessed = pipeline.fit_transform(X_train)
X_test_preprocessed= pipeline.transform(X_test)

smt = SMOTE()
X_train_sm, y_train_sm = smt.fit_resample(X_train_preprocessed, y_train)
```
_Python code of the spilting and oversampling data_

# Model Selection and Tuning
The following models were chosen for this project:
- Logistic Regression
- Support Vector Classifier
- Decision Tree Classifier
- Random Forest Classifier
- XGBoost Classifier
  
Each model was trained and evaluated using various parameters to identify the optimal configuration based on the test data.
```python
models = {
    'LogisticRegression': LogisticRegression(),
    'SVC': SVC(),
    'DecisionTree': DecisionTreeClassifier(random_state=42),
    'RandomForest': RandomForestClassifier(random_state=42),
    'XGBoost': XGBClassifier(random_state=42)
}

param_grids = {
    'LogisticRegression': {},
    'SVC': {
        'C': [0.5, 0.75, 1], 
        'kernel': ['rbf']
    },
    'DecisionTree': {
        'max_depth': [None, 10, 30],
        'min_samples_split': [2, 5, 10]
    },
    'RandomForest': {
        'n_estimators': [100, 200, 500],
        'max_depth': [None, 10, 30],
        'min_samples_split': [2, 5, 10],
    },
    'XGBoost': {
        'n_estimators': [100, 200, 500],
        'learning_rate': [0.01, 0.1, 0.3],
        'max_depth': [3, 6, 10]
    }   
}

cv = KFold(n_splits=3, shuffle=True, random_state=42)
```
_Python code of model selection and tuning_

# Model Training
All models were trained using the GridSearchCV library, which evaluates them based on their accuracy scores. The best-performing model was XGBoost, achieving an accuracy score of 97.86%.

```python
grids = {}
for model_name, model in models.items():
    print(f'Training and tuning {model_name}...')
    grids[model_name] = GridSearchCV(estimator=model, param_grid=param_grids[model_name], cv=cv,scoring='accuracy', n_jobs=-1, verbose=1)
    grids[model_name].fit(X_train_sm, y_train_sm)
    best_params = grids[model_name].best_params_
    best_score = grids[model_name].best_score_
    
    print(f'Best parameters for {model_name}: {best_params}')
    print(f'Best accuracy for {model_name}: {best_score}\n')
```
_Python code of model training_

# Model Evaluation
All models were assessed to identify the best performer on the test data. Once again, the XGBoost model emerged as the top performer, achieving the highest scores in accuracy, precision, recall, and F1.

```python
for i in grids.keys():
    y_pred = grids[i].predict(X_test_preprocessed)
    print (i)
    print(classification_report(y_test, y_pred))
    print(confusion_matrix(y_test, y_pred))
    print("==========================================================")
    print()
```
_Python code of model evaluation on test data_

![alt text](asset/xgboost_cm.png)
_Confusion Matrix of XGBoost model_

![alt text](asset/xgboost_report.png)
_Classification Report of XGBoost model_

# Conclusion
This project focused on predicting customer churn using a Kaggle dataset. We addressed the dataset's imbalance through SMOTE and explored various machine learning models. After thorough evaluation and tuning, XGBoost emerged as the best performer, achieving the highest scores in accuracy, precision, recall, and F1.

The insights gained from this analysis can help credit card companies enhance customer retention strategies by identifying at-risk customers and offering targeted interventions. XGBoost's superior performance underscores its effectiveness in predicting churn and supporting customer loyalty initiatives.