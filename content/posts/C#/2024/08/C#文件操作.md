---
title: "C#文件操作"
author: "mona"
date: 2024-08-01T11:04:00+08:00
categories: ["C#"]
tags: []
---

## 文件操作File类的常用内容

**判断文件是否存在**

```c#
if(File.Exists(Application.dataPath + "/UnityTeach.tang"))
{
    print("文件存在");
}
else
{
    print("文件不存在");
}
```

**创建文件**

```c#
//参数一：路径
//参数二：缓存大小
//参数三：描述如何创建或覆盖该文件（不常用）
    //  Asynchronous 可用于异步读写
    //  DeleteOnClose 不在使用时，自动删除
    //  Encrypted 加密
    //  None 不应用其它选项
    //  RandomAccess 随机访问文件
    //  SequentialScan 从头到尾顺序访问文件
    //  WriteThrough 通过中间缓存直接写入磁盘
FileStream fs = File.Create(Application.dataPath + "/Test.test");
//通过File.Open创建
//参数一：路径
//参数二：打开模式
FileStream fs2 = File.Open(Application.dataPath + "/Test.test"", FileMode.Create);
```

**写入文件**

```c#
//将指定字节数组 写入到指定路径的文件中
byte[] bytes = BitConverter.GetBytes(999);
File.WriteAllBytes(Application.dataPath + "/Test.test", bytes);
//将指定的string数组内容 一行行写入到指定路径中
string[] strs = new string[] { "123", "123123kdjfsalk", "123123123125243"};
File.WriteAllLines(Application.dataPath + "/Test.test", strs);
//将指定字符串写入指定路径
File.WriteAllText(Application.dataPath + "/Test.test", "1111\n123123131231241234123");
```

**读取文件**

```c#
//读取字节数据
byte[] bytes = File.ReadAllBytes(Application.dataPath + "/Test.test");
print(BitConverter.ToInt32(bytes, 0));
//读取所有行信息
string[] strs = File.ReadAllLines(Application.dataPath + "/Test.test");
for (int i = 0; i < strs.Length; i++)
{
    print(strs[i]);
}
```

**读取所有文本信息**

```c#
print(File.ReadAllText(Application.dataPath + "/Test.test"));
```

**删除文件**

```c#
//注意 如果删除打开着的文件 会报错
File.Delete(Application.dataPath + "/Test.test");
```

**复制文件**

```c#
//参数一：现有文件 需要是流关闭状态
//参数二：目标文件
//参数三：如果文件已有是否覆盖
File.Copy(Application.dataPath + "/Test.test", Application.dataPath + "/TestCopy.test", true);
```

**文件替换**

```c#
//将指定文件的内容替换为另一个文件的内容，删除原始文件并创建已替换文件的备份。
//参数一：用来替换的文件
//参数二：被替换的文件
//参数三：备份替换前的文件
File.Replace(Application.dataPath + "/Test.test", Application.dataPath + "/Test2.test", Application.dataPath + "/Test备份.test");
//Test2替换为Test 并将替换前的Test2备份
```

**以流的形式 打开文件并写入或读取**

```c#
//参数一：路径
//参数二：打开模式
//参数三：访问模式
FileStream fs = File.Open(Application.dataPath + "/Test.test", FileMode.OpenOrCreate, FileAccess.ReadWrite);
```

## 什么是文件流

在C#中提供了一个文件流类 FileStream类

它主要作用是用于读写文件的细节

File只能整体读写文件

而FileStream可以以读写字节的形式处理文件

## FileStream文件流类常用方法

**打开或创建指定文件**

```c#
//参数一：路径
//参数二：打开模式
    //  CreateNew:创建新文件 如果文件存在 则报错
    //  Create:创建文件，如果文件存在 则覆盖
    //  Open:打开文件，如果文件不存在 报错
    //  OpenOrCreate:打开或者创建文件根据实际情况操作
    //  Append:若存在文件，则打开并查找文件尾，或者创建一个新文件
    //  Truncate:打开并清空文件内容
//参数三：访问模式
//参数四：共享权限
    //  None 谢绝共享
    //  Read 允许别的程序读取当前文件
    //  Write 允许别的程序写入该文件
    //  ReadWrite 允许别的程序读写该文件
FileStream fs = new FileStream(Application.dataPath + "/Test.test", FileMode.Create, FileAccess.ReadWrite);
```

**FileStream重要属性和方法**

```c#
//文本字节长度
print(fs.Length);
//是否可写
if(fs.CanRead){}
//是否可读
if(fs.CanWrite){}
//将字节写入文件 当写入后 一定执行一次
fs.Flush();
//关闭流 当文件读写完毕后 一定执行
fs.Close();
//缓存资源销毁回收
fs.Dispose();
```

