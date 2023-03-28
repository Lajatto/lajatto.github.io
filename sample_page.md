## SQL Website Traffic Analysis

**Project description:** Website traffic analysis using SQL. Pulling monthly trends, checking the performance of various campaigns, checking conversion rates of landing pages, and doing full funnel conversion analysis. 

## The dataset that is used here is comprised of the following tables: 
<ul>
  <li>order_items_refunds</li>
  <li>order_items</li>
  <li>orders</li>
  <li>products</li>
  <li>website_pageviews</li>
  <li>website_sessions</li>
</ul>

### 1.  Pull monthly trends for gsearch sessions and orders so that we can showcase the growth here.

Limitations: Request was made on 2012-11-27. 

```SQL
SELECT
    YEAR(website_sessions.created_at) AS year,
    MONTH(website_sessions.created_at) AS month, 
    COUNT(DISTINCT website_sessions.website_session_id) AS monthly_sessions,
    COUNT(orders.items_purchased) AS total_orders
FROM 
	website_sessions 
		LEFT JOIN orders 
			ON website_sessions.website_session_id = orders.website_session_id 
WHERE 
	website_sessions.utm_source = 'gsearch'
    AND website_sessions.created_at < '2012-11-27'
GROUP BY 
	1,2;
```
<a href="https://lookerstudio.google.com/reporting/0eefca3d-9f2e-4e06-8a2d-11b8aa40fb49">
<img src="images/Q1.jpg?raw=true"/>
</a>

### 2. Similar monthly trend for gsearch but splitting out the brand and nonbrand campaigns. 

```SQL
SELECT
    website_sessions.utm_campaign,
    MONTH(website_sessions.created_at) AS month, 
    COUNT(DISTINCT website_sessions.website_session_id) AS monthly_sessions,
    COUNT(orders.items_purchased) AS total_orders
FROM 
	website_sessions 
		LEFT JOIN orders 
			ON website_sessions.website_session_id = orders.website_session_id 
WHERE 
	website_sessions.utm_source = 'gsearch'
    AND website_sessions.created_at < '2012-11-27'
    AND website_sessions.utm_campaign IN ('nonbrand', 'brand')
GROUP BY 
	1, 2;
```
<a href="https://lookerstudio.google.com/reporting/e59a198e-cc2d-478d-b659-3dece8702153">
<img src="images/Q22.jpg?raw=true"/>
</a>
<img src="images/Q222.jpg?raw=true"/>

### 3. Checking gsearch and nonbrand monthly trend for sessions and orders filtered by device type. 

```SQL
SELECT
    website_sessions.device_type,
    MONTH(website_sessions.created_at) AS month, 
    COUNT(DISTINCT website_sessions.website_session_id) AS monthly_sessions,
    COUNT(orders.items_purchased) AS total_orders
FROM 
	website_sessions 
		LEFT JOIN orders 
			ON website_sessions.website_session_id = orders.website_session_id 
WHERE 
	website_sessions.utm_source = 'gsearch'
    AND website_sessions.created_at < '2012-11-27'
    AND website_sessions.utm_campaign IN ('nonbrand')
GROUP BY 
	1, 2;
```
<a href="https://lookerstudio.google.com/reporting/8e3734e2-4c7e-41f9-8f1d-92df96bdc59e">
<img src="images/Q3.jpg?raw=true"/>

### 4. Comparing website traffic sources (paid vs organic searches)

```SQL
SELECT
    YEAR(website_sessions.created_at) AS year,
    MONTH(website_sessions.created_at) AS month, 
    COUNT(DISTINCT CASE WHEN website_sessions.utm_source = 'gsearch' THEN website_sessions.website_session_id ELSE NULL END) AS gsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN website_sessions.utm_source = 'bsearch' THEN website_sessions.website_session_id ELSE NULL END) AS bsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN website_sessions.utm_source IS NULL AND http_referer IS NOT NULL THEN website_sessions.website_session_id ELSE NULL END) AS organic_search_sessions,
    COUNT(DISTINCT CASE WHEN website_sessions.utm_source IS NULL AND http_referer IS NULL THEN website_sessions.website_session_id ELSE NULL END) AS direct_type_in_sessions
FROM 
	website_sessions 
		LEFT JOIN orders 
			ON website_sessions.website_session_id = orders.website_session_id 
WHERE 
    website_sessions.created_at < '2012-11-27'
GROUP BY 
	1, 2;
```
<a href="https://lookerstudio.google.com/reporting/4ab05325-c81c-479e-8698-d547e7378491">
<img src="images/Q4.jpg?raw=true"/>

### 5. Pulling sessions to orders conversion rates per month for the first 8 months of the website. 
```SQL
CREATE TEMPORARY TABLE session_to_ty
SELECT
	website_sessions.website_session_id, 
	website_pageviews.pageview_url, 
	website_pageviews.created_at AS pageview_created_at,
    CASE WHEN website_pageviews.pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END as ty_page
FROM 
	website_sessions
		LEFT JOIN website_pageviews 
			ON website_sessions.website_session_id = website_pageviews.website_session_id 
WHERE
	 website_sessions.created_at < '2012-11-27'
GROUP BY 
	1, 2, 3;

SELECT* 
FROM session_to_ty;

-- create temp table for only the sessions that made it to ty page
CREATE TEMPORARY TABLE conversion_table
SELECT 
	website_session_id, 
    MAX(ty_page) AS TY
FROM (
SELECT
	website_sessions.website_session_id, 
	website_pageviews.pageview_url, 
	website_pageviews.created_at AS pageview_created_at,
    CASE WHEN website_pageviews.pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END as ty_page
FROM 
	website_sessions
		LEFT JOIN website_pageviews 
			ON website_sessions.website_session_id = website_pageviews.website_session_id 
WHERE
	 website_sessions.created_at < '2012-11-27'
GROUP BY 
	1, 2, 3
) made_ty
GROUP BY 
	1;

-- create conversion rate 
SELECT 
	MONTH(website_sessions.created_at) AS month,
	COUNT(DISTINCT conversion_table.website_session_id) AS sessions,
	COUNT(DISTINCT CASE WHEN conversion_table.TY = 1 THEN conversion_table.website_session_id ELSE NULL END) AS orders,
    COUNT(DISTINCT CASE WHEN conversion_table.TY = 1 THEN conversion_table.website_session_id ELSE NULL END)  / COUNT(DISTINCT website_sessions.website_session_id) AS conversion_rate
FROM 
	website_sessions
		INNER JOIN conversion_table 
			ON website_sessions.website_session_id = conversion_table.website_session_id 
GROUP BY 
	1;
```
<a href="https://lookerstudio.google.com/reporting/63dd1b31-841c-4c74-9710-fa1509f95223">
<img src="images/Q5.jpg?raw=true"/>


For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
