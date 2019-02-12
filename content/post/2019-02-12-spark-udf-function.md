---
title: spark udf function
author: Glidis
date: '2019-02-12'
slug: spark-udf-function
categories:
  - Spark
tags: [udf]
---

只在spark shell中写过简单的udf函数，由于没有接触过java之类的语言，掉进几个坑里：

### UDF函数定义中要加入对null值的处理

在处理spark dataset时，有些列中含有null值，如果在UDF中不做处理，最终会报错。

示例代码如下

```
def FOO(keys:String): String = {
var r_values = ""
r_values = keys match {
case "1" => "ONE"
case "2" => "TWO"
case "3" => "THREE"
case _ => keys
}
r_values
}
```
### spark SaveMode 不支持类型为NullType的Column的写入

在用spark.sql执行SQL语句时，如果有“NULL as xx”，则产生的xx列为NullType，无法save。  
解决方案： 

  - 将NULL全变为''  
  - cast(NULL as string) as xx  
  - 先生成不含xx列的df，然后df.withColumn("xx", lit(null).cast("string"))
  
