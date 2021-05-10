# 智能影像修复

PaddleGAN提供一系列影像修复能力，包括**图像/视频上色、图像/视频分辨率提升**，以及**视频补帧**（提高视频播放流畅度）三大功能，使得历史影像恢复往日鲜活的色彩，清晰流畅的呈现于我们眼前。

在未来，PaddleGAN也将不断补充与优化影像修复的能力，比如增加去噪、图像修复等功能，还请大家敬请期待！

## 目录
* 视频修复
  * 上色
  * 超分
  * 补帧

* 照片修复
  * 上色
  * 超分

## 视频修复

### 视频上色
针对视频上色，PaddleGAN提供两种上色模型：DeOldify与DeepRemaster。

#### 1. 上色模型DeOldifyPredictor

[完整API接口使用说明]()

DeOldify采用自注意力机制的生成对抗网络，生成器是一个U-NET结构的网络。在图像/视频的上色方面有着较好的效果。
![](../../imgs/deoldify_network.png)

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
deep_remaster.run("docs/imgs/test_old.jpeg")  #原视频所在路径

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
