上次修改时间: {docsify-updated}

# JS脚本

本节主要用于介绍 RestCloud 中相关 Javascript脚本 的内容  
由于 RestCloud 中的 Javascript 相关的函数都是重写过的，所以在 Java 中可以使用的相关函数功能，在这里也是可以进行使用的。  

# 请求相关参数

以下相关操作与在 [java脚本](/scripts/java#请求相关参数) 中的操作是相同的

## 获取请求链接相关数据

用于在 js 脚本获取 http 请求链接中的相关参数

```javascript
function execute(engine, modelNodeDoc, indoc,params){
    // 获取 query 的参数，即 ? 后边的参数
    var param1 = engine.getRequestParam("hello");
    PrintUtil.o("param1: "+param1);
	return "1";
}
```

## 获取请求头

用于在 js 脚本中直接获取 http 请求传来的请求头  

```javascript
function execute(engine, modelNodeDoc, indoc,params){
    // 1. 使用 engine 获取请求头的 Map
    var headers = engine.getRequestHeader();
    for (var key in headers) {
        PrintUtil.o(key + ":" + headers[key]);
    }
    // 2. 使用 engine 的方法直接获取
    PrintUtil.o(engine.getRequestHeader("user-agent"));
    // 3. 使用 RequestUtil 来获取一个 Map
    var headers2 = RequestUtil.getHeaders();
    for (var key in headers2) {
        PrintUtil.o(key + ":" + headers2[key]);
    }

	return "1";
}
```

## 获取请求体

用于在 js 脚本中直接获取 http 请求传来的数据  

```javascript
function execute(engine, modelNodeDoc, indoc,params){
    // 1. 使用 engine 获取请求体
    var requestBody1 = engine.getRequestBody();
    PrintUtil.o("requestBody1: " + requestBody1);
    // 2. 使用 RequestUtil 工具类获取请求体
    var requestBody2 = RequestUtil.getRequestBody();
    PrintUtil.o("requestBody2: "+requestBody2);

	return "1";
}
```

## 其他获取数据方式
```javascript
function execute(engine, modelNodeDoc, indoc,params){
    // 1. indoc 用于存储上一节点传入数据，可用 get 方法获取
    PrintUtil.o(indoc.get("hello"));
    // 2. 使用 engine 从全局变量中获取
    var globalVariables = engine.getGlobalVariable();
    PrintUtil.o(globalVariables.get("hello"));
    // 也可以直接从 engine 获取
    PrintUtil.o(engine.get("hello"));

	return "1";
}
```

# 数据传出
## 传出数据到下一节点

传入下一节点的数据可以接一个 `结果数据裁剪` 的节点，用于组装最终输出的数据。  

```javascript
function execute(engine, modelNodeDoc, indoc,params){
    var result = new Document();
    result.put("hello", "world");

    // 1. 将数据填入 indoc 中，供下一节点使用
    // 在测试过程中发现，如果 indoc 放到了例如 if 等代码块中，传入的数据会不起作用，
    // 建议放到代码最后的部分进行组装
    indoc.put("myResult", result);

    // 2. 将数据填入 engine 的全局变量中
    // 后边可以通过 $.global.变量名 来获取结果
    var globalVariables = engine.getGlobalVariable();
    globalVariables.put("myResult2", result)
    // 也可以直接从 engine 添加
    engine.put("myResult3", result);

	return "1";
}
```

# 数据库操作
## 一般的更新和查询
```javascript
function execute(engine, modelNodeDoc, indoc,params){
    // 获取一个数据源，用于连接数据库操作
    // getConnection 需要填入 数据源id
    // 可以从 首页 系统设置 数据源管理 来进行查找
    var conn = RdbUtil.getConnection("hello"); 
    // 组装相关 增删改sql ，传入变量的地方使用 ? 作为占位符
    var insertSql = "insert into my_table(id, age) values(?, ?)";
    // 执行 sql 并传入 变量参数。
    // 变量参数为 可变参数，可传入任意多个
    var updatedRows = RdbUtil.executeUpdateSql(conn, insertSql, "hello", 2); 
    PrintUtil.o("updatedRows: "+updatedRows);

    // 组装相关 查询sql ，传入变量的地方使用 ? 作为占位符
    var querySql = "select id, age from my_table where age = ?";
    // 执行 sql 并传入 变量参数。
    // 变量参数为 可变参数，可传入任意多个
    var rs = RdbUtil.executeQuerySql(conn, querySql, 2);
    // 将查询结果转为 Document数组
    var docs = RdbUtil.resultSet2Docs(rs);
    PrintUtil.o("docs: "+docs);

	return "1";
}
```

# 其他操作
## json字符串与Document互转
```javascript
function execute(engine, modelNodeDoc, indoc,params){
    // 1. json字符串 转 Document
    var doc1 = JsonUtil.json2doc("{\"hello\":\"world\"}");
    var result1 = DocumentUtil.getString(doc1, "hello");
    PrintUtil.o("result1: "+result1);
    // 2. json数组字符串 转 Document数组
    var doc2 = JsonUtil.jsonArray2docs("[{\"hello\":\"world\"},{\"hello\":\"world2\"}]");
    doc2.forEach(function(doc) {
        PrintUtil.o("result2: "+doc["hello"]);
    });

    var docs = [];
    var doc3 = new Document();
    doc3.put("test", "result");
    var doc4 = new Document();
    doc4.put("str", "hello world");
    doc4.put("integer", 1);
    doc4.put("boolean", true);
    doc4.put("json", doc3);
    var doc5 = new Document();
    doc5.put("str", "hello world");
    doc5.put("integer", 1);
    doc5.put("boolean", true);
    doc5.put("json", doc3);
    docs.push(doc4);
    docs.push(doc5);
    // 3. Document 转 json字符串
    var str1 = JsonUtil.doc2Json(doc4);
    PrintUtil.o("str1: "+str1);
    // 4. Document数组 转 json数组字符串
    var str2 = JsonUtil.docs2Json(docs);
    PrintUtil.o("str2: "+str2);

	return "1";
}
```

-------------------------

# 裁剪规则

可以用于 [结果数据裁剪组件](/modules/unit#结果数据裁剪组件) 中的 `裁剪规则` 。  

```javascript
// 使用 engine 中的 getResponseMap() 方法获取 map ，来为最后的 API响应 输出结果
function converter(engine,nodeDoc,nodeId,indoc){
    // 获取最后输出结果的 map
    var map = engine.getResponseMap();
    // 常用方法1. 清除已有的某个字段的数据
    map.remove("myTest");
    // 常用方法2. 清除所有数据
    map.clear();
    // 常用方法3: 添加新的字段数据
    map.put("myTest2", {
        code: 0,
        message: "hello world",
        data: {
            name: "hello",
            age: 12
        }
    });
    map.put("myTest3", "name");
}
```

-------------------------

上次修改时间: {docsify-updated}
