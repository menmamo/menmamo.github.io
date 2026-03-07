---
title: "HTTP类"
author: "mona"
date: 2025-03-03T20:24:00+08:00
categories: ["Unity"]
tags: ["Unity网络开发"]
---

## HttpWebRequest类

命名空间：`System.Net`

`HttpWebRequest`是主要用于发送客户端请求的类

主要用于：发送HTTP客户端请求给服务器，可以进行消息通信、上传、下载等等操作

------

### HttpWebRequest方法

**创建WebRequest**

```c#
HttpWebRequest req = HttpWebRequest.Create(new Uri("http://192.168.50.109:8000/Http_Server/")) as HttpWebRequest;
```

**终止传输**

```c#
req.Abort();
```

**获取用于上传的流**

```c#
Stream s = req.GetRequestStream();
```

**返回HTTP服务器响应**

```c#
HttpWebResponse res = req.GetResponse() as HttpWebResponse;
```

**异步获取用于上传的流**

```c#
req.BeginGetRequestStream();
req.EndGetRequestStream();
```

**异步获取返回的HTTP服务器响应**

```
req.BeginGetResponse();
req.EndGetResponse();
```

------

### HttpWebRequest成员

**通信凭证**

**设置为NetworkCredential对象**

```c#
 req.Credentials = new NetworkCredential("", "");
```

**是否随请求发送一个身份验证标头**

一般需要进行身份验证时需要将其设置为true

```c#
req.PreAuthenticate = true;
```

**构成标头的名称/值对的集合**

```c#
req.Headers;
```

**发送信息的字节数** 

上传信息时需要先设置该内容长度

```c#
req.ContentLength = 100;
```

 **在进行POST请求时，需要对发送的内容进行内容类型的设置**

```
req.ContentType
```

**操作命令设置**

```c#
req.Method = WebRequestMethods.Http.Get;
//  Get     获取请求，一般用于获取数据
//  Post    提交请求，一般用于上传数据，同时可以获取
//  Head    获取和Get一致的内容，只是只会返回消息头，不会返回具体内容
//  Put     向指定位置上传最新内容
//  Connect 表示与代理一起使用的 HTTP CONNECT 协议方法，该代理可以动态切换到隧道
//  MkCol   请求在请求 URI（统一资源标识符）指定的位置新建集合
```

**了解该类的更多信息**

https://docs.microsoft.com/zh-cn/dotnet/api/system.net.httpwebrequest?view=net-6.0

------

## HttpWebResponse类

命名空间：`System.Net`
它主要用于获取服务器反馈信息的类
我们可以通过`HttpWebRequest`对象中的`GetResponse()`方法获取
当使用完毕时，要使用`Close`释放

------

### HttpWebResponse方法

1. `Close`:释放所有资源
2. `GetResponseStream`：返回从FTP服务器下载数据的流

------

### HttpWebResponse成员

1. `ContentLength`:接受到数据的长度
2. `ContentType`：接受数据的类型
3. `StatusCode`:HTTP服务器下发的最新状态码
4. `StatusDescription`:HTTP服务器下发的状态代码的文本
5. `BannerMessage`:登录前建立连接时HTTP服务器发送的消息
6. `ExitMessage`:HTTP会话结束时服务器发送的消息
7. `LastModified`:HTTP服务器上的文件的上次修改日期和时间

**了解该类的更多信息**

https://docs.microsoft.com/zh-cn/dotnet/api/system.net.httpwebresponse?view=net-6.0

------

## HTTP下载数据

**检测资源可用性**

```c#
try
{
    //利用Head请求类型，获取信息
    //1.创建HTTP通讯用连接对象HttpWebRequest对象
    HttpWebRequest req = HttpWebRequest.Create(new Uri("http://192.168.50.49:8000/Http_Server/实战就业路线.jpg")) as HttpWebRequest;
    //2.设置请求类型 或 其它相关参数
    req.Method = WebRequestMethods.Http.Head;
    req.Timeout = 2000;
    //3.发送请求，获取响应结果HttpWebResponse对象
    HttpWebResponse res = req.GetResponse() as HttpWebResponse;

    if (res.StatusCode == HttpStatusCode.OK)
    {
        print("文件存在且可用");
        print(res.ContentLength);
        print(res.ContentType);

        res.Close();
    }
    else
        print("文件不能用" + res.StatusCode);
}
catch (WebException w)
{
    print("获取出错" + w.Message + w.Status);
}
```

**下载资源**

