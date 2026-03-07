---
title: "Unity编辑器扩展-Inspector窗口扩展"
author: "mona"
date: 2024-08-08T15:07:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## Inspector窗口扩展

我们可以完全自定义 某一个脚本 在Inspector窗口的显示

### SerializedObject和SerializedProperty的作用

Unity通过反射将类中的成员显示在Inspector窗口中

SerializedObject 和 SerializedProperty用于在 Unity 编辑器中操作和修改序列化对象的属性。

它们通常在自定义编辑器中使用，以创建更灵活、可定制的属性面板

- SerializedObject 代表脚本对象

  https://docs.unity.cn/cn/2022.1/ScriptReference/SerializedObject.html

- SerializedProperty 代表脚本对象中的属性

  https://docs.unity.cn/cn/2022.1/ScriptReference/SerializedProperty.html

### 自定义 脚本在Inspector窗口中显示的内容

1. 需要单独写一个脚本，并且脚本继承自**Editor**
2. 在该脚本前添加特性**[CustomEditor(想要自定义脚本类名的Type)]**
3. 声明对应**SerializedProperty**序列化属性进行关联
4. 重写**OnInspectorGUI**函数

```c#
public class Test : MonoBehaviour
{
    public int atk;
    public float def;
    public GameObject obj;
    public List<GameObject> objList;
}

[CustomEditor(typeof(Test))]
public class TestEditor : Editor
{
    private SerializedProperty atk;
    private SerializedProperty def;
    private SerializedProperty obj;
    private SerializedProperty objList;
    private void OnEnable()
    {
        atk = serializedObject.FindProperty("atk");
        def = serializedObject.FindProperty("def");
        obj = serializedObject.FindProperty("obj");
        objList = serializedObject.FindProperty("objList");
    }
    
    //  该函数控制了Inspector窗口中显示的内容
    //  只需要在其中重写内容便可以自定义窗口
    public override void OnInspectorGUI()
    {
        serializedObject.Update();
        EditorGUILayout.IntSlider(atk, 0, 100, "攻击力");
        def.floatValue = EditorGUILayout.FloatField("防御力", def.floatValue);
        EditorGUILayout.ObjectField(obj, new GUIContent("敌对对象"));
        EditorGUILayout.PropertyField(objList,new GUIContent("敌人对象们"));
        //自定义Inspector窗口的内容
        if (GUILayout.Button("测试自定义Inspector窗口"))
        {
            Debug.Log(target.name);
        }
        serializedObject.ApplyModifiedProperties();
    }
}
```

- serializedObject.Update();更新
- serializedObject.ApplyModifiedProperties(); 保存修改

**Inspector窗口中显示数组**

- 方法一：使用Unity默认的方式显示EditorGUILayout.PropertyField(objList,new GUIContent("敌人对象们"));
- 方法二：使用自定义布局的方式显示
  - arraySize 获取数组或List容量
  - InsertArrayElementAtIndex(索引) 为数组在指定索引插入默认元素（容量会变化）
  - DeleteArrayElementAtIndex(索引) 为数组在指定索引删除元素（容量会变化）
  - GetArrayElementAtIndex(索引) 获取数组中指定索引位置的 SerializedProperty 对象

```c#
//
//方法二：自己实现
[CustomEditor(typeof(Lesson22))]
public class Lesson22Editor : Editor
{
    private SerializedProperty listObjs;
    private int count;//记录当前size
    private void OnEnable()
    {
        //默认得到的数组和List容量为空
        listObjs = serializedObject.FindProperty("listObjs");
        //初始化当前容量 否则 每次一开始都是0
        count = listObjs.arraySize;
    }
    public override void OnInspectorGUI()
    {
        serializedObject.Update();
        //是否要缩减 移除尾部的内容
        //从后往前去移除 避免移除不干净
        //当容量变少时 才会走这的逻辑
        for (int i = listObjs.arraySize - 1; i >= count; i--)
        {
            listObjs.DeleteArrayElementAtIndex(i); 
        }
        //根据容量绘制需要设置的每一个索引位置的对象
        for (int i = 0; i < count; i++)
        {
            //去判断如果数组或者LIst容量不够 去通过插入的形式去扩容
            if (listObjs.arraySize <= i)
            {
               listObjs.InsertArrayElementAtIndex(i); 
            }
            SerializedProperty indexPro = listObjs.GetArrayElementAtIndex(i);
            EditorGUILayout.ObjectField(indexPro, new GUIContent($"索引{i}"));
        }
        serializedObject.ApplyModifiedProperties();
    }
}
```

