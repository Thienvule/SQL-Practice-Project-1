# TrendyGear ecommerce
Using SQL to answer business questions from an ecommerce dataset
## Understanding the data
Dataset details: View here
The dataset includes fields with the "RECORD" datatype, which requires a deeper understanding. In databases like Google BigQuery, a RECORD (often referred to as a struct or nested field) is a complex data type that allows for the grouping of multiple fields into a single entity. This capability is beneficial for organizing related data that can be treated as a cohesive unit.

It's important to note that while some arrays may not be explicitly labeled as "ARRAY" in the schema description, their repetitive structure indicates that they function as arrays. This distinction highlights the need to interpret the data schema carefully, as the presence of repeating records indicates nested or array-like behavior even when not explicitly shown.

## Context Summary
Current time: September 2017
### Company Overview
Company Name: TrendyGear
- Industry: E-commerce specializing in lifestyle, fashion, and tech accessories.
- Vision: To be a leading online destination for trendy products that resonate with a modern, urban lifestyle, offering customers a seamless shopping experience.
- Mission: To provide high-quality, curated products while leveraging data-driven insights to enhance customer satisfaction and drive sales growth.

### Stakeholders
Chief Marketing Officer (CMO):
- Concern: The CMO is focused on understanding the effectiveness of various marketing channels and their contributions to overall revenue. High bounce rates from specific traffic sources raise concerns about campaign effectiveness and messaging alignment.
- Request: Understand the current performance and wish to analyze bounce rates by traffic source to identify underperforming areas and optimize marketing strategies.

Product Manager:
- Concern: The Product Manager is interested in understanding customer behavior regarding specific items, such as the "YouTube Men's Vintage Henley." Insights into related purchases and average spend per session can inform product recommendations and inventory decisions.
- Request: Identify cross-selling opportunities and analyze average transactions and spending behavior among purchasers.

Chief Financial Officer (CFO):
- Concern: The CFO is focused on financial performance metrics, including revenue generation and cost efficiencies. Understanding which channels generate the most revenue and having a clear understanding of customer purchasing behaviors are essential for budgeting and forecasting.
- Request: Examine revenue by traffic source and overall monthly trends in transactions and pageviews, along with cost per acquisition metrics.

User Experience (UX) Designer:
- Concern: The UX Designer aims to improve website engagement and reduce bounce rates. Understanding how different user segments (purchasers vs. non-purchasers) interact with the site can lead to better design decisions.
- Request: Analyze average pageviews by purchaser type to enhance the experience for non-purchasers to encourage them toward conversion.


### Problems Leading to Analysis Requests
- Declining Engagement Metrics: Stakeholders have observed a decline in user engagement metrics, such as increased bounce rates across certain traffic sources. This has led to concerns about the effectiveness of current marketing campaigns and the relevance of the landing pages.
- Need for Data-Driven Insights: The company recognizes the necessity for data-driven insights to make informed decisions. Stakeholders are seeking actionable insights based on monthly performance data to continuously refine marketing strategies and product offerings.
- Understanding Customer Journey: There is a growing imperative to understand the customer journey from product view to purchase. Stakeholders are particularly concerned with ensuring that the drop-off rates between these stages are minimized, highlighting a need for robust cohort analysis.
- Maximizing Revenue Potential: Given the competitive landscape of e-commerce, stakeholders are motivated to maximize revenue potential through strategic product placements, effective cross-selling, and upselling based on user behavior.
- User Segmentation for Tailored Strategies: The need to segment users into groups (purchasers vs. non-purchasers) is essential for creating targeted marketing strategies. This segmentation is crucial for understanding different engagement patterns and optimizing the user experience accordingly.

