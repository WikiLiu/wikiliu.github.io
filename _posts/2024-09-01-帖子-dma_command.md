---
title: "dma-command"
date: 2024-09-2T15:47:30-04:00
classes: wide
categories:
  - 帖子
tags:
  - GPU
  - OPENGL
---
usermode 填写command在ELite3K层，就是在gl warpper层下具体的实现，功能是由service层给的，但是full是这一层实现的，hardware command在chip common层。


usermode的command分在哪？
resident_list 也会挂再zx_file/driver上
