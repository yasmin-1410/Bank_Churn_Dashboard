# Bank Customer Churn Dashboard

An interactive Power BI dashboard that analyzes **why bank customers leave (churn)** and which customer groups are most at risk.

![Dashboard Preview](images/dashboard.png)

## Dataset

Bank customer data for **10,000 customers** in **France, Germany, and Spain**, split into two tables linked by `CustomerId` (one-to-one):

| Table | Columns |
|---|---|
| `Customer_Info` | CustomerId, Surname, CreditScore, Geography, Gender, Age, EstimatedSalary |
| `Account_Info` | CustomerId, Balance, NumOfProducts, HasCrCard, IsActiveMember, Tenure, Exited |

## What the Dashboard Shows

| Section | Details |
|---|---|
| **KPI cards** | Churn rate, total customers, churned vs retained, average balance (all customers and excluding zero balances), balance at risk, active member rate |
| **Churn by age group** | One gauge per age group, with a marker at the overall churn rate |
| **Churned vs Retained** | Donut chart showing the split of customers |
| **Customers by Country** | Map of the three countries |
| **Churn by Tenure** | Line chart of churn rate by years as a customer |
| **Balance at Risk by Country** | Chart of the balance held by customers who left |

## Key Findings

- **20.37%** of customers churned (2,037 out of 10,000).
- Churn rises sharply with age: **8%** for ages 18-29, **31%** for 40-49, and peaks at **56%** for ages 50-59.
- **185.59M** in balances belongs to customers who left, mostly from **Germany (about 98M)**, then France (about 58M) and Spain (about 30M).
- Average balance is **76.49K** across all customers but **119.83K** when zero-balance accounts are excluded, so a large share of accounts (roughly a third) hold no money.
- Only **51.5%** of customers are active members.
- Churn by tenure stays between about **17% and 23%**, so tenure alone is not a strong driver.

## Data Preparation

Calculated columns (DAX):

- `Age Group`: 18-29, 30-39, 40-49, 50-59, 60-69, 70+
- `Credit Score Band`: Poor, Fair, Good, Very Good, Excellent
- `Balance Band`: Zero, Low, Medium, High
- `Tenure Group`: 0-2, 3-5, 6-8, 9-10 years

Columns that existed in both tables (`Tenure`, `Currency Sign`) were hidden from one table to avoid confusion in slicers.

## Key Measures (DAX)

```DAX
Total Customers = DISTINCTCOUNT(Account_Info[CustomerId])

Churned = CALCULATE([Total Customers], Account_Info[Exited] = 1)

Retained = CALCULATE([Total Customers], Account_Info[Exited] = 0)

Churn Rate % =
DIVIDE(
    CALCULATE(COUNTROWS(Account_Info), Account_Info[Exited] = 1),
    COUNTROWS(Account_Info)
)

Overall Churn Rate =
CALCULATE([Churn Rate %], ALL(Customer_Info[Age Group]))

Balance at Risk =
CALCULATE(SUM(Account_Info[Balance]), Account_Info[Exited] = 1)

Average Balance = AVERAGE(Account_Info[Balance])

Average Balance (Non-Zero) =
CALCULATE(AVERAGE(Account_Info[Balance]), Account_Info[Balance] > 0)

Active % =
DIVIDE(
    CALCULATE([Total Customers], Account_Info[IsActiveMember] = "Yes"),
    [Total Customers]
)
```

## Tools

Power BI Desktop, DAX

## How to Use

1. Download the `.pbix` file from this repository.
2. Open it with [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free).

## Repository Structure

```
├── BankChurnDashboard.pbix
├── data/
│   ├── Account_Info.csv
│   └── Customer_Info.csv
├── images/
│   └── dashboard.png
└── README.md
```

## Author

**[Your Name]** | [LinkedIn](https://linkedin.com/in/your-profile)
