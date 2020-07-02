##### MyISAM和InnoDB的区别

1. 事务安全（MyISAM不支持事务，InnoDB支持事务）
2. 外键（MyISAM 不支持外键， INNODB支持外键）
3. 锁机制（MyISAM是表锁，InnoDB是行锁）
4. 查询和添加速度（MyISAM批量插入速度更快）
5. 支持全文索引（MyISAM支持全文索引，INNODB不支持全文索引）
6. MyISAM内存空间使用率比InnoDB低