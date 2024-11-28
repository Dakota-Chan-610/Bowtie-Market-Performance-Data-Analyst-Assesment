# Bowtie-Market-Performance-Data-Analyst-Assesment
Marketing Performance Data Analyst Technical Challenge Q5 - Dakota Chan Ching Lam

### Q5 i a Obtain the number of submitted claims in the year 2021 by products.  
**SQL File:**  
https://console.cloud.google.com/bigquery?sq=721656769525:0f2ac9894f6440a58d14c2878f88c06a  
`SELECT 
  p.product, 
  COUNT(c.id) AS number_of_claims
FROM
  bowtie-data-analyst.Sample_Data_Set.claim c
JOIN 
  bowtie-data-analyst.Sample_Data_Set.policy p ON c.policy_number = p.policy_number
WHERE 
  EXTRACT(YEAR FROM c.submit_date) = 2021
GROUP BY 
  p.product;
`
#### Results  
product	| number_of_claims  
critical-illness	4  
vhis	436  
life	5  
medical	4  
accidental-medical	13  

### Q5 i b Compare the average net premium received from new policies vs returning policies (i.e. the 2nd+ policy of the same user) Deliver your submission in .sql files. Note that you are NOT required to give out concrete numbers in the above tasks. The queries are the only required deliverables. Please also state as comments any assumptions you have made.   
**SQL File:**  
https://console.cloud.google.com/bigquery?sq=721656769525:a4ce5f6a638a4c24ad85479fd8f89222
`WITH policy_orders AS (
  SELECT 
    user_id, 
    policy_number,
    ROW_NUMBER() OVER(PARTITION BY user_id ORDER BY issue_date) AS policy_order
  FROM
    bowtie-data-analyst.Sample_Data_Set.policy
),
invoice_premiums AS (
  SELECT 
    policy_number, 
    AVG(pre_levy_amount) AS avg_net_premium
  FROM 
    bowtie-data-analyst.Sample_Data_Set.invoice
  WHERE 
    invoice_type = 'regular' AND status = 'paid'
  GROUP BY 
    policy_number
)
SELECT 
  CASE 
    WHEN po.policy_order = 1 THEN 'New'
    ELSE 'Returning'
  END AS policy_type,
  AVG(ip.avg_net_premium) AS average_net_premium
FROM 
  policy_orders po
JOIN 
  invoice_premiums ip ON po.policy_number = ip.policy_number
GROUP BY 
  policy_type; 
`
#### Results  
policy_type	| average_net_premium
New	344.7408
Returning	282.3412


### Q5 ii Create a dashboard to help management monitor the following business metrics




