# SQL中关键字执行顺序

在SQL语句中每个关键字都会按照顺序往下执行 , 而每一步操作 , 会生成一个虚拟表 , 最后产生的虚拟表会作为执行的最终结果返回 . 

下面的是常用的关键字的执行顺序 : 

```
(1)FROM <left_table>
(2)ON <join_condition>
(3)<join_type> JOIN <right_table>
(4)WHERE <where_condition>
(5)GROUP BY<group_by_list>
(6)WITH{CUBE|ROLLUP}
(7)HAVING<having_condition>
(8)SELECT (9)DISTINCT<select_list>
(10)ORDER BY<order_by_list>
(11)LIMIT<limit_number>
```

* **FROM** : 对FROM左边的表和右边的表计算笛卡尔积 , 产生虚表VT1;
* **ON** : 对虚拟表VT1进行ON筛选 , 只有那些符合&lt;join\_condition&gt;条件的行才会被记录在虚拟表VT2中;
* **JOIN** : 如果是OUT JOIN , 那么将保留表中\(如左表或者右表\)未匹配的行作为外部行添加到虚拟表VT2中 , 从而产生虚拟表VT3;
* **WHERE** : 对虚拟表VT3进行WHERE条件过滤 , 只有符合&lt;where\_condition&gt;的记录才会被放入到虚拟表VT4;
* **GROUP BY** : 根据GROUP BY子句中的列 , 对虚拟表VT4进行分组操作 , 产生虚拟表VT5;
* **CUBE\|ROLLUP** : 对虚拟表VT5进行CUBE或者ROLLUP操作 , 产生虚拟表VT6;
* **HAVING** : 对虚拟表VT6进行HAVING条件过滤 , 只有符合&lt;having\_condition&gt;的记录才会被插入到虚拟表VT7中;
* **SELECT** : 执行SELECT操作 , 选择指定的列 , 插入到虚拟表VT8中;
* **DISTINCT** : 对虚拟表VT8中的记录进行去重 , 产生虚拟表VT9;
* **ORDER BY** : 将虚拟表VT9中的记录按照&lt;order\_by\_list&gt;进行排序操作 , 产生虚拟表VT10;
* **LIMIT** : 取出指定行的记录 , 产生虚拟表VT11 , 并将结果返回 . 



