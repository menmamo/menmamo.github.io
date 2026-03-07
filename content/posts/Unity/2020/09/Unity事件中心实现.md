---
title: "Unity事件中心实现"
author: "mona"
date: 2020-09-11T15:05:00+08:00
categories: ["Unity"]
tags: ["小框架"]
---

| EventManager | 事件管理类 |
| ------------ | ---------- |
| EventType    | 事件枚举类 |

## EventManager

```c#
using System.Collections.Generic;
using ProjectBase.SingleBase;
using UnityEngine.Events;

namespace ProjectBase.EventCenter
{
    /// <summary>
    /// 事件中心
    /// </summary>
    public class EventManager : Single<EventManager>
    {
      
        private Dictionary<EventType, IEventData> eventDicts = new Dictionary<EventType, IEventData>();

        private interface IEventData
        {
        }

        private class EventData<T> : IEventData
        {
            public UnityAction<T> action;

            public EventData(UnityAction<T> action)
            {
                this.action += action;
            }
        }


        /// <summary>
        /// 注册事件
        /// </summary>
        /// <param name="type"></param>
        /// <param name="action"></param>
        public void AddEventListener<T>(EventType type, UnityAction<T> action)
        {
            if (eventDicts.ContainsKey(type))
            {
                EventData<T> eventData = (EventData<T>) eventDicts[type];
                eventData.action += action;
            }
            else
            {
                eventDicts.Add(type, new EventData<T>(action));
            }
        }

        /// <summary>
        /// 删除事件
        /// </summary>
        /// <param name="type"></param>
        /// <param name="action"></param>
        public void RemoveEventListener<T>(EventType type, UnityAction<T> action)
        {
            if (eventDicts.ContainsKey(type))
            {
                EventData<T> eventData = (EventData<T>) eventDicts[type];
                eventData.action -= action;
            }
        }

        /// <summary>
        /// 分发事件
        /// </summary>
        /// <param name="type"></param>
        /// <param name="info"></param>
        public void EventTrigger<T>(EventType type, T info)
        {
            if (eventDicts.ContainsKey(type))
            {
                EventData<T> eventData = (EventData<T>) eventDicts[type];
                eventData.action.Invoke(info);
            }
        }

        /// <summary>
        /// 清理所有事件
        /// </summary>
        public void Clear()
        {
            eventDicts.Clear();
        }
    }
}
```

## EventType

```c#
namespace  ProjectBase.EventCenter
{
    public enum EventType
    {
        Dead,
        SceneLoadProgress
    }
}
```

## 示例： 使用事件中心 封装场景加载类

```c#
using System.Collections;
using ProjectBase.EventCenter;
using ProjectBase.SingleBase;
using UnityEngine.Events;
using UnityEngine.SceneManagement;
using AsyncOperation = UnityEngine.AsyncOperation;
using EventType = ProjectBase.EventCenter.EventType;

namespace ProjectBase.Scene
{
    public class SceneMgr : Single<SceneMgr>
    {
        
        /// <summary>
        /// 同步加载场景
        /// </summary>
        /// <param name="name"></param>
        public void LoadScene(string name)
        {
            SceneManager.LoadScene(name);
        }


        /// <summary>
        /// 异步加载场景
        /// </summary>
        /// <param name="name"></param>
        /// <param name="callBack"></param>
        public void LoadSceneAsync(string name, UnityAction callBack = null)
        {
            MonoManager.Instance.StartCoroutine(LoadSceneByAsync(name, callBack));
        }

        private IEnumerator LoadSceneByAsync(string name, UnityAction callBack)
        {
            AsyncOperation ap = SceneManager.LoadSceneAsync(name);
            while (!ap.isDone)
            {
                EventManager.Instance.EventTrigger(EventType.SceneLoadProgress, ap.progress);
                yield return 1; //下一帧继续
            }
            callBack?.Invoke();
        }
    }
}
```

## 观察者模式(Observer Pattern)：

定义对象之间的一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象皆得到通知并被自动更新