# 商品系统设计

#### 通用的商品表

**商品主表**

    # 商品主表(即时更新)
    # ID,商品名称,商品分类,商品简介,入库时间,最后修改时间,如果需要可能会还添加作者ID,也就是类似userid
    CREATE TABLE `prod_main` (
      `prod_id` int(11) NOT NULL AUTO_INCREMENT,
      `prod_name` varchar(50) NOT NULL COMMENT '商品名称',
      `prod_classid` int(11) NOT NULL COMMENT '商品分类',
      `prod_intr` text COMMENT '商品简介',
      `prod_addtime` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '入库时间',
      `prod_lasteddate` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '最后修改时间',
      PRIMARY KEY (`prod_id`)
    ) ENGINE=MyISAM AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

    # 延时更新/日志更新
    # 点击总量,月点击量,总销量,月销量,总评价数,月评价数
    # 这些字段可以添加到主表中,也可以拆分,(C2C要拆开)
    CREATE TABLE `prod_main` (
      `prod_id` int(11) NOT NULL AUTO_INCREMENT,
      `prod_name` varchar(50) NOT NULL COMMENT '商品名称',
      `prod_classid` int(11) NOT NULL COMMENT '商品分类',
      `prod_intr` text COMMENT '商品简介',
      `prod_addtime` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '入库时间',
      `prod_lasteddate` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '最后修改时间',
      `prod_click_all` int(11) DEFAULT '0' COMMENT '总点击量',
      `prod_click_month` int(11) DEFAULT '0' COMMENT '月点击量',
      `prod_sale_all` int(11) DEFAULT '0' COMMENT '总销量',
      `0prod_sale_month` int(11) DEFAULT '0' COMMENT '月销量',
      `prod_rate_all` int(11) DEFAULT '0' COMMENT '总评价数',
      `prod_rate_month` int(11) DEFAULT '0' COMMENT '月评价数',
      PRIMARY KEY (`prod_id`)
    ) ENGINE=MyISAM AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

    # 加单的分类表
    CREATE TABLE `prod_class` (
      `prod_classid` int(11) NOT NULL AUTO_INCREMENT COMMENT '分类ID',
      `prod_classname` varchar(50) NOT NULL COMMENT '分类名称',
      `prod_pclassid` int(11) NOT NULL DEFAULT '0' COMMENT '父分类ID',
      PRIMARY KEY (`prod_classid`)
    ) ENGINE=MyISAM AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

    # 点击量日志表
    # 商品的周点击和月点击(这里简化到月)是不能记录在商品主表中的,同时还需要有个日志表,用户没登录,只记录IP,userid=0
    CREATE TABLE `prod_clicklog` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `prod_id` int(11) NOT NULL COMMENT '商品ID',
      `user_ip` varchar(15) NOT NULL COMMENT '用户IP',
      `user_id` int(11) NOT NULL DEFAULT '0' COMMENT '用户ID',
      `click_date` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '点击时间',
      PRIMARY KEY (`id`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8;

    # 存储过程模拟
    1.从商品主表根据ID读取商品所有信息
    2.如果读取到,则记录点击量日志
    函数:
    FOUND_ROWS():select时返回最近一条sql的结果集条数
    ROW_COUNT():update delete insert 受影响的条数
    sp_load_clicklog
    BEGIN
        set @num=0;
        select * from prod_main where prod_id=_prod_id limit 1;
        set @num=FOUND_ROWS();
        if @num=1 then # 代表商品取出成功
        insert into prod_clicklog(prod_id,user_ip,user_id) values(_prod_id,_user_ip,_user_id);
        end if;
    END

    # 



