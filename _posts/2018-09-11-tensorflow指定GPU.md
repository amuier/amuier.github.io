---
layout:     post
title:      "GPU"
subtitle:   "模型压缩"
date:       2018-09-10 21:00:00
author:     "Amui"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 神经网络
    - Tensorflow
    
---



> 如果服务器有多个GPU，tensorflow会默认使用所有能用的GPU，若想只使用部分GPU，可以设置CUDA\_VISIBLE\_DEVICES。

## 1. 命令行设置使用的GPU ##
在命令行执行python脚本时，可以如下指定要使用的GPU：

    CUDA_VISIBLE_DEVICES=1 python my_script.py

指定不同GPU方式如下：

    CUDA_VISIBLE_DEVICES=1   	 指定使用第一块
    CUDA_VISIBLE_DEVICES=0,1 	 使用0和1
    CUDA_VISIBLE_DEVICES="0,1"   与上面一样，使用0和1，引号是可选的
    CUDA_VISIBLE_DEVICES=0,2,3   使用0,2,3设备，设备1被屏蔽

## 2. 在python代码中指定使用的GPU ##
若要在python代码中设置使用的GPU，使用方式如下：

    import os
	os.environ["CUDA_VISIBLE_DEVICES"] = "0"	 使用GPU0
	os.environ["CUDA_VISIBLE_DEVICES"] = "0，1"  使用GPU0,1

## 3. 设置tensorflow使用的显存大小 ##
### 3.1 指定固定大小 ###
默认tensorflow是使用GPU尽可能多的显存，可以使用per_process_gpu_memory_fraction，来设置使用的GPU显存，表示每个GPU应该拿出多少容量给进程用，0.7代表70%：

    gpu_options = tf.GPUOptions(per_process_gpu_memory_fraction=0.7)
    sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options)) 

	或者
	
	gpu_options=tf.GPUOptions(per_process_gpu_memory_fraction=0.7)
	config=tf.ConfigProto(gpu_options=gpu_options)
	session = tf.Session(config=config, ...)
    
上面分配给tensorflow的GPU显存大小：GPU实际显存*0.7
可以按照需要，设置不同的值，分配显存

### 3.2 按需设置显存 ###
使用allow_growth 这个option，刚一开始分配少量的GPU容量，然后按需慢慢的增加，但是不会释放内存，所以会导致碎片。

    gpu_options = tf.GPUOptions(allow_growth=True)
    sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))

    或者

    config = tf.ConfigProto()
	config.gpu_options.allow_growth = True
	session = tf.Session(config=config, ...)


---

*tf.ConfigProto一般用在创建session的时候，用来对session进行参数配置，如下：*

    with tf.Session(config = tf.ConfigProto(...),...)

*比如，可以指定tf.ConfigProto()如下参数*

	log_device_placement=True : 是否打印设备分配日志
	allow_soft_placement=True ： 如果你指定的设备不存在，允许TF自动分配设备
	tf.ConfigProto(log_device_placement=True,allow_soft_placement=True)

---