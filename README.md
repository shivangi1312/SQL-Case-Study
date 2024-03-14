# SQL-Case-Study
Danny's Diner SQL Case Study

                                                                             Introduction
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.
Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

                                                                          Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.
He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.
Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!
Danny has shared with you 3 key datasets for this case study:

sales
menu
members

Example Datasets

Table 1: sales
The sales table captures all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered.

customer_id	 order_date	 product_id
A	           2021-01-01	     1
A	           2021-01-01	     2
A	           2021-01-07	     2
A	           2021-01-10	     3
A	           2021-01-11	     3
A	           2021-01-11	     3
B	           2021-01-01	     2
B	           2021-01-02	     2
B	           2021-01-04	     1
B	           2021-01-11	     1
B	           2021-01-16	     3
B	           2021-02-01	     3
C	           2021-01-01	     3
C	           2021-01-01	     3
C	           2021-01-07	     3

Table 2: menu
The menu table maps the product_id to the actual product_name and price of each menu item.

product_id	product_name	 price
1	           sushi	        10
2	           curry	        15
3	           ramen	        12

Table 3: members
The final members table captures the join_date when a customer_id joined the beta version of the Danny’s Diner loyalty program.

customer_id	 join_date
A	           2021-01-07
B	           2021-01-09

                                                   Case Study Questions
Each of the following case study questions can be answered using a single SQL statement:

What is the total amount each customer spent at the restaurant?
SELECT customer_id, SUM(menu.price) AS total_spent
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id;

How many days has each customer visited the restaurant?
SELECT customer_id, COUNT(DISTINCT order_date) AS days_visited
FROM sales
GROUP BY customer_id;

What was the first item from the menu purchased by each customer?
SELECT customer_id, MIN(order_date) AS first_purchase_date, menu.product_name AS first_item_purchased
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id, menu.product_name;

What is the most purchased item on the menu and how many times was it purchased by all customers?
SELECT menu.product_name, COUNT(*) AS total_purchases
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY menu.product_name
ORDER BY total_purchases DESC
LIMIT 1;

Which item was the most popular for each customer?
SELECT customer_id, menu.product_name AS most_popular_item, COUNT(*) AS total_purchases
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id, menu.product_name
ORDER BY customer_id, total_purchases DESC

Which item was purchased first by the customer after they became a member?
SELECT sales.customer_id, menu.product_name AS first_purchase_after_membership
FROM sales
JOIN menu ON sales.product_id = menu.product_id
JOIN members ON sales.customer_id = members.customer_id
WHERE sales.order_date > members.join_date
GROUP BY sales.customer_id, menu.product_name;

Which item was purchased just before the customer became a member?
SELECT sales.customer_id, menu.product_name AS last_purchase_before_membership
FROM sales
JOIN menu ON sales.product_id = menu.product_id
JOIN members ON sales.customer_id = members.customer_id
WHERE sales.order_date < members.join_date
GROUP BY sales.customer_id, menu.product_name;

What is the total items and amount spent for each member before they became a member?
SELECT sales.customer_id, COUNT(*) AS total_items, SUM(menu.price) AS total_amount_spent
FROM sales
JOIN menu ON sales.product_id = menu.product_id
JOIN members ON sales.customer_id = members.customer_id
WHERE sales.order_date < members.join_date
GROUP BY sales.customer_id;

If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
SELECT customer_id, 
       SUM(CASE WHEN product_name = 'sushi' THEN (menu.price * 2 * 10) ELSE (menu.price * 10) END) AS total_points
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id;

In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
SELECT sales.customer_id, 
       SUM(CASE WHEN menu.product_name = 'sushi' THEN (menu.price * 2 * 10) ELSE (menu.price * 2 * 10) END) AS total_points
FROM sales
JOIN menu ON sales.product_id = menu.product_id
JOIN members ON sales.customer_id = members.customer_id
WHERE sales.order_date >= members.join_date AND sales.order_date <= DATE_ADD(members.join_date, INTERVAL 7 DAY)
GROUP BY sales.customer_id;
