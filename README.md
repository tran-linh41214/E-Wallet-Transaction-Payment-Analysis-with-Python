# data_wrangling_project
# UNIGAP - PORTFOLIO BUILDING MODULE - SQL/ PYTHON PROJECT

🔥Use this template as an outline for your SQL/ Python Github projects - which is task by task type of projects. 
Make a copy of this readme file and tailor it your own. Happy portfolio-ing and start applying aggressively :"))

---
![KPMG Transaction Analysis](https://github.com/Dorothy-Ho-Vy/Sample_SQL_Python_template/blob/4dee6ff56077b90b1aea82e8517136f7185a77a3/Blue%20White%20Modern%20Payment%20Gateway%20Service%20Twitter%20Post.png.crdownload)

👉🏻Change Icon emoji 🔥🔍📘🚩 to your likings by clicking "Start" + "."

# 📊 Project Title: [Your Project Name]  
Author: [Your Name]  
Date: YYYY-MM-DD  
Tools Used: SQL/ Python  

---

## 📑 Table of Contents  
1. [📌 Background & Overview](#-background--overview)  
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)  
3. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)

---

## 📌 Background & Overview  

### Objective:
### 📖 What is this project about? What Business Question will it solve?

Clearly outline what this project does, what business questions the project will solve. 

- Provide a brief introduction - Write in bullet point format
- Point out the main business question


 _Example:_
  This project uses Python to analyze transaction data from KPMG to:

✔️ Identify the behavior in customer's first transaction.  
✔️ Provide actionable insights to increase retention rate   
 


### 👤 Who is this project for?  

Mention who might benefit from this project 

 _Example:_

✔️ Data analysts & business analysts  
✔️ Decision-makers & stakeholders  



---

## 📂 Dataset Description & Data Structure  

### 📌 Data Source  
- Source: (Mention where the dataset is obtained from—Kaggle, company database, government sources, etc.)  
- Size: (Mention the number of rows & columns)  
- Format: (.csv, .sql, .xlsx, etc.)  

### 📊 Data Structure & Relationships  

#### 1️⃣ Tables Used:  
Mention how many tables are in the dataset.  

#### 2️⃣ Table Schema & Data Snapshot  

Table 1: Products Table  

👉🏻 Insert a screenshot of table schema 

📌If the table is too big, only capture a part of it that contains key metrics you used in the projects or put the table in toggle

 _Example:_

| Column Name | Data Type | Description |  
|-------------|----------|-------------|  
| Product_ID  | INT      | Unique identifier for each product |  
| Name        | TEXT     | Product name |  
| Category    | TEXT     | Product category |  
| Price       | FLOAT    | Price per unit |  


Table 2: Sales Transactions  

👉🏻 Insert a screenshot of table schema.


---

## ⚒️ Main Process

1️⃣ Data Cleaning & Preprocessing  
2️⃣ Exploratory Data Analysis (EDA)  

Merge payment_report và product
```python
payment_enriched = pd.merge(payment_report, product, on='product_id', how='left')
```
1. Check each column:
```python
#Check missing data và data type của payment_enriched và transactions
print(payment_enriched.info())
print(payment_enriched.isnull().sum())
print(transactions.info())
print(transactions.isnull().sum())

# Kiểm tra duplicates
print(payment_enriched.duplicated().sum())
print(transactions.duplicated().sum())
```

![image](https://github.com/user-attachments/assets/d79e78ef-684c-4f40-a5c3-45b201dd0471)

![image](https://github.com/user-attachments/assets/246b7474-3abb-4457-8d7d-977727141c71)

![image](https://github.com/user-attachments/assets/edf0dfdc-fd46-42a5-bcd6-80356ffa1981)

1. df payment_enriched:
- 919 rows, 7 columns
- report_month, payment_group, category, team_own: object
- product_id, source_id, volume: int64
- category, team_own: 22 null => *thay thế bằng unknown*
- Không có duplicate
2. df transactions
- 492 rows, 3 columns
- transaction_id, merchant_id, volume, transType, transStatus, timeStamp: int64
- sender_id, receiver_id: float_64 => *đổi int64*
- extra_info: object
- sender_id: 49059 giá trị null.
- receiver_id có 164795 giá trị null.
- extra_info có 1,317,907 giá trị null => gần như không có thông tin trong cột này => *no action*
- 28 duplicates => *bỏ duplicate*

```python
# Thay thế giá trị null trong cột 'category' và 'team_own' bằng 'unknown'
payment_enriched['category'] = payment_enriched['category'].fillna('unknown')
payment_enriched['team_own'] = payment_enriched['team_own'].fillna('unknown')
# Bỏ duplicates trong transactions
transactions.drop_duplicates(inplace=True)
#Thay thế giá trị null trong cột 'sender_id' và 'receiver_id' bằng -1 (không có dữ liệu)
transactions['sender_id'] = transactions['sender_id'].fillna(-1)
transactions['receiver_id'] = transactions['receiver_id'].fillna(-1)
#Chuyển kiểu dữ liệu sender_id và receiver_id từ float64 sang int64
transactions['sender_id'] = transactions['sender_id'].astype('int64')
transactions['receiver_id'] = transactions['receiver_id'].astype('int64')
```

2. Summarize numerical data:

```python
# Kiểm tra số liệu trong 2 df
print(payment_enriched.describe())
print(transactions.describe())
```

![image](https://github.com/user-attachments/assets/b00636f5-159b-475a-bcd0-18b9f1e4bf14)

=> Không có incorrect values


3️⃣ Data Wrangling

**1. Top 3 product_ids with the highest volume.**

```python
# Top 3 product_id có volume cao nhất
top_3_product_ids = payment_enriched.groupby('product_id')['volume'].sum().nlargest(3)
print(top_3_product_ids)
```
![image](https://github.com/user-attachments/assets/138c6f3c-6f5d-42f9-b0d8-182d23a8f994)

Product_id của Top 3 sản phẩm có hiệu suất lớn nhất: 1976, 429, 372


 

---

## 🔎 Final Conclusion & Recommendations  

👉🏻 Based on the insights and findings above, we would recommend the [stakeholder team] to consider the following:  

📌 Key Takeaways:  
✔️ Recommendation 1  
✔️ Recommendation 2  
✔️ Recommendation 3
