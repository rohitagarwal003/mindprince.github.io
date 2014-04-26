---
layout: post
title: "Sparse matrix multiplication using SQL"
date: 2013-06-07 21:57
comments: true
description: "How to multiply two sparse matrices using relational databases and SQL."
categories: [sql, how-to]
---

`a` and `b` are two sparse matrices. We want to perform `a * b` as shown below:
{% codeblock %}
0   1   0   0   9                1   0   0                0   0   0
0   0   3   0   0       \ /      0   0   0      -----     0  21   0
0   0   0   2   0        *       0   7   0      -----     0   0   4
0   0   0   0   0       / \      0   0   2                0   0   0
                                 0   0   0
{% endcodeblock %}

We can represent a sparse matrix in a relational database as a table `matrix_name(row_num, col_num, value)`.
Each non-zero cell in the matrix is represnted as a record (i, j, value) in the table.

Here's how to do the multiplication. First, create the tables:
{% codeblock lang:sql %}
CREATE TABLE a (
row_num INT,
col_num INT,
value INT,
PRIMARY KEY(row_num, col_num)
); 

CREATE TABLE b (
row_num INT,
col_num INT,
value INT,
PRIMARY KEY(row_num, col_num)
); 
{% endcodeblock %}

Next, insert values into the tables:
{% codeblock lang:sql %}
INSERT INTO a VALUES
(1, 2, 1),
(1, 5, 9),
(2, 3, 3),
(3, 4, 2);

INSERT INTO b VALUES
(1, 1, 1),
(3, 2, 7),
(4, 3, 2);
{% endcodeblock %}

Finally, perform the multiplication using the following query:
{% codeblock lang:sql %}
SELECT a.row_num, b.col_num, SUM(a.value*b.value)
FROM a, b
WHERE a.col_num = b.row_num
GROUP BY a.row_num, b.col_num;

2|2|21
3|3|4
{% endcodeblock %}

To see how this query works, remember the formula for cell (i,j) of the product. It is the sum of a(i,k)*b(k,j) for all k.
The `JOIN` condition `a.col_num = b.row_num` makes sure that both `a.value` and `b.value` has the same k.
The `GROUP BY` clause makes sure that we sum over all k's.
