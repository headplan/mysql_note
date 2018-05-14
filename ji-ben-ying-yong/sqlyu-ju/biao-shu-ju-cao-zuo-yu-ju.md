# 表数据操作语句

#### 查询表数据

**基本查询语句**

* 根据过滤条件查询表中的单列或者多列或者全部列的信息**SELECT FROM WEHERE**：`SELECT cust_id,cust_name FROM customers WHERE cust_id=10086;`其中过滤条件操作符有：`=，<>,!=,<,<=,>,>=,BETWEEN AND,IS NULL;`
* 为查询出的某一列信息去重**DISTINCT**：`SELECT DISTINCT cust_name FROM customers;`
* 限制单列查询结果的行数：`SELECT cust_name FROM customers LIMIT 5;`LIMIT后跟一个数值 , 表示从第0行开始取 , 共取5行数据;如果LIMIT 5 , 5表示从第5行\(数据库中实际第6行记录\)开始取 , 共取5行数据 . 注意 : 数据是从第0行开始计数的;
* ORDER BY子句取一个或者多个列 , 据此对输出进行排序 : `SELECT cust_id,cust_name FROM customers ORDER BY cust_id DESC, cust_name;`
* IN操作符用来指定条件范围 , 范围中的每个条件都可以进行匹配 : `SELECT cust_id, cust_name FROM customers WHERE cust_id IN (1000,2000)` . 另外 , NOT操作符可以和IN操作符配合使用 , 用于表示检索出不符合条件的所有数据;
* LIKE操作符用来表明模糊查询 , 与之配合使用的通配符有\*\*%\*\* , `%`**表示任何字符出现任何次数 .** `_`**表示只能匹配一个字符** : `SELECT cust_id,cust_name FROM customers WHERE cust_name LIKE '%happy%';`
* 使用分组查询并可以满足一定的分组过滤条件**GROUP BY HAVING** . 如检索总计订单金额大于等于50的订单号和订单总金额 , 并按总金额进行排序 : `SELECT order_num,SUM(quantity*item_price) AS order_total FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price)>=50 ORDER BY order_total`
* **WHERE和HAVING的比较** . WHERE是行级过滤 , 而HAVING是组级过滤 . 被WHERE过滤掉的数据不会出现在分组中 . WHERE中通配符以及多个WHERE子句的连接同样适用于HAVING子句;
* **GROUP BY**的使用注意事项 : 
  * GROUP BY子句中可以嵌套分组\(即通过多个列进行分组GROUP BY cust\_id, cust\_name\) , 但是进行数据汇总时 , 是在最后规定的分组上进行;
  * GROUP BY子句中列出的每个列都必须是检索列或者是有效的表达式 . 
  * 如果有NULL值 , 将值NULL作为一个分组进行返回 , 如果有多行NULL值 , 它们将分为一组 .
* 嵌套其他查询中的查询 , 称之为子查询 . 执行过程由里向外 , 里层查询结果作为外层查询的条件 : `SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num FROM orderitems WHERE prod_id = 'happy')`.当然 , 多表的查询可以使用联结查询 . 

**联结查询**

* 内联结用又称之为内部联结 , 是基于两个表之间的的相等测试 . 如果不加过滤条件 , 会造成“**笛卡尔积**” . `SELECT vend_name,prod_name,prod_price FROM vendors INNER JOIN products ON vendors.vend_id=products.vend_id;`同样可以使用WHERE进行多表联结查询 , 但是更推荐使用INNER JOIN等联结方式 ; 
* 外部联结包括**左外联结LEFT JOIN**和**右外联结RIGHT JOIN**和**全连接FULL JOIN** . 例如查询每个客户的订单数 : `SELECT customers.cust_id,orders.orders_num FROM customers LEFT JOIN orders ON orders.cust_id =customers.cust_id;`LEFT JOIN 会全部返回左表数据 , RIGHT JOIN会全部返回右表数据 , FULL JOIN会将左右两个表的数据全部返回;
* 联结查询与聚集函数一起使用 . 如查询每个客户的订单数 : `SELECT customers.cust_name,customers.cust_id,COUNT(orders.order_num) AS num_ord FROM customers INNER JOIN orders ON customers.cust_id=orders.cust_id GROUP BY customers.cust_id`

**组合查询**

* 多个查询\(SELECT\)可以使用UNION将多个查询结果进行合并成一个结果集返回 , UNION必须包含两个及两个以上的SELECT查询 , 并且每个传必须包含相同的列、表达式或聚集函数 , 数据类型不必完全相同 , MySQL会进行隐式的类型转换 . `SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price>5 UINON SELECT vend_id,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002);`
* **UNION**返回的是去重后的结果 , 如果不需要去重则可以使用**UNION ALL**;
* 可以多组合查询使用ORDER BY进行排序 , 但是是针对的最终的结果集进行排序 , 而不是其中单个SELECT查询进行排序 , 因此对于组合查询来说**ORDER BY子句只有一个** . `SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price>5 UINON SELECT vend_id,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002) ORDER BY vend_id`

**使用函数对数据进行处理**

* 拼接列名 : `SELECT Concat (vendName,'(',vendCountry,')') FROM vendors ORDER BY vendName;`
* 执行算术表达式计算 : `SELECT prodId, quantity,price, quantity*price AS expandedPrice FROM orderItems;`
* 文本处理函数如**Upper\(\),LTrim\(\),RTrim\(\)**等函数 . 比如使用Upper函数将文本转换成大写 : `SELECT vendName, Upper(vendName) FROM vendors ORDER BY vendName;`
* 时间和日期处理函数 , 如**Date\(\),Day\(\)**等 . `SELECT custId, orderNum FROM orders WHERE Date(orderDate)='2015-09-01';`
* 数值处理函数 , 如**Abs\(\),Cos\(\)**等;
* 常用的聚集函数 . 如**AVG\(\),COUNT\(\),MAX\(\),MIN\(\)**以及**SUM\(\)** . `SELECT COUNT(*) AS numbers, MIN(prod_price) AS price_min, MAX(prod_price) AS price_max,AVG(prod_price) AS price_avg FROM products;`

#### 插入表数据

#### 更新表数据

#### 删除表数据



