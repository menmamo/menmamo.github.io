---
title: "UI框架"
author: "mona"
date: 2020-09-10T22:40:00+08:00
categories: ["Unity"]
tags: ["小框架", "UGUI"]
---

| UIPanelType   | 枚举类 存放UI名字和资源路径               |
| ------------- | ----------------------------------------- |
| EnumExtension | 枚举类扩展 方便读取Description属性中的值  |
| UIManager     | UI管理类 实例化UI 使用栈控制UI的显示/关闭 |
| BaseUI        | UI基类 被管理的UI需要继承此类             |

## UIPanelType

```c#
using System.ComponentModel;

namespace ProjectBase.UIFramework
{
    public enum UIType
    {
        [Description("Prefabs/UIPanel/ItemMessagePanel")]
        ItemMessage,
        [Description("Prefabs/UIPanel/KnapsackPanel")]
        Knapsack ,
        [Description("Prefabs/UIPanel/MainMenuPanel")]
        MainMenu,
        [Description("Prefabs/UIPanel/ShopPanel")]
        Shop,
        [Description("Prefabs/UIPanel/SkillPanel")]
        Skill,
        [Description("Prefabs/UIPanel/SystemPanel")]
        System ,
        [Description("Prefabs/UIPanel/TaskPanel")]
        Task,
        
    }
}
```

通过Description属性来存储UI的资源路径，但是读取这个属性比较麻烦所以写了个扩展方法

## EnumExtension

```c#
using System;
using System.ComponentModel;
using System.Reflection;

namespace ProjectBase.UIFramework
{
    public static class EnumExtension
    {
        /// <summary>
        /// 扩展方法，获得枚举的Description
        /// </summary>
        /// <param name="value">枚举值</param>
        /// <param name="nameInstend">当枚举没有定义DescriptionAttribute,是否用枚举名代替，默认使用</param>
        /// <returns>枚举的Description</returns>
        public static string GetDescription(this Enum value, bool nameInstend = true)
        {
            Type type = value.GetType();
            string name = Enum.GetName(type, value);
            if (name==null)
            {
                return null;
            }
            FieldInfo field = type.GetField(name);
            DescriptionAttribute attribute = Attribute.GetCustomAttribute(field, typeof(DescriptionAttribute)) as DescriptionAttribute;
            if (attribute==null&&nameInstend)
            {
                return name;
            }
            return attribute?.Description;
        }
    }
}
```

## UIManager

```c#
using System.Collections.Generic;
using ProjectBase.SingleBase;
using UnityEngine;

namespace ProjectBase.UIFramework
{
    public class UIManager : Single<UIManager>
    {
        private Dictionary<UIType, BaseUI> panelDict = new Dictionary<UIType, BaseUI>();
        private Stack<BaseUI> panelStack = new Stack<BaseUI>();
        private Transform canvasTransform;

        public UIManager()
        {
            canvasTransform = GameObject.Find("Canvas").transform;
        }

        /// <summary>
        /// 获得panel 如果没有就实例化一个新的
        /// </summary>
        /// <param name="type"></param>
        /// <returns></returns>
        private BaseUI GetPanel(UIType type)
        {
            if (panelDict.TryGetValue(type, out BaseUI baseUI)) return baseUI;
            GameObject obj = Object.Instantiate(Resources.Load<GameObject>(type.GetDescription()), canvasTransform, false);
            baseUI = obj.GetComponent<BaseUI>();
            panelDict.Add(type, baseUI);
            return baseUI;
        }

        /// <summary>
        /// 入栈
        /// </summary>
        /// <param name="type"></param>
        public void PushPanel(UIType type)
        {
            if (panelStack.Count>0)
            {
                panelStack.Peek().OnPause();
            }
            BaseUI baseUI = GetPanel(type);
            baseUI.OnEnter();
            panelStack.Push(baseUI);
        }
        
        /// <summary>
        /// 出栈
        /// </summary>
        public void PopPanel()
        {
            if (panelStack.Count <= 0) return;
            BaseUI baseUI = panelStack.Pop();
            baseUI.OnExit();
            if (panelStack.Count>0)
            {
                panelStack.Peek().OnResume();
            }
        }
    }
}
```

## BaseUI

```c#
using UnityEngine;

namespace ProjectBase.UIFramework
{
    public class BaseUI : MonoBehaviour
    {
        protected CanvasGroup canvasGroup;

        /// <summary>
        /// 界面被显示出来
        /// </summary>
        public virtual void OnEnter()
        {
            if (canvasGroup == null)
            {
                canvasGroup = GetComponent<CanvasGroup>();
                if (canvasGroup == null)
                {
                    canvasGroup = gameObject.AddComponent<CanvasGroup>();
                }
            }

            canvasGroup.alpha = 1;
            canvasGroup.blocksRaycasts = true;
        }

        /// <summary>
        /// 界面暂停
        /// </summary>
        public virtual void OnPause()
        {
            canvasGroup.blocksRaycasts = false;
        }

        /// <summary>
        /// 界面继续
        /// </summary>
        public virtual void OnResume()
        {
            canvasGroup.blocksRaycasts = true;
        }

        /// <summary>
        /// 退出页面
        /// </summary>
        public virtual void OnExit()
        {
            canvasGroup.alpha = 0;
            canvasGroup.blocksRaycasts = false;
        }

    }
}
```