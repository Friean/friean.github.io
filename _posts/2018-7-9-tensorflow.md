---
layout: post
title: shell脚本
tags: [文章]
categories: article
---

## TensorFlow深度学习工具

### 计算图

计算图样例：

import tensorflow as tf
a = tf.constant([1.0,2.0],name = "a")
b = tf.constant([3.0,3.0],name = "b")
result = a + b

在Tensorflow中，系统会自动维护一个默认的计算图，通过下面的函数获取默认计算图

tf.get_default_graph() 

通过a.graph可以查看张量所属的计算图

a.graph 

判断是否是默认计算图

print(a.graph is tf.get_default_graph())

### 张量

初始化变量(2x3矩阵，方差2,随机种子 1 保证每次运行结果一致)

weights = tf.Variable(tf.random_normal([2,3],stddev=2),seed = 1) 

初始化常量

x = tf.constant([0.7,0.9])

矩阵乘法 

a = tf.matmul(x,weights)

需要session计算初始值 

sess.run(weights.initializer)

实现所有变量的初始化 

init_op = tf.initialize_all_variables()
sess.run(init_op)


<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js?uid=2159763"></script>
<!-- UY END -->