**写入字节**

```c#
using (FileStream fs = new FileStream(Application.persistentDataPath + "/Test.test", FileMode.OpenOrCreate, FileAccess.Write))
{
    Byte[] bytes = Encoding.UTF8.GetBytes("哈哈哈哈");
    //先写入数组长度 int类型4个字节
    //方法：Write
    //参数一：写入的字节数组
    //参数二：数组中的开始索引
    //参数三：写入多少个字节
    fs.Write(BitConverter.GetBytes(bytes.Length), 0, 4);
    //写入字符串具体内容
    fs.Write(bytes, 0, bytes.Length);
    //避免数据丢失 一定写入后要执行的方法
    fs.Flush();
    //销毁缓存 释放资源
    fs.Dispose();
}
```

**读取字节（挨个读取字节数组）**

```c#
using (FileStream fs2 = File.Open(Application.persistentDataPath + "/Test.test", FileMode.Open, FileAccess.Read))
{
    //读取字符串 byte数组长度
    byte[] bytes2 = new byte[4];
    
    //参数一：用于存储读取的字节数组的容器
    //参数二：容器中开始的位置
    //参数三：读取多少个字节装入容器
    //返回值：当前流索引前进了几个位置
    fs2.Read(bytes2, 0, 4);
    
    //根据读取到的长度 声明一个新的byte数组
    bytes2 = new byte[BitConverter.ToInt32(bytes2, 0);];
    //读取字符串内容
    index = fs2.Read(bytes2, 0, length);
    
    //得到最终的字符串 打印出来
    print(Encoding.UTF8.GetString(bytes2));
    fs2.Dispose();
}
```

**读取字节（一次性读取再挨个读取）**

```c#
using (FileStream fs3 = File.Open(Application.persistentDataPath + "/Test.test", FileMode.Open, FileAccess.Read))
{
    //一开始就申明一个 和文件字节数组长度一样的容器
    byte[] bytes3 = new byte[fs3.Length];
    fs3.Read(bytes3, 0, (int)fs3.Length);
    fs3.Dispose();
    //读取整数
    print(BitConverter.ToInt32(bytes3, 0));
    //得去字符串字节数组的长度
    int length2 = BitConverter.ToInt32(bytes3, 4);
    //得到字符串
    print(Encoding.UTF8.GetString(bytes3, 8, length2));
}
```

## 更加安全的使用文件流对象（using关键字）

```c#
//using关键字重要用法
using (声明一个引用对象)
{
    //使用对象
}
//无论发生什么情况 当using语句块结束后 
//会自动调用该对象的销毁方法 避免忘记销毁或关闭流
//using是一种更安全的使用方法
```

## 文件夹操作

**判断文件夹是否存在**

```c#
if( Directory.Exists(Application.dataPath + "/Test")){}
```

**创建文件夹**

```c#
DirectoryInfo info = Directory.CreateDirectory(Application.dataPath + "/Test");
```

**删除文件夹**

```c#
//参数一：路径
//参数二：是否删除非空目录，如果为true，将删除整个目录，如果是false，仅当该目录为空时才可删除
Directory.Delete(Application.dataPath + "/Test");
```

**查找文件夹和文件**

```c#
//得到指定路径下所有文件夹名
string[] strs = Directory.GetDirectories(Application.dataPath);
//得到指定路径下所有文件名
strs = Directory.GetFiles(Application.dataPath);
```

**移动文件夹**

```c#
//如果第二个参数所在的路径 已经存在了一个文件夹 那么会报错
//移动会把文件夹中的所有内容一起移到新的路径
Directory.Move(Application.dataPath + "/Test", Application.dataPath + "/123123123");
```

## DirectoryInfo目录信息类

```c#
//我们可以通过它获取文件夹的更多信息
//它主要出现在两个地方
//1.创建文件夹方法的返回值
DirectoryInfo dInfo = Directory.CreateDirectory(Application.dataPath + "/Test");
//全路径
print(dInfo.FullName);
//文件名
print(dInfo.Name);
//2.查找上级文件夹信息
dInfo = Directory.GetParent(Application.dataPath + "/Test");
//全路径
print(dInfo.FullName);
//文件名
print(dInfo.Name);
//重要方法
//得到所有子文件夹的目录信息
DirectoryInfo[] dInfos = dInfo.GetDirectories();
```

## FileInfo文件信息类

```c#
FileInfo[] fInfos = dInfo.GetFiles();
for (int i = 0; i < fInfos.Length; i++)
{
    print(fInfos[i].Name);//文件名
    print(fInfos[i].FullName);//路径
    print(fInfos[i].Length);//字节长度
    print(fInfos[i].Extension);//后缀名
}
```