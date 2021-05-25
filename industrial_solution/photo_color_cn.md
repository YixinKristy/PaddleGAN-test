# 图片上色
针对图片的上色，PaddleGAN提供了[DeOldify](https://github.com/PaddlePaddle/PaddleGAN/blob/develop/docs/zh_CN/apis/apps.md#ppganappsdeoldifypredictor)模型。

## DeOldifyPredictor

[DeOldify](https://github.com/PaddlePaddle/PaddleGAN/blob/develop/docs/zh_CN/apis/apps.md#ppganappsdeoldifypredictor)采用自注意力机制的生成对抗网络，生成器是一个U-NET结构的网络。在图像/视频的上色方面有着较好的效果。

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117925538-fd526a80-b329-11eb-8924-8f2614fcd9e6.png'>
</div>

### 使用方式
**1. API预测**

```
from ppgan.apps import DeOldifyPredictor
deoldify = DeOldifyPredictor()
deoldify.run("/home/aistudio/先烈.jpg") #原图片所在路径
```
*`run`接口为图片/视频通用接口，由于这里对象是图片，可以使用`run_image`的接口

[完整API接口使用说明]()

**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/先烈.jpg \ #原图片路径
                               --process_order DeOldify \ #对原图片处理的顺序
                               --output output_dir #成品图片所在的路径
```

### 在线项目体验
**1. [老北京城影像修复](https://aistudio.baidu.com/aistudio/projectdetail/1161285)**

**2. [PaddleGAN ❤️ 520特辑](https://aistudio.baidu.com/aistudio/projectdetail/1956943?channelType=0&channel=0)**
