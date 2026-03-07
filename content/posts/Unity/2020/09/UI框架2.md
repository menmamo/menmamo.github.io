---
title: "UI框架2"
author: "mona"
date: 2020-09-19T13:37:00+08:00
categories: ["Unity"]
tags: ["小框架", "UGUI"]
---

| BasePanel | UI面板基类 |
| --------- | ---------- |
| UIMgr     | UI管理类   |
| EUiLayer  | UI层级枚举 |

## UIMgr

```c#
using System.Collections.Generic;
using ProjectBase.Res;
using ProjectBase.SingleBase;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.EventSystems;

namespace ProjectBase.UI
{
    /// <summary>
    /// UI面板层级枚举
    /// </summary>
    public enum EUiLayer
    {
        Top,
        Mid,
        Bot
    }

    /// <summary>
    /// UI面板管理器
    /// </summary>
    public class UIMgr : Single<UIMgr>
    {
        /// <summary>
        /// Canvas
        /// </summary>
        private RectTransform mCanvas;

        /// <summary>
        /// 顶层
        /// </summary>
        private Transform mTop;

        /// <summary>
        /// 中间层
        /// </summary>
        private Transform mMid;

        /// <summary>
        /// 底层
        /// </summary>
        private Transform mBot;

        /// <summary>
        /// UI路径
        /// </summary>
        private string mPath = "Prefabs/UI/UIPanel/";

        /// <summary>
        /// 面板字典
        /// </summary>
        private Dictionary<string, BasePanel> mPaelDict = new Dictionary<string, BasePanel>();

        /// <summary>
        /// 构造方法
        /// </summary>
        public UIMgr()
        {
            GameObject ui = Object.Instantiate(ResMgr.Instance.Load<GameObject>("Prefabs/UI/UI"));
            Object.DontDestroyOnLoad(ui);
            mCanvas = ui.transform.Find("Canvas").transform as RectTransform;
            mTop = GetCanvas.Find("Top");
            mMid = GetCanvas.Find("Mid");
            mBot = GetCanvas.Find("Bot");
        }

        /// <summary>
        /// 显示面板
        /// </summary>
        /// <param name="name">面板名</param>
        /// <param name="layer">面板层级</param>
        /// <typeparam name="T"></typeparam>
        public void ShowPanel<T>(string name, EUiLayer layer = EUiLayer.Mid)
            where T : BasePanel
        {
            if (mPaelDict.ContainsKey(name))
            {
                mPaelDict[name].transform.SetParent(GetLayer(layer));
                mPaelDict[name].Show();
            }
            else
            {
                //异步加载面板
                ResMgr.Instance.LoadAsync<GameObject>(mPath + name, (obj) =>
                {
                    GameObject panelObj = Object.Instantiate(obj, GetLayer(layer), false);
                    panelObj.name = obj.name; //方便UI面板下的脚本获取name
                    T panel = panelObj.GetComponent<T>();
                    mPaelDict.Add(name, panel);
                    panel.Show();
                });
            }
        }


        /// <summary>
        /// 隐藏面板
        /// </summary>
        /// <param name="name">面板名</param>
        public void HidePanel(string name)
        {
            if (!mPaelDict.ContainsKey(name)) return;
            mPaelDict[name].Hide();
            Object.Destroy(mPaelDict[name].gameObject);
            mPaelDict.Remove(name);
        }

        /// <summary>
        /// 获得Canvas
        /// </summary>
        public RectTransform GetCanvas => mCanvas;

        /// <summary>
        /// 获得面板
        /// 调用ShowPanel显示的面板才能被获取
        /// </summary>
        /// <param name="name">面板名</param>
        /// <returns></returns>
        public BasePanel GetPanel(string name) => mPaelDict.ContainsKey(name) ? mPaelDict[name] : null;


        /// <summary>
        /// 获得层级Transform
        /// </summary>
        /// <param name="layer"></param>
        /// <returns></returns>
        public Transform GetLayer(EUiLayer layer)
        {
            Transform uiLayer = null;
            switch (layer)
            {
                case EUiLayer.Top:
                    uiLayer = mTop;
                    break;
                case EUiLayer.Mid:
                    uiLayer = mMid;
                    break;
                case EUiLayer.Bot:
                    uiLayer = mBot;
                    break;
            }

            return uiLayer;
        }

        /// <summary>
        /// 添加自定义事件
        /// </summary>
        /// <param name="obj">添加事件的物体</param>
        /// <param name="type">事件类型</param>
        /// <param name="callBack">事件回调</param>
        public void AddCustomizeEventListener(GameObject obj, EventTriggerType type,
            UnityAction<BaseEventData> callBack)
        {
            EventTrigger trigger = obj.GetComponent<EventTrigger>();
            if (trigger == null)
            {
                trigger = obj.AddComponent<EventTrigger>();
            }

            foreach (var value in trigger.triggers)
            {
                if (value.eventID != type) continue;
                value.callback.AddListener(callBack);
                return;
            }

            EventTrigger.Entry entry = new EventTrigger.Entry {eventID = type};
            entry.callback.AddListener(callBack);
            trigger.triggers.Add(entry);
        }

        /// <summary>
        /// 删除自定义事件
        /// </summary>
        /// <param name="obj">添加事件的物体</param>
        /// <param name="type">事件类型</param>
        /// <param name="callBack">移除的函数</param>
        public void RemoveCustomizeEventListener(GameObject obj, EventTriggerType type,
            UnityAction<BaseEventData> callBack = null)
        {
            EventTrigger trigger = obj.GetComponent<EventTrigger>();
            if (trigger == null || trigger.triggers.Count == 0) return;
            foreach (var value in trigger.triggers)
            {
                if (value.eventID != type) continue;
                if (callBack == null)
                {
                    value.callback.RemoveAllListeners();
                }
                else
                {
                    value.callback.RemoveListener(callBack);
                }

                break;
            }
        }

        /// <summary>
        /// 屏幕坐标转局部坐标
        /// </summary>
        /// <param name="data"></param>
        /// <param name="transform"></param>
        /// <returns></returns>
        public Vector2 ScreenPointToLocalPoint(PointerEventData data, RectTransform transform)
        {
            RectTransformUtility.ScreenPointToLocalPointInRectangle(transform,
                data.position,
                data.enterEventCamera,
                out var localPos
            );
            return localPos;
        }
    }
}
```