```c#
//利用Get请求类型，下载资源
try
{
    //1.创建HTTP通讯用连接对象HttpWebRequest对象
    HttpWebRequest req = HttpWebRequest.Create(new Uri("http://192.168.50.49:8000/Http_Server/实战就业路线.jpg")) as HttpWebRequest;
    //2.设置请求类型 或 其它相关参数
    req.Method = WebRequestMethods.Http.Get;
    req.Timeout = 3000;
    //3.发送请求，获取响应结果HttpWebResponse对象
    HttpWebResponse res = req.GetResponse() as HttpWebResponse;
    //4.获取响应数据流，写入本地路径
    if (res.StatusCode == HttpStatusCode.OK)
    {
        print(Application.persistentDataPath);
        using (FileStream fileStream = File.Create(Application.persistentDataPath + "/httpDownLoad.jpg"))
        {
            Stream downLoadStream = res.GetResponseStream();
            byte[] bytes = new byte[2048];
            //读取数据
            int contentLength = downLoadStream.Read(bytes, 0, bytes.Length);
            //一点一点的写入本地
            while (contentLength != 0)
            {
                fileStream.Write(bytes, 0, contentLength);
                contentLength = downLoadStream.Read(bytes, 0, bytes.Length);
            }
            fileStream.Close();
            downLoadStream.Close();
            res.Close();
        }
        print("下载成功");
    }
    else
        print("下载失败" + res.StatusCode);
}
catch (WebException w)
{
    print("下载出错" + w.Status + w.Message);
}
```

**Get请求类型携带额外信息**

我们在进行HTTP通信时，可以在地址后面加一些额外参数传递给服务端

一般在和短连接游戏服务器通讯时，需要携带额外信息

举例：

http://www.aspxfans.com:8080/news/child/index.asp?boardID=5&ID=24618&page=1

这个链接可以分成几部分

**协议部分**：取决于服务器端使用的哪种协议

- http普通的http超文本传输协议
- https加密的超文本传输协议

**域名部分：**

www.aspxfans.com

也可以填写服务器的公网IP地址

**端口部分：**

8080

可以不写，如果不写默认为80

**虚拟目录部分：**

news/child/

域名后的/开始，到最后一个/之前的部分

**文件名部分：**

index.asp

**参数部分：**

boardID=5&ID=24618&page=1

？之后的部分就是参数部分，多个参数一&分隔开

-----

### 实现HTTPMgr

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

public class HttpMgr
{
    private static HttpMgr instance = new HttpMgr();

    public static HttpMgr Instance => instance;


    private string HTTP_PATH = "http://192.168.50.49:8000/Http_Server/";

    /// <summary>
    /// 下载指定文件到本地指定路径中
    /// </summary>
    /// <param name="fileName">远程文件名</param>
    /// <param name="loacFilePath">本地路径</param>
    /// <param name="action">下载结束后的回调函数</param>
    public async void DownLoadFile(string fileName, string loacFilePath, UnityAction<HttpStatusCode> action)
    {
        HttpStatusCode result = HttpStatusCode.OK;
        await Task.Run(() =>
        {
            try
            {
                //判断文件是否存在 Head 
                //1.创建HTTP连接对象
                HttpWebRequest req = HttpWebRequest.Create(HTTP_PATH + fileName) as HttpWebRequest;
                //2.设置请求类型 和 其它相关参数
                req.Method = WebRequestMethods.Http.Head;
                req.Timeout = 2000;
                //3.发送请求
                HttpWebResponse res = req.GetResponse() as HttpWebResponse;

                //存在才下载
                if(res.StatusCode == HttpStatusCode.OK)
                {
                    res.Close();
                    //下载
                    //1.创建HTTP连接对象
                    req = HttpWebRequest.Create(HTTP_PATH + fileName) as HttpWebRequest;
                    //2.设置请求类型 和 其它相关参数
                    req.Method = WebRequestMethods.Http.Get;
                    req.Timeout = 2000;
                    //3.发送请求
                    res = req.GetResponse() as HttpWebResponse;
                    //4.存储数据到本地
                    if(res.StatusCode == HttpStatusCode.OK)
                    {
                        //存储数据
                        using (FileStream fileStream = File.Create(loacFilePath))
                        {
                            Stream stream = res.GetResponseStream();
                            byte[] bytes = new byte[4096];
                            int contentLength = stream.Read(bytes, 0, bytes.Length);

                            while (contentLength != 0)
                            {
                                fileStream.Write(bytes, 0, contentLength);
                                contentLength = stream.Read(bytes, 0, bytes.Length);
                            }

                            fileStream.Close();
                            stream.Close();
                        }
                        result = HttpStatusCode.OK;
                    }
                    else
                    {
                        result = res.StatusCode;
                    }
                }
                else
                {
                    result = res.StatusCode;
                }

                res.Close();
            }
            catch (WebException w)
            {
                result = HttpStatusCode.InternalServerError;
                Debug.Log("下载出错" + w.Message + w.Status);
            }
        });

        action?.Invoke(result);
    }
}

```

