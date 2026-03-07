---
title: "Inspector窗口"
author: "mona"
date: 2024-06-17T11:46:00+08:00
categories: ["Unity"]
tags: ["Unity基础组件"]
---

## Inspector窗口可编辑的变量

- Inspector显示的可编辑内容就是脚本的成员变量
- 私有（private）的和保护（protected）的默认不会显示 可在变量前面加上强制序列化字段特性[SerializeField]使其显示出来
- 公共的（public）默认会在Inspector窗口中显示出来，如果不想让其显示可在变量前增加特性[HideInInspector]
- 大部分的类型都可以显示编辑，字典不可以被Inspector窗口显示
- 可让自定义类型可以被访问 加上序列化特性[System.Serializable]

```c#
//在inspector中显示字段
public int test;
//不再inspector显示公共的字段
[HideInInspector]
public int test1;
//在inspector显示私有和保护的字段
[SerializeField]
private int test2;
//让自定义类型可以被访问
[System.Serializable]
public class test3{
    public int age;
    public bool sex;
} 
public test3 test4;
```

### 一些辅助特性

1. **分组说明特性Header 为成员分组**

```c#
[Header("基础属性")]
public int age;
public bool sex;
[Header("战斗属性")]
public int atk;
public int def;
```

2. **悬停注释Tooltip为变量添加说明**

```c#
[Tooltip("闪避")]
public int miss;
```

3. **间隔特性Space 让两个字段出现间隔**

```c#
[Space()]
```

4. **修饰数值的滑条范围Range**

```c#
[Range(0,10)]//[Range(最小值,最大值)]
public float luck;
```

5. **多行显示字符串 不写参数默认显示3行**

```c#
[Multiline(5)]
public string tips;
```

6. **滑动条显示字符串 不写参数默认就是超过3行显示滚动条**

```c#
[TextArea(3,4)]//最少显示3行，最多4行，超过4行显示滚动条
public string myLife;
```

7. **为变量添加快捷方法ContextMenuItem**

```c#
//[ContextMenuItem("显示按钮名", "方法名")]
[ContextMenuItem("重置钱", "Test")]
public int money;
private void Test()
{
    money = 99;
}
```

8. **为方法添加特性能够在Inspector中执行[ContextMenu("测试函数")]**

```c#
[ContextMenu("哈哈哈哈")]
private void TestFun()
{
    print("测试方法");
}
```