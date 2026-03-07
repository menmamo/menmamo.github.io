---
title: "数据持久化-XML"
author: "mona"
date: 2024-06-17T11:40:00+08:00
categories: ["Unity"]
tags: ["数据持久化"]
---

## XML

### 格式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Test>测试</Test>
```

### 规则

- 每个元素都必须有关闭标签
- 元素命名规则 基本遵循C#中文变量名命名规则
- XML标签对大小写敏感
- XML为胆囊必须带有根元素
- 特殊符号应该用实体引用
  - &lt    小于号
  - &gl   大于号
  - &aml  &符号
  - &apos 单引号
  - &quot  引号

### 属性

```xml
<Item>
    <id>1</id>
    <num>2</num>
</Item>
<!-- 属性的写法-->
<Item id="1" num="10"/>
```

### Xml文件的存放位置

- 只读不写的xml文件 可以放在Resources或者StreamingAssets文件夹下
- 动态存储的xml文件可以放在Application.persistenDataPath路径下

### C#读取xml的方式

- XmlDocument把数据加载到内存中方便读取
- XMLTextReader 以流形式加载 占用内存更少 但是单向只读 一般不会使用
- Linq

### XmlDocument读取xml文件信息

#### 加载xml文件

```c#
XmlDocument xml = new XmlDocument();
TextAsset asset = Resources.Load<TextAsset>("TestXml");
xml.LoadXml(text.text);
//通过路径来加载xml 不可以加载resources文件夹下面的
xml.load(Application.persistenDataPath+"TestXml.xml");
```

#### 读取xml文件

```c#
//通过XmlDocument对象 得到XmlNode 传入根节点
XmlNode root = xml.SelectSingleNode("Root");
//在通过根节点的对象 找到下面的子节点
XmlNode name = root.SelectSingleNode("name");
//获取内容
print(name.InnerText);
//获取属性
XmlNode item = root.SelectSingleNode("item");
print(item.Attributes["id"].Value);
//第二种方式
print(item.Attributes.GetNamedItem("id").Value);
//找到所有的xmlnode
XmlNodeList nodelist = xml.SelectNodes("Root");
```

### 存储Xml文件

- 存储Xml文件 在Unity中一定是使用各平台都可读可写可找到的路径
- Resources 可读 但是不可写 打包后找不到
- Application.streamingAssetsPath 可读 Pc可写 可找到
- Application.dataPath 打包后找不到
- Application.persistentDataPath 可读可写找得到

#### 关键类

- XmlDocument 用于创建节点 存储文件
- XmlDeclaration用于添加版本信息
- XmlElement 节点类

#### 创建xml

```c#
//创建文本对象
XmlDocument xml = new XmlDocument();
//创建xml 版本信息
XmlDeclaration xmlDec = xml.CreateXmlDeclaration("1.0","UTF-8","");
//将版本信息添加到xml中
xml.AppendChild(xmlDec);
//添加根节点
XmlElment root = xml.CreateElement("Root");
xml.AppendChild(root);
//为根节点添加子节点  <name>路飞</name>
XmlElement name = xml.CreateElement("name");
name.InnerTest = "路飞";
//将name添加到root节点中
root.AppendChild(name);
```

#### 保存xml

```c#
xml.Save("路径");
```

### 修改xml

```c#
//判断文件是否存在
if(File.Exists(path)){
	//创建xml文本对象
	XmlDocument xml = new XmlDocument();
    xml.load(path);  
    //修改 就是在原有文件的基础上 删除 或者添加
    XmlNode root = xml.SelectSingleNode("Root");
    XmlNode node = root.SelectSingleNode("name");
    XmlNode node2 = xml.SelectSingleNode("Root/name");//第二种写法 
    //移除节点
    root.RemoveChild(node);
    XmlElement name = xml.CreateElement("name");
	name.InnerTest = "王路飞";
    root.AppendChild(name);
   //保存
    xml.Save(path);
}
```

## XML序列化

### using关键字

- using关键字 一般是配合 内存占用较大 或者又读写操作时 使用的
- using关键字 将会在大括号内代码执行完毕后 自动释放掉小括号中的对象
- 可以理解为是一种语法糖

```c#
using(){

}
```

### 创建文件写入流

```c#
//首先确认存储的路径
string path = Application.persistentDataPath+"/Test.xml";
//创建文件写入流
StreamWriter stream = new StreamWriter(path)
```

### XmlSerializer用于序列化对象为xml的关键类

- 构造函数需要传入一个Type类型的参数
- 这种方式只可以 保存公共的成员
- 默认不可以序列化字典

```c#
XmlSerializer xs = new XmlSerializer(type);
//序列化
s.Serialize(文件流对象,对象);
```

可通过添加特性 使字段实例化成xml的属性 还可以修改名字

```c#
public class Test {
    [XmlAttribute("名字")]//把name序列化成属性 字段名是名字
    public string name;
    [XmlElement("年龄")]//把age序列化成节点 字段名是年龄
    public int age;
    public bool sex;
    //修改数组的节点名字
    [XmlArray("IntList")]
    //修改数组内容的节点名字
    [XmlArrayItem("Int32")]
    public List<int> listInt;
}
```

## XML反序列化

### 判断路径是否存在

```c#
string path = Application.persistentDataPath + "/Test.xml";
//判断文件是否存在 传入路径
if(File.Exists(path)){

}
```

### 反序列化

- 反序列化list 不会覆盖掉list原来的值 而是会在原有的基础上添加

```c#
//文件读取流
using(StreamReader stream = new StreamReader(path)){
	XmlSerializer xs = new XmlSerializer(Typeof(Test));
    Test test = xs.Deserialize(stream) as Test;
}
```

### IXmlSerializable接口

- 可以通过继承此接口 来重写序列化和反序列化的方法 从而实现序列化字典等等功能
- 要在接口中重写所有字段的 序列化或反序列化规则
- GetSchema返回null就可以了
- 写属性和读属性的规则要一致

```c#
public class Test : IXmlSerializable
{
    public string name;
    public int age;
    public bool sex;

