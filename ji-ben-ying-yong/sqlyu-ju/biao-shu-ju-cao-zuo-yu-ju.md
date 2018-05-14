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

#### 插入表数据

#### 更新表数据

#### 删除表数据



