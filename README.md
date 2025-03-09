# E-Wallet Transaction & Payment Analysis

---
# 📊 Project Title: [E-Wallet Transaction & Payment Analysis]  
Author: [Linh Tran]  
Tools Used: Python  

---

## 📑 Table of Contents  
1. [📌 Background & Overview](#-background--overview)  
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)  
3. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)

---

## 📌 Background & Overview  

### Objective:
### 📖 What is this project about? What Business Question will it solve?

This project analyzes transaction and payment data from an e-wallet company to:

✔️ Understand payment and transaction trends within the platform.  
✔️ Identify data quality issues (missing values, duplicates, incorrect data types).  
✔️ Analyze transaction types to uncover insights about user behavior.  
✔️ Determine the top-performing and lowest-performing products and teams.  
✔️ Investigate anomalies in product ownership and transactions.  
✔️ Find the primary contributors to refund transactions.  

**The main business question:**
✔️ How can we optimize payment and transaction processes to enhance business performance? 
 


### 👤 Who is this project for?  

✔️ **Data Analysts & Business Analysts** – to derive insights and improve reporting accuracy.  
✔️ **Product Managers** – to understand product performance and user engagement.  
✔️ **Finance & Operations Teams** – to track financial trends and identify revenue opportunities.  
✔️ **Decision-makers & Stakeholders** – to guide strategic decisions based on transaction patterns.  


---

## 📂 Dataset Description & Data Structure  

### 📌 Data Source  
- Source: Company database   
- Format: .csv  

### 📊 Data Structure & Relationships  

#### 1️⃣ Tables Used:  

3 tables:
- payment_report.csv (monthly payment volume of products)
- product.csv (product information)
- transactions.csv (transactions information)


#### 2️⃣ Table Schema & Data Snapshot  

Table 1: payment_report  
  
| Column Name    | Data Type | Description                           |  
|---------------|----------|---------------------------------------|  
| report_month  | object     | Transaction reporting month           |  
| payment_group | object     | Payment group                         |  
| product_id    | INT      | Unique identifier for the product     |  
| source_id     | INT      | Source ID of the transaction          |  
| volume        | INT      | Total transaction value               |  


Table 2: product  

| Column Name  | Data Type | Description                          |  
|-------------|----------|--------------------------------------|  
| product_id  | INT      | Unique identifier for each product  |  
| category    | TEXT     | Product category                     |  
| team_own    | TEXT     | Team that owns the product           |  

 
Table 3: Transactions
| Column Name  | Data Type | Description                          |  
|-------------|----------|--------------------------------------| 
| transaction_id | int | Unique identifier for each transaction |
| merchant_id   |  int  | Unique identifier for the merchant involved |
| volume      | int |  Transaction amount | 
| transType   | int  | Type of transaction | 
| transStatus  | int  | Status of the transaction |
| sender_id  | float | Unique identifier of the sender |
| receiver_id | float | 	Unique identifier of the receiver |
| extra_info  | object |  Additional transaction details |
| timeStamp | int  | Timestamp when the transaction was recorded |  


---

## ⚒️ Main Process

1️⃣ Data Cleaning & Preprocessing  

```python
import pandas as pd
import numpy as np
```

```python
from google.colab import drive
drive.mount('/content/drive')
```

Import dataframes

```python
payment_report = pd.read_csv('/content/drive/My Drive/DAC_Python_Project 2/payment_report.csv')
product = pd.read_csv('/content/drive/My Drive/DAC_Python_Project 2/product.csv')
transactions = pd.read_csv('/content/drive/My Drive/DAC_Python_Project 2/transactions.csv')
```

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

Product IDs of the top 3 highest-performing products: 1976, 429, 372

**2**. **Given that 1 product_id is only owed by 1 team, are there any abnormal products against this rule?**

```python
# Kiểm tra sản phẩm thuộc nhiều hơn 1 team
team_check = payment_enriched.groupby('product_id')['team_own'].nunique()
abnormal_products = team_check[team_check > 1]
print(abnormal_products)
 ```

→ No product belongs to more than one team.

**3. Find the team has had the lowest performance (lowest volume) since Q2.2023. Find the category that contributes the least to that team.**

```python
# Lọc các dữ liệu từ Q2.2023 trở đi
payment_enriched['report_month'] = pd.to_datetime(payment_enriched['report_month'], format='%Y-%m')
q2_2023 = payment_enriched[payment_enriched['report_month'] >= '2023-04']

# Team có hiệu suất thấp nhất
team_lowest_performance = q2_2023.groupby('team_own')['volume'].sum().nsmallest(1)
print(team_lowest_performance)

# Tìm category đóng góp ít nhất trong team đó
lowest_team = team_lowest_performance.idxmin()
lowest_team_category = q2_2023[q2_2023['team_own'] == lowest_team].groupby('category')['volume'].sum().nsmallest(1)
print(lowest_team_category)
```

