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


1. **Loan Application**
	i. Total Loan Application
	 ```sql
	select count(id) as mtd_total_loan_application from bank_loan_data
	```
	ii. MTD Total Loan Application
	 ```sql
	select count(id) as mtd_total_loan_application from bank_loan_data
	where extract(month from issue_date) = 12 and extract(year from issue_date) = 2021
	```
     iii. MOM Total Loan Application
	 ```sql
	select
	  case 
	    when pmtd.pmtd_total_loan_application > 0 then
	      ROUND((mtd.mtd_total_loan_application :: NUMERIC - pmtd.pmtd_total_loan_application) 
	            / pmtd.pmtd_total_loan_application * 100, 2)
	    else null
	  end as mom_percentage_change
	from
	(select count(id) as mtd_total_loan_application 
	from bank_loan_data
	where extract(month from issue_date) = 12 
	and extract(year from issue_date) = 2021) mtd,
	
	(select count(id) as pmtd_total_loan_application 
	from bank_loan_data
	where extract(month from issue_date) = 11 
	and extract(year from issue_date) = 2021) pmtd
	```

  2. **Funded Amount**
	i. Total Funded Amount
	 ```sql
		select sum(loan_amount) as total_funded_amount 
		from bank_loan_data
	 ```
ii. MTD Total Funded Amount
```sql
	select sum(loan_amount) as mtd_total_funded_amount 
	from bank_loan_data
	where extract(month from issue_date) = 12
	and extract(year from issue_date) = 2021
```
iii. MOM Total Funded Amount
```sql
		select
		case when pmtd.pmtd_total_funded_amount > 0 then
		round((mtd.mtd_total_funded_amount :: numeric - pmtd.pmtd_total_funded_amount) 
		/ pmtd.pmtd_total_funded_amount * 100, 2)
		else null
		end as mom_percentage_change from
		
		(select sum(loan_amount) as mtd_total_funded_amount 
		from bank_loan_data
		where extract(month from issue_date) = 12
		and extract(year from issue_date) = 2021) as mtd,
		
		(select sum(loan_amount) as pmtd_total_funded_amount 
		from bank_loan_data
		where extract(month from issue_date) = 11
		and extract(year from issue_date) = 2021) as pmtd
```

3. **Amount Received**
	i. Total Amount Received
	 ```sql
		select sum(total_payment) as total_loan_received from bank_loan_data
	 ```
ii. MTD Amount Received
```sql
	select sum(total_payment) as mtd_total_loan_received from bank_loan_data
WHERE EXTRACT(month from issue_date) = 12
and extract(year from issue_date) = 2021
```
iii. MOM Total Amount Received
```sql
		select 
case when pmtd.pmtd_total_loan_received > 0 then
round((mtd.mtd_total_loan_received::numeric - pmtd.pmtd_total_loan_received)
/ pmtd.pmtd_total_loan_received * 100, 2) 
else null
end as mom_percentage_change from

(select sum(total_payment) as mtd_total_loan_received from bank_loan_data
WHERE EXTRACT(month from issue_date) = 12
and extract(year from issue_date) = 2021) as mtd,

(select sum(total_payment) as pmtd_total_loan_received from bank_loan_data
WHERE EXTRACT(month from issue_date) = 11
and extract(year from issue_date) = 2021) as pmtd
```


4. **Interest Rate**
	i. Average Interest Rate
	 ```sql
		select (avg(int_rate) * 100) as avg_interest_rate from bank_loan_data
	 ```
ii. MTD Average Interest Rate
```sql
	select (avg(int_rate) * 100) as avg_interest_rate 
from bank_loan_data where
extract(month from issue_date) = 12
and extract (year from issue_date) = 2021
```
iii. MOM Average Interest Rate
```sql
	select 
case 
	when pmtd.pmtd_avg_interest_rate > 0 then
	round(((mtd.mtd_avg_interest_rate - pmtd.pmtd_avg_interest_rate)
	/ pmtd.pmtd_avg_interest_rate * 100)::numeric, 2)
	else null
	end as mom_percentage_change from

(select (avg(int_rate) * 100) as mtd_avg_interest_rate 
from bank_loan_data where
extract(month from issue_date) = 12
and extract (year from issue_date) = 2021) as mtd,

(select (avg(int_rate) * 100) as pmtd_avg_interest_rate 
from bank_loan_data where
extract(month from issue_date) = 11
and extract (year from issue_date) = 2021) as pmtd
```

5. **Debt To Income Ratio**
	i. Average Debt To Income Ratio
	 ```sql
		select (avg(dti) * 100) as avg_dti from bank_loan_data
	 ```
