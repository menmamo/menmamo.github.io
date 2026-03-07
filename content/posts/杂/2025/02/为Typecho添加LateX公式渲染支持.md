---
title: "为Typecho添加LateX公式渲染支持"
author: "mona"
date: 2025-02-26T15:18:00+08:00
categories: ["杂"]
tags: []
---

Typecho原生并不支持`TaTex`公式的渲染，可以通过导入`KaTaX`或`MathJax` 这两个库来实现，本文以`KataX`为例。

此方法不适用于`Pjax`加载页面的主题

## 为文章添加自定义字段按需渲染LaTeX

修改`functions.php`在`themeFields`函数中添加如下代码

```php
//公式支持
    $layout->addItem(new Typecho_Widget_Helper_Form_Element_Radio('isLatex', array(
        1 => _t('启用'),
        0 => _t('关闭')),
        0,_t('LaTeX 渲染'), _t('默认关闭增加网页访问速度，如文章内存在LaTeX语法则需要启用')));
```

## 导入KataX库

下载最新的[Releases](https://github.com/KaTeX/KaTeX/releases)版 解压到 主题文件夹中

在`header.php`中添加(`themeUrl`中的参数填入`KataX`库的位置)

```php+HTML
<?php if ($this->is('post') && $this->fields->isLatex == 1): ?>
<script type="text/javascript" src="<?php $this->options->themeUrl('assets/katex/katex.min.js'); ?>"></script>
<link rel="stylesheet" type="text/css" href="<?php $this->options->themeUrl('assets/katex/katex.min.css'); ?>" />
<script type="text/javascript" src="<?php $this->options->themeUrl('assets/katex/contrib/auto-render.min.js'); ?>"></script>
<?php endif; ?>
```

在`footer.php`中的合适位置添加：

```php+HTML
<?php if ($this->is('post') && $this->fields->isLatex == 1): ?>
<!--LaTeX支持-->
<script type = "text/javascript" >
  document.addEventListener("DOMContentLoaded", function() {
    renderMathInElement(document.querySelector('.post-content'), {
      delimiters: [{
          left: "$$",
          right: "$$",
          display: true
      }, {
          left: "$",
          right: "$",
          display: false
      }],
      ignoredTags: ["script", "noscript", "style", "textarea", "pre", "code"]
    });
  });
</script>
<?php endif; ?>
```

## 效果：

$$
\begin{bmatrix}
{a_{11}}&{a_{12}}&{\cdots}&{a_{1n}}\\
{a_{21}}&{a_{22}}&{\cdots}&{a_{2n}}\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\
{a_{m1}}&{a_{m2}}&{\cdots}&{a_{mn}}\\
\end{bmatrix}
$$

**参考：**

- https://nwdan.com/tutorials/typecho-latex-support.html
- https://github.com/Llf0703/KaTeX4Typecho