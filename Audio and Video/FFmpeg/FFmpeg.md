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

### 1.3 FFmpeg 版本分类

- Static 版：仅包含 3 个体积很大的 `.exe`文件
- Shared 版：包含了 3 个体积较小的 `.exe`文件及**dll 动态库文件**
- Dev 版:包含了**开发用的头文件`.h`** 和 **导入库文件`.lib`**，音视频开发者需要下载。

### 1.4 FFmpeg Ubuntu 源码安装

```
./configure --prefix=/home/annjeff/FFmpeg --enable-shared --enable-gpl --enable-version3 --enable-sdl2 --enable-fontconfig --enable-gnutls --enable-iconv --enable-libass  --enable-libbluray  --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb  --enable-libopus --enable-libshine --enable-libsnappy --enable-libsoxr --enable-libtheora --enable-libtwolame --enable-libvpx --enable-libwavpack --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxml2 --enable-lzma --enable-zlib --enable-gmp --enable-libvorbis --enable-libvo-amrwbenc  --enable-libspeex --enable-libxvid --enable-libaom     --enable-avisynth --enable-libopenmpt
```





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

#### 2.4.0 桌面与音频捕获设备

FFmpeg Devices Documentation: <https://ffmpeg.org/ffmpeg-devices.html#Options-20>

- Mac

    - avfoundation

- Windows

    - dshow (Windows DirectShow input device.)

        - 依赖：screen-capture-recorder (include virtual-audio-capturer)  桌面捕获设备 和 音频捕获设备

        - > ```
            > ffmpeg -list_devices true -f dshow -i dummy
            > ```
            >
            > Print the list of DirectShow supported devices and exit

    - gdigrab (Win32 GDI-based screen capture device, This device allows you to capture a region of the display on Windows.)

- Linux 

    - alsa （Advanced Linux Sound Architecture）
    - X11 grab

#### 2.4.1 录制视频

- Mac

> ffmpeg -f avfoundation -i 1 -r 30 out.yuv
>
> -f：指定使用 avfoundation 库采集数据 （Mac 系统专用的专用于音视频处理）
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

- Windows 抓取屏幕

```
# -t 10 for 10 seconds recording
ffmpeg -f dshow  -i video="screen-capture-recorder"  -r 20 -t 10 screen-capture.mp4
```



- Windows 抓取摄像头

```
ffmpeg -f dshow -i video="Camera" annjeffCamera.mp4
```



#### 2.4.2 录制音频

> ffmpeg -f avfoundation -i :0 out.wav
>
> :0 代表**音频设备**

#### 2.4.3 FFmpeg 是否可以同时录制视频与音频？

当然可以！！！

- Windows 同时录制音视频

> ffmpeg -f dshow -i audio="virtual-audio-capturer":video="screen-capture-recorder" annjeffVideo.mp4
>
> 同时指定了获取位置与视频获取位置，保存格式为：.mp4

### 2.5 分解(demuxer)与复用(muxer)

> 例如，将视频中的音频抽取出来（分解），视频如果抽取出来是未解码的 `H.264`文件，音频抽取出来是未解码的`AAC` 文件。

#### 2.5.1 多媒体格式转换

```
ffmpeg -i out.mp4 -vcodec copy -acodec copy out.flv

-i: 输入文件 
-vcodec copy: 视频编码处理方式
-acodec copy: 音频编码处理方式

copy：意味着不对视频音频参数进行调整

# 抽取 in.mov 视频中的视频，保存为 h.264
ffmpeg -i in.mov -an -vcodec copy out.h264
-an:表示不要音频 [n:代表 no]

# 抽取音频，不要视频(抽取了音频，保存为.aac 格式)
ffmpeg -i in.mov -acodec copy -vn out.aac

```



### 2.6 处理原始数据命令

#### 2.6.1 什么是原始数据

> 这里，我们讲的`原始数据即`**ffmpeg 解码后的数据** ，音频是`PCM 数据`；视频是 `YUV 数据`。

