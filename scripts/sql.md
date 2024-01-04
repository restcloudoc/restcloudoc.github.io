上次修改时间: {docsify-updated}

# SQL脚本

本节主要用于介绍 RestCloud 中相关 SQL脚本 的内容  

# 语句写法
基本语句如下样例
```sql
-- 当使用传入 json 时，可以使用 ${变量id} 的形式，也可以使用 ${$.变量id} 的形式
-- 如果需要调用其他形式的数据，可使用例如 ${$.global.变量id} 的形式
select name from public.my_table where code = '${id}';
```

-------------------------

上次修改时间: {docsify-updated}