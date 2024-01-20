**Overview**

In this project, I will use my database design and development skills to reorganize spreadsheet data 
into a database for an online grocery business to help them expand their offerings. The objective was 
to catalyze their expansion by transitioning their product dataset from a spreadsheet to a well-organized
relational database.

**Project Scenario**

Greenspot Grocer, a fictional family-owned online grocery store, was experiencing rapid growth and planning
a major expansion. Their product dataset is stored in a spreadsheet, and they need a database to store more data.
So, our goal is to examine their current data and design a relational database. This database will help them to 
organize and store all the data as they expand their business. 

**Project Objectives**

1. Examine the current data and reorganize it into relational tables using the modeling tool in MySQL Workbench. 
2. Create and load the database with the sample data provided. 
3. Test the database design and verify the design by generating SQL JOIN queries.
The steps that we took to reach our goal in this project:

**Task 1 – Design a Relational Database:**

I carefully reviewed the grocery store data in the GreenspotDataset.csv file to Identify fields that should be
stored together in tables, considering the principle of grouping data about specific entities. Then, I started 
forming tables based on the identified entities in the dataset to ensure each table had a meaningful name, 
listed fields with appropriate data types, designated primary key(s) and added foreign keys where applicable.
Moreover, I analyzed relationships between tables to create connectors. I also added foreign keys to establish 
relationships between tables. Meanwhile, I created a New Model in MySQL Workbench to develop an EER Diagram 
capturing all tables, fields, primary keys, foreign keys, and connectors.

**Task 2 – Build Database Tables:**

I implemented the database design in MySQL Workbench. There are two different options to be considered,
including manual creation using menu options or the toolbar, or utilizing the Forward Engineer option from
the Database menu. Here, I used the forward engineering option to create my EER diagram.

**Task 3 – Load Database Tables with Sample Data:**

I worked through each row of the sample data in the .csv file to ensure each table had at least one row
before proceeding. After completing all rows, I checked and ensured correctness in data entry, paying 
attention to data types, foreign key references, and uniqueness of primary keys.

**Task 4 – Test the Database Design using SQL Queries:**

I formulated SQL code for JOIN queries to test table relationships. Also, I created complex queries
joining all tables together in one query and I developed three business questions, each requiring 
data retrieval from multiple tables which are as follows:

**Query 1: Joining Tables**

Retrieve all purchases with associated vendor information:

<pre>
```
SELECT purchases.purchase_id, purchases.item_num, purchases.vendor_id, purchases.purchase_date, purchases.price,
       vendors.vendor_name, vendors.vendor_address
FROM purchases
INNER JOIN vendors ON purchases.vendor_id = vendors.vendor_id;
       
```
</pre>


Retrieve all sales with associated customer information:

<pre>
```
SELECT sales.sale_id, sales.item_num, sales.cust_id, sales.date_sold, sales.quantity_sold,
       customers.cust_name
FROM sales
INNER JOIN customers ON sales.cust_id = customers.cust_id;
```
</pre>


Retrieve product details along with its unit information:

<pre>
```
SELECT products.item_num, products.description, products.quantity_on_hand, products.cost,
       units.unit_name
FROM products
INNER JOIN units ON products.unit_id = units.unit_id;
```
</pre>



Query 2: Business Question

- Find the total quantity sold for each item, including details about the items and customers.

<pre>
```
SELECT products.item_num, products.description,
       SUM(sales.quantity_sold) AS total_quantity_sold,
       customers.cust_name
FROM sales
INNER JOIN products ON sales.item_num = products.item_num
INNER JOIN customers ON sales.cust_id = customers.cust_id
GROUP BY products.item_num, products.description, customers.cust_name
LIMIT 0, 1000;

```
</pre>



- Identify the top 5 customers who made the highest total purchases, including details about the purchases.

<pre>
```
SELECT customers.cust_id, customers.cust_name,
       COUNT(purchases.purchase_id) AS total_purchases,
       SUM(purchases.price) AS total_purchase_amount
FROM purchases
INNER JOIN sales ON purchases.item_num = sales.item_num
INNER JOIN customers ON sales.cust_id = customers.cust_id
GROUP BY customers.cust_id, customers.cust_name
ORDER BY total_purchase_amount DESC
LIMIT 5;

```
</pre>


- Retrieve a list of all products with their current quantity on hand, along with the corresponding unit information and the total quantity sold in sales.

<pre>
```
SELECT products.item_num, products.description,
       products.quantity_on_hand, units.unit_name,
       COALESCE(SUM(sales.quantity_sold), 0) AS total_quantity_sold
FROM products
INNER JOIN units ON products.unit_id = units.unit_id
LEFT JOIN sales ON products.item_num = sales.item_num
GROUP BY products.item_num, products.description, products.quantity_on_hand, units.unit_name
ORDER BY products.item_num;
```
</pre>

Query 3: Proof that data can be retrieved from all of the tables in one query

<pre>
```
SELECT customers.cust_id, customers.cust_name,
       sales.sale_id, sales.date_sold,
       products.item_num, products.description, products.quantity_on_hand
FROM customers
INNER JOIN sales ON customers.cust_id = sales.cust_id
INNER JOIN products ON sales.item_num = products.item_num
LIMIT 0, 1000;
```
</pre>

