# 🎯 Kickstarter Projects Power BI Dashboard

## 📘 Project Overview
The **Kickstarter Projects Dashboard** is an interactive Power BI report designed to analyze crowdfunding data.  
It helps users understand campaign performance by visualizing funding trends, success rates, top-performing projects, and category insights.

This project demonstrates:
- Power Query data transformation (Epoch time → Date)
- DAX-based KPI and time intelligence
- Currency conversion to USD
- Calendar table creation
- End-to-end Power BI modeling

---

## 🧠 Business Objectives
1. Convert all date fields from **Epoch time** to **Natural Date** format.  
2. Build a **Calendar Table** with full time intelligence fields.  
3. Design a **Data Model** using provided Excel sources.  
4. Convert **Goal Amounts** into **USD** using a static exchange rate.  
5. Create KPIs for **Project Overview**, **Success Metrics**, and **Category-wise Analysis**.  
6. Build interactive dashboards for deep insights.

---

## 🗂️ Data Model
**Tables Used**
- `Crowdfunding_fact`
- `Category_dim`
- `Location_dim`
- `Creator_dim`
- `Calendar_dim`

**Relationships**
- `Calendar_dim[Created_date]` → `Crowdfunding_fact[Create_date]`
- `Category_dim[id]` → `Crowdfunding_fact[category_id]`
- `Location_dim[id]` → `Crowdfunding_fact[location_id]`
- `Creator_dim[id]` → `Crowdfunding_fact[creator_id]`

---

## ⚙️ Data Transformation

### 1️⃣ Convert Epoch to Natural Date
```DAX
Created Date = DATE(1970,1,1) + ('Crowdfunding_fact'[Create_date]/86400)
Launched Date = DATE(1970,1,1) + ('Crowdfunding_fact'[Launched_date]/86400)
Deadline Date = DATE(1970,1,1) + ('Crowdfunding_fact'[Deadline_date]/86400)
```

---

### 2️⃣ Create Calendar Table
```DAX
Calendar_dim =
ADDCOLUMNS (
    CALENDAR ( MIN ( Crowdfunding_fact[Create_date] ), MAX ( Crowdfunding_fact[Create_date] ) ),
    "Year", YEAR ( [Date] ),
    "MonthNo", MONTH ( [Date] ),
    "MonthFullName", FORMAT ( [Date], "MMMM" ),
    "Quarter", "Q" & FORMAT ( [Date], "Q" ),
    "YearMonth", FORMAT ( [Date], "YYYY-MMM" ),
    "WeekdayNo", WEEKDAY ( [Date] ),
    "WeekdayName", FORMAT ( [Date], "DDDD" ),
    "FinancialMonth", "FM" & IF ( MONTH ( [Date] ) >= 4, MONTH ( [Date] ) - 3, MONTH ( [Date] ) + 9 ),
    "FinancialQuarter",
        "FQ-" & SWITCH (
            TRUE(),
            MONTH ( [Date] ) IN {4,5,6}, 1,
            MONTH ( [Date] ) IN {7,8,9}, 2,
            MONTH ( [Date] ) IN {10,11,12}, 3,
            MONTH ( [Date] ) IN {1,2,3}, 4
        )
)
```

---

### 3️⃣ Convert Goal Amount to USD
```DAX
Goal (USD) = 'Crowdfunding_fact'[Goal] * 'Crowdfunding_fact'[static_usd_rate]
```

---

## 📊 Key KPIs & DAX Measures

### 🧱 1. Project Overview KPIs
| Metric | Description | Example DAX |
|--------|--------------|-------------|
| **Total Projects** | Total count of all projects | `Total No.of Projects = DISTINCTCOUNT(Crowdfunding_fact[id])` |
| **Projects by Outcome** | Count of projects by state | `Successful Projects = CALCULATE([Total No.of Projects], Crowdfunding_fact[state] = "successful")` |
| **Projects by Location** | Distribution by country | Visualized using `Location_dim` |
| **Projects by Category** | Distribution by category | Visualized using `Category_dim` |

---

### 💰 2. Successful Projects Metrics
| Metric | Description | Example DAX |
|--------|--------------|-------------|
| **Amount Raised** | Total USD raised by successful projects | `Amount_Raised = SUM(Crowdfunding_fact[usd_pledged])` |
| **Number of Backers** | Total backers for successful projects | `No.of Backers(Successful) = CALCULATE(SUM(Crowdfunding_fact[backers_count]), Crowdfunding_fact[state] = "Successful")` |
| **Average Duration (Days)** | Avg. duration between launch and deadline | `Avg_Number_of_days(Successful) = CALCULATE(AVERAGEX(Crowdfunding_fact,DATEDIFF(Crowdfunding_fact[Launched_date],Crowdfunding_fact[Deadline_date],DAY)),Crowdfunding_fact[state] = "Successful")` |

---

### 🏆 3. Top Successful Projects
| Ranking | Basis | Example DAX |
|----------|-------|-------------|
| Top 10 by Backers | Highest backers | Use `TOPN(10, Crowdfunding_fact, Crowdfunding_fact[backers_count], DESC)` |
| Top 10 by Amount Raised | Highest funding | Use `TOPN(10, Crowdfunding_fact, Crowdfunding_fact[usd_pledged], DESC)` |

---

### 📈 4. Success Rate Analysis
| KPI | Description | DAX Measure |
|-----|--------------|-------------|
| **Overall Success %** | Overall percentage of successful projects | `% Successful_Projects = DIVIDE([Successful Projects],[Total No.of Projects],0)` |
| **Success % by Category** | Category-wise success percentage | Based on `Category_dim` |
| **Success % by Year/Month** | Trend over time | Using `Calendar_dim` |
| **Success % by Goal Range** | Binned analysis by goal range | Create a calculated column with goal buckets |

---

## 📅 Dashboard Visuals
- 🟢 **Cards:** Total Projects, % Successful, Amount Raised  
- 📊 **Bar/Column Charts:** Projects by Category, Projects by Country  
- 📈 **Line Charts:** % Successful by Year, Total Projects by Year  
- 🍩 **Donut Chart:** Projects by Outcome  
- 🏆 **Tables:** Top Successful Projects by Amount & Backers  

---

## 🧰 Tools & Technologies
- **Power BI Desktop**
- **Power Query (M Language)**
- **DAX**
- **Excel / CSV Sources**
- **GitHub**

---

## 📂 Folder Structure
```
📁 Kickstarter-Crowdfunding-Dashboard
 ┣ 📂 Data
 ┃ ┣ Crowdfunding_data.xlsx
 ┣ 📂 PowerBI
 ┃ ┣ Kickstarter_Crowdfunding.pbix
 ┣ 📂 Screenshots
 ┃ ┣ Dashboard.png
 ┣ 📄 README.md
```

---

## 📷 Dashboard Preview
<img width="1599" height="899" alt="Screenshot 2025-10-29 233652" src="https://github.com/user-attachments/assets/68744ef0-e52f-4dd7-84b1-4e5d874fd2f8" />


---

## 🔗 References
- [Epoch Converter](https://www.epochconverter.com/)
- [Microsoft Power BI Docs](https://learn.microsoft.com/power-bi/)

---

## 👨‍💻 Author

**Nabaluru Shyam**  
📧 [nabalurushyam1327@gmail.com](mailto:nabalurushyam1327@gmail.com)  
🔗 [LinkedIn](https://www.linkedin.com/in/shyamnabaluru)

