---
title: "AssetBundle资源管理器"
author: "mona"
date: 2020-09-22T18:12:00+08:00
categories: ["Unity"]
tags: ["资源系统", "小框架"]
---

## AbMgr

```c#
using System.Collections;
using System.Collections.Generic;
using ProjectBase.Mono;
using ProjectBase.SingleBase;
using UnityEngine;
using UnityEngine.Events;

namespace ProjectBase.Res
{
    /// <summary>
    /// AB包资源加载
    /// </summary>
    public class AbMgr : Single<AbMgr>
    {
        /// <summary>
        /// AB包字典
        /// </summary>
        Dictionary<string, AssetBundle> mAbDict = new Dictionary<string, AssetBundle>();

        /// <summary>
        /// AB包主包
        /// </summary>
        private AssetBundle mMainAb;

        /// <summary>
        /// 主包AssetBundleManifest对象
        /// </summary>
        private AssetBundleManifest mMainAbm;

        /// <summary>
        /// AB包的存放路径
        /// </summary>
        private string mPathUrl => Application.streamingAssetsPath + "/";

        /// <summary>
        /// 主包路径
        /// </summary>
        private string mMainUrl
        {
            get
            {
#if UNITY_IOS
                return "IOS"+"/";
#elif UNITY_EDITOR_OSX
                return "StandaloneOSXUniversal";
#else
                return "PC"+"/";
#endif
            }
        }

        /// <summary>
        /// 同步加载AB包
        /// </summary>
        /// <param name="abName"></param>
        /// <returns></returns>
        private AssetBundle Load(string abName)
        {
            LoadRely(abName);
            if (!mAbDict.ContainsKey(abName))
            {
                mAbDict.Add(abName, AssetBundle.LoadFromFile(mPathUrl + abName));
            }

            return mAbDict[abName];
        }

        /// <summary>
        /// 同步加载AB包依赖
        /// </summary>
        /// <param name="abName"></param>
        private void LoadRely(string abName)
        {
            if (mMainAb == null)
            {
                mMainAb = AssetBundle.LoadFromFile(mPathUrl + mMainUrl);
                mMainAbm = mMainAb.LoadAsset<AssetBundleManifest>("AssetBundleManifest");
            }

            string[] names = mMainAbm.GetAllDependencies(abName);
            foreach (var value in names)
            {
                if (mAbDict.ContainsKey(value)) continue; //如果已经加载了 就不再加载
                mAbDict.Add(value, AssetBundle.LoadFromFile(mPathUrl + value));
            }
        }

        /// <summary>
        /// 异步加载AB包
        /// </summary>
        /// <param name="abName"></param>
        /// <param name="callBack"></param>
        private void LoadAsync(string abName, UnityAction<AssetBundle> callBack)
        {
            MonoManager.Instance.StartCoroutine(IELoadAsync(abName, callBack));
        }

        /// <summary>
        /// 异步加载AB包协程
        /// </summary>
        /// <param name="abName"></param>
        /// <param name="callBack"></param>
        /// <returns></returns>
        private IEnumerator IELoadAsync(string abName, UnityAction<AssetBundle> callBack)
        {
            Coroutine coroutine = MonoManager.Instance.StartCoroutine(IELoadRelyAsync(abName));
            yield return coroutine;
            if (!mAbDict.ContainsKey(abName))
            {
                AssetBundleCreateRequest abcq = AssetBundle.LoadFromFileAsync(mPathUrl + abName);
                yield return abcq;
                if (abcq.assetBundle == null) yield break;
                mAbDict.Add(abName, abcq.assetBundle);
                callBack.Invoke(abcq.assetBundle);
                yield break;
            }

            callBack.Invoke(mAbDict[abName]);
        }

        /// <summary>
        /// 异步加载AB包依赖携程
        /// </summary>
        /// <param name="name"></param>
        /// <returns></returns>
        private IEnumerator IELoadRelyAsync(string abName)
        {
            //异步加载主包
            if (mMainAb == null)
            {
                var abcq = AssetBundle.LoadFromFileAsync(mPathUrl + mMainUrl);
                yield return abcq;
                mMainAb = abcq.assetBundle;
                var assetBundleRequest = abcq.assetBundle.LoadAssetAsync<AssetBundleManifest>("AssetBundleManifest");
                yield return assetBundleRequest;
                mMainAbm = assetBundleRequest.asset as AssetBundleManifest;
            }

            string[] names = mMainAbm.GetAllDependencies(abName);
            foreach (var value in names)
            {
                if (mAbDict.ContainsKey(value)) continue; //如果已经加载了 就不再加载
                var request = AssetBundle.LoadFromFileAsync(mPathUrl + value);
                yield return request;
                mAbDict.Add(value, request.assetBundle);
            }
        }

        /// <summary>
        /// 同步加载AB包中的资源
        /// </summary>
        /// <param name="abName"></param>
        /// <param name="resName"></param>
        /// <typeparam name="T"></typeparam>
        /// <returns></returns>
        public T LoadAsset<T>(string abName, string resName) where T : Object
        {
            return !mAbDict.ContainsKey(abName) ? Load(abName).LoadAsset<T>(resName) : mAbDict[abName].LoadAsset<T>(resName);
        }

        /// <summary>
        /// 异步加载AB包中的资源
        /// </summary>
        /// <param name="abName"></param>
        /// <param name="resName"></param>
        /// <param name="callBack"></param>
        /// <typeparam name="T"></typeparam>
        public void LoadAssetAsync<T>(string abName, string resName, UnityAction<T> callBack) where T : Object
        {
            if (!mAbDict.ContainsKey(abName))
            {
                LoadAsync(abName,
                    (ab) => { MonoManager.Instance.StartCoroutine(IELoadAssetAsync(ab, resName, callBack)); });
            }
            else
            {
                MonoManager.Instance.StartCoroutine(IELoadAssetAsync(mAbDict[abName], resName, callBack));
            }
        }

        /// <summary>
        /// 异步加载AB包中的资源协程
        /// </summary>
        /// <param name="assetBundle"></param>
        /// <param name="resName"></param>
        /// <param name="callBack"></param>
        /// <typeparam name="T"></typeparam>
        /// <returns></returns>
        private IEnumerator IELoadAssetAsync<T>(AssetBundle assetBundle, string resName, UnityAction<T> callBack)
            where T : Object
        {
            var abq = assetBundle.LoadAssetAsync<T>(resName);
            yield return abq;
            if (abq.asset == null) yield break;
            callBack.Invoke(abq.asset as T);
        }

        /// <summary>
        /// 卸载AB包
        /// </summary>
        /// <param name="name"></param>
        /// <param name="unloadObjects"></param>
        public void UnLoad(string name, bool unloadObjects = false)
        {
            if (!mAbDict.ContainsKey(name)) return;
            mAbDict[name].Unload(unloadObjects);
            mAbDict.Remove(name);
        }

        /// <summary>
        /// 卸载所有ab包
        /// </summary>
        public void UnLoadAll(bool unloadObjects = false)
        {
            AssetBundle.UnloadAllAssetBundles(unloadObjects);
            mAbDict.Clear();
        }
    }
}
```

## 测试

```c#
//同步加载
        Instantiate(AbMgr.Instance.LoadAsset<GameObject>("cube", "cube2"));
        //异步加载
        AbMgr.Instance.LoadAssetAsync<GameObject>("cube", "cube2", (obj =>
        {
            Instantiate(obj);
        }));
```