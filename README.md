# Churn-Risk-Analysis-for-Premium-Clients-SQL-Deliverables-
Churn Risk Analysis for Premium Clients – SQL Deliverables 
## Introduction 
This project outlines the SQL-based analysis conducted to address the increasing churn rate 
(25%) among premium clients at Bank of America. These clients make up 15% of the customer 
base but contribute 80% of the bank's revenue, making them critical to retain.

1. SQL Query 1: Show structure 
```
SELECT * 
From Customers LIMIT 10;
```
Output and Explanation – Query 1 

<img width="482" height="167" alt="image" src="https://github.com/user-attachments/assets/21c677ba-b2d0-4f05-ac38-29a9438b7e7d" />

This query returns a list of Ten Customers from Customer Table 

2. SQL Query 2: Count Premium and Regular
 ```
SELECT Ispremium, count (*) from Customers Group by Ispremium;
```
Output and Explanation – Query 2

<img width="482" height="109" alt="image" src="https://github.com/user-attachments/assets/73ad05d4-7a23-4228-aaf3-77d628760b54" />

The output show us how many customers are Premium and how many customers are 
regular. As we can see the Premium customers are 15 while the regular customers are 
85 the true value stand for Premium customers and false stand for Regular Customers.

3. SQL Query 3: Churn Rate by Premium Status
```
SELECT Ispremium, 
Count (*) as Total, 
SUM (CASE when Churned then 1 else 0 end) as Churned, 
ROUND ((SUM (CASE when Churned then 1 else 0 end) :: DECIMAL/COUNT(*))*100,2) as 
ChurnRate 
From Customers  
Group by Ispremium;
```

Output and Explanation – Query 3

<img width="489" height="122" alt="image" src="https://github.com/user-attachments/assets/cca59cd4-71ee-40e7-8b9a-94f25461e0e4" />


Total false and total true that is the total Premium customers and total regular Customers. 
From our Premium customers six have exited, from our Regular customers 13 have exited so 
the Churn Rate from our Premium customers is 40% while the churn rate from our regular 
customers is 15.29%

4. SQL Query 4: Temporary Table with Tiers --create a temporary table with tiers 
```
Create Temp table Costumers_tiers as 
select 
CustomerID, 
IsPremium, 
TotalRevenue, 
Churned, 
EngagementScore, 
LastActiveDate, 
MonthlyTransactions, 
BalanceTrendScore, 
Region, 
AccountType, 
case 
when TotalRevenue>=10000 then 'High' 
when TotalRevenue>=5000 then 'Medium' 
else 'Low' end as Profitability_tier 
from customers; --to see the temporary table 
select * from Costumers_tiers limit 10;
```

Output and Explanation – Query 4

Our Temporary table have a Profitability tier column where when the total Revenue is greater than or 
equal to 10,000 it return High and when the same total revenue is greater than or equal to 500 itv 
return Medium and when its less than 5000 it return Low.

5. SQL Query 5: Identify Risk Profiles using Multifactor Conditions 
```
-- identify Risk profile using multy factor coditions 
select  
CustomerID, 
IsPremium, 
Churned, 
TotalRevenue, 
EngagementScore, 
BalanceTrendScore, 
MonthlyTransactions, 
case 
when Churned and EngagementScore <50 and BalanceTrendScore <0 then 'High Risk' 
when Churned and ( 
(EngagementScore >=50 and EngagementScore <70) 
or BalanceTrendScore <0) then 'Moderate Risk' 
when not Churned and ( 
EngagementScore <70 or 
BalanceTrendScore <0 or  
MonthlyTransactions <3 
) then 'Watch' 
else 'Stable' end as RiskFlag 
from Costumers_tiers 
Where Ispremium = true;
```

Output and Explanation – Query 5

<img width="394" height="193" alt="image" src="https://github.com/user-attachments/assets/f97ea507-581f-4d6a-bbd4-b5a4ececc675" />

Our risk flag is based on multiple conditions like when we have a churn and engagement score 
is less than 50 and Balance trend is less than zero then it’s a High Risk 
In the case when churned and engagement score is greater than or equal to 50 and 
engagement score is less than 70 and Balance trend is less than zero then its moderate risk  
Watch is for Active users with sign of risk.

6. SQL Query 6:. Group by to summarize Patterns
```
-- let us use group by to summarize patterns 
select  
Profitability_tier, 
count(*) as TotaCusttomers, 
round(avg(EngagementScore),2) as AvgeEngagement, 
Sum(CASE when Churned then 1  else 0 end) as TotalChurned, 
round(avg(Monthlytransactions),2) as Avgtransactions 
from Costumers_tiers 
group by Profitability_tier 
order by  
case Profitability_tier 
when 'High' then 1 
when 'Medium' then 2 
when 'Low' then 3 
end; 

``
Output and Explanation – Query 6
<img width="535" height="113" alt="image" src="https://github.com/user-attachments/assets/51f63ded-b939-49e3-82d1-ad2bd437569f" />

The goal here is to show profit Category for each customer group: High, Medium and Low 
Where for the high profitability group we have 33 customers with 8 churn and 9.52 Average 
transaction for the medium we have 21 customers with 3 churns while Low profitability have 46 
customers and 8 churns.

7. SQL Query 7: Rank top 15 Customers
```
 -- lets rank our to 15 cuustomers 
with Ranked_Customer as ( 
select*, 
rank() over (order by TotalRevenue desc) as Revenue_Rank 
from Costumers_tiers 
) 
select * 
from Ranked_Customer 
where Revenue_Rank <= (select round(0.15*count(*))from Costumers_tiers);

``
Output and Explanation – Query 7

<img width="482" height="201" alt="image" src="https://github.com/user-attachments/assets/d80a4b13-9213-4776-9416-80be8fd8a35f" />

This output helps identify the top 15 revenue generating customers for urgent and special 
attention from retention teams because no company want to lose their top customers. 

## Conclusion 
The Churn Risk Analysis for Premium has identified specific high-risk premium clients. The bank 
can develop Immediate personalized actions can help reduce churn, retain revenue, and 
improve client satisfaction.