![image](https://github.com/user-attachments/assets/b41ed502-1c95-4c86-954e-ac48da0fd995)

- Lowest-performing team: APS (total volume: 51,141,753)
- Least contributing category: PXXXXXE (total volume: 25,232,438)

**4. Find the contribution of source_ids of refund transactions (payment_group = ‘refund’), what is the source_id with the highest contribution?**

```python
# Tìm source_id có đóng góp cao nhất cho giao dịch 'refund'
refunds = payment_enriched[payment_enriched['payment_group'] == 'refund']
top_refund_source = refunds.groupby('source_id')['volume'].sum().idxmax()
print(top_refund_source)
```

Source_ID with the highest number of refunds: 38  
**5. Define type of transactions (‘transaction_type’) for each row**

```python
# Thiết lập hàm để xác định các loại giao dịch
def classify_transaction(row):
    if row['transType'] == 2 and row['merchant_id'] == 1205:
        return 'Bank Transfer Transaction'
    elif row['transType'] == 2 and row['merchant_id'] == 2260:
        return 'Withdraw Money Transaction'
    elif row['transType'] == 2 and row['merchant_id'] == 2270:
        return 'Top Up Money Transaction'
    elif row['transType'] == 2:
        return 'Payment Transaction'
    elif row['transType'] == 8 and row['merchant_id'] == 2250:
        return 'Transfer Money Transaction'
    elif row['transType'] == 8:
        return 'Split Bill Transaction'
    else:
        return 'Invalid Transaction'

# Áp dụng hàm để thêm cột 'transaction_type'
transactions['transaction_type'] = transactions.apply(classify_transaction, axis=1)

# Kiểm tra kết quả
transactions[['transType', 'merchant_id', 'transaction_type']].head()
```

![image](https://github.com/user-attachments/assets/ac3156e9-287a-4de7-b71f-2c509090321d)

**6. Of each transaction type (excluding invalid transactions): find the number of transactions, volume, senders and receivers.**

```python
summary_stats = transactions.groupby('transaction_type').agg(
    number_of_transactions=('transaction_type', 'count'),
    total_volume=('volume', 'sum'),
    unique_senders=('sender_id', 'nunique'),
    unique_receivers=('receiver_id', 'nunique')
).reset_index()

# Kiểm tra kết quả
print(summary_stats)
```
![image](https://github.com/user-attachments/assets/7b3ab292-e8bc-4e16-8026-5aeada4c5d60)

---

## 🔎 Final Conclusion & Recommendations  
  

#### **Final Conclusion:**  

✔️ The **top 3 highest-performing products** are **Product IDs: 1976, 429, and 372**. These products have the highest transaction volumes, indicating strong user demand.  
✔️ There is **no product that belongs to multiple teams**, meaning product ownership follows the expected structure.  
✔️ The **lowest-performing team** is **APS**, with a total transaction volume of **51,141,753**. This indicates a potential need for strategy adjustments to improve performance.  
✔️ The **least contributing product category** is **PXXXXXE**, with a volume of **25,232,438**, suggesting that products in this category may not be meeting customer needs or facing market challenges.  
✔️ The **most refunded source_id** is **38**, which may indicate an issue with this payment source, such as frequent transaction failures or customer dissatisfaction.  

---

#### **Recommendations:**  
🔹 **For high-performing products (1976, 429, 372):**  
- Investigate the success factors of these products and apply similar strategies to other products.  
- Increase marketing efforts to further boost their adoption and retention rates.  

🔹 **For Team APS:**  
- Conduct a deeper analysis of why this team has the lowest performance.  
- Consider providing additional support, such as better resources, training, or promotional activities.  
- Review the product offerings managed by APS to identify areas for improvement.  

🔹 **For Category PXXXXXE:**  
- Assess the demand for this category and explore whether product modifications, rebranding, or better positioning could improve its performance.  
- Conduct customer surveys to understand why this category has lower traction.  

🔹 **For Refund Issues (Source ID 38):**  
- Investigate the reasons for frequent refunds from this source.  
- Check for potential technical issues, fraudulent activities, or customer complaints.  
- Implement corrective actions, such as improving fraud detection, enhancing transaction security, or optimizing the refund process.  


