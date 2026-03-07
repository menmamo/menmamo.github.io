---
title: "TCP异步通信"
author: "mona"
date: 2024-12-09T11:15:00+08:00
categories: ["Unity"]
tags: ["Unity网络开发"]
---

## 异步方法和同步方法的区别

同步方法：

方法中逻辑执行完毕后，再继续执行后面的方法

异步方法：

方法中逻辑可能还没有执行完毕，就继续执行后面的内容

注意：Unity中的协同程序中的某些异步方法，有的使用的是多线程有的使用的是迭代器分步执行

------

##  举例说明异步方法原理

我们以一个异步倒计时方法举例

1.线程回调

```c#
CountDownAsync(5, ()=> 
               {
                    print("倒计时结束");
               });
print("异步执行后的逻辑");
```

2.async和await 会等待线程执行完毕 继续执行后面的逻辑

```c#
//相对第一种方式 可以让函数分步执行
CountDownAsync(5);
print("异步执行后的逻辑2");
```

------

## TCP通信中的异步方法(begin,end)

**服务器监听**

```c#
Socket socketTcp = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.TCP);
socketTcp.BeginAccept(AcceptCallBack,socketTcp);
private void AcceptCallBack(IAsyncResult result)
{
    try
    {        
        //获取传入的参数
        Socket s = result.AsyncState as Socket;
        //得到连入的客户端
        Socket clientSocket = s.EndAccept(result); 
        //继续监听
        s.BeginAccept(AcceptCallBack, s);
    }
    catch(SocketException e)
    {
        print(e.SocketErrorCode);
    }
}
```

**客户端连接**

```c#
IPEndPoint ipAddress = new IPEndPoint(IPAddress.Parse("127.0.0.1"),8080);
socketTcp.BeginConnect(ipPoint,(result)=>
                       {
                           try
                           {
                               socketTcp.EndConnect(result);
                           }
                           catch(SocketException e)
                           {
                               print($"连接出错{e.SocketErrorCode}{e.Message}");
                           }
                       }, scoketTcp);
```

**接收消息**

```c#
socketTcp.BeginReceive(resultBytes, 0, resultBytes.Length, SocketFlags.None, (result)=>
                       {
                           try
                           {
                               Socket s = result.AsyncState as Socket;
                               //返回值是接受了多少字节
                               int size = s.EndReceive(result);
                               print(Encoding.UTF8.GetString(resultBytes));
                           }
                           catch(SocketException e)
                           {
                               print($"接受消息错误{e.SocketErrorCode}{e.Message}");
                           }
                       }, socketTcp);
```

**发送消息**

```c#
byte[] bytes = Encoding.UTF8.GetBytes("你好");
socketTcp.BeginSend(bytes, 0, bytes.Length, SocketFlags.None, (result)=>
                    {
                        try
                        {
                            Socket s = result.AsyncState as Socket;
                            s.EndSend(result);
                        }
                        catch(SocketException e)
                        {
                             print($"接受发送错误{e.SocketErrorCode}{e.Message}");
                        }    
                    }, socktTcp);
```

## TCP通信中的异步方法(SocketAsyncEventArgs)

`SocketAsyncEventArgs`它会作为Async异步方法的传入值

我们需要通过它进行一些关键参数的赋值

**AcceptAsync接收客户端连入**

```c#
SocketAsyncEventArgs e = new SocketAsyncEventArgs();
e.Completed += (socket, args) =>
{
    //首先判断是否成功
    if (args.SocketError == SocketError.Success)
    {
        //获取连入的客户端socket
        Socket clientSocket = args.AcceptSocket;

        (socket as Socket).AcceptAsync(args);
    }
    else
    {
        print("连入客户端失败" + args.SocketError);
    }
};
socketTcp.AcceptAsync(e);
```

**ConnectAsync客户端连接服务端**

```c#
SocketAsyncEventArgs e2 = new SocketAsyncEventArgs();
e2.Completed += (socket, args) =>
{
    if (args.SocketError == SocketError.Success)
    {
        //连接成功
    }
    else
    {
        //连接失败
        print(args.SocketError);
    }
};
socketTcp.ConnectAsync(e2);
```

**SendAsync发送消息**

```c#
SocketAsyncEventArgs e3 = new SocketAsyncEventArgs();
byte[] bytes2 = Encoding.UTF8.GetBytes("123123的就是拉法基萨克两地分居");
e3.SetBuffer(bytes2, 0, bytes2.Length);
e3.Completed += (socket, args) =>
{
    if (args.SocketError == SocketError.Success)
    {
        print("发送成功");
    }
    else
    {

    }
};
socketTcp.SendAsync(e3);
```

**ReceiveAsync接收消息**

```c#
SocketAsyncEventArgs e4 = new SocketAsyncEventArgs();
//设置接受数据的容器，偏移位置，容量
e4.SetBuffer(new byte[1024 * 1024], 0, 1024 * 1024);
e4.Completed += (socket, args) =>
{
    if(args.SocketError == SocketError.Success)
    {
        //收取存储在容器当中的字节
        //Buffer是容器
        //BytesTransferred是收取了多少个字节
        Encoding.UTF8.GetString(args.Buffer, 0, args.BytesTransferred);

        args.SetBuffer(0, args.Buffer.Length);
        //接收完消息 再接收下一条
        (socket as Socket).ReceiveAsync(args);
    }
    else
    {

    }
};
socketTcp.ReceiveAsync(e4);
```

------

## 服务端实现（begin，end）

**ServerSocket**

