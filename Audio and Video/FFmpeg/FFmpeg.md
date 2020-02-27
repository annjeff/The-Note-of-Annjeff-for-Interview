# FFmpeg

## 零、音视频背景知识

### 0.1 音视频的广泛应用

- 直播类：音视频会议、教育直播、娱乐/游戏直播等
- 短视频：抖音、快手、小咖秀
- 网络视频：优酷、腾讯视频、爱奇艺等
- 音视频通话：微信、QQ、Skype 等
- 视频监控
- 人工智能：人脸识别、智能音箱等（关注算法）

### 0.2 播放器的架构

1. 多媒体文件解复用（将多媒体文件，音视频流与视频流拆流）

2. 拿到音频流与视频流，然后分别进行解码。

    - 音频流解码--> PCM 数据
    - 视频流解码--> YUV 数据

3. 音频播放器，播放 PCM数据；

    视频渲染，播放 YUV 数据

4. 音视频同步

**步骤 1、2 主要是由 FFmpeg 负责**

**步骤 3 主要由 SDL 库负责**

<img src="assets/0.2 播放器架构.png" style="zoom:75%;" /> 

### 0.3 渲染流程

1. 视频解码为 YUV 数据，YUV 数据交给**渲染器**，计算出**纹理**
2. 将纹理拷贝到显卡
3. 显卡经过计算，显示到屏幕

<img src="assets/0.3 渲染流程.png" style="zoom:75%;" />

---

## 一、FFmpeg 背景

### 1.1 FFmpeg 的前世今生

- 2000 年，由 FabriceBellard 创建
- 2004 年，Michael Niedermayer 接管
- 2011 年，Libav 从 FFmpeg 分离

### 1.2 FFmpeg 可以做什么？

- FFmpeg 是一个非常优秀的**多媒体框架**
- FFmpeg 可以运行在 Linux、Mac，Windows等平台
- 能够解码，编码，转码，复用，解复用，过滤音视频数据

## 二、FFmpeg 常用命令

### 2.1 FFmpeg 命令分类

<img src="assets/2.1 FFmpeg 命令分类.png" style="zoom:75%;" />

### 2.2 FFmpeg 处理流程

<img src="assets/2.2 FFmpeg 处理音视频流程.png" style="zoom:75%;" />

- 对输入文件（例如 mp4,avi 等封装数据），进行**解复用**操作
- 解复用后，我们得到**编码数据包**（其中包含音频、视频）
- 对编码数据包进行**解码**操作，得到**解码后的数据帧**（和原始摄像头采集的数据有部分不同）
- 对解码后的数据进行**各种各样的处理**（比如720P 转 480P）
- 对处理后的解码后数据帧进行**编码**
- 编码后**封装**编码数据包，得到输出文件

例子：将 Mp4 格式视频，转换为 avi格式

1. 对输入 mp4 文件进行**demuxer**得到编码数据包
2. 因为仅仅转换封装格式，所以可以不用**decoder**再**encoder**
3. 对 **demuxer得到的编码数据包**进行 avi 格式 **muxer**,即可输出 avi 格式视频

例 2：将1080P --> 720P

1. 将 1080P视频**demuxer**,得到**编码数据包**
2. 对编码数据包**decoder**得到**解码后数据帧**
3. 对解码后数据帧进行**降分辨率操作**（将1080P转720P)
4. 对操作后的数据重新**encoder**，得到**编码数据包**
5. 对编码数据包进行**muxer**得到 720P视频输出文件

### 2.3 基本信息查询命令

<img src="assets/2.3 FFmpeg 基本信息查询命令.png" style="zoom:75%;" />

### 2.4 录制命令

#### 2.4.1 录制视频

> ffmpeg -f avfoundation -i 1 -r 30 out.yuv
>
> -f：指定使用 avfoundation 库采集数据 （Mac 系统专用的专用于音视频处理）
>
> ​       Linux 使用 alsa 库（Advanced Linux Sound Architecture）
>
> ​		FFmpeg Devices Documentation
>
> ​		<https://ffmpeg.org/ffmpeg-devices.html#Options-20>
>
> -i: 指定从哪采集数据，它是一个**文件索引号**
>
> > 0: 代表摄像头
> >
> > 1: 代表屏幕（因为我们是录制，所以输入是屏幕）
>
> -r: 指定帧率（一般25帧比较流畅，15帧勉强可以接受，高清电影一般60帧以上）
>
> out.yuv: **yuv数据是一种原始的数据格式**，采集到是什么样子，存成什么样子，无压缩，数据量大。

#### 2.4.2 录制音频

> ffmpeg -f avfoundation -i :0 out.wav
>
> :0 代表**音频设备**

#### 2.4.3 FFmpeg 是否可以同时录制视频与音频？

### 2.5 分解与复用

> 例如，将视频中的音频抽取出来（分解）

#### 2.5.1 多媒体格式转换

> ffmpeg -i out.mp4 -vcodec copy -acodec copy out.flv
>
> -i: 输入文件 
>
> -vcodec copy: 视频编码处理方式
>
> -acodec copy: 音频编码处理方式



