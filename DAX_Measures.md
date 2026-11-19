# 📐 DAX Calculations & Measures Documentation

This repository documents all DAX calculations created for the **Bank Loan & Credit Risk Analytics Dashboard**. Measures are organized into functional categories: Core KPIs, Time Intelligence (MTD & MoM), and Credit Risk Classification (Good vs. Bad Loans).

---

## 1. Core Portfolio KPIs

### Total Loan Applications
```dax
Total Loan Application = COUNT(financial_loan[id])
```
* **Purpose**: Calculates the aggregate number of loan applications recorded in the portfolio.

### Total Funded Amount
```dax
Total Funded Amount = SUM(financial_loan[loan_amount])
```
* **Purpose**: Computes the total monetary value of capital disbursed to borrowers.

### Total Amount Received
```dax
Total Amount Received = SUM(financial_loan[total_payment])
```
* **Purpose**: Tracks cumulative cash collected from borrowers (principal + interest + fees).

### Average Interest Rate
```dax
Average Interest Rate = AVERAGE(financial_loan[int_rate])
```
* **Purpose**: Measures the overall weighted average lending rate across the portfolio.

### Average Debt-to-Income (DTI)
```dax
Avg Dti = AVERAGE(financial_loan[dti])
```
* **Purpose**: Monitors borrower leverage and repayment risk capacity.

---

## 2. Time Intelligence (Month-to-Date & Month-over-Month Velocity)

### Month-to-Date (MTD) Calculations
```dax
MTD Loan Applications = 
CALCULATE(
    [Total Loan Application], 
    DATESMTD('Date Table'[Date])
)

MTD Funded Amount = 
CALCULATE(
    [Total Funded Amount], 
    DATESMTD('Date Table'[Date])
)

MTD Total Amount Received = 
CALCULATE(
    [Total Amount Received], 
    DATESMTD('Date Table'[Date])
)

MTD Avg Int Rate = 
CALCULATE(
    [Average Interest Rate], 
    DATESMTD('Date Table'[Date])
)

MTD Avg DTI = 
CALCULATE(
    [Avg Dti], 
    DATESMTD('Date Table'[Date])
)
```

### Previous Month-to-Date (PMTD) Helper Measures
```dax
PMTD Loan Applications = 
CALCULATE(
    [Total Loan Application], 
    DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
)

PMTD Funded Amount = 
CALCULATE(
    [Total Funded Amount], 
    DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
)

PMTD Total Amount Received = 
CALCULATE(
    [Total Amount Received], 
    DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
)

PMTD Avg Int Rate = 
CALCULATE(
    [Average Interest Rate], 
    DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
)

PMTD Avg DTI = 
CALCULATE(
    [Avg Dti], 
    DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
)
```

### Month-over-Month (MoM) Growth Rates
```dax
MOM Loan Application = 
DIVIDE(
    [MTD Loan Applications] - [PMTD Loan Applications], 
    [PMTD Loan Applications], 
    0
)

MOM Funded Amount = 
DIVIDE(
    [MTD Funded Amount] - [PMTD Funded Amount], 
    [PMTD Funded Amount], 
    0
)

MOM Receive Amount = 
DIVIDE(
    [MTD Total Amount Received] - [PMTD Total Amount Received], 
    [PMTD Total Amount Received], 
    0
)

MOM Avg Int Rate = 
DIVIDE(
    [MTD Avg Int Rate] - [PMTD Avg Int Rate], 
    [PMTD Avg Int Rate], 
    0
)

MOM Avg DTI = 
DIVIDE(
    [MTD Avg DTI] - [PMTD Avg DTI], 
    [PMTD Avg DTI], 
    0
)
```

---

## 3. Good Loan vs. Bad Loan Risk Segregation

Loans are categorized based on repayment status:
* **Good Loans**: `Fully Paid` and `Current`
* **Bad Loans**: `Charged Off`

### Good Loan Metrics
```dax
Good Loan Application = 
CALCULATE(
    [Total Loan Application], 
    financial_loan[Good vs Bad Loan] = "Good Loan"
)

Good Loan % = 
DIVIDE(
    [Good Loan Application], 
    [Total Loan Application], 
    0
)

Good Loan Funded Amount = 
CALCULATE(
    [Total Funded Amount], 
    financial_loan[Good vs Bad Loan] = "Good Loan"
)

Good Loan Received Amount = 
CALCULATE(
    [Total Amount Received], 
    financial_loan[Good vs Bad Loan] = "Good Loan"
)
```

### Bad Loan Metrics
```dax
Bad Loan Application = 
CALCULATE(
    [Total Loan Application], 
    financial_loan[Good vs Bad Loan] = "Bad Loan"
)

Bad Loan Application % = 
DIVIDE(
    [Bad Loan Application], 
    [Total Loan Application], 
    0
)

Bad Loan Funded Amount = 
CALCULATE(
    [Total Funded Amount], 
    financial_loan[Good vs Bad Loan] = "Bad Loan"
)

Bad Loan Received Amount = 
CALCULATE(
    [Total Amount Received], 
    financial_loan[Good vs Bad Loan] = "Bad Loan"
)
```

---

## 4. Dynamic Field Parameter (`Select Measure`)

A dynamic Field Parameter table was configured to enable user-driven measure switching across all categorical visuals on the Overview Page:
```dax
Select Measure = {
    ("Total Loan Applications", NAMEOF('Date Table'[Total Loan Application]), 0),
    ("Total Funded Amount", NAMEOF('Date Table'[Total Funded Amount]), 1),
    ("Total Amount Received", NAMEOF('Date Table'[Total Amount Received]), 2)
}
```
