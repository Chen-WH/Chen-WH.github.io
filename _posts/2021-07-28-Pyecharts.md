---
layout:     post
title:      Pyecharts
subtitle:   基于Python调用Echarts
date:       2021-07-28
author:     ChenWH
header-img: img/the-first.png
catalog:   true
tags:

    - Visualization
---



<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      processEscapes: true
    }
  });
</script>
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Pyecharts

----

## 配置

### phantomjs安装

[windows下安装phantomjs实战（亲测有效）-2020年5月2日_lunhui601的博客-CSDN博客](https://blog.csdn.net/lunhui601/article/details/105892613)

[Python - pyecharts：直接将图片保存为 png, pdf, gif 格式的文件 - 共感的艺术 - 博客园 (cnblogs.com)](https://www.cnblogs.com/my-global/articles/12662614.html)

-----

## Snapshot使用

```python
from snapshot_phantomjs import snapshot
from pyecharts.render import make_snapshot

make_snapshot(snapshot,"*.html","*.png")

##支持格式
PNG_FORMAT = "png"
JPG_FORMAT = "jpeg"
GIF_FORMAT = "gif"
PDF_FORMAT = "pdf"
SVG_FORMAT = "svg"
EPS_FORMAT = "eps"
B64_FORMAT = "base64"
```



-----

## 示例

[中文简介 - Document (pyecharts.org)](https://gallery.pyecharts.org/#/README)