## Queries
Table to be feched: FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
### Query 01: Monthly Metrics for 2017
- Stakeholder Context: All
- Problem: All stakeholders want to undestand overall business performance and identifying trends in traffic and conversions from the start of the year as they all felt that something is getting worse.
- Task to be done: To establish a solid baseline for performance and understand customer engagement from the start of the year to now, we will analyze the total visits, pageviews, and transactions from Jan to Aug 2017. This will help track any fluctuations and identify patterns that need addressing.
#### Code:
 
  ![image](https://github.com/user-attachments/assets/1db5f48b-833d-4f9d-be69-92154a0dfd1a)

Code explanation:
`EXTRACT(MONTH FROM PARSE_DATE('%Y%m%d', date)) AS month`
- The date column in this dataset is stored as a string in the format YYYYMMDD (e.g., "20170101" for January 1, 2017).
- PARSE_DATE('%Y%m%d', date) converts this string into a proper date format that BigQuery can understand.
- EXTRACT(MONTH FROM ...) takes the month part of the date (numbers 1 to 12 representing January to December).
- This creates a column called month (e.g., 1 for January, 2 for February, etc.).

`SUM`
`SUM(totals.pageviews) AS pageviews`: adds up all the pageviews
`SUM(totals.visits) AS visits`:  adds up all the visits/sessions 
`SUM(totals.transactions) AS transactions`: adds up all the completed transactions

GROUP BY month: Groups all the data for each month together as we use an aggregate function above
ORDER BY month: sorts the rows by months of 2017

#### Result

![image](https://github.com/user-attachments/assets/c228cd6f-0c45-489d-8568-50c89a12113f)

![image](https://github.com/user-attachments/assets/2d4785e6-a5a5-4ea3-98ae-69e3b7ee4cfe)

=> The chart highlights a significant observation: a sudden drop in all metrics during the final month, August, following a period of consistent performance. This, in a way, confirm the concern of the CMO, implying a deeper investigation regarding the following aspects: 

- Analyze Traffic Sources: Compare the traffic volume from different channels (e.g., organic search, paid ads) between August and previous months.
- Check for Technical Issues: Review site performance logs and analytics tracking setup in August.
- Assess Marketing Campaigns: Examine whether any campaigns ended, underperformed, or shifted their targeting.
- Review Customer Behavior: Look for shifts in user demographics, including geographic trends and returning vs. new customers.
- Evaluate External Factors: Check for competitor actions, regional events, or economic patterns impacting customer behavior.

### Query 02: Bounce Rate per Traffic Source in 2017
- Stakeholder: Chief Marketing Officer (CMO)
- Problem: Observing high bounce rates from certain traffic sources raises concerns about the effectiveness of marketing campaigns.
- Lead: To optimize our marketing strategies, it's essential to analyze the bounce rate for each traffic source in  2017. This data will help identify which channels are underperforming and require adjustments to enhance user engagement.

#### Code:
![image](https://github.com/user-attachments/assets/42d3a5d0-7dc3-4511-96d2-32c5e366d303)

Code explanation:
- `SELECT trafficSource.source`: This retrieves the source of traffic
- SUM codes: `SUM(totals.visits) AS total_visits/SUM(totals.bounces) AS num_bounce`: Shows sources' visits and bounces
- `ROUND(SUM(totals.bounces) * 100 / SUM(totals.visits), 2) AS bounce_rate`: Calculate bounce rates of each source

#### Result
![image](https://github.com/user-attachments/assets/cdf651d2-eba8-49b5-8e24-60159fefd66d)

To gain deeper insights, I imported the data into Power BI and followed these steps:
- Load the CSV File: Imported the CSV file from Google BigQuery into Power BI.
- Format the Bounce Rate: Adjusted the bounce_rate column to display values as percentages with a simple DAX
 ![image](https://github.com/user-attachments/assets/c2b53e1a-89e9-4684-9b83-e84669208492)

- Visualize Data: Created a visualization highlighting the top 10 traffic sources with the highest bounce rates, filtered to include only those with the highest number of visits.

![image](https://github.com/user-attachments/assets/a92f0f92-794d-4d9c-b228-0d74bb1c3ea7)

#### Result
![image](https://github.com/user-attachments/assets/1acf2421-e0cf-4005-822c-1000beea2411)

=> From this chart, the CMO can direct her investigation towards the top 10 traffic sources that exhibit underperformance.

### Query 03: Revenue by Traffic Source 
Stakeholder Context: Chief Financial Officer (CFO)

Problem: Understanding which marketing channels are driving revenue and which are underperforming is critical for budget allocation and optimization.

Lead: To identify effective traffic sources, we will analyze revenue generated by each traffic source in June 2017. This will allow the CFO to make data-driven decisions about where to focus marketing investments.
#### Code:
![image](https://github.com/user-attachments/assets/30610b6a-5d7d-413a-8c12-e177877e0fa4)

Code explanation:
- `date`: This selects the date of each session from the dataset. This will be used to build date hierarchy in PBI for deeper granularities.
- `trafficSource.source`: This selects the source of the traffic to the website, indicating where visitors originated from (e.g., social media, search engines).
- `SUM(productRevenue) AS revenue`: This calculates the total revenue generated from products sold, aggregating the productRevenue values.
- `UNNEST(hits) AS hits`/`UNNEST(product) AS product`: This function flattens the hits AND product array, which contains multiple hit records per session, converting them into individual rows allow us to access their properties directly.
- `productRevenue IS NOT NULL`: This condition filters out any records where productRevenue is null. It ensures that only entries with actual revenue values are included in the results.
  
For better insights gathering, I utilize PBI and:

- Edit data type using Power Query

![image](https://github.com/user-attachments/assets/7ae3975e-fb4e-429d-9950-2ebdf8d7e171)

- Build Hierarchy:
  
![image](https://github.com/user-attachments/assets/25f06c44-318a-49a4-aeea-3754ac6820bf)
![image](https://github.com/user-attachments/assets/9f8f59cf-c571-44e0-8ddd-70bd1de15a95)

#### Result

- Revenue by Month:
![image](https://github.com/user-attachments/assets/183a7751-12b8-49df-9c06-8494c7b7852e)

- Revenue by Weekday:

![image](https://github.com/user-attachments/assets/38753dd2-7a81-429e-8f64-ef95c3e8bdee)

Based on these insights, the CFO can identify revenue spikes in April and July. She can then collaborate with the CMO to investigate the reasons behind the increases occurring on specific weekdays, such as Tuesday and Wednesday.


### Query 04: Average Pageviews by Purchaser Type in 2017
Stakeholder Context: User Experience (UX) Designer

Situation: Understanding how user types engage with the site can provide insights for enhancing personalization and navigation.

Action: To improve the user experience, we will examine the average number of pageviews by purchasers and non-purchasers in 2017. This information will help in crafting strategies to engage non-purchasers more effectively.

#### Code:

![image](https://github.com/user-attachments/assets/14fa29a9-6fb0-4d65-93cd-ae89d58dd05d)

Code explanation
- The `WITH cte AS (...)` defines a common table expression (CTE) named cte which is temporarily created for use in the subsequent query.
- `WHEN product.productRevenue IS NOT NULL AND totals.transactions >= 1 THEN 'Purchaser' ELSE 'Non-purchaser' END AS purchaser_type`: Label who purchased and who didn't
- `AVG(CASE WHEN purchaser_type = 'Purchaser' THEN pageviews END) AS Purchaser`: This calculates the average pageviews for visitors categorized as purchasers.
- `AVG(CASE WHEN purchaser_type = 'Non-purchaser' THEN pageviews END) AS Non_purchaser`: : This calculates the average pageviews for visitors classified as non-purchasers.

#### Result
- The final result will show a list of months (from the dataset), along with the average number of pageviews for both purchasers and non-purchasers.
- Each row represents a month, with two average values indicating how many pages were viewed on average by users in each category.

![image](https://github.com/user-attachments/assets/09c5c23a-5a58-4df3-9c1a-312bd46116bf)

=> The User Experience (UX) Designer can analyze the peak in March to determine the factors that increased pageviews among purchasers, providing insights that the CMO can utilize.

### Query 05: Average Transactions per User That Made a Purchase in March 2017
Stakeholder Context: Product Manager and CMO

Situation: The Product Manager and the CMO is interested to see if this peak in pageviews in March did generate substantial value as they were notified by the UX Designer.

Action: To discover how much value was generated from that peak, we will calculate the average number of transactions per user who made a purchase in March 2017. This information aids in developing strategies for encouraging repeat purchases.

#### Code:

![image](https://github.com/user-attachments/assets/55952eac-0874-4739-816a-4415a9dd15b6)

Code Explanation: 
- `CTE`: To identify purchasers as the last query
- `ROUND(SUM(transactions) / COUNT(DISTINCT fullVisitorId), 2) AS avg_trans_per_user`: This calculates the average number of transactions per user by dividing the total number of transactions by the count of distinct visitors (fullVisitorId). The result is rounded to two decimal places.

- `ROUND(SUM(revenue) / COUNT(DISTINCT fullVisitorId), 2) AS avg_rev_per_user`: Similarly, this calculates the average revenue generated per user by dividing the total revenue by the count of distinct visitors. The value is rounded to two decimal places.

#### Result

![image](https://github.com/user-attachments/assets/7742fb32-767e-44a8-a4e2-238e6301144f)

![image](https://github.com/user-attachments/assets/e97317a3-3904-41b1-9776-6c3596b01ff4)

![image](https://github.com/user-attachments/assets/40b94522-45e9-4021-81c7-a4455290d5cd)

=> It has been confirmed that March is an ideal starting point for our investigation.

### Query 06: Average Amount Spent per Session by Purchasers in  2017
Stakeholder Context: Chief Financial Officer (CFO) and Chief Marketing Officer (CMO)

Problem: Understanding expenditure patterns is vital for optimizing pricing strategies and promotions. Wonder if 

Lead: To determine how much customers spend on average during their sessions, we'll calculate the average amount spent per session for purchasers in July 2017. This insight can identify pricing opportunities and promotional strategies.

#### Code

![image](https://github.com/user-attachments/assets/ee72bc98-9119-4df5-b54e-1a15bfd55ab9)
Code explanation:
-`SUM(totals.visits) AS total_visits`: This calculates the total number of visits across all sessions for each month.
- `SUM(product.productRevenue) AS total_revenue`: This sums up the total revenue generated from product sales for each month.
-  `SUM(product.productRevenue)/SUM(totals.visits) AS avg_revenue_per_visit`: This calculates the average revenue per visit by dividing the total revenue by the total number of visits.
#### Result
![image](https://github.com/user-attachments/assets/91904619-10b6-4777-8bd3-4c7ef9f17d93)
![image](https://github.com/user-attachments/assets/91a45dd8-4599-4151-acda-3c2fff1ebbd9)
![image](https://github.com/user-attachments/assets/b480243d-00fd-4bdc-befa-1b533ac5808d)
![image](https://github.com/user-attachments/assets/0c8d7db4-ef6f-4409-b0f4-2e225a437ded)

### Query 07: Other Products Purchased by Customers of "YouTube Men's Vintage Henley" in July 2017
Stakeholder Context: Product Manager

Problem: Identifying cross-selling opportunities enhances the product mix and boosts overall sales.

Lead: To effectively market related products, we will analyze the purchasing patterns of customers who bought the "YouTube Men's Vintage Henley" in July 2017, revealing other products frequently bought together. This insight drives cross-selling strategies.

### Query 08: Cohort Map from Product View to Add to Cart to Purchase in Q1 2017
Stakeholder Context: User Experience (UX) Designer / Chief Financial Officer (CFO)

Problem: Understanding user behavior from product views to purchases aids in funnel optimization.

Lead: To visualize the journey from product views to purchases through a cohort map, we will analyze the conversion rates at each step in January, February, and March 2017. This will help in understanding the efficacy of the purchase funnel and informing strategies to reduce drop-offs.
