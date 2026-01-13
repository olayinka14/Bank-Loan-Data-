### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `Bank_loanDB`.
- **Table Creation**: A table named `bank_loan_data` is created to store the  data. 

```sql
Create table bank_loan_data(
	id	int,
	address_state varchar(5),	
	application_type varchar(20),	
	emp_length varchar(50),
	emp_title varchar(255),
	grade varchar(50),
	home_ownership varchar(50),
	issue_date text,	
	last_credit_pull_date text,
	last_payment_date text,
	loan_status	varchar(20),
	next_payment_date text,
	member_id int,
	purpose	varchar(50),
	sub_grade varchar(50),
	term varchar(50),
	verification_status	varchar(50),
	annual_income float,	
	dti	float,
	installment	float,
	int_rate float,	
	loan_amount	int,
	total_acc int,
	total_payment int
)
```

### 2. Data Exploration & Cleaning
```sql
UPDATE bank_loan_data
SET last_credit_pull_date = to_date(last_credit_pull_date, 'dd-mm-yyyy'),
    issue_date = to_date(issue_date, 'dd-mm-yyyy'),
	last_payment_date = to_date(last_payment_date, 'dd-mm-yyyy'),
	next_payment_date = to_date(next_payment_date, 'dd-mm-yyyy');

ALTER TABLE bank_loan_data
ALTER COLUMN last_credit_pull_date TYPE DATE USING last_credit_pull_date::DATE,
ALTER COLUMN issue_date TYPE DATE USING issue_date::DATE,
ALTER COLUMN last_payment_date TYPE DATE USING last_payment_date::DATE,
ALTER COLUMN next_payment_date TYPE DATE USING next_payment_date::DATE;
```

### 3. Data Analysis & Findings


1. **Total funded amount
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```
i. ***MTD Total funded amount
 ```sql
select count(id) as mtd_total_loan_application from bank_loan_data
where extract(month from issue_date) = 12 and extract(year from issue_date) = 2021
```
