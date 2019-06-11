# HTTP 

### Header

---

#### 常用的Content-Type

[推荐博文](https://imququ.com/post/four-ways-to-post-data-in-http.html)

1. ##### application/x-www-form-urlencoded

   > 浏览器的原生post表单，<form>不设置entype的话将以此种方式提交数据

2. ##### multipart/form-data

   > 当使用表单上传文件的时候，将form的entype设置为multipart/form-data。才能上传文件

3. ##### application/json

   > 传入序列化后的 JSON 字符串(JSON.stringify)
   >
   > 注意:这里有个坑，php无法用$_POST读取这种格式的post数据。要用 file_get_contents("php://input");
   >
   > 获取字节流。得到json数据，再解析即可。
   >
   > PS：还需在服务端设置 header("Access-Control-Allow-Headers:Content-Type"); 不然请求时无法识别该格式的content-type。

4. ##### text/xml

它是一种使用 HTTP 作为传输协议，XML 作为编码方式的远程调用规范。典型的 XML-RPC 请求是这样的：

```xml
HTMLPOST http://www.example.com HTTP/1.1 
Content-Type: text/xml

<?xml version="1.0"?>
<methodCall>
    <methodName>examples.getStateName</methodName>
    <params>
        <param>
            <value><i4>41</i4></value>
        </param>
    </params>
</methodCall>
```







