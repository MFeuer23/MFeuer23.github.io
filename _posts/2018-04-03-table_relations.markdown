---
layout: post
title:      "Table Relations"
date:       2018-04-03 14:47:24 +0000
permalink:  table_relations
---


After having gotten through my final projects in the Object Oriented Ruby section, I was excited to start learning about SQL and databases. The next step! 

SQL allows us to create tables that are related to each other through primary keys and foreign keys. The primary key column automatically increments as we add more records to our table, so every record has a unique primary key we can use to refer to it. 

Say we are making a collection of tables to represent a family tree. We might make a couple of tables: 

parents

| id | first_name |
| -------- | -------- |
| 1     | Meryl      |
| 2     | Jeff   |
| 3     | Sharon      |
| 4     | Eric      |

children

| id | first_name |
| -------- | -------- |
| 1     | Mallory      |
| 2     | Marcus   |

in this case, we can implement a "has many/belongs to" relationship. Mallory has two parents: Meryl and Jeff, and Marcus has two parents: Sharon and Eric. We can show this by adding a foreign key column to the parents table (the foreign key column goes on the "child" table, which is the parents table in this case, since children have many parents ... cooky):

parents

| id | first_name | child_id |
| -------- | -------- | -------- |
| 1     | Meryl      | 1   | 
| 2     | Jeff   | 1   |
| 3     | Sharon      |  2   |
| 4     | Eric      | 2   |

but what if we added Mallory's sibling, Harrison to the children table? Then we have a "many to many" relationship, so we would need a joins table.

children

| id | first_name |
| -------- | -------- |
| 1     | Mallory      |
| 2     | Marcus   |
| 3     | Harrison   |

familytree

| parent_id | child_id |
| -------- | -------- |
| 1     | 1      |
| 1     | 3   |
| 2    | 1   |
| 2     | 3      |
| 3     | 2   |
| 4     | 2   |

so as we can see here, our joins table, familytree, can show that Mallory and Harrison's parents are Meryl and Jeff, and Marcus' parents are Sharon and Eric. So we can use our joins table to show both "many to many" and  "has many/belongs to" relationships.

what if we added a grandparents table? or more siblings?