#### 2.6.2 FFmpeg 提出 YUV 数据

```
ffmpeg -i input.mp4 -an -c:v rawvideo -pix_fmt yuv420p out.yuv
# -an:表示结果里不包含音频
# -c:v rawvideo: 对视频使用 rawvideo 进行编码
# -pix_fmt yuv420p：指定像素格式为：yuv420p
# yuv,4:2:0 是音视频最常用的格式
```

> Note: .yuv 视频无法直接使用 ffplay 播放，需要制定视频宽高才能顺利播放
>
> ```
> ffplay -s widthxheight xxx.yuv
> ```

#### 2.6.3 FFmpeg 提取 PCM 数据

```
ffmpeg -i input.mp4 -vn -ar 44100 -ac 2 -f s16le out.pcm
# -vn: 不要视频
# -ar 44100：a 代表 audio r 代表 read，音频的采样率
# -ac 2: a 代表 audio c 代表 channel ，2 指定声音双声道
# -f s16le: 抽取出的音频的存储格式为  sl16le
```

> Note: xxx.pcm 数据同样无法直接使用 ffplay 播放，需要指定 采样率，声道数，数据存储格式
>
> `ffplay -ar 44100 -ac 2 -f s16le out.pcm`

### 2.7 FFmpeg 滤镜命令

> 视频加减水印、画中画、视频裁剪、音频倍速都可通过滤镜实现。

### 2.7.1 FFmpeg 滤镜工作流程

<img src="assets/2.7.1FFmpeg滤镜工作流程.png" style="zoom:61%;" />

 #### 2.7.2 FFmpeg 滤镜命令

```
ffmpeg -i input.mov -vf crop=in_w-200:in_h-200 -c:v libx264 -c:a copy out.mp4
# 可以指定x,y 起始点， 默认是中心点
# -vf crop=in_w-200:in_h-200：-vf 代表视频滤镜，具体是 crop滤镜，in_w 视频宽度 in_w-200 本身视频宽度 - 200
# -c:v libx264：指定视频的编码器
```

### 2.8 裁剪与合并命令

#### 2.8.1 FFmpeg 音视频裁剪

```
ffmpeg -i input.mp4 -ss 00:00:00 -t 10 out.ts
# -ss 00:00:00: 指定开始裁剪时间
# -t 10: 指定裁剪时长
```

#### 2.8.2 FFmpeg 音视频合并

```
ffmpeg -f concat -i inputs.txt out.flv
# -f concat: 告诉 FFmpeg 对后面文件进行拼接
# -i inputs.txt: 文件列表，记录了所有希望合并的文件名字(*.txt 即可)
## *.txt 内容为：'file filename'格式（每一行代表一个单独的文件）

> 将 dream.mp4 与 dream.flv 拼接到一起
> file 'dream.mp4'
> file 'dream.flv'
```

### 2.9 图片 / 视频互转命令

#### 2.9.1 视频转图片

```
ffmpeg -i input.flv -r 1 -f image2 image-%3d.jpeg
# -r 1: 指定转换图片的帧率，一秒转1张图片
# -f image2: 指定多媒体文件转成什么格式，image 是一种协议格式，image2 是第二版
# image-%3d.jpeg: 输出文件，%3d 有3 个数字组成
```

#### 2.9.2 图片转视频

```
ffmpeg -i image-%3d.jpeg out.mp4
# 要转视频的图片命名需要指定固定的格式
```

### 2.10 直播推流 / 拉流

#### 2.10.1 直播推流

```
ffmpeg -re -i input.mp4 -c copy -f flv rtmp://server/live/streamName
# -re: 减慢帧率速度，本地文件播放，会尽可能快的播放。加了 -re 会让帧率减慢
# -c copy:音视频编解码 -c:a 专门制定音频 -c:v 专门制定视频
# -f flv: 指定的推出去流的格式
# server(IP 低着)

```

#### 2.10.2 直播拉流

```
ffmpeg -i rtmp://server/live/streamName -c copy save.mp4
```







