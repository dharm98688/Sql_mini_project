# Sql_mini_project
Sql_Interview_Preparation


CREATE DATABASE ECommerce

use ecommerce

CREATE TABLE customers (
    customer_id INT PRIMARY KEY,             -- Unique ID for each customer
    name VARCHAR(100),                       	      -- Full name of the customer
    email VARCHAR(100) UNIQUE,              -- email address- EMAIL SHOULD BE UNIQUE
    country VARCHAR(50)                             -- Country where the customer lives
);
INSERT INTO customers (customer_id, name, email, country) VALUES
(1, 'Ankit Sharma', 'ankit@gmail.com', 'India'),
(2, 'Ravi Mehra', 'ravi@yahoo.com', 'India'),
(3, 'John Smith', 'john@outlook.com', 'USA'),
(4, 'Sara Khan', 'sara@gmail.com', 'UAE'),
(5, 'Priya Roy', 'priya@gmail.com', 'India');

CREATE TABLE products (
    product_id INT PRIMARY KEY,             -- Unique ID for each product
    product_name VARCHAR(100),              -- Name of the product
    price DECIMAL(10,2),                    -- Price of one unit
    category VARCHAR(50)                    -- Category like 'Electronics', 'Clothing', etc.
);
INSERT INTO products (product_id, product_name, category, price) VALUES
(1, 'iPhone 14', 'Electronics', 70000.00),
(2, 'Samsung TV', 'Electronics', 45000.00),
(3, 'Dell Laptop', 'Electronics', 65000.00),
(4, 'Nike Shoes', 'Footwear', 5000.00),
(5, 'Adidas T-Shirt', 'Clothing', 1500.00);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,              -- Unique ID for each order
    customer_id INT,                       -- Customer who placed the order
    order_date DATE,                       -- Date of order
    total_amount DECIMAL(10,2),            -- Total bill of the order
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
INSERT INTO orders (order_id, customer_id, order_date, total_amount) VALUES
(101, 1, '2024-11-01', 75000.00),
(102, 2, '2024-11-03', 1500.00),
(103, 3, '2024-11-05', 110000.00),
(104, 1, '2024-12-01', 65000.00),
(105, 4, '2024-12-10', 5000.00);

CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY,          -- Unique ID for each row (item in order)
    order_id INT,                           -- Order in which this product is bought
    product_id INT,                         -- Which product was bought
    quantity INT,                           -- Kitne units kharide
    price_each DECIMAL(10,2),               -- Us waqt per unit ka price
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);


INSERT INTO order_items (order_item_id, order_id, product_id, quantity, price_each) VALUES
(1, 101, 1, 1, 70000.00),
(2, 101, 5, 1, 1500.00),
(3, 102, 5, 1, 1500.00),
(4, 103, 2, 1, 45000.00),
(5, 103, 3, 1, 65000.00),
(6, 104, 3, 1, 65000.00),
(7, 105, 4, 1, 5000.00);

--Q1. Get top 3 most sold products by quantity.
SELECT 
	TOP 3
	p.product_name,
	SUM(oi.quantity)[TOTAL_QUANTITY] 
FROM order_items oi
JOIN products p ON oi.product_id=p.product_id
GROUP BY P.product_name
ORDER BY TOTAL_QUANTITY DESC

--Q2. Find customers who never placed an order
SELECT * FROM customers C
LEFT JOIN orders O ON C.customer_id=O.customer_id 
WHERE order_id IS NULL

--Q3. Total revenue generated from each country
SELECT 
	c.country,
	sum(o.total_amount) AS [TOTAL_AMOUNT]
FROM customers C
JOIN orders O on O.customer_id=C.customer_id
GROUP BY C.country

--Q4. Monthly revenue for last 6 months
SELECT 
	FORMAT(order_date,'yyyy-mm-dd') AS [DATE],
	SUM(total_amount)[Total_Amount]
FROM orders
WHERE order_date>=DATEADD(MONTH,-6,GETDATE())
GROUP BY FORMAT(order_date,'yyyy-mm-dd')
ORDER BY FORMAT(order_date,'yyyy-mm-dd')

SELECT * FROM orders

--Q5. Customers who bought ‘iPhone 14’

SELECT DISTINCT C.name FROM customers C
JOIN orders O ON O.customer_id=C.customer_id
JOIN order_items OI ON OI.order_id=O.order_id
JOIN products P ON P.product_id=OI.product_id
WHERE P.product_name='iPhone 14'

--Q6. Average order value
SELECT AVG(total_amount) [TOTAL_ORDER_VALUE] FROM orders

--Q7. Customer-wise total order value
SELECT 
	C.name,
	SUM(O.total_amount) AS TOTAL_SPENT
FROM customers C
JOIN orders O ON O.customer_id=C.customer_id
GROUP BY C.name

--Q8. Product which generated most revenue
SELECT 
	TOP 1
	P.product_name,
	SUM(OI.quantity * OI.price_each) [REVENUE]
FROM products P
JOIN order_items OI ON OI.product_id=P.product_id
GROUP BY P.product_name
ORDER BY REVENUE DESC

--Q9. Duplicate emails in customers
SELECT email,COUNT(*) FROM customers
GROUP BY email
HAVING COUNT(*)>1

