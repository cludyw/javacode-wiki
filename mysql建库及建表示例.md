# 建库示例
```sql
CREATE DATABASE IF NOT EXISTS xxx DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci; 
```

COLLATE：校对规则，翻译为排序规则不准确，影响查询校对和排序，例如等号查询'='  
general_ci、unicode_ci：中英文没有差别，一般用general_ci即可
- general_ci：校对速度更快，但准确度更低
- unicode_ci：校对速校对度更慢，但准确度更高，法文和德文一定要使用unicode_ci

ci：case insensitive，大小写不敏感，不区分大小写  
cs：大小写敏感，区分大小写  
bin：区分大小写，每个字符串用二进制数据编译存储，例如：utf8mb4_bin 

注意：有些mysql版本不支持cs编码，可以考虑使用bin规则

##  大小写敏感查询

**查询where指定BINARY**
```sql
select * from tb_user where BINARY username ='user';
```

**建表时字段指定BINARY**
```sql
`username` VARCHAR (50) BINARY NOT NULL COMMENT '用户名'
```

**建表时字段指定校验规则COLLATE utf8_bin**
```sql
`username` varchar(50) COLLATE utf8mb4_bin NOT NULL COMMENT '用户名'
```

**建库时指定校验规则COLLATE=utf8_bin**
```sql
ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin COMMENT='用户表'
```

# 建表示例
```sql
CREATE TABLE `table` (
`id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
`deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '软删除标识',
`create_no` varchar(32) not null default '' COMMENT '创建者工号',
`create_name` varchar(32) not null default '' COMMENT '创建者姓名',
`create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
`update_no` varchar(32) not null default '' COMMENT '更新者工号',
`update_name` varchar(32) not null default '' COMMENT '更新者姓名',
`update_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
PRIMARY KEY (`id`),
KEY `idx_create_time` (`create_time`),
KEY `idx_update_time` (`update_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT = '';
```

## 命名示例
- 微服务：test_payment、test_order、test_user
- 同一个库内水平分表：xxx_001、xxx_002
- 普通索引：idx_xxx
- 唯一索引：idx_uq_xxx
- 临时表：xxx_tmp
- 备份表：xxx_bak

## 字段类型
- 枚举，如性别,状态等：TINYINT
- 金额：DECIMAL(N,M)
- 时间：DATETIME
- 日期：DATE

## ip地址存储
ip地址推荐使用int类型存储，占用4个字节，比char(15)更节省空间。

将ip转换为数字后存储
```sql
INSERT INTO user (id, ip) VALUES
(1, inet_aton('192.168.1.1'),
(2, inet_aton('172.16.11.66'),
(3, inet_aton('220.117.131.12');
```
- inet_aton：ip转换为数字，存储和查询条件使用
- inet_ntoa：数字转换为ip，查询结果显示使用
