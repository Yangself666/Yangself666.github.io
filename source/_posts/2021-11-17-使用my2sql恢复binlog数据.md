---
title: 使用my2sql恢复binlog数据
author: Yangself
date: 2021-11-17 15:42:00 +0800
categories: [解决方案, 数据库]
tags: [数据库]
---

[Github项目路径](https://github.com/liuhr/my2sql)

[我的Gitee保存的项目路径](https://gitee.com/Yangself/my2sql)

```shell
# 通过本地文件读取binlog生成sql文件
./my2sql -mode file -local-binlog-file ./mysql-bin.000030 -start-file mysql-bin.000030 -work-type rollback -user root -password 123456 -port 3306 -host 127.0.0.1 -databases hwzxoa -tables briefing -output-dir ./sql2/

# 使用运行中的数据库读取binlog文件
./my2sql -start-file mysql-bin.000030 -work-type rollback -user root -password 123456 -port 3306 -host 127.0.0.1 -databases hwzxoa -tables briefing -output-dir ./sql2/
```

