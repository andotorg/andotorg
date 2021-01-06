---
title: Python利用urlib2传输一个超大的集合给Java后端
date: 2018-03-01 22:50:48
tags: python, java, urlib2, json, list
---
### 我们需求中需要传输的超大集合

> 集合长度为：492*6条

```
arr = [{
    "cityName": "北京",
    "temperature": 25, 
    "humidity": 23,
    "population": 23,
    "english": "beijing"
}, {
    "cityName": "山东省",
    "temperature": 25, 
    "humidity": 23,
    "population": 23,
    "english": "shandong"
}, {
    "cityName": "上海",
    "temperature": 25, 
    "humidity": 23,
    "population": 23,
    "english": "shanghai"
}, ...]
```

### Python通过POST发送集合

> 集合传输只能依靠于JSON字符串传输，这里就需要非常的注意了

1. 集合转为JSON字符串，中文按照UTF-8编码

```
    strs = json.dumps(result, ensure_ascii=False, encoding='UTF-8')
    params = urllib.urlencode({"saveList": strs})
```

2. 发送POST请求，get请求不合适，也不能传输大文本

```
    # 定义一些文件头
    headers = {"Content-Type": "application/x-www-form-urlencoded", "Connection": "Keep-Alive",
               "Referer": 'http://127.0.0.1:8080/test'};
    # 与网站构建一个连接
    conn = httplib.HTTPConnection("127.0.0.1", 8080);
    # 开始进行数据提交，同时也可以使用get进行
    conn.request(method="POST", url="/test", body=params, headers=headers);
    # 返回处理后的数据
    response = conn.getresponse();
    # 判断是否提交成功
    if response.status == 200:
        print"发布成功!^_^!";
    else:
        print"发布失败\^0^/";
    # 关闭连接
    conn.close();strs = json.dumps(result, ensure_ascii=False, encoding='UTF-8')
    params = urllib.urlencode({"saveList": strs})
```
### 这样传输，既可以解决传输大量数据，也可以传输中文，java直接接受到字符串，也不需要转码，转成JSON字符串，就可以循环遍历存储数据库了

```
    @RequestMapping(value="/test", produces="text/html;charset=UTF-8")
	@ResponseBody
	public String getWeatherList(HttpServletRequest request){
	    String json = request.getParameter("saveList");
	    JSONArray jsonArray = JSONArray.fromObject(json);
        Object[] os = jsonArray .toArray();
        for(int i=0; i<os.length; i++) {
            JSONObject jsonObj = JSONObject.fromObject(os[i]);
            System.out.println(jsonObj.get("cityName"));
        }
	}
    
```
> 打印结果: 上海
