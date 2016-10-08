---
title: Shell文件读取相关脚本
date: 2016-9-13 15:23:45
categories: 学习笔记
tags:
- Shell脚本
description: Shell读取文件相关脚本
---
<!-- more -->

## 将表中的字段信息存入文本中

### 说明-表名存储在ta.txt中,字段文本放在tabname目录下
``` 
#!/bin/bash
cat ta.txt | while read line 
do
arr=($line) 
hive -e "desc ${arr[0]}" > tabname/${arr[0]}.txt;
done
```

## 将表的partition部分存入pat文本

### 说明-表名存储在alltable.txt中 pat文本放在alltable文件夹下
``` 
#!/bin/bash
cat alltable.txt | while read linedo
hive -S -e "show partitions $line" > alltable/$line.pat;
done 
``` 

## 提取字段数大于30的表

###说明-表名在field.txt 提取到的表名存储在 ta.txt
``` 
#!/bin/bash
while read line
do
if [ $(hive -e "desc $line" |ec -l) -gt 30 ];
then
echo $line >> ./ta.txt
fi
done <field.txt
``` 
## 在目录下查询包含某字段的文件

### 说明-查询到的文件名写入tabname.txt 放在tabname目录下
``` 
#!/bin/bash
find .|xargs grep -ri "phone" -l > tabname/tabname.txt
``` 