```c#
using System;
using System.Collections.Generic;
using System.Net;
using System.Net.Sockets;

namespace TcpServerLearnSync
{
    class ServerSocket
    {
        private Socket socket;
        private Dictionary<int, ClientSocket> clientDic = new Dictionary<int, ClientSocket>();
        public ServerSocket(string ip, int port, int num = 1024) 
        {
            socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
            IPEndPoint ipAddress = new IPEndPoint(IPAddress.Parse(ip),port);
            try
            {
                socket.Bind(ipAddress);
                socket.Listen(num);
                socket.BeginAccept(AcceptCallBack, socket);


            }
            catch (SocketException e)
            {
                Console.WriteLine($"ip绑定错误{e.Message}");
            }
        }
        private void AcceptCallBack(IAsyncResult result) 
        {
            try
            {
                Socket socket = result.AsyncState as Socket;
                
                Socket client = socket.EndAccept(result);
                ClientSocket clientSocket = new ClientSocket(client);
                clientDic.Add(clientSocket.clientID, clientSocket);
                //继续监听
                socket.BeginAccept(AcceptCallBack, socket);
            }
            catch (SocketException e) 
            {
                Console.WriteLine($"客户端连接错误{e.Message}");
            }
        }
        public void Broadcast(string str) 
        {
            foreach (var client in clientDic.Values)
            {
                client.Send(str);
            }
        }
    }
}
```

**ClientSocket**

```c#
using System;
using System.Net.Sockets;
using System.Text;

namespace TcpServerLearnSync
{
    class ClientSocket
    {
        private readonly Socket socket;
        public readonly int clientID;
        public static int CLIENT_BEGIN_ID = 1;
        private byte[] bytes = new byte[1024];
        public ClientSocket(Socket socket)
        {
            this.socket = socket;
            clientID = CLIENT_BEGIN_ID;
            CLIENT_BEGIN_ID++;
            //开始接受消息
            socket.BeginReceive(bytes, 0, bytes.Length, SocketFlags.None, ReceiveCallBack, socket);
        }
        private void ReceiveCallBack(IAsyncResult result)
        {
            try
            {
                int receiveSize = socket.EndReceive(result);
                if (receiveSize > 0)
                {
                    Console.WriteLine(Encoding.UTF8.GetString(bytes));
                }
                receiveSize = 0;
                if (socket.Connected)
                {
                    socket.BeginReceive(bytes, 0, bytes.Length, SocketFlags.None, ReceiveCallBack, socket);
                }
                else
                {
                    Console.WriteLine("客户端断开连接");
                }
            }
            catch (SocketException e)
            {
                Console.WriteLine($"消息接收错误{e.Message}");
            }
        }
        public void Send(string message)
        {
            if (socket == null) return;
            byte[] msgbytes = Encoding.UTF8.GetBytes(message);
            socket.BeginSend(msgbytes, 0, msgbytes.Length, SocketFlags.None, SendCallBack, socket);
        }
        private void SendCallBack(IAsyncResult result)
        {
            try
            {
                socket.EndSend(result);
            }
            catch (SocketException e)
            {
                Console.WriteLine($"消息发送错误{e.Message}");
            }
        }
    }
}
```

------

## 客户端实现（SocketAsyncEventArgs）

```c#
using System.Net;
using System.Net.Sockets;
using System.Text;
using UnityEngine;

public class NetAsyncMgr : MonoBehaviour
{
    private static NetAsyncMgr _instance;//单例
    public static NetAsyncMgr Instance => _instance;
    private byte[] buffer = new byte[1024 * 1024];
    private Socket socket;

    private void Awake()
    {
        _instance = this;
    }

    public void Connect(string ip, int port)
    {
        if (socket == null)
        {
            socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
        }
        IPEndPoint ipAdd = new IPEndPoint(IPAddress.Parse(ip), port);

        SocketAsyncEventArgs args = new SocketAsyncEventArgs();
        args.RemoteEndPoint = ipAdd;
        args.Completed += (socket, args) =>
         {
             if (args.SocketError == SocketError.Success)
             {
                 //开始接收服务器消息
                 Debug.Log("开始接收服务器消息");
                 SocketAsyncEventArgs args2 = new SocketAsyncEventArgs();
                 args2.SetBuffer(buffer, 0, buffer.Length);
                 args2.Completed += ReceiveCallBack;
                 this.socket.ReceiveAsync(args2);
             }
             else
             {
                 Debug.Log("连接错误" + args.SocketError);
             }
         };
        socket.ConnectAsync(args);
    }

    public void ReceiveCallBack(object socket, SocketAsyncEventArgs args)
    {
        if (args.SocketError == SocketError.Success)
        {
            var msg = Encoding.UTF8.GetString(args.Buffer, 0, args.BytesTransferred);
            Debug.Log(msg);
            if (this.socket != null && this.socket.Connected) 
            {
                this.socket.ReceiveAsync(args);
            }
        }
        else
        {
            Debug.Log("消息接收错误" + args.SocketError);
        }
    }

    public void Send(string str)
    {
        byte[] bytes = Encoding.UTF8.GetBytes(str);
        SocketAsyncEventArgs args = new SocketAsyncEventArgs();
        args.SetBuffer(bytes, 0, bytes.Length);
        args.Completed += (socket, args) =>
        {
            if (args.SocketError != SocketError.Success)
            {
                Debug.Log("消息发送错误" + args.SocketError);
            } 
        };
        socket.SendAsync(args);
    }

    public void Close()
    {
        if (socket != null)
        {
            socket.Shutdown(SocketShutdown.Both);
            socket.Disconnect(false);
            socket.Close();
            socket = null;
        }
    }
}
```

