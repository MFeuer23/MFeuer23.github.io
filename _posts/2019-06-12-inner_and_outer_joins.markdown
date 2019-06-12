---
layout: post
title:      "Inner and Outer Joins"
date:       2019-06-12 14:32:22 +0000
permalink:  inner_and_outer_joins
---


A SQL JOIN is a way to combne roys from two or more tables based on a common column between them. Relational databases allow us to store related data and then retrieve that data in ways that reflect its interconnectivity.

JOIN statements allow us to query multiple related tables and return information from more than one table. For example, if we had two tables, a Parents table and a Childrens Table, and the two tables are associated by a foreign key of `parent_id` in the Children's Table, we could craft a query with JOINS that would grab all of the children with a particular parent, and even include information about the parent in the data returned.

## **Join Types**

| Type | Description |
| -------- | -------- |
| Inner Join | Returns all rows when there is at least one match in BOTH tables |
| Left Outer Join | Returns all rows from the left table, and the matched rows from the right table |
| Right Outer Join | Returns all rows from the right table, and the matched rows from the left table |
| Full Join | Returns all rows when there is a match in ONE of the tables |

### **Inner Join**
An inner join will return all the rows from both tables we are querying where a certain condition is met as long as there is a match between the specified columns of each table.

```
SELECT column_name(s)
FROM first_table
INNER JOIN second_table
ON first_table.column_name = 
second_table.column_name
```

First, we specify the columns from each table we would like to select data from. We use `table_name.column_name` to grab data from two different columns. Then, we join our two tables together wtih `INNER JOIN`. Lastly, we tell our query how to join the two tables with `ON` - we tell the query which columns in each table function as the foreign key/primary key connection.  This query will return all of the data in the specified colmns from both tables.

We can also use the `AS` keyword to alias names of different columns to make our output easier to interpret.

With Inner Join, any data that does not meet a JOIN condition will not be returned. Our JOIN condition is the thing that our two tables are joined on. In our example, `...ON Childrens.parent_id = Parent.id;`. So any childrent that have an empty `parent_id` column or have a value in the column that does not match the `id` of an existing parent, will not be selected. Enter the Outer Join.

### **Left Outer Join**
A left outer join query will return all rows from the left, or first, table regardless of whether or not they meet the JOIN condition, and it will return the matched data from the right, or second, table. If there is data from the first table that doesn't meet the join condition (for example, a child with no parent_id), the resulting output will include `NULL`or empty values for the missing matched columns. So we would have all of the children listed, with a blank or null space if there is no associated parent.

```
SELECT column_name(s)
FROM first_table
LEFT JOIN second_table
ON first_table.column_name =
second_table.column_name
```

### **Right Outer Join**
A right outer join is the reverse of the left outer join. It will return all the data from the right, or second, table and the matched data from the left, or first table. If there is data from the second table that doesn't meet the join condition (for example, a parent who's id is not listed in the children's table- a parent with no children stored),  the resulting output will include `NULL` or empty values for the missing matched columns. So we would have all of the parents listed, with a blank or null space if there is no associated child.

```
SELECT column_name(s)
FROM first_table
RIGHT JOIN second_table
ON first_table.column_name =
second_table.column_name
```

### **Full Outer Join**
A full outer join query will combine the result of both a left and right outer join. It will return all the data from both the first and second tables regardless of matches. Our result would include parents without children and children without parents alike.

```
SELECT column_name(s)
FROM first_table
FULL OUTER JOIN second_table
ON first_table.column_name =
second_table.column_name
```


![](https://media.giphy.com/media/NZCnpj2c4qoE0/giphy.gif)

Thanks for reading!


