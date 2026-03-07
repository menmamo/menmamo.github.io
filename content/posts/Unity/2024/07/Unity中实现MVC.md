---
title: "Unity中实现MVC"
author: "mona"
date: 2024-07-26T11:07:00+08:00
categories: ["Unity"]
tags: ["小框架", "UGUI"]
---

## 框架基类

### View层

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;

public abstract class BaseView : MonoBehaviour
{
    /// <summary>
    /// View更新方法
    /// </summary>
    /// <param name="model"></param>
    public abstract void UpdateView<T>(T model) where T : BaseModel<T>, new();
}
```

### Controller层

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public abstract class BaseController<T> : Single<T> where T : BaseController<T>,new()
{
    /// <summary>
    /// 绑定model和view
    /// </summary>
    /// <param name="model"></param>
    /// <param name="view"></param>
    public void Band<K>(K model, BaseView view) where K : BaseModel<K>, new()
    {
        model.AddUpdateEventListener(value => view.UpdateView(value));
    }
    protected BaseController() { }
}
```

### Model层

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;

public class BaseModel<T> : Single<T> where T : BaseModel<T>, new()
{
    protected BaseModel() { }

    private UnityAction<T> UpdateEvent;

    /// <summary>
    /// 更新Model数据
    /// </summary>
    public virtual void UpdateModel()
    {
        UpdateEvent?.Invoke(this as T);
    }

    /// <summary>
    /// 添加Model更新监听
    /// </summary>
    /// <param name="callback"></param>
    public void AddUpdateEventListener(UnityAction<T> callback) 
    {
        UpdateEvent -= callback;
        UpdateEvent += callback;
    }

    /// <summary>
    /// 移除Model更新监听
    /// </summary>
    /// <param name="callback"></param>
    public void RemoveUpdateEventlListener(UnityAction<T> callback) 
    {
        UpdateEvent -= callback;
    }
}
```

### 单例基类

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

/// <summary>
/// 单例基类（懒加载）
/// </summary>
/// <typeparam name="T"></typeparam>
public class Single<T> where T : new()
{
    private static readonly Lazy<T> instance = new(() => new T());
    public static T Instance => instance.Value;
    protected Single() { }
}
```

## 使用MVC实现对话框

### DialogueModel

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;

public class DialogueModel : BaseModel<DialogueModel>
{

