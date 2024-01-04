上次修改时间: {docsify-updated}

# Java脚本

本节主要用于介绍 RestCloud 中相关 Java脚本 的内容

# 常用内容
## 常用方法链接

这里提供了 RestCloud 的 java 中常用方法的一些总结

|相关链接|内容描述|
|:-----:|:----:|
|[RestCloud文档](https://www.restcloud.cn/javadoc/index.html)|RestCloud内置对象文档|
|[IESBBaseProcessEngine](https://www.restcloud.cn/javadoc/cn/restcloud/apiflow/base/IESBBaseProcessEngine.html)|engine的类|
|[JsonUtil](https://www.restcloud.cn/javadoc/cn/restcloud/framework/core/util/JsonUtil.html)|json处理相关|
|[DocumentUtil](https://www.restcloud.cn/javadoc/cn/restcloud/framework/core/util/DocumentUtil.html)|document对象处理相关|
|[RequestUtil](https://www.restcloud.cn/javadoc/cn/restcloud/framework/core/util/RequestUtil.html)|请求的内容处理相关|
|[RdbUtil](https://www.restcloud.cn/javadoc/cn/restcloud/framework/core/util/db/rdb/RdbUtil.html)|数据库相关|
|[RestClient](https://www.restcloud.cn/javadoc/cn/restcloud/framework/client/RestClient.html)|restful处理相关|  
|[Document](https://mongodb.github.io/mongo-java-driver/4.0/apidocs/bson/org/bson/Document.html)|组装json类型的数据结构|


# 请求相关参数

参数获取的时候，更多情况下使用的是 `IESBBaseProcessEngine` 类（即 java脚本 中的 engine）,可以从[该链接](https://www.restcloud.cn/javadoc/cn/restcloud/apiflow/base/IESBBaseProcessEngine.html)中找到相应常用方法  

## 获取请求链接相关数据

用于在 java 脚本获取 http 请求链接中的相关参数

```java
public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    // 获取 query 的参数，即 ? 后边的参数
    // http://192.168.12.125:8080/myModule/myTest/hello?hello=world#mytest
    // 链接中没有该参数，则返回 空字符串
    String param1 = engine.getRequestParam("hello");
	PrintUtil.o("param1: " + param1);

    // TODO: 获取整个链接

    // TODO: 获取链接中的 hash 值

    return "1";
}
```

## 获取请求头

用于在 java 脚本中直接获取 http 请求传来的请求头  

```java
public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    // 1. 使用 engine 获取请求头的 Map
    Map<String, String> headers = engine.getRequestHeader();
    for (String key: headers.keySet()) {
        PrintUtil.o(key + ":" + headers.get(key));
    }
    // 2. 使用 engine 的方法直接获取
    PrintUtil.o(engine.getRequestHeader("user-agent"));
    // 3. 使用 RequestUtil 来获取一个 Map
    Map<String, String> headers2 = RequestUtil.getHeaders();
    for (String key: headers2.keySet()) {
        PrintUtil.o(key + ":" + headers2.get(key));
    }

    return "1";
}
```

## 获取请求体

用于在 java 脚本中直接获取 http 请求传来的数据  

```java
public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    // 第一个: 用于没有 engine 这个变量的情况
    String requestBody = AppContext.getRequestBody();
    PrintUtil.o("requestBody: "+requestBody);

    // 第二个: 用于有 engine 这个变量的情况
    // 变量所在的类为
    String requestBody1 = engine.getRequestBody();
    PrintUtil.o("requestBody1: "+requestBody1);

    // 第三个: 使用 RequestUtil 工具类
    String requestBody2 = RequestUtil.getRequestBody();
    PrintUtil.o("requestBody2: "+requestBody2);

    return "1";
}
```

## 其他获取数据方式
```java
public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    // 1. indoc 用于存储上一节点传入数据，可用 get 方法获取
    PrintUtil.o(indoc.get("hello"));
    // 2. 使用 engine 从全局变量中获取
    // 先获取 全局变量 的 ConcurrentHashMap，然后通过 get 方法获取
    Map<String, Object> globalVariables = engine.getGlobalVariable();
    PrintUtil.o(globalVariables.get("hello"));
    // 也可以直接从 engine 获取
    PrintUtil.o(engine.get("hello"));

    return "1";
}
```

# 数据传出
## 传出数据到下一节点

传入下一节点的数据可以接一个 `结果数据裁剪` 的节点，用于组装最终输出的数据。  

```java
public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    Document result = new Document();
    result.put("hello", "world");

    // 1. 将数据填入 indoc 中，供下一节点使用
    // 在测试过程中发现，如果 indoc 放到了例如 if 等代码块中，传入的数据会不起作用，
    // 建议放到代码最后的部分进行组装
    indoc.put("myResult", result);

    // 2. 将数据填入 engine 的全局变量中
    // 后边可以通过 $.global.变量名 来获取结果
    Map<String, Object> globalVariables = engine.getGlobalVariable();
    globalVariables.put("myResult2", result)
    // 也可以直接从 engine 添加
    engine.put("myResult3", result);

    return "1";
}
```

# 数据库操作
## 一般的更新和查询
```java
import java.sql.ResultSet;

public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    // 获取一个数据源，用于连接数据库操作
    // getConnection 需要填入 数据源id
    // 可以从 首页 系统设置 数据源管理 来进行查找
    Connection conn = RdbUtil.getConnection("hello"); 
    // 组装相关 增删改sql ，传入变量的地方使用 ? 作为占位符
    String insertSql = "insert into my_table(id, age) values(?, ?)";
    // 执行 sql 并传入 变量参数。
    // 变量参数为 可变参数，可传入任意多个
    int updatedRows = RdbUtil.executeUpdateSql(conn, insertSql, "hello", 2); 
    PrintUtil.o("updatedRows: "+updatedRows);

    // 组装相关 查询sql ，传入变量的地方使用 ? 作为占位符
    String querySql = "select id, age from my_table where age = ?";
    // 执行 sql 并传入 变量参数。
    // 变量参数为 可变参数，可传入任意多个
    ResultSet rs = RdbUtil.executeQuerySql(conn, querySql， 2);
    // 将查询结果转为 List<Document>
    List<Document> docs = RdbUtil.resultSet2Docs(rs);
    PrintUtil.o("docs: "+docs);

    return "1";
}
```

# 其他操作
## json字符串与Document互转
```java
public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    // 1. json字符串 转 Document
    Document doc1 = DocumentUtil.json2doc("{\"hello\":\"world\"}");
    String result1 = DocumentUtil.getString(doc1, "hello");
    PrintUtil.o("result1: "+result1);
    // 2. json数组字符串 转 List<Document>
    List<Document> doc2 = DocumentUtil.jsonArray2docs("[{\"hello\":\"world\"},{\"hello\":\"world2\"}]");
    for (Document doc : doc2) {
        String result2 = DocumentUtil.getString(doc, "hello");
        PrintUtil.o("result2: "+result2);
    }

    List<Document> docs = new ArrayList<>();
    Document doc3 = new Document();
    doc3.put("test", "result");
    Document doc4 = new Document();
    doc4.put("str", "hello world");
    doc4.put("integer", 1);
    doc4.put("boolean", true);
    doc4.put("json", doc3);
    Document doc5 = new Document();
    doc5.put("str", "hello world");
    doc5.put("integer", 1);
    doc5.put("boolean", true);
    doc5.put("json", doc3);
    docs.add(doc4);
    docs.add(doc5);
    // 3. Document 转 json字符串
    String str1 = DocumentUtil.doc2Json(doc4);
    PrintUtil.o("str1: "+str1);
    // 4. List<Document> 转 json数组字符串
    String str2 = DocumentUtil.docs2Json(docs);
    PrintUtil.o("str2: "+str2);

    return "1";
}
```

# 规则编写
## 路径
在 服务集成中心 =》 API编排 =》 进入相关编排应用=》 规则管理 =》 所有规则列表 ，新建规则即可。  

## 相关脚本编写
```java
/**
 * 在这个处理函数当中
 * engine 基本上是什么数据都可以取到和设置，例如 请求体的数据， 设置为全局变量 等等
 * indoc 为上一节点流入输入，修改后数据将会传给下一个节点
 */
@Override
public String execute(IESBBaseProcessEngine engine, Document modelNodeDoc, Document indoc,String fieldId,String params) throws Exception {
    // 获取 上一节点 传入数据
    String orderNo=indoc.getString("orderNo");
    // 获取 http 请求中获取的数据
    String userId=engine.getRequestParam("userId");
    // 获取前边节点的 断言结果
    String assertResult=engine.getAssert("T100001");
    PrintUtil.o("orderNo:"+orderNo);
    PrintUtil.o("userId:"+userId);
    PrintUtil.o("assertResult:"+assertResult);

    // 这里可以对传入的数据进行相关的操作，例如拼接上 UUID
    String uuid =  UUID.randomUUID().toString();
    orderNo += uuid;

    // 将数据放到 indoc 供后边的 规则参数 获取
    indoc.put("generatedUuid", orderNo);

    return "1";
}
```

-------------------------

上次修改时间: {docsify-updated}