## Baseanel

```c#
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.EventSystems;

namespace ProjectBase.UI
{
    /// <summary>
    /// UI面板基类
    /// </summary>
    public class BasePanel : MonoBehaviour
    {
        /// <summary>
        /// 组件字典
        /// 存储面板子物体里的组件
        /// </summary>
        private Dictionary<string, List<UIBehaviour>> mControlDict = new Dictionary<string, List<UIBehaviour>>();

        /// <summary>
        /// 从字典获取面板中子物体上的组件
        /// </summary>
        /// <param name="objectName">子物体名字</param>
        /// <typeparam name="T">组件类型</typeparam>
        /// <returns></returns>
        public T GetControl<T>(string objectName) where T : UIBehaviour
        {
            if (mControlDict.ContainsKey(objectName))
            {
                List<UIBehaviour> uiBehaviours = mControlDict[objectName];
                foreach (var t in uiBehaviours)
                {
                    if (t as T)
                    {
                        return t as T;
                    }
                }
            }
            return null;
        }

        /// <summary>
        /// 获得自身以及子物体上的组件 并存入字典
        /// </summary>
        /// <typeparam name="T">组件类型</typeparam>
        protected void GetChildControl<T>() where T : UIBehaviour
        {
            T[] t = gameObject.GetComponentsInChildren<T>();
            string objectName;
            foreach (var t1 in t)
            {
                objectName = t1.gameObject.name;
                if (!mControlDict.ContainsKey(objectName))
                {
                    mControlDict.Add(objectName, new List<UIBehaviour>());
                }

                mControlDict[objectName].Add(t1);
            }
        }
        
        /// <summary>
        /// 在面板显示
        /// </summary>
        public virtual void Show()
        {
        }

        /// <summary>
        /// 在面板退出
        /// </summary>
        public virtual void Hide()
        {
        }
    }
}
```

## Tips

- 要把层级物体的锚点设置在屏幕的四角 也就是让层级物体的大小和屏幕相同 否则层级面板下的物体会变形
- EventTrigger不是ui组件 因为它并不继承自UIBehaviours
- 不要直接删除EventTrigger.triggers列表中的元素 会报错