--Q10. Day-wise order count and revenue
SELECT 
	order_date,
	COUNT(order_id)[ORDER_PLACED],
	SUM(total_amount)[REVENUE]
FROM orders
GROUP BY order_date

--Q11. Top 5 customers by spend
SELECT TOP 5
	C.name,
	SUM(O.total_amount) AS TOTAL_SPENT
FROM customers C
JOIN orders O ON O.customer_id = C.customer_id
GROUP BY C.name
ORDER BY TOTAL_SPENT desc

--Q12. Customers who ordered all products
SELECT 
	C.name
FROM customers C
JOIN orders O ON O.customer_id=C.customer_id
JOIN order_items OI ON OI.order_id=O.order_id
GROUP BY C.customer_id,C.name
HAVING COUNT(DISTINCT OI.product_id)= (SELECT COUNT(*) FROM products)

--Q13. Products never sold
SELECT * FROM products P
JOIN order_items OI ON P.product_id=OI.product_id
WHERE OI.order_item_id IS NULL


INSERT INTO customers (customer_id, name, email, country) VALUES
(6, 'Mohit Verma', 'mohitv@gmail.com', 'India'),
(7, 'Amit Patel', 'amitp@yahoo.com', 'USA'),
(8, 'Rekha Joshi', 'rekhaj@gmail.com', 'UK'),
(9, 'Divya Singh', 'divya@gmail.com', 'India'),
(10, 'Harshita Rao', 'harshita@hotmail.com', 'Canada');

INSERT INTO products (product_id, product_name, category, price) VALUES
(6, 'HP Printer', 'Electronics', 12000.00),
(7, 'Reebok Shorts', 'Clothing', 1800.00),
(8, 'Canon Camera', 'Electronics', 30000.00),
(9, 'Sony Headphones', 'Electronics', 4000.00),
(10, 'Puma Cap', 'Accessories', 800.00);

INSERT INTO orders (order_id, customer_id, order_date, total_amount) VALUES
(106, 6, '2024-12-12', 40000.00),
(107, 7, '2024-12-13', 1800.00),
(108, 8, '2024-12-14', 42800.00),
(109, 9, '2024-12-15', 5800.00),
(110, 10, '2024-12-16', 800.00);

INSERT INTO order_items (order_item_id, order_id, product_id, quantity, price_each) VALUES
(8, 106, 6, 1, 12000.00),
(9, 106, 8, 1, 30000.00),
(10, 107, 7, 1, 1800.00),
(11, 108, 9, 1, 4000.00),
(12, 108, 10, 1, 800.00),
(13, 109, 4, 1, 5000.00),
(14, 109, 10, 1, 800.00),
(15, 110, 10, 1, 800.00);

--Q14. Top-selling product in each category
SELECT category,product_name, total_sold
FROM 
(SELECT 
	P.category,
	P.product_name,
	SUM(OI.quantity) AS TOTAL_SOLD,
	RANK() OVER(PARTITION BY P.category order by sum(oi.quantity) desc) as rnk
FROM order_items OI
JOIN products P ON OI.product_id=P.product_id
group by p.category,p.product_name
) ranked
where rnk=1

--Q15. Total products per order
SELECT 
	order_id,
	SUM(quantity) AS TOTAL_ITEMS
FROM order_items
GROUP BY order_id
ORDER BY TOTAL_ITEMS DESC

--Q16. Most frequently ordered product
SELECT TOP 1
	P.product_name,
	COUNT(*) AS TOTAL_COUNT
FROM order_items OI
JOIN products P ON OI.product_id=P.product_id
GROUP BY P.product_name
ORDER BY TOTAL_COUNT DESC

--OR 


SELECT TOP 1
	P.product_name,
	COUNT(*) AS TOTAL_COUNT
FROM products P
JOIN order_items OI ON P.product_id=OI.product_id
GROUP BY P.product_name
ORDER BY TOTAL_COUNT DESC

--Q17. Most ordered product by each customer
SELECT * FROM
(
	SELECT 
		C.name,
		P.product_name,
		SUM(OI.quantity) AS TOTAL_QUANTITY,
		RANK() OVER(PARTITION BY C.customer_id ORDER BY SUM(OI.quantity) DESC) AS rnk
	FROM customers C
	JOIN orders O ON C.customer_id=O.customer_id
	JOIN order_items OI ON O.order_id=OI.order_id
	JOIN products P ON OI.product_id=P.product_id
	GROUP BY C.name,P.product_name,C.customer_id
) Ranked
WHERE rnk=1

--Q18. Order count per category
SELECT 
	P.category,
	COUNT(DISTINCT OI.order_id) AS ORDERS
FROM products P
JOIN order_items OI ON P.product_id=OI.product_id
GROUP BY P.category

--Q19. Revenue for each product-category combo
SELECT 
    p.category,
    p.product_name,
    SUM(oi.quantity * oi.price_each) AS total_revenue
FROM products p
JOIN order_items oi ON p.product_id = oi.product_id
GROUP BY p.category, p.product_name;

--Q20. Orders with wrong totals (validation)
SELECT 
    o.order_id,
    o.total_amount,
    SUM(oi.quantity * oi.price_each) AS calculated_total
FROM orders o
JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY o.order_id, o.total_amount
HAVING o.total_amount != SUM(oi.quantity * oi.price_each);