### 自定义属性显示

```c#
myCustom = serializedObject.FindProperty("myCustom");//先获取到对象
myCustomI = myCustom.FindPropertyRelative("i");//获取对象中的属性
myCustomF = myCustom.FindPropertyRelative("f");

//直接通过类名.属性 获取
myCustomI = serializedObject.FindProperty("myCustom.i");
myCustomF = serializedObject.FindProperty("myCustom.f");
```

### 字典属性显示

Unity默认是不支持Dictionary在Inspector窗口被显示的

我们可以使用两个List（或数组）来存储Key Value来间接设置Dictionary

**ISerializationCallbackReceiver接口**

该接口是Unity提供的用于序列化和反序列化时执行自定义逻辑的接口

接口中函数：

- OnBeforeSerialize: 在对象被序列化之前调用
- OnAfterDeserialize: 在对象从磁盘反序列化后调用

```c#
public class DictTest : MonoBehaviour,ISerializationCallbackReceiver
{
    public Dictionary<int, string> myDic;
    [SerializeField]
    private List<int> keys = new();
    [SerializeField]
    private List<string> values = new();
    //反序列化后调用的方法
    public void OnAfterDeserialize()
    {
        //先清空字典
        myDic.Clear();
        //遍历list 将key value添加到字典中
        for (int i = 0; i < keys.Count; i++)
        {
            if (!myDic.ContainsKey(keys[i]))
            {
                myDic.Add(keys[i], values[i]);
            }
            else
            {
                Debug.LogWarning("字典Dictionary容器中不允许有相同的键");
            }                
        }
    }
    //序列化前调用的方法
    public void OnBeforeSerialize()
    {
        //先清空list
        keys.Clear();
        values.Clear();
        //遍历字典 将key和value添加到list中
        foreach (var item in myDic)
        {
            keys.Add(item.Key);
            values.Add(item.Value);
        }
    }
}

[CustomEditor(typeof(DictTest))]
public class DictTestEditor : Editor
{
    private SerializedProperty keys;
    private SerializedProperty values;
    private int dicCount; //字典大小
    private void OnEnable()
    {
        keys = serializedObject.FindProperty("keys");
        values = serializedObject.FindProperty("values");
        dicCount = keys.arraySize;
    }
    public override void OnInspectorGUI()
    {
        serializedObject.Update();
        dicCount = EditorGUILayout.IntField("字典容量", dicCount);
        //容量变少时 把多的删了
        for (int i = keys.arraySize - 1; i >= dicCount; i--)
        {
            keys.DeleteArrayElementAtIndex(i);
            values.DeleteArrayElementAtIndex(i);
        }
        for (int i = 0; i < dicCount; i++)
        {
            //如果容量不够 扩容
            if(keys.arraySize <= i)
            {
                keys.InsertArrayElementAtIndex(i);
                values.InsertArrayElementAtIndex(i);
            }
            //去真正的自定义键值对的修改
            SerializedProperty indexKey = keys.GetArrayElementAtIndex(i);
            SerializedProperty indexValue = values.GetArrayElementAtIndex(i);
            //横向布局
            EditorGUILayout.BeginHorizontal();
            indexKey.intValue = EditorGUILayout.IntField("字典的键", indexKey.intValue);
            indexValue.stringValue = EditorGUILayout.TextField("字典的值", indexValue.stringValue);
            EditorGUILayout.EndHorizontal();
        }
        serializedObject.ApplyModifiedProperties();
    } 
}
```

