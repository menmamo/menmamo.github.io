---
title: "Unity对象池实现"
author: "mona"
date: 2020-09-06T20:39:00+08:00
categories: ["Unity"]
tags: ["小框架"]
---

| PoolManager对象池管理类 | 管理不同的对象池 实现对象池的增删查功能                      |
| ----------------------- | ------------------------------------------------------------ |
| PoolData 对象池实例     | 每个不同种类的对象 都存放在一个单独的对象池实例中            |
| IPool 生命周期函数接口  | 因为对象池是通过SetActive的方式来取出或者回收物体的 这个过程中不会调用Unity的声明周期函数 所以通过一个接口 让对象池中的对象实现这个接口 实现自己的声明周期函数 |

## PoolManager

```c#
using System.Collections.Generic;
using ProjectBase.SingleBase;
using UnityEngine;

namespace ProjectBase.Pool
{
    /// <summary>
    /// 对象池管理类
    /// </summary>
    public class PoolManager : Single<PoolManager>
    {
        private Dictionary<string, PoolData> poolDicts = new Dictionary<string, PoolData>();

        /// <summary>
        /// 创建对象池
        /// </summary>
        /// <param name="path">资源路径</param>
        /// <param name="size">对象池大小</param>
        /// <returns></returns>
        public PoolData AddPool(string path, int size = 100)
        {
            PoolData poolData = null;
            GameObject obj = Resources.Load<GameObject>(path);
            if (obj != null)
            {
                poolDicts.Add(obj.name, new PoolData(obj, size)); //对象池的用物体名来命名
                poolData = poolDicts[obj.name];
            }

            return poolData;
        }

        /// <summary>
        /// 根据name获取对象池
        /// </summary>
        /// <param name="name"></param>
        /// <returns></returns>
        public PoolData GetPool(string name) => poolDicts.ContainsKey(name) ? poolDicts[name] : null;
        
        /// <summary>
        /// 清空对象池
        /// </summary>
        public void Clear(string name = "")
        {
            if (name.Equals(""))
            {
                poolDicts.Clear();
            }
            else if (poolDicts.ContainsKey(name))
            {
                poolDicts.Remove(name);
            }
        }
    }
}
```

Single是一个泛形的单例模式基类，使用物体名来命名对象池 好处是非常方便 对象池中的物体只需要通过gameObject.name或者name属性就能找到自己所在的对象池

```c#
PoolManager.Instance.GetPool(name).Recycle();
```

## PoolData

```c#
using System.Collections.Generic;
using UnityEngine;

namespace ProjectBase.Pool
{
    /// <summary>
    /// 对象池类
    /// </summary>
    public class PoolData
    {
        private GameObject baseObject; //父物体
        private GameObject poolObject; //对象池中的物体
        private Queue<GameObject> idleObjs; //闲置池
        private Queue<GameObject> activityObjs; //活动池
        private int size;

        /// <summary>
        /// 初始化对象池
        /// </summary>
        /// <param name="poolObject">对象池内的对象</param>
        /// <param name="size">对象池大小</param>
        public PoolData(GameObject poolObject, int size)
        {
            this.size = size;
            this.poolObject = poolObject;
            GameObject poolObj = GameObject.Find("pool");
            if (poolObj == null) poolObj = new GameObject("pool");
            baseObject = new GameObject(poolObject.name);
            baseObject.transform.SetParent(poolObj.transform);
            idleObjs = new Queue<GameObject>(size);
            activityObjs = new Queue<GameObject>(size);
        }

        /// <summary>
        /// 获得对象池对象数量
        /// </summary>
        private int GetPoolCount => idleObjs.Count + activityObjs.Count;

        /// <summary>
        /// 获得活动对象数量
        /// </summary>
        public int GetActiveCount => activityObjs.Count;

        /// <summary>
        /// 获取对象
        /// 从对象池中取对象 如果对象池没满 创建新对象 否则释放活动对象
        /// </summary>
        public void GetObj()
        {
            GameObject obj;
            if (idleObjs.Count <= 0)
            {
                if (GetPoolCount < size)
                {
                    obj = Object.Instantiate(poolObject);
                    obj.name = poolObject.name;
                }
                else
                {
                    Recycle();
                    obj = idleObjs.Dequeue();
                }
            }
            else
            {
                obj = idleObjs.Dequeue();
            }

            obj.SetActive(true);
            obj.SendMessage("OnGetObj", SendMessageOptions.DontRequireReceiver);
            obj.transform.SetParent(null);
            activityObjs.Enqueue(obj);
        }

        /// <summary>
        /// 回收对象
        /// </summary>
        public void Recycle()
        {
            if (activityObjs.Count <= 0) return;
            GameObject obj = activityObjs.Dequeue();
            obj.SendMessage("OnRecycle", SendMessageOptions.DontRequireReceiver);
            obj.SetActive(false);
            obj.transform.SetParent(baseObject.transform);
            idleObjs.Enqueue(obj);
        }
    }
}
```

为了解决对象池在取出对象后对象池失去对已取出对象控制的问题 使用两个池分别存放闲置对象和活动对象 不仅解决问题 还顺便实现了对对象池容量的控制

## IPool

```c#
namespace ProjectBase.Pool
{
    /// <summary>
    /// 对象池生命周期函数接口
    /// </summary>
    public interface IPool //对象池声明周期接口 对象池中的对象需要实现此接口（可选）
    {
        void OnGetObj(); //物体从对象池中取出时调用
        void OnRecycle(); //被对象池回收时调用
    }
}
```

## 测试脚本：

```c#
using System;
using ProjectBase.Pool;
using UnityEngine;

public class Test : MonoBehaviour
{
    private PoolManager poolManager;
    private PoolData cubePool;
    private PoolData spherePool;

    private void Awake()
    {
        poolManager = PoolManager.Instance;
        cubePool = poolManager.InitPool("Prefabs/Cube",700);
        spherePool = poolManager.InitPool("Prefabs/Sphere",300);
    }
    void Update()
    {
        if (Input.GetMouseButton(0))
        {
            cubePool.GetObj();
            spherePool.GetObj();
        }
    }
}
```