# Technical-Interview

-----------------------------------
## Exercise 2 - Product Sales

We have the following tables organized in a database as follows:

    * customers
        * id
        * firstname
        * lastname
        * date_of_birth
        * country
    * order 
        * id
        * id_customer
        * order_cost
    * order_items
        * id
        * id_order
        * id_product
        * quantity
    * products
        * id
        * name
        * price

1. Explain the purpose of the **order_items** table.

The **order_items** table connects the **order** table to the **products** table. It displays, for every order, what product was sold and its quantity. The **id** column is the Primary Key of the table, **id_order** and **id_product** are Foreign Keys that links to the **order** table and the **products** table, respectively.

2. Write a SQL query to find the average order cost per country.

If we want to take into account only the customers that placed at least 1 order, so the ones that appear in both the **customers** and the **orders** tables, the query can be written as:

```
SELECT c.country, AVG(o.order_cost) as average_order_cost
FROM customers c JOIN order o
ON c.id = o.id_customer
GROUP BY c.country;
```

We basically perform an Inner Join on the two tables, matching the **id** of the customers, group the rows by the **country** column, and calculate the average of **order_cost**. 
If we instead want to also take into account the customers that didn't place an order, we can do a Left Join instead:

```
SELECT c.country, AVG(o.order_cost) as average_order_cost
FROM customers c LEFT JOIN order o
ON c.id = o.id_customer
GROUP BY c.country;
```

3. Write a SQL query to find the name of the highest price product sold to an Italian customer.

Assuming that the Italian customers are identified by having 'Italy' in the **country** column of the **customers** table:

```

```