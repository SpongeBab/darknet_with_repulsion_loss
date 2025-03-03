# darknet_with_repulsion_loss
------
* 关于yolov4的详细信息,官方链接：[darknet](https://github.com/AlexeyAB/darknet)
-----
## 目的
这个项目是对AlexeyAB版darknet源代码的分析。本项目基于/hgpvision/darknet的[项目](https://github.com/hgpvision/darknet) 感谢👏。虽然已不再更新，但是也非常厉害了。
* 本项目最初的目的是对c版yolov4进行分析。
* 逐步实现了repolusion_loss(已实现！[darknrt_with_repo](https://github.com/SpongeBab/darknet/tree/local)）
  需要声明，经过我的实现，我发现repulsion_loss不适合一阶段检测器，只适合Two-stage检测器。详细信息可以阅读我的这篇[博客](https://blog.csdn.net/weixin_40557160/article/details/116174316).

### TODO
1.repulison_loss （done) [Paper]（https://arxiv.org/abs/1711.07752）

---

为了方便区分，我们将最初的[pjreddie/darknet](https://github.com/pjreddie/darknet)称为darknet。将AlexeyAB版的darknet称为yolov4。  

原作者的darknet更多的目的是想写一个类似于pytorch或者tensorflow那样的深度学习框架。无奈一个人的力量比不上大公司啊。但是依然是神一样的🐂！（各种链表，各种指针，尤其整个代码的逻辑，真的是每看一次代码就膜拜一次。。）  

而到了AlexeyAB这里，进一步将这个框架的潜力发扬光大，主要是将检测的功能继续进行开发（因为原版的darknet其实有很多功能，想了解的可以去[darknet官网](pjreddie/darknet)看一看。  

加入了多线程，并发，内存管理，以及其他各种优化，但是不好的地方就是代码混乱了许多，因为是直接在原版代码上修改的，原来的都没删，直接注释掉了。还有的就是直接写了新的文件，就比如detection_layer.c其实替换成了yolo_layer.c(因为我要找一下loss是在哪计算的。网上查找资料，是detection_layer.c，但这是原版darknet的文件。AlexeyAB大神新写了yolo_layer.c。说实话，这个框架到现在已经非常难以修改了，除非是一开始就以这个学习的，从yolov1到v4所有代码堆积在一起，代码非常混乱。虽然得益于c/c++的高效性，检测速度很快。这也是这么多人学习yolo的原因吧。）  

yolov4基本上不需要任何依赖，因为这是一个c模型，甚至没用太多的c++，这也得益于原版的darknet。  

我相信很多人在装pytorch或者tensorflow的时候都可能遇到过这样那样的问题。但是yolov4的安装实在是非常简单（不清楚的欢迎查看我的博客[没得过奖学金的研究生](https://blog.csdn.net/weixin_40557160?spm=1001.2014.3001.5343)）。  

如果是cpu上检测，测试，无需安装任何依赖（opencv只是为了显示图片），可直接编译运行，这样的好处就是非常适合迁移。多平台移植。说白了，为工业而生（很多公司用的都是yolov4。。）。但相信没人会用cpu跑深度学习吧。。GPU部署也非常简单。

---

yolov4和darknet的区别讲完了。
讲一下AlexeyAB版darknet与原版的区别，以及我个人认为比较重要的几个知识点。  

* 1.检测文件的迭代：detection_layer.c->region_layer.c->yolo_layer.c(v4)。
* 2.[最多可以检测目标](https://github.com/AlexeyAB/darknet/issues/3896)
* 3.[yolov3&v4损失函数——yololayer.c解析](https://blog.csdn.net/qq_33614902/article/details/85063287)
* 4.[cfg文件参数含义](https://github.com/AlexeyAB/darknet/wiki/CFG-Parameters-in-the-different-layers)  
* 5.[net参数](https://github.com/AlexeyAB/darknet/wiki/CFG-Parameters-in-the-%5Bnet%5D-section)
    
## 已注释代码
1.主函数。（darknet.c）  
2.训练，测试，计算map（detector.c)  
3.加载cfg文件函数（parser.c)  
4.加载网络。（network.c)(包括卷积，池化，下采样，上采样，route，shorcut，yolo等）  
5.最重要的损失函数。(yolo_layer.c)  
6.IoU,CIoU，GIoU,DIoU,以及改进的IoG（box.c)  
7.NMS。  
 
## 一些小改动
1.只检测一类（人），并对检测到的目标进行计数。  
2.[提交了一些准备训练数据的脚本](https://github.com/xiaoxiaopeng1998/darknet/tree/xiaoPeng/scripts/%E8%AE%AD%E7%BB%83%E5%87%86%E5%A4%87)     
3.
   * 保存日志命令：`darknet.exe detector train cfg/coco.data cfg/yolov4.cfg yolov4.conv.137 >> log/yolov4-train.log`       
   * [可视化loss，绘制matlab图片](https://github.com/SpongeBab/yolo_data_scripts/tree/master/v4loss%E5%8F%AF%E8%A7%86%E5%8C%96)


---

对理解程序有帮助的issues：
* [How many objects are detectable in Yolo](https://github.com/AlexeyAB/darknet/issues/3896#)