    private DialogueData[] dialogueDatas;//对话数据
    private int index;//对话索引
    public bool IsLastDialogue => dialogueDatas?.Length - 1 == index;//是否最后一句话
    private readonly Dictionary<string, Sprite> avatarDict = new();//头像字典
    private string AvatarName => dialogueDatas[index].avatar == string.Empty ? dialogueDatas[index].name : dialogueDatas[index].avatar;//如果没有指定头像 使用name作为头像
    public string[] Tags => dialogueDatas[index].tag.Split('|');//字符串拆分Tag
    public string Name => dialogueDatas[index].name; //对话框 显示名字
    public Sprite Avatar => avatarDict[AvatarName];//对话框头像
    public string Value => dialogueDatas[index].value; //对话内容
    public override void UpdateModel()
    {
        //更新时加载头像 字典中有则不用加载
        if (avatarDict.ContainsKey(AvatarName))
        {
            base.UpdateModel();
        }
        else
        {
            //异步加载头像
            ResourcesManager.Instance.LoadAsync<Sprite>("Sprite/Avatar/" + AvatarName, (obj) =>
            {
                if (obj != null)
                {
                    avatarDict.Add(AvatarName, obj);
                }
                base.UpdateModel();
            });
        }
    }
    /// <summary>
    /// 加载对话
    /// </summary>
    /// <param name="dialogueID">对话id</param>
    /// <param name="callback">加载完成后调用的方法</param>
    public void LoadDialogue(string dialogueID, UnityAction callback = null)
    {
        ResourcesManager.Instance.LoadAsync<DialogueSO>("SO/" + dialogueID, (data) =>
        {
            if (data == null) return;
            dialogueDatas = data.datas;
            index = 0;
            callback?.Invoke();
            UpdateModel();
        });
    }
    /// <summary>
    /// 下一句话
    /// </summary>
    public void NextDialogue()
    {
        if (!IsLastDialogue) index++;
        UpdateModel();
    }
}
```

### DialogueController

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DialogueController : BaseController<DialogueController>
{

    private DialogueView view;

    public DialogueController()
    {
        //测试：注册切换对话Tag事件
        EventManager.Instance.AddEventListener<string>(E_EventType.DialogueTagEvent, str =>
        {
            var strs = str.Split(':');
            if (strs[0] == "Jump")
            {
                Show(strs[1]);
            }
        });
    }

    /// <summary>
    /// 显示对话
    /// </summary>
    /// <param name="id">对话id</param>
    public void Show(string id)
    {
        if (view == null)
        {
            //加载对话面板
            ResourcesManager.Instance.LoadAsync<GameObject>("UI/DialoguePanel", (obj) =>
            {
                if (obj == null) return;
                if (GameObject.Instantiate(obj, GameObject.Find("Canvas").transform, false).TryGetComponent(out view))
                {
                    Band(DialogueModel.Instance, view);
                    DialogueModel.Instance.LoadDialogue(id, () =>
                    {
                        view.gameObject.SetActive(true);
                    });
                }
            });
        }
        else
        {
            DialogueModel.Instance.LoadDialogue(id, () =>
             {
                 view.gameObject.SetActive(true);
             });
        }
    }

    /// <summary>
    /// 隐藏面板
    /// </summary>
    public void Hide()
    {
        view.gameObject.SetActive(false);
    }

    /// <summary>
    /// 下一句对话
    /// </summary>
    public void Next()
    {
        if (!view.typeWriter.IsTextShowDone) 
        {
            view.typeWriter.SkipWriteText();
            return;
        }
        //分发Tag事件
        foreach (var tagValue in DialogueModel.Instance.Tags)
        {
            EventManager.Instance.EventTrigger(E_EventType.DialogueTagEvent, tagValue);
        }
        //如果是最后一句话 隐藏面板
        if (DialogueModel.Instance.IsLastDialogue)
        {
            Hide();
            return;
        }
        //加载下一句话
        DialogueModel.Instance.NextDialogue();
    }
}
```

### DialogueView

```c#
using System.Collections;
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using System.Text.RegularExpressions;

public class DialogueView : BaseView
{
    public Image imgAvatar;
    public TMP_Text textName;
    public TMP_Text textValue;
    public TypeWriter typeWriter;
    private void Start()
    {
        imgAvatar.sprite = null;
        textName.text = string.Empty;
        textValue.text = string.Empty;
    }

    /// <summary>
    /// 更新数据方法
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="model"></param>
    public override void UpdateView<T>(T model)
    {
        if (!(model is DialogueModel)) return;
        var dialogueModel = model as DialogueModel;
        imgAvatar.sprite = dialogueModel.Avatar;
        textName.text = dialogueModel.Name + ":";
        E_TypeWriterType type = E_TypeWriterType.Cover;
        float time = 0.05f;
        //测试
        foreach (var item in dialogueModel.Tags)
        {
            if (item == "Append")
            {
                type = E_TypeWriterType.Append;
            }
            else if (item.Split(':')[0] == "Time") 
            {
                time = float.Parse(item.Split(':')[1]);    
            }
        }
        typeWriter.WriteText(dialogueModel.Value, type , time);
    }
}
```

### 对话框SO

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;

[CreateAssetMenu(fileName = "DialogueSO", menuName = "SO/DialogueSO", order = 0)]
public class DialogueSO : ScriptableObject
{
    public DialogueData[] datas;
}
[Serializable]
public class DialogueData
{
    [Header("角色名字")]
    public string name;
    [Header("角色头像")]
    public string avatar;
    [TextArea]
    [Header("对话内容")]
    public string value;
    [Header("标签")]
    public string tag;
}
```

