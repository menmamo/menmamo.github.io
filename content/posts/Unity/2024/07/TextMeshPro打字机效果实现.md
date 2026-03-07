---
title: "TextMeshPro打字机效果实现"
author: "mona"
date: 2024-07-26T10:44:00+08:00
categories: ["Unity"]
tags: ["UGUI"]
---

```c#
using System.Collections;
using System.Collections.Generic;
using System.Text.RegularExpressions;
using TMPro;
using UnityEngine;

/// <summary>
/// 打字机输入类型
/// </summary>
public enum E_TypeWriterType
{
    Append, Cover
}

/// <summary>
/// 打字机效果组件
/// </summary>
public class TypeWriter : MonoBehaviour
{

    [Header("文本框对象")]
    public TMP_Text textValue;

    [Header("默认文字间隔时间")]
    public float defaultTime = 0.2f;

    public bool IsTextShowDone => textValue.maxVisibleCharacters == StripHtmlTags(textValue.text).Length;//是否显示完成

    private Coroutine typingCoroutine;//当前携程

    private void Awake()
    {
        if (textValue == null) if (TryGetComponent(out textValue)) WriteText();
    }

    /// <summary>
    /// 输入文本
    /// </summary>
    /// <param name="text"></param>
    /// <param name="speed">如果不指定时间使用默认时间</param>
    public void WriteText(string text = null, E_TypeWriterType type = E_TypeWriterType.Cover, float? time = null)
    {
        if (textValue == null) return;
        if (typingCoroutine != null) StopCoroutine(typingCoroutine);
        switch (type)
        {
            case E_TypeWriterType.Cover:
                textValue.maxVisibleCharacters = 0;
                textValue.text = text ?? textValue.text;
                break;
            case E_TypeWriterType.Append:
                textValue.maxVisibleCharacters = StripHtmlTags(textValue.text).Length;
                textValue.text += text ?? string.Empty;
                break;
        }
        if (gameObject.activeSelf) typingCoroutine = StartCoroutine(TypeText(time == null ? defaultTime : (float)time));
    }

    /// <summary>
    /// 跳过当前输入
    /// </summary>
    public void SkipWriteText()
    {
        if (textValue == null) return;
        if (typingCoroutine != null) StopCoroutine(typingCoroutine);
        if (gameObject.activeSelf) typingCoroutine = StartCoroutine(TypeText(0f));
    }

    /// <summary>
    /// 去除富文本
    /// </summary>
    /// <param name="text"></param>
    /// <returns></returns>
    private string StripHtmlTags(string text) => Regex.Replace(text, "<.*?>", string.Empty);

    /// <summary>
    /// 打字机携程
    /// </summary>
    private IEnumerator TypeText(float time)
    {
        while (textValue.maxVisibleCharacters < StripHtmlTags(textValue.text).Length)
        {
            textValue.maxVisibleCharacters++;
            yield return new WaitForSeconds(time);
        }
    }
}
```

