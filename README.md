# E-Commerce Database Management System (MySQL)

## Project Overview
This project demonstrates how to build a simple **E-Commerce Database System** using **MySQL**.  
It includes creating databases, defining relationships between tables, inserting data, and running SQL queries for data retrieval and manipulation.

---

##  Features
- Customer, Product, and Order management  
- Relational database with foreign key constraints  
- Example queries for data retrieval, updates, and normalization  
- Demonstrates database normalization (3NF) using an `order_items` table  

---

##  Create the Database
```sql
-- Create a new database named 'ecommerce'
CREATE DATABASE ecommerce;

-- Switch to the ecommerce database
USE ecommerce;

---
## Create the Tables

1)customers

CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    address VARCHAR(255)
);

2)products

CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10,2),
    description TEXT
);

3)orders

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

---

## Insert Sample Data

1)customers

INSERT INTO customers (name, price, description) VALUES
(1,'Vel','vel@example.com','96, Peramal Kadu Street, Chennimalai, Erode'),
(2,'Vihan','vihan@example.com','29, Gandhi Mahan Road, Chennai'),
(3,'Dhuva','dhuva@example.com','1122, KK Nagar, Gudalur, Ooty');


2)products

INSERT INTO products (name, price, description) VALUES
('Product A', 50.00, 'High quality product A'),
('Product B', 30.00, 'Affordable product B'),
('Product C', 40.00, 'Premium product C'),
('Product D', 60.00, 'Top rated product D');

3)orders

INSERT INTO orders (customer_id, order_date, total_amount) VALUES
(1, CURDATE() - INTERVAL 10 DAY, 120.00),
(2, CURDATE() - INTERVAL 35 DAY, 200.00),
(1, CURDATE() - INTERVAL 5 DAY, 80.00),
(3, CURDATE() - INTERVAL 20 DAY, 160.00);

---

## Required Queries

1)Retrieve all customers who have placed an order in the last 30 days

SELECT DISTINCT c.*
FROM customers c
JOIN orders o ON c.id = o.customer_id
WHERE o.order_date >= CURDATE() - INTERVAL 30 DAY;

2)Get the total amount of all orders placed by each customer

SELECT c.name, SUM(o.total_amount) AS total_spent
FROM customers c
JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name;

3)Update the price of Product C to 45.00

UPDATE products
SET price = 45.00
WHERE name = 'Product C';

4)Add a new column discount to the products table

ALTER TABLE products
ADD COLUMN discount DECIMAL(5,2) DEFAULT 0.00;

5)Retrieve the top 3 products with the highest price
SELECT name, price
FROM products
ORDER BY price DESC
LIMIT 3;

6)Get the names of customers who have ordered Product A

SELECT DISTINCT c.name
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE p.name = 'Product A';

7)Join orders and customers to retrieve customers name and order date

SELECT c.name AS customer_name, o.order_date, o.total_amount
FROM orders o
JOIN customers c ON o.customer_id = c.id;

8)Retrieve orders with a total amount greater than 150.00

SELECT *
FROM orders
WHERE total_amount > 150.00;

9)Normalization — Creating order_items Table

CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    subtotal DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

## Insert order_items

INSERT INTO order_items (order_id, product_id, quantity, subtotal) VALUES
(1, 1, 2, 100.00),
(1, 2, 1, 20.00),
(2, 3, 5, 200.00),
(3, 1, 1, 50.00),
(3, 2, 1, 30.00),
(4, 4, 2, 160.00);

10)Retrieve the Average Total of All Orders
SELECT AVG(total_amount) AS average_order_total
FROM orders;
