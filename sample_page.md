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

### 2. Assess assumptions on which statistical inference will be based

```javascript
if (isAwesome){
  return true
}
```

### 3. Support the selection of appropriate statistical tools and techniques

<img src="images/dummy_thumbnail.jpg?raw=true"/>

### 4. Provide a basis for further data collection through surveys or experiments

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. 

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
