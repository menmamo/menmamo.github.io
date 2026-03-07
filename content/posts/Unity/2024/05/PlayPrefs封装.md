---
title: "PlayPrefs封装"
author: "mona"
date: 2024-05-17T18:37:00+08:00
categories: ["Unity"]
tags: []
---

```c#
using System;
using System.Collections;
using System.Reflection;
using UnityEngine;

public class PlayerPrefsDataMgr
{
    private static PlayerPrefsDataMgr _instance = new PlayerPrefsDataMgr();
    public static PlayerPrefsDataMgr Instance
    {
        get
        {
            return _instance;
        }
        private set
        {
        }
    }

    private PlayerPrefsDataMgr()
    {

    }

    /// &lt;summary&gt;
    /// 保存数据
    /// &lt;/summary&gt;
    /// &lt;param name=&quot;obj&quot;&gt;要保存的对象&lt;/param&gt;
    /// &lt;param name=&quot;key&quot;&gt;唯一值&lt;/param&gt;
    public void SaveData(object obj, string key)
    {
        Type type = obj.GetType();
        FieldInfo[] fields = type.GetFields();
        string keyName;
        for (int i = 0; i &lt; fields.Length; i++)
        {
            keyName = key + &quot;_&quot; + type.Name + &quot;_&quot; + fields[i].FieldType.Name + &quot;_&quot; + fields[i].Name;
            SaveValue(keyName, fields[i].GetValue(obj));
        }
        PlayerPrefs.Save();
    }

    /// &lt;summary&gt;
    /// 获得数据
    /// &lt;/summary&gt;
    /// &lt;param name=&quot;type&quot;&gt;传入对象类型&lt;/param&gt;
    /// &lt;param name=&quot;key&quot;&gt;唯一值Type&lt;/param&gt;
    /// &lt;returns&gt;&lt;/returns&gt;
    public object GetData(Type type, string key)
    {
        object obj = Activator.CreateInstance(type);
        FieldInfo[] fildinfo = type.GetFields();
        string keyName;
        foreach (var info in fildinfo)
        {
            keyName = key + &quot;_&quot; + type.Name + &quot;_&quot; + info.FieldType.Name + &quot;_&quot; + info.Name;
            Debug.Log(keyName);
            info.SetValue(obj, GetValue(info.FieldType, keyName));
        }
        return obj;
    }

    /// &lt;summary&gt;
    /// 把数据存入PlayerPrefs
    /// &lt;/summary&gt;
    /// &lt;param name=&quot;keyName&quot;&gt;&lt;/param&gt;
    /// &lt;param name=&quot;value&quot;&gt;&lt;/param&gt;
    private void SaveValue(string keyName, object value)
    {
        Type type = value.GetType();
        if (type == typeof(string)) PlayerPrefs.SetString(keyName, value.ToString());
        else if (type == typeof(int)) PlayerPrefs.SetInt(keyName, (int)value);
        else if (type == typeof(float)) PlayerPrefs.SetFloat(keyName, (float)value);
        else if (type == typeof(bool)) PlayerPrefs.SetInt(keyName, (bool)value ? 1 : 0);
        else if (typeof(IList).IsAssignableFrom(type))
        {
            IList list = value as IList;
            PlayerPrefs.SetInt(keyName, list.Count);
            for (int i = 0; i &lt; list.Count; i++)
            {
                SaveValue(keyName + &quot;_&quot; + i, list[i]);
            }
        }
        else if (typeof(IDictionary).IsAssignableFrom(type))
        {
            IDictionary dict = value as IDictionary;
            PlayerPrefs.SetInt(keyName, dict.Count);
            var keys = dict.Keys;
            int index = 0;
            foreach (var key in dict.Keys)
            {
                SaveValue(keyName + &quot;_&quot; + index + &quot;_&quot; + &quot;key&quot;, key);
                SaveValue(keyName + &quot;_&quot; + index + &quot;_&quot; + &quot;value&quot;, dict[key]);
                index++;
            }
        }
        else
        {
            SaveData(value, keyName);
        }
    }

    /// &lt;summary&gt;
    /// 把数据从PlayerPrefs取出
    /// &lt;/summary&gt;
    /// &lt;param name=&quot;type&quot;&gt;&lt;/param&gt;
    /// &lt;param name=&quot;key&quot;&gt;&lt;/param&gt;
    /// &lt;returns&gt;&lt;/returns&gt;
    private object GetValue(Type type, string key)
    {

        if (type == typeof(string)) return PlayerPrefs.GetString(key);
        else if (type == typeof(int)) return PlayerPrefs.GetInt(key);
        else if (type == typeof(float)) return PlayerPrefs.GetFloat(key);
        else if (type == typeof(bool)) return PlayerPrefs.GetInt(key) == 1;
        else if (typeof(IList).IsAssignableFrom(type))
        {
            IList list = Activator.CreateInstance(type) as IList;
            for (int i = 0; i &lt; PlayerPrefs.GetInt(key); i++)
            {
                list.Add(GetValue(type.GetGenericArguments()[0], key + &quot;_&quot; + i));
            }
            return list;
        }
        else if (typeof(IDictionary).IsAssignableFrom(type))
        {
            IDictionary dict = Activator.CreateInstance(type) as IDictionary;
            Type keyType = type.GetGenericArguments()[0];
            Type valueType = type.GetGenericArguments()[1];
            for (int i = 0; i &lt; PlayerPrefs.GetInt(key); i++)
            {
                dict.Add(GetValue(keyType, key + &quot;_&quot; + i + &quot;_&quot; + &quot;key&quot;), GetValue(valueType, key + &quot;_&quot; + i + &quot;_&quot; + &quot;value&quot;));
            }
            return dict;
        }
        else
        {
            return GetData(type, key);
        }
    }
}
```

