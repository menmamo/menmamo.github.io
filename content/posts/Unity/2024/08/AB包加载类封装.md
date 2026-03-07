---
title: "AB包加载类封装"
author: "mona"
date: 2024-08-17T12:59:00+08:00
categories: ["Unity"]
tags: ["资源系统", "小框架"]
---

```c#
using System.Collections;
using System.Collections.Generic;
using System.IO;
using UnityEngine;
using UnityEngine.Events;

public class AssetBundleManager : Single<AssetBundleManager>
{
    /// <summary>
    /// AB包路径
    /// </summary>
    private string PathURL
    {
        get
        {
            return $"{Application.streamingAssetsPath}/ab/{MainName}"; 
        }
    }

    /// <summary>
    /// 主包名字
    /// </summary>
    private string MainName
    {
        get
        {
#if UNITY_IOS
                return "IOS";
#elif UNITY_EDITOR_OSX
                return "StandaloneOSXUniversal";
#else
            return "StandaloneWindows";
#endif
        }
    }

    /// <summary>
    /// 依赖信息
    /// </summary>
    private AssetBundleManifest mainABM;

    /// <summary>
    /// AB包字典
    /// </summary>
    private readonly Dictionary<string, AssetBundle> abDict = new();

    /// <summary>
    /// 加载中的AB包
    /// </summary>
    private readonly HashSet<string> loadingName = new();


    /// <summary>
    /// 加载AB包资源
    /// </summary>
    /// <typeparam name="T">资源类型</typeparam>
    /// <param name="abName">AB包名</param>
    /// <param name="resName">资源名</param>
    /// <param name="callBack">回调函数</param>
    public void Load<T>(string abName, string resName, UnityAction<T> callBack, bool isAsync = true) where T : Object
    {
        PublicMono.Instance.StartCoroutine(LoadRes(abName, resName, callBack, isAsync));
    }

    /// <summary>
    /// 加载AB包资源携程
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="abName"></param>
    /// <param name="resName"></param>
    /// <param name="callBack"></param>
    /// <returns></returns>
    private IEnumerator LoadRes<T>(string abName, string resName, UnityAction<T> callBack, bool isAsync) where T : Object
    {
        yield return LoadAB(isAsync, MainName);//加载主包
        if (mainABM == null)//加载依赖信息
        {
            if (isAsync)
            {
                var mainABMRq = abDict[MainName].LoadAssetAsync<AssetBundleManifest>("AssetBundleManifest");
                yield return mainABMRq;
                mainABM = mainABMRq.asset as AssetBundleManifest;
            }
            else
            {
                mainABM = abDict[MainName].LoadAsset<AssetBundleManifest>("AssetBundleManifest");
            }
        }
        string[] strs = mainABM.GetAllDependencies(abName);//加载依赖
        if (strs.Length > 0) yield return LoadAB(isAsync, strs);
        yield return LoadAB(isAsync, abName);//加载AB包
        if (isAsync)//加载资源
        {
            var rq = abDict[abName].LoadAssetAsync<T>(resName);
            yield return rq;
            callBack?.Invoke(rq?.asset as T);
        }
        else
        {
            callBack?.Invoke(abDict[abName].LoadAsset<T>(resName));
        }
    }

    /// <summary>
    /// 加载AB包携程
    /// </summary>
    /// <param name="abNames"></param>
    /// <returns></returns>
    private IEnumerator LoadAB(bool isAsync, params string[] abNames)
    {
        foreach (var abName in abNames)
        {
            while (loadingName.Contains(abName)) yield return null;//如果这个ab包正在加载 就等他加载完成
            if (abDict.ContainsKey(abName)) continue;//如果字典中有直接进入下一次循环
            loadingName.Add(abName);
            if (isAsync)
            {
                var rq = AssetBundle.LoadFromFileAsync($"{PathURL}/{abName}");
                yield return rq;
                abDict.Add(abName, rq.assetBundle);
            }
            else
            {
                abDict.Add(abName, AssetBundle.LoadFromFile($"{PathURL}/{abName}"));
            }
            loadingName.Remove(abName);
        }
    }

    /// <summary>
    /// 卸载AB包
    /// </summary>
    /// <param name="abName">包名</param>
    /// <param name="callback">回调函数</param>
    /// <param name="unloadObjects">是否卸载场景上的资源</param>
    public void UnLoad(string abName, UnityAction callback = null, bool unloadObjects = false)
    {
        PublicMono.Instance.StartCoroutine(UnLoadAB(abName, callback, unloadObjects));
    }

    /// <summary>
    /// 卸载AB包携程
    /// </summary>
    /// <param name="abName"></param>
    /// <param name="callback"></param>
    /// <param name="unloadObjects"></param>
    /// <returns></returns>
    private IEnumerator UnLoadAB(string abName, UnityAction callback, bool unloadObjects)
    {
        //如果这个ab包正在加载 就等他加载完成后卸载
        while (loadingName.Contains(abName)) yield return null;
        if (abDict.ContainsKey(abName))
        {
            var op = abDict[abName].UnloadAsync(unloadObjects);
            yield return op;
            abDict.Remove(abName);
        }
        callback?.Invoke();
    }

    /// <summary>
    /// 卸载所有AB包
    /// </summary>
    /// <param name="callback">回调函数</param>
    /// <param name="unloadObjects">是否卸载场景上的资源</param>
    public void UnLoadAll(UnityAction callback = null, bool unloadObjects = false)
    {
        PublicMono.Instance.StartCoroutine(UnLoadAllAB(callback, unloadObjects));
    }

    /// <summary>
    /// 卸载全部AB包携程
    /// </summary>
    /// <param name="callback"></param>
    /// <param name="unloadObjects"></param>
    /// <returns></returns>
    private IEnumerator UnLoadAllAB(UnityAction callback, bool unloadObjects)
    {
        //等待所有的AB包加载完成后卸载
        while (loadingName.Count > 0) yield return null;
        AssetBundle.UnloadAllAssetBundles(unloadObjects);
        abDict.Clear();
        callback?.Invoke();
    }
}
```