    public XmlSchema GetSchema()
    {
        return null;
    }

    public void ReadXml(XmlReader reader)
    {
        //读取属性 传入name中
        name = reader["name"];
        //读取
        reader.Read();//读节点
        reader.Read();//读节点的内容
        //拿到节点的value
        age = int.Parse(reader.Value);

        //通过循环读取
        while (reader.Read())
        {
            //如果读取到节点
            if (reader.NodeType == XmlNodeType.Element) {
                switch (reader.Name)
                {
                    case "age":
                        reader.Read();//读到属性内的值
                        age = int.Parse(reader.Value);
                        break;
                }
            }
        }
        
        //跳过根节点
        reader.Read();
        reader.ReadStartElement("age");
        reader.Read();//读节点内容
        age = int.Parse(reader.Value);
        reader.ReadEndElement();

        XmlSerializer xs = new XmlSerializer(typeof(string));
        reader.ReadStartElement("name");
        name = xs.Deserialize(reader).ToString();
        reader.ReadEndElement();
    }

    public void WriteXml(XmlWriter writer)
    {
        //写入属性 参数 属性名 内容
        writer.WriteAttributeString("name", name);
        //写入 节点
        writer.WriteElementString("age", age.ToString());
        //写子节点
        writer.WriteStartElement("age");
        writer.WriteStartElement("int", 300.ToString());
        writer.WriteEndElement();
        //可以使用这个类型原来的序列化方法
        XmlSerializer xs = new XmlSerializer(typeof(string));
        writer.WriteStartElement("age");
        xs.Serialize(writer, name);
        writer.WriteEndElement();
        
    }
}
```

### 创建自定义字典类使其可以序列化

```c#
using System.Collections;
using System.Collections.Generic;
using System.Xml;
using System.Xml.Schema;
using System.Xml.Serialization;
using UnityEngine;

public class MyDic<TKey, TValue> : Dictionary<TKey, TValue>, IXmlSerializable
{
    public XmlSchema GetSchema()
    {
        return null;
    }

    public void ReadXml(XmlReader reader)
    {
        XmlSerializer keyxs = new XmlSerializer(typeof(TKey));
        XmlSerializer valuexs = new XmlSerializer(typeof(TValue));
        reader.Read();//跳过根节点
        while (reader.NodeType != XmlNodeType.EndElement) {
            var key = keyxs.Deserialize(reader);
            var value = valuexs.Deserialize(reader);
            Add((TKey)key,(TValue)value);
        }
        reader.Read();//跳过尾节点
    }

    public void WriteXml(XmlWriter writer)
    {
        XmlSerializer keyxs = new XmlSerializer(typeof(TKey));
        XmlSerializer valuexs = new XmlSerializer(typeof(TValue));
        foreach (var item in this)
        {
            keyxs.Serialize(writer, item.Key);
            valuexs.Serialize(writer, item.Value);
        }
    }
}
```

