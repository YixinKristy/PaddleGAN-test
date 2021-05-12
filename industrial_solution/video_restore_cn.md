# 智能影像修复

PaddleGAN提供一系列影像修复能力，包括**图像/视频上色、图像/视频分辨率提升**，以及**视频补帧**（提高视频播放流畅度）三大功能，使得历史影像恢复往日鲜活的色彩，清晰流畅的呈现于我们眼前。

在未来，PaddleGAN也将不断补充与优化影像修复的能力，比如增加去噪、图像修复等功能，还请大家敬请期待！

**一行代码快速使用影像修复能力：**

```
cd applications
python tools/video-enhance.py --input you_video_path.mp4 --process_order DAIN DeOldify EDVR --output output_dir
```

**参数**

- `--input (str)`: 输入的视频路径。
- `--output (str)`: 输出的视频路径。
- `--process_order`: 调用的模型名字和顺序，比如输入为 `DAIN DeOldify EDVR`，则会顺序调用 `DAINPredictor` `DeOldifyPredictor` `EDVRPredictor` 。

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117925494-e9a70400-b329-11eb-9f38-a48ef946a3a4.gif' width='600'>
</div>

## 目录
* 视频修复
  * [上色](#视频上色)
  * [超分](#视频分辨率提升)
  * [补帧](#视频补帧)

* 照片修复
  * [上色](#图片上色)
  * [超分](#图片超分)

* [在线体验](#在线体验)
  * [老北京视频修复](https://aistudio.baidu.com/aistudio/projectdetail/1161285)


## 视频修复

### 视频上色
针对视频上色，PaddleGAN提供两种上色模型：DeOldify与DeepRemaster。

#### 1. 上色模型DeOldifyPredictor

[完整API接口使用说明]()

DeOldify采用自注意力机制的生成对抗网络，生成器是一个U-NET结构的网络。在图像/视频的上色方面有着较好的效果。
![](../../imgs/deoldify_network.png)

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117925538-fd526a80-b329-11eb-8924-8f2614fcd9e6.png'>
</div>

```
ppgan.apps.DeOldifyPredictor(output='output', weight_path=None, render_factor=32)
```
#### 参数

- `output (str，可选的)`: 输出的文件夹路径，默认值：`output`.
- `weight_path (None，可选的)`: 载入的权重路径，如果没有设置，则从云端下载默认的权重到本地。默认值：`None`。
- `artistic (bool)`: 是否使用偏"艺术性"的模型。"艺术性"的模型有可能产生一些有趣的颜色，但是毛刺比较多。
- `render_factor (int)`: 会将该参数乘以16后作为输入帧的resize的值，如果该值设置为32，
                         则输入帧会resize到(32 * 16, 32 * 16)的尺寸再输入到网络中。


#### 使用方式
**1. API预测**

```
from ppgan.apps import DeOldifyPredictor
deoldify = DeOldifyPredictor()
deoldify.run("/home/aistudio/Peking_input360p_clip6_5s.mp4") #原视频所在路径
```
*`run`接口为图片/视频通用接口，由于这里对象是视频，可以使用`run_video`的接口

**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/Peking_input360p_clip6_5s.mp4 \ #原视频路径
                               --process_order DeOldify \ #对原视频处理的顺序
                               --output output_dir #成品视频所在的路径
```



#### 2. 上色模型DeepRemasterPredictor

[完整API接口使用说明]()

DeepRemaster 模型目前只能用于对视频上色，基于时空卷积神经网络和自注意力机制。并且能够根据输入的任意数量的参考帧对视频中的每一帧图片进行上色。
![](../../imgs/remaster_network.png)

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117925558-05120f00-b32a-11eb-9727-d1c0d5814dc5.png'>
</div>

```
ppgan.apps.DeepRemasterPredictor(
                                output='output',
                                weight_path=None,
                                colorization=False,
                                reference_dir=None,
                                mindim=360):
```

#### 参数

- `output (str，可选的)`: 输出的文件夹路径，默认值：`output`.
- `weight_path (None，可选的)`: 载入的权重路径，如果没有设置，则从云端下载默认的权重到本地。默认值：`None`。
- `colorization (bool)`: 是否对输入视频上色，如果选项设置为 `True` ，则参考帧的文件夹路径也必须要设置。默认值：`False`。
- `reference_dir (bool)`: 参考帧的文件夹路径。默认值：`None`。
- `mindim (bool)`: 输入帧重新resize后的短边的大小。默认值：360。

#### 使用方式
**1. API预测**

```
from ppgan.apps import DeepRemasterPredictor
deep_remaster = DeepRemasterPredictor()
deep_remaster.run("docs/imgs/test_old.jpeg")  #原视频所在路径

```

**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/Peking_input360p_clip6_5s.mp4 \ #原视频路径
                               --process_order DeepRemaster \ #对原视频处理的顺序
                               --output output_dir #成品视频所在的路径
```
### 视频分辨率提升

针对视频超分，PaddleGAN提供了两种模型，RealSR和EDVR。

#### 1. 超分模型RealSR

[完整API接口使用说明]()

RealSR模型通过估计各种模糊内核以及实际噪声分布，为现实世界的图像设计一种新颖的真实图片降采样框架。基于该降采样框架，可以获取与真实世界图像共享同一域的低分辨率图像。并且提出了一个旨在提高感知度的真实世界超分辨率模型。对合成噪声数据和真实世界图像进行的大量实验表明，该模型能够有效降低了噪声并提高了视觉质量。

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117925551-02afb500-b32a-11eb-9a11-14e484daa953.png'>
</div>

```
ppgan.apps.RealSRPredictor(output='output', weight_path=None)
```
#### 参数

- `output (str，可选的)`: 输出的文件夹路径，默认值：`output`.
- `weight_path (None，可选的)`: 载入的权重路径，如果没有设置，则从云端下载默认的权重到本地。默认值：`None`。


#### 使用方式
**1. API预测**

```
from ppgan.apps import DeepRemasterPredictor
deep_remaster = DeepRemasterPredictor()
deep_remaster.run("/home/aistudio/Peking_input360p_clip6_5s.mp4")  #原视频所在路径

```

**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/Peking_input360p_clip6_5s.mp4 \ #原视频路径
                               --process_order DeepRemaster \ #对原视频处理的顺序
                               --output output_dir #成品视频所在的路径
```


#### 2. 超分模型EDVR

[完整API接口使用说明]()

EDVR模型提出了一个新颖的视频具有增强可变形卷积的还原框架：第一，为了处理大动作而设计的一个金字塔，级联和可变形（PCD）对齐模块，使用可变形卷积以从粗到精的方式在特征级别完成对齐；第二，提出时空注意力机制（TSA）融合模块，在时间和空间上都融合了注意机制，用以增强复原的功能。

EDVR模型是一个基于连续帧的超分模型，能够有效利用帧间的信息，速度比RealSR模型快。

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117925546-004d5b00-b32a-11eb-9af9-3b19d666de01.png'>
</div>

```
ppgan.apps.EDVRPredictor(output='output', weight_path=None)
```

#### 参数

- `output (str，可选的)`: 输出的文件夹路径，默认值：`output`.
- `weight_path (None，可选的)`: 载入的权重路径，如果没有设置，则从云端下载默认的权重到本地。默认值：`None`。


#### 使用方式
**1. API预测** 

目前API预测方式只支持在静态图下运行，需加上启动静态图命令，后续会支持动态图，敬请期待~

```
paddle.enable_static()

from ppgan.apps import EDVRPredictor
sr = EDVRPredictor()
# 测试一个视频文件
sr.run("/home/aistudio/Peking_input360p_clip6_5s.mp4")  #原视频所在路径

paddle.disable_static()

```

**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/Peking_input360p_clip6_5s.mp4 \ #原视频路径
                               --process_order EDVR \ #对原视频处理的顺序，此处注意“EDVR”四个字母都需大写
                               --output output_dir #成品视频所在的路径
```
### 视频补帧

针对老视频的流畅度提升，PaddleGAN提供了DAIN模型接口。

#### 补帧模型DAIN

[完整API接口使用说明]()

DAIN 模型通过探索深度的信息来显式检测遮挡。并且开发了一个深度感知的流投影层来合成中间流。在视频补帧方面有较好的效果。

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117925889-76ea5880-b32a-11eb-9917-17cea27b64d0.png'>
</div>

```
ppgan.apps.DAINPredictor(
                        output='output',
                        weight_path=None,
                        time_step=None,
                        use_gpu=True,
                        remove_duplicates=False)
```
#### 参数

- `output (str，可选的)`: 输出的文件夹路径，默认值：`output`.
- `weight_path (None，可选的)`: 载入的权重路径，如果没有设置，则从云端下载默认的权重到本地。默认值：`None`。
- `time_step (int)`: 补帧的时间系数，如果设置为0.5，则原先为每秒30帧的视频，补帧后变为每秒60帧。
- `remove_duplicates (bool，可选的)`: 是否删除重复帧，默认值：`False`.

#### 使用方式
**1. API预测** 

除了定义输入视频路径外，此接口还需定义time_step，同时，目前API预测方式只支持在静态图下运行，需加上启动静态图命令，后续会支持动态图，敬请期待~

```
paddle.enable_static()

from ppgan.apps import DAINPredictor
dain = DAINPredictor(output='output', time_step=0.5)
# 测试一个视频文件
dain.run("/home/aistudio/Peking_input360p_clip6_5s.mp4",)
paddle.disable_static()

paddle.disable_static()

```

**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/Peking_input360p_clip6_5s.mp4 \ #原视频路径
                               --process_order DAIN \
                               --output output_dir #成品视频所在的路径
```
## 图片修复

### 图片上色
针对图片上色，PaddleGAN提供了DeOldify模型。

#### 1. 上色模型DeOldifyPredictor

[完整API接口使用说明]()

#### 使用方式
**1. API预测**

```
from ppgan.apps import DeOldifyPredictor
deoldify = DeOldifyPredictor()
deoldify.run("/home/aistudio/先烈.jpg") #原图片所在路径
```
*`run`接口为图片/视频通用接口，由于这里对象是图片，可以使用`run_image`的接口

**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/先烈.jpg \ #原图片路径
                               --process_order DeOldify \ #对原图片处理的顺序
                               --output output_dir #成品图片所在的路径
```


### 图片超分
针对图片分辨率提升，PaddleGAN提供了RealSR、ESRGAN、LESRCNN三种模型。接下来将介绍模型预测方式。

#### 1. 超分模型RealSR

[完整API接口使用说明]()

```
ppgan.apps.RealSRPredictor(output='output', weight_path=None)
```
#### 参数

- `output (str，可选的)`: 输出的文件夹路径，默认值：`output`.
- `weight_path (None，可选的)`: 载入的权重路径，如果没有设置，则从云端下载默认的权重到本地。默认值：`None`。


#### 使用方式
**1. API预测**

```
from ppgan.apps import DeepRemasterPredictor
deep_remaster = DeepRemasterPredictor()
deep_remaster.run("docs/imgs/先烈.jpg")  #原图片所在路径
```
**2. 命令行预测**

```
!python applications/tools/video-enhance.py --input /home/aistudio/Peking_input360p_clip6_5s.mp4 \ #原视频路径
                               --process_order DeepRemaster \ #对原视频处理的顺序
                               --output output_dir #成品视频所在的路径
```
#### 2. 超分模型ESRGAN 
目前ESRGAN还未封装为API供开发者们使用，因此如需使用模型，可下载使用：

| 模型 | 数据集 | 下载地址 |
|---|---|---|
| esrgan_psnr_x4  | DIV2K | [esrgan_psnr_x4](https://paddlegan.bj.bcebos.com/models/esrgan_psnr_x4.pdparams)
| esrgan_x4  | DIV2K | [esrgan_x4](https://paddlegan.bj.bcebos.com/models/esrgan_x4.pdparams)

#### 3. 超分模型LESRCNN（敬请期待）
目前LESRCNN还未封装为API供开发者们使用，因此如需使用模型，可下载使用：

| 模型 | 数据集 | 下载地址 |
|---|---|---|
| lesrcnn_x4  | DIV2K | [lesrcnn_x4](https://paddlegan.bj.bcebos.com/models/lesrcnn_x4.pdparams)

## 在线体验
为了让大家快速体验影像修复的能力，PaddleGAN在飞桨人工智能学习与实训平台AI Studio准备了完整的实现步骤及详细代码，同时，AI Studio还为大家准备了免费的GPU算力，大家登录即可亲自实践 **[老北京城影像修复](https://aistudio.baidu.com/aistudio/projectdetail/1161285)** 的项目，快上手体验吧！

<div align='center'>
  <img src='https://user-images.githubusercontent.com/48054808/117924001-a0ee4b80-b327-11eb-8ab8-189f4afb8c23.png'>
</div>
