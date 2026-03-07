---
title: "使用JsonUtility解析Json"
author: "mona"
date: 2020-03-04T21:53:00+08:00
categories: ["Unity"]
tags: []
---

Unity5.3开始，提供了一个JSON data的序列化与反序列化的类JsonUtility

现在我要解析的Json数据如下

```json
{
  "infoList": [
    {
      "panelTypeString": "ItemMessage",
      "path": "UIPanel/ItemMessagePanel"
    },
    {
      "panelTypeString": "Knapsack",
      "path": "UIPanel/KnapsackPanel"
    },  
  ]
}
```

注意，不能通过JsonUtility直接解析Json数组,需要把数组存入对象中才可以正常解析。

接下来需要创建用[Serializable]标记的类，类中的属性名要与Json中对象的名字对应，如infoList就要对应类中的的infoList

由于infoList对象中是一个数组，所以使用List来存放数组中的对象

```c#
[System.Serializable]
public class UIPanelInfo
{
   public string panelTypeString;
   public string path; 
}

[System.Serializable] //序列化标签
class UIPanelTypeJson
{
   //属性名和Json对象名保持一致
   public List<UIPanelInfo> infoList;
}
```

JsonUtility解析出来的数据都是string类型的，如果想要获得其他类型如枚举类型的数据，则可以通过实现ISerializationCallbackReceiver接口中的OnAfterDeserialize()方法进行转换，该方法在反序列化后会被调用。

```c#
public class UIPanelInfo : ISerializationCallbackReceiver
{
   // 枚举类型 不能反序列化
   [NonSerialized] public UIPanelType panelType;
//[NonSerialized]不被序列化 标签
   //反序列化： 文本 转 对象
   //属性名要和Json的key对应
   public string panelTypeString;
   public string path;
   public void OnBeforeSerialize()
   {
      //该方法在反序列化前调用
   }
  /// <summary>
   /// 因为FromJson不能反序列化枚举类型
   /// 所以将序列化的String字符串，转换成enum枚举类型 并赋值给panelTyle
   /// </summary>
   public void OnAfterDeserialize()
   {
      //把String转成UIPanelType类型Enum
      UIPanelType type = (UIPanelType)Enum.Parse(typeof(UIPanelType), panelTypeString);
      //赋值给panelType
      panelType = type;
   }
}
```

最后调用JsonUtility中的FromJson方法来解析Json

```c#
private Dictionary<UIPanelType, string> panelPathDict;
private void ParseUIPanelTypeJson()
    {
        //初始化字典
        panelPathDict = new Dictionary<UIPanelType, string>();
        //读取Json文本
        TextAsset ta = Resources.Load<TextAsset>("UIPanelType");
        //解析json 序列化到UIPanelTypeJson对象中
        UIPanelTypeJson jsonObject = JsonUtility.FromJson<UIPanelTypeJson>(ta.text);
        //便利jsonObjcet中的infoList
        foreach (UIPanelInfo info in jsonObject.infoList)
        {
            //将反序列化得到的属性，添加到字典中
            panelPathDict.Add(info.panelType, info.path);
        }
    }
```

从文件中解析Json

```c#
public Player ReadPlayerFromJson()
        {
            var json = Encoding.UTF8.GetString(File.ReadAllBytes(mPlayerInfo_URL));
            return JsonUtility.FromJson<Player>(json);
        }
```

从将Json写入到文件

```c#
 public void SavePlayerInfo(Player player)
        {
            //反序列化
            var json = JsonUtility.ToJson(player);
            //将json写入硬盘
            File.WriteAllBytes(mPlayerInfo_URL,Encoding.UTF8.GetBytes(json));
        }
```