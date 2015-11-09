---
author: yingyun001
layout: post
title: "Python 之 __str__ 与 __repr__"
date: 2015-10-25 15:00
category: Python
tags:
- Python
- 学习
---

* 先来说一说 __str__ 这个函数:

  ~~~ python
  # Stu.py
  class Stu():
     def __init__(self, name, score):
        self.name = name
        self.score = score
      
     def __str__(self):
        return 'className: %s' % self.name # 注意：这里使用的是 ":"
      
     def __repr__(self):
        return 'className = %s' % self.name # 注意：这里使用的是 "="

  s1 = Stu('helen', 100)
  print s1
  print Stu('ply', 100)
  ~~~
  ~~~ bash
  # 运行结果
  className: helen
  className: ply
  ~~~

* 下面看看 __repr__

  ~~~ bash
  >>> class Stu():
  ...    def __init__(self, name, score):
  ...       self.name = name
  ...       self.score = score
  ...    def __str__(self):
  ...       return 'className: %s' % self.name
  ...    def __repr__(self):
  ...       return 'className = %s' % self.name
  ... 
  >>> s1 = Stu('helen', 100)
  >>> s1
  className = helen
  >>> Stu('ply', 100)
  className = ply
  ~~~

* 小节
  
  __str__ 输出的内容是给使用者看的；__repr__ 输出的内容是给开发者看的，多用于调试。
