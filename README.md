# Technical-Interview

## Exercise 1 - Bank Customer Churn Prediction



-----------------------------------
## Exercise 2 - Product Sales

A Database is organized as follows:

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

The **order_items** table connects the **order** table to the **products** table. It displays, for every order, what product was sold and its quantity. The **id** column is the Primary Key of the table, **id_order** and **id_product** are Foreign Keys that link to the **order** table and the **products** table, respectively.

2. Write a SQL query to find the average order cost per country.

If we want to take into account only the customers that placed at least 1 order, so the ones that appear in both the **customers** and the **orders** tables, the query can be written as:

```
SELECT c.country, AVG(o.order_cost) as average_order_cost
FROM customers c JOIN order o
ON c.id = o.id_customer
GROUP BY c.country;
```

We perform an Inner Join on the two tables, matching the **id** of the customers, group the rows by the **country** column, and calculate the average of **order_cost**. 
If we instead want to also take into account the customers that didn't place an order, we can do a Left Join instead:

```
SELECT c.country, AVG(o.order_cost) as average_order_cost
FROM customers c LEFT JOIN order o
ON c.id = o.id_customer
GROUP BY c.country;
```

3. Write a SQL query to find the name of the highest price product sold to an Italian customer.

Assuming that the Italian customers are identified by having 'Italy' in the **country** column of the **customers** table, we can write the query this way:

```
SELECT p.name as product_name, p.price as product_price
FROM products p JOIN order_items oi 
ON p.id = oi.id_product JOIN order o 
ON oi.id_order = o.id JOIN customers c
ON o.id_customer = c.id
WHERE c.country = 'Italy'
ORDER BY p.price DESC
LIMIT 1;
```

This query performs a join on all the tables of the database and finds all orders placed by an Italian customer. It then sorts the result in descending order by the price of each product sold and limits the result to the first row, the highest price product. It finally displays the product name and its price.


4. How could you optimize the orders table assuming you have to manage a lot of queries?

Since we probably want to perform a JOIN operation between the **order** and **customers** tables, we might want to index the **id_customer** column to make this process faster. We can also index the **order_cost** column since in many queries we may need to sort or filter by the order cost. However we have to notice that this method slows down the INSERT operation, so we need to evaluate the trade-off in performance and see if this solution is affordable.
It might be a good idea to also add the customers' information we are mostly interested in to the table, to avoid frequent JOIN between the **order** and **customers** table. 

5. What would you change if the amount of data is too big to run these queries?

One thing we can do to improve performance is to partition the data in various databases or tables following certain criteria; for example, we can partition the **customers** data based on the country of origin, or partition the **order** data based on the DateTime of each order. Another thing we can do is to optimize the queries so that we minimize the number of rows scanned, or caching the most frequent queries so that we don't need to run them each time. It is also possible to apply some data compression techniques to reduce the size of the database, but this will also slow down INSERT operations.

---------------------------

## Exercise 3 - Machine Translation

Suppose you work for a company that offers Machine Translation to its customers.
You have access to a large amount of translation data (i.e., parallel files containing a sentence in a source language and the corresponding translation in the target language). Some of this data comes from public datasets, others have been produced by professional translators working for your own company.

1. How would you design a data pipeline for a Machine Translation system?

The first thing to do is to clean the data from possible duplicates, empty or irrelevant files. 
After this we need to preprocess the source data (not the target, since we'll use it as a reference and evaluate the machine translation), by lowercasing, tokenizing, and normalizing the text, while also removing unwanted characters; we might also need to vectorize the tokens, depending on the architecture of our model.
Then we'll feed this cleaned and preprocessed source data into a Machine Learning Model capable of generating text (such as a Transformer Neural Network) and compare the generated data to the target data to evaluate the loss and optimize the model.

Some of the main challenges of this process are:
* Making sure we are dealing with **high-quality data**, with good translations and a perfect alignment between source and target;
* The data should space in various domains (scientific papers, movie reviews, blogs, etc.) to enable the model to translate all sorts of text;
* The **high volume data** needed for machine translation can be difficult to deal with, especially with complex and deep models;
* The **complexity** of some languages might be difficult to face for a Machine Learning Model; there might be a significant difference in the syntax of a sentence between two languages and is possible to have words with ambiguous meanings.


2. What would you do to collect new and good quality data from the web?

First of all, I'd search for good public datasets, whose quality has been certified by a multitude of professional translators. I'd also scrape from multilingual websites that contain text in both the source and target language. If I have access to professional translators, I can also scrape text of only one language (the source one for example), and use the translations as the target text data; this might be expensive but can also bring higher quality data. If I also have access to a translation model that produces high-quality translations
I can let the model do the translation and post-edit them by professional translators.

3. Suppose that low quality translations created by the system are post-edited by professional translators. How would you use this process to monitor the quality of the Machine Translation system?

I'd keep track of the most common errors or inaccuracies the machine translation system keeps incurring into, and use these informations to improve performances. For example by updating some parameters of the model or by choosing training data that can better teach the model to solve those errors.