ii. MTD Average Debt To Income Ratio
```sql
	select (avg(dti) * 100) as avg_dti 
from bank_loan_data
where extract(month from issue_date) = 12
and extract(year from issue_date) = 2021
```
iii. MOM Average Debt To Income Ratio
```sql
	select
case when pmdt.pmdt_avg_dti > 0 then
round(((mdt.mdt_avg_dti - pmdt.pmdt_avg_dti) 
/ pmdt.pmdt_avg_dti * 100)::numeric, 2)
else null
end as mom_percentage_change from

(select (avg(dti) * 100) as mdt_avg_dti 
from bank_loan_data
where extract(month from issue_date) = 12
and extract(year from issue_date) = 2021) mdt,

(select (avg(dti) * 100) as pmdt_avg_dti 
from bank_loan_data
where extract(month from issue_date) = 11
and extract(year from issue_date) = 2021) pmdt
```

6. **Good Loan**
	i. Good Loan Percentage
	 ```sql
		select round((count(case when loan_status = 'Fully Paid' or loan_status = 'Current' 
		then id end)* 100.0) / count(id), 2) as good_loan_percentage
		from bank_loan_data
	 ```
ii. Good Loan Application
```sql
	select count(id) as good_loan_application from bank_loan_data
	where loan_status = 'Fully Paid' or loan_status = 'Current'
```
iii. Good Loan Funded Amount
```sql
	select sum(loan_amount) from bank_loan_data
	where loan_status = 'Fully Paid' or loan_status = 'Current'
```
iv. Good Loan Total Received Amount
```sql
	select sum(total_payment) from bank_loan_data
	where loan_status = 'Fully Paid' or loan_status = 'Current'
```

7. **Bad Loan**
	i. Bad Loan Percentage
	 ```sql
		select round((count(case when loan_status = 'Charged Off'
		then id end)* 100.0) / count(id), 2) as bad_loan_percentage
		from bank_loan_data
	 ```
ii. Bad Loan Application
```sql
	select count(id) as bad_loan_application from bank_loan_data
	where loan_status = 'Charged Off'
```
iii. Bad Loan Funded Amount
```sql
	select sum(loan_amount) from bank_loan_data
	where loan_status = 'Charged Off'
```
iv. Bad Loan Total Received Amount
```sql
	select sum(total_payment) from bank_loan_data
	where loan_status = 'Charged Off'
```

8. **Loan status grid view**
	 ```sql
	with all_stat as (
	select 
		loan_status, 
		count(id) as total_loan_application, 
		sum(loan_amount) as total_funded_amount,
		sum(total_payment) as total_amount_received,
		avg(int_rate * 100) as interest_rate,
		avg(dti * 100) as dti
	from bank_loan_data
	group by loan_status ),

	mdt_stat as(
		select 
			loan_status, 
			sum(total_payment) as mtd_total_amount_received, 
			sum(loan_amount) as mdt_total_funded_amount
		from bank_loan_data
		where extract(month from issue_date) = 12
		group by loan_status )
	
	select a.*, m.mtd_total_amount_received, m.mdt_total_funded_amount 
	from all_stat a
	left join mdt_stat m
	on a.loan_status = m.loan_status
	```

  9. **Monthly trends by issue date**
	 ```sql
		select 
		extract(month from issue_date) as months_number,
		TO_CHAR(issue_date, 'Month') AS month_name, 
		count(id) as total, 
		sum(loan_amount) as total_funded_amount, 
		sum(total_payment) as total_received_amount
		from bank_loan_data
		group by months_number, month_name
		order by extract(month from issue_date), month_name
  		```

  10. **Regional Analysis by state**
	  ```sql
			select 
			extract(month from issue_date) as months_number,
			TO_CHAR(issue_date, 'Month') AS month_name, 
			count(id) as total, 
			sum(loan_amount) as total_funded_amount, 
			sum(total_payment) as total_received_amount
			from bank_loan_data
			group by months_number, month_name
			order by extract(month from issue_date), month_name
	  ```

   11. **Loan Term Analysis**
	  ```sql
		select 
		term,
		count(id) as total, 
		sum(loan_amount) as total_funded_amount, 
		sum(total_payment) as total_received_amount
		from bank_loan_data
		group by term
		order by total_funded_amount desc

	  ```
12. **Employee Length Analysis**
	  ```sql
		select 
	emp_length,
	count(id) as total,
	sum(loan_amount) as total_funded_amount, 
	sum(total_payment) as total_received_amount
	from bank_loan_data
	group by emp_length
	order by total desc
	  ```

13. **Loan Purpose Breakdown**
	  ```sql
		select 
	purpose,
	count(id) as total,
	sum(loan_amount) as total_funded_amount, 
	sum(total_payment) as total_received_amount
	from bank_loan_data
	group by purpose
	order by total desc
	  ```

14. **Home Ownership Analysis**
	  ```sql
		select 
	home_ownership,
	count(id) as total,
	sum(loan_amount) as total_funded_amount, 
	sum(total_payment) as total_received_amount
	from bank_loan_data
	where grade = 'A' and address_state = 'CA'
	group by home_ownership
	order by total desc
	  ```
