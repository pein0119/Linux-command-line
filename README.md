# Linux 常用命令行操作

标签（空格分隔）： 工作

---
## 快速登陆服务器
两个脚本`myssh`,`relay`
``` bash
myssh work
```

## 学会使用手册

### man

``` bash
man man
```

### info

``` bash
info grep
```

### -h 或 --help

``` bash
yum -h
yum --help
```

## 字符串搜索
### grep
``` bash
cd /home/work/vdoota
grep 'xiaocongzhang' -nr ./ --color
```
说明：`-n` 显示行号，`-r` 递归搜索，`--color` 彩色输出
跳过`.svn`目录:
``` bash
grep 'xiaocongzhang' -nr ./ --color --exclude-dir='.svn'
```
### ag
grep 增强版，会跳过`.svn`等目录
```bash
ag 'xiaocongzhang'
```

## 文件查找

### find

``` bash
find . -iname "order*" -type f
```

## 系统管理

### 1. reboot restart
重启系统

### 2. halt shutdown
关机

### 3. netstat
查看本机开放的端口(仅针对已当前用户身份启动的进程)
``` bash
netstat -lntup
```

### 4. lsof
查看监听特定端口的进程(该进程必须以当前用户身份启动)
``` bash
lsof -i:80
```

### 5. top
查看系统运行信息

### 6. ps kill
查看某一进程是否已经启动
``` bash
ps aux | grep nginx
```
杀死进程
``` bash
kill $pid
```

### 7. yum apt-get

包管理工具

* 查看使用说明
``` bash
yum -h
```
* 查找软件包
``` bash
yum search man
```
* 安装软件包
``` bash
yum install man.x86_64
```
* 更新软件包
``` bash
yum update man
```
*  卸载软件包
``` bash
yum remove man
```

## 文本编辑
强大的编辑器
### vim
[无插件Vim编程技巧][1]
[简明 Vim 练级攻略][2]

## 查看大文本
不要用编辑器直接打开
### 1. head
从前数
``` bash
head -n10
```
### 2. tail
从尾数
``` bash
tail -n10
```
查看更新
``` bash
tail -f a.log
```

### more less
建议使用less,less的功能更强大
支持翻页、查找
```
less a.log
more a.log
```

### 2. 


## 文本处理

### 1. sort
排序
``` bash
sort -nr -k1
```
### 2. uniq
去重，需要配合sort使用，因为uniq使用去除相邻的重复行
统计每条数据出现了多少次
``` bash
sort -nr | uniq -c
```
交、并、差
``` bash
cat a b | sort | uniq > c   # c is a union b 并集
cat a b | sort | uniq -d > c   # c is a intersect b 交集
cat a b b | sort | uniq -u > c   # c is set difference a - b 差集
```
### sed 
流编辑器,擅长处理文本的每一行
[sed 简明教程][3]

* 文本替换
a.txt
```
Line 1 Line
Line 1
Line 2
Line 3
Line 4
```
只替换每行的第一个匹配
``` bash
sed 's/Line/line/' a.txt
```
替换每行的所有匹配
``` bash
sed 's/Line/lineg/g' a.txt
```

* 输出指定范围的文本
指定行号：
``` bash
sed -n 1,3p a.txt
```
指定模式：
统计某个时段请求数量
``` bash
st='26\/Oct\/2015:12'
ed='26\/Oct\/2015:18'
sed -n "/$st/,/$ed/p" risk.access.2015102612.log | wc -l
```

### AWK
一种简单的编程语言，擅长处理文本的每一列
文档：[AWK 简明教程][4]
[pdf][5]

* 基本组成
``` awk
BEGIN {初始化}
{作用于每一行的命令}
END {收尾工作}
```

* 常量
```
$0	当前记录（这个变量中存放着整个行的内容）
$1~$n	当前记录的第n个字段，字段间由FS分隔
FS	输入字段分隔符 默认是空格或Tab
NF	当前记录中的字段个数，就是有多少列
NR	已经读出的记录数，就是行号，从1开始，如果有多个文件话，这个值也是不断累加中。
FNR	当前记录数，与NR不同的是，这个值会是各个文件自己的行号
RS	输入的记录分隔符， 默认为换行符
OFS	输出字段分隔符， 默认也是空格
ORS	输出的记录分隔符，默认为换行符
FILENAME	当前输入文件的名字
```

* 示例
1. 当前文件夹下的所有文件共占多少字节

   ``` bash
   ls -l | awk 'BEGIN {sum=0} {sum=sum+$5} END
{print sum}'
   ```
2. 当前文件夹下的文件夹下的文件平均占多少字节

   ``` bash
    ls -l | awk 'BEGIN {sum=0} {sum=sum+$5} END
{print sum/NR}'
   ```

## 复杂任务示例
###1. 统计每秒的请求量

``` bash
awk '{print $4}' paydz.access.2015102420.log | uniq -c | sort -k1 -nr | head -n20
```

###2. 统计每个接口的请求时间落在不同范围的数量：

## 推荐网站

[http://commandlinefu.cn/][6]
``` awk
awk '{split($6, request, "?"); api_stat[request[1], "count"] += 1; time=$(NF-2); gsub(/[\[\]]/, "", time); if (time <= 0.5) {api_stat[request[1], "count1"] += 1;} if (time > 0.5 && time <= 1.0) {api_stat[request[1], "count2"] += 1;} if (time > 1.0 && time <= 1.5) {api_stat[request[1], "count3"] += 1;} if (time > 1.5 && time <= 2.0) {api_stat[request[1], "count4"] += 1;} if (time > 2.0 && time <= 2.5) {api_stat[request[1], "count5"] += 1;} if (time > 2.5 && time <= 3.0) {api_stat[request[1], "count6"] += 1;} if (time > 3.0) {api_stat[request[1], "count7"] += 1;}} END {for (api in api_stat) { split(api, key, SUBSEP); print key[1], key[2], api_stat[api];} }'
```

###3. 统计你最常用的10个命令

``` bash
history | awk '{print $2}' | sort | uniq -c | sort -k1 -nr | head
```


  [1]: http://coolshell.cn/articles/11312.html
  [2]: http://coolshell.cn/articles/5426.html
  [3]: http://coolshell.cn/articles/9104.html
  [4]: http://coolshell.cn/articles/9070.html
  [5]: http://www-users.york.ac.uk/~mijp1/teaching/2nd_year_Comp_Lab/guides/grep_awk_sed.pdf
  [6]: http://commandlinefu.cn/