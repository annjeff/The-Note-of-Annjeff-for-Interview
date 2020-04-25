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
# ERROR: gmp not found
sudo apt-get install  libgmp3-dev

# ERROR: gnutls not found using pkg-config
sudo apt install pkg-config
sudo apt install gnutls-bin

# ERROR: aom >= 1.0.0 not found using pkg-config
sudo aptitude install libaom-dev

# ERROR: libass not found using pkg-config
sudo aptitude install libass-dev

# ERROR: libbluray not found using pkg-config
sudo aptitude install libbluray-dev

# ERROR: libfdk_aac not found
sudo aptitude install libfdk-aac-dev

ERROR: libmp3lame >= 3.98.3 not found
sudo aptitude install libmp3lame-dev

ERROR: libopencore_amrnb not found
sudo aptitude install libopencore-amrnb-dev

ERROR: libopencore_amrwb not found
sudo aptitude install libopencore-amrwb-dev

ERROR: libopenmpt >= 0.2.6557 not found using pkg-config
sudo aptitude install  libopenmpt-dev

ERROR: opus not found using pkg-config
sudo aptitude install libopus-dev

ERROR: shine not found using pkg-config
sudo aptitude install libshine-dev

ERROR: libsnappy not found
sudo aptitude install libsnappy-dev

ERROR: libsoxr not found
sudo aptitude install libsoxr-dev

ERROR: speex not found using pkg-config
sudo aptitude install libspeex-dev

ERROR: libtheora not found
sudo aptitude install libtheora-dev

ERROR: libtwolame not found
sudo aptitude install libtwolame-dev

ERROR: libvo_amrwbenc not found
sudo aptitude install libvo-amrwbenc-dev

libvpx enabled but no supported decoders found
sudo aptitude install llibvpx-dev

ERROR: libwavpack not found
sudo aptitude install libwavpack-dev

ERROR: libwebp >= 0.2.0 not found using pkg-config
sudo aptitude install libwebp-dev

ERROR: libx264 not found
sudo aptitude install libx264-dev

ERROR: x265 not found using pkg-config
sudo aptitude install libx265-dev

ERROR: libxvid not found
sudo aptitude install libxvidcore-dev

ERROR: lzma requested but not found
sudo aptitude install liblzma-dev

ffmpeg: error while loading shared libraries: libavdevice.so.58: cannot open shared object file: No such file or directory
没有指定动态链接库的位置
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/annjeff/FFmpeg/lib

export PATH=$PATH:/home/annjeff/FFmpeg/bin
```



```
./configure --prefix=/home/annjeff/FFmpeg --enable-shared --enable-gpl --enable-version3 --enable-sdl2 --enable-fontconfig --enable-gnutls --enable-iconv --enable-libass  --enable-libbluray  --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb  --enable-libopus --enable-libshine --enable-libsnappy --enable-libsoxr --enable-libtheora --enable-libtwolame --enable-libvpx --enable-libwavpack --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxml2 --enable-lzma --enable-zlib --enable-gmp --enable-libvorbis --enable-libvo-amrwbenc  --enable-libspeex --enable-libxvid --enable-libaom --enable-avisynth --enable-libopenmpt --enable-libfdk-aac --enable-nonfree
```



##  二、FFmpeg 常用命令

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



## 三、Vim 编辑器

### 3.1 处理模式

- 命令模式
    - 拷贝、删除、粘贴等，可以通过 i(insert) / a(append) / o 等键切换到编辑模式
- 编辑模式
    - 编辑字符，通过 Esc 键或者 ctrl + [ 组合键，切换回命令模式

### 3.2 常用命令

- 创建文件
    - vim filename
- 保存文件
    - `:w`
- 关闭文件
    - `:q`
- 拷贝
    - `yy `： 代表拷贝一行
    - `yw `： 拷贝一个词
- 粘贴
    - `p` 将拷贝命令放进缓冲区的内容粘贴出来
- 删除
    - `dd`: 删除一行
    - `dw`: 删除一个词
- 光标移动
    - 左下右上：`h / j / k / l`
    - 跳转到文件头： `gg`
    - 跳转文件尾部:  `G`
    - 移动到行首：`^`
    - 移动到行尾： `$`
    - 按单词移动：向前 `w`、`2w`；向后 n 词：`b（back）`、`2b`
- 查找与替换
    - 查找关键字：`/关键字`
    - 替换：`%s(search)/关键字/替换字/gc(c 指定要用户决定是否替换)`
- 多窗口
    - `split`: 横向切分窗口
    - `vsplit`: 竖向切换窗口
    - 窗口间跳转：` ctrl ww` / `ctrl w [hjkl]`

## 四、C 语言基础

### 4.1 常用基本类型

- short、int、long
- float、double
- char
- void

### 4.2 内存管理

<img src="assets/4.2内存管理.png" style="zoom:75%;" />

```c
#include <stdio.h>                                                      #include <stdlib.h>
int main(int argc, char* argv[])
{
     int* p_a, *p_b;
     printf("addr of p_a:%p\n", &p_a);
     printf("addr of p_b:%p\n\n", &p_b);
    
     // 为指针申请内存空间
     p_a = (int*)malloc(sizeof(int));
     p_b = (int*)malloc(sizeof(int));
     
     printf("p_a 指向的内存区域是:%p\n", p_a);
     printf("p_b 指向的内存区域是:%p\n\n", p_b);
     // 为申请的内存空间赋值
     *p_a = 99;
     *p_b = 88;
     printf("p_a 指向的空间中存储的数据是:%d\n", *p_a);                             
     printf("p_b 指向的空间中存储的数据是:%d\n\n", *p_b);
 
     return 0;
}
```

### 4.3 指针

- 指针的物理意义
    - 它就是内存中的一个地址
- 指针本身运算
    - 指针可进行 `+`、`-`、`*`运算
- 指针所指向内容
    - 指针所指向的内容可以进行`+`、`-`、`*`、`/`

### 4.4 编译命令

```
gcc -g -O2 -o app test.c -I... -L... -l
#-g: 输出文件中的调试信息
#-O: 对输出文件做指令优化，默认 O1 不对指令进行优化 ；O2 做第2 级别优化
#-o: 输出文件名字
#-I: 指定头文件位置 （第三方库头文件，或者自己写的头文件不在一起 需要指定头文件位置）
#-L: 指定第三方库文件位置 
#-l: 指明具体只用的库
```

### 4.5 编译过程

- 预编译
    - 将头文件与源代码拷贝到一起
- 编译
- 链接，动态链接库 / 静态链接库

 ## 五、FFmpeg 多媒体文件处理

### 5.1 初级开发介绍

- FFmpeg 日志的使用及目录操作
- 介绍 FFmpeg 的基本概念及常用的结构体
- 对复用 / 解复用及流操作的各种实践

- FFmpeg 代码结构

    - |      库       |                       功能                        |
        | :-----------: | :-----------------------------------------------: |
        |  libavcodec   |           提供了一系列**编码器**的实现            |
        |  libavformat  | 实现在**流协议**、**容器格式**及**基本 IO 访问**  |
        |   libavutil   | 包括了**hash 器**、**解码器**、和**各种工具函数** |
        |  libavfilter  |            提供了**各种音视频过滤器**             |
        |  libavdevice  |    提供了**访问捕获设备**和**回放设备**的接口     |
        | libswresample |            提供了**混音**和**重采样**             |
        |  libswscale   |         实现了**色彩转换**和**缩放**功能          |

### 5.2 FFmpeg 日志系统

```c
#include <libavutil/log.h>
// 设置日志级别，设置哪些日志需要打印，此处设置 DEBUG 级别日志
// DEDUG 及之上所有日志都会打印出来	Warning、Error、Info 都会打印出来
// DEBUG ：是FFmpeg 系统中最低级别的日志
// 如果设置了 ERROR 级别的日志，DEBUG 级别的日志则不会被打印出来
av_log_set_level(AV_LOG_DEBUG)
// 第一个参数：NULL
// 第二个参数：日志级别
// 第三个及以后与 printf 类似
av_log(NULL, AV_LOG_INFO, "...%s\n", op)

// gcc -g -o ffmpeg_log ffmpeg_log.c -lavutil   
```

- **常用日志级别**
    - AV_LOG_ERROR
    - AV_LOG_WARNING
    - AV_LOG_INFO

### 5.3 FFmpeg 文件与目录操作

> Note: `pkg-config`本身是一个 linux 命令,其功能是**用于获得某一个库/模块的所有编译相关的信息**.
>
> pkg-config --libs libavformat 
>
> // 可以找到 libavformat 库的路径

- 文件的删除

    - avpriv_io_delete()

    - ```c
        #include <stdio.h>
        #include <libavformat/avformat.h>
        #include <libavutil/log.h>                                                             
        
        int main(int argc, char* argv[])
        {
            // 删除一个文件
            int ret;
            ret = avpriv_io_delete("./mydeletedFile.txt");
            if(ret < 0){ 
                av_log(NULL, AV_LOG_ERROR, "Failed to delete file mydeletedFile.txt\n");
                return -1; 
            }   
        
            return 0;
        }
        
        // gcc ffmpeg_file.c -o ffmpeg_file `pkg-config --libs libavformat` -lavutil
        
        // gcc ffmpeg_file.c -o ffmpeg_file `pkg-config --libs libavformat` `pkg-config --libs libavutil`
        ```

- 文件重命名

    - avpriv_io_move()

    - ```c
        #include <stdio.h>                                                                     
        #include <libavformat/avformat.h>
        #include <libavutil/log.h>
        
        int main(int argc, char* argv[])
        {
            // 删除一个文件
            int ret;
            ret = avpriv_io_delete("./mydeletedFile.txt");
            if(ret < 0){
                av_log(NULL, AV_LOG_ERROR, "Failed to delete file mydeletedFile.txt\n");
                return -1;
            }
            // 成功删除文件后也打个 log
            av_log(NULL, AV_LOG_INFO, "Success to delete mydeletedFile.txt\n");
        
            // 文件重命名
            ret = avpriv_io_move("myRename.txt","newName.txt");
            if( ret < 0){
                av_log(NULL, AV_LOG_ERROR, "Failed to rename\n");
                return -1;
            }
            // 成功命名也打个 log                                                              
            av_log(NULL, AV_LOG_INFO, "Success to rename!\n");
        
            return 0;
        }
        
        ```

- 操作目录重要函数

    - avio_open_dir(): 打开目录
    - avio_read_dir(): 读取目录中每一项的信息
    - avio_close_dir(): 关闭目录

- 操作目录重要结构体

    - AVIODirContext: 操作目录的上下文
    - AVIODirEntry: 目录项，用于存放文件名，文件大小信息等

- 实战：实现简单的 `ls` 命令

    - ```c
        #include <libavutil/log.h>                                                                                                                                     
        #include <libavformat/avformat.h>
        
        int main(int argc, char* argv[])
        {
            // 声明上下文
            AVIODirContext  *ctx = NULL;
            int ret;
        
            // 设置日志级别
            av_log_set_level( AV_LOG_INFO);
            
            // 上下文 要操作的目录
            ret = avio_open_dir(&ctx, "./", NULL);
            if (ret < 0){
                // av_err2str(): 函数将错误码数字，转换成字符串
                av_log(NULL, AV_LOG_ERROR, "Cant open dir:%s\n", av_err2str(ret));
                return -1;
            }
            // 成功打开目录， 一项一项访问目录中文件
            AVIODirEntry *entry = NULL; // entry 目录中的每一项
            while( 1 ){
            ret = avio_read_dir(ctx, &entry);
            if (ret < 0){
                av_log(NULL, AV_LOG_ERROR, "Cann't read dir:%s\n", av_err2str(ret));
                goto __fail;
            }
        
            if (!entry){
                break;
            }
        
            av_log(NULL, AV_LOG_INFO, "%12"PRId64" %s \n",
                entry->size,
                entry->name);
            avio_free_directory_entry(&entry);
        
            }
            
        __fail:
            avio_close_dir(&ctx);
                                                                                                                                                                       
            return 0;
        }
        ```

### 5.4 多媒体 文件的基本概念

- 多媒体文件其实是个**容器**，容器中可以放，**音频数据**、**视频数据**、**字幕数据**等。
- 在容器里有很多**流 （Stream / Track）**又称**轨**
- 每种流是由不同的**编码器编码**的
- 从流中读出的数据成为**包**
- 在一个包中包含着**一个或多个帧**
- 几个重要的结构体
    - `AVFormatContext`：格式上下文，连接多个 API 之间的桥梁。例如打开多媒体文件时，首先创建一个 `AVFormatContext` 上下文，把基本信息放到上下文中，这样读取数据流时就要将上下文传到 FFmpeg 函数里，这样它就知道我在处理这个多媒体而不是别的多媒体。
    - `AVStream`:  流，或轨。打开一个多媒体文件后，所有的流都暴露在我们眼前，通过 `AVStream` 读取。
    - `AVPacket`: 包，从流中可以拿到一个个包。

- FFmpeg 操作流数据的基本步骤

    <img src="assets/5.4FFmpeg操作流的基本步骤.png" style="zoom:75%;" />

### 5.5 [实战] 打印音 / 视频 Meta 信息

- `av_register_all()`: 该函数将 FFmpeg 中所定义的**编解码库**、**格式库**、**格式协议**、**网络协议**全部注册到程序中。**所有 FFmpeg 程序必须首先注册**
- `avformat_open_input()`: 打开一个多媒体文件，根据文件后缀名识别多媒体格式，然后输出 `AVFormatContxt`结构体；`avformat_close_input()`
- `av_dump_format()`: 将多媒体文件中的 meta 信息打印出来

```c
#include <libavutil/log.h>
#include <libavformat/avformat.h>

int main(int argc, char* argv[])
{
	// 初始 log 级别
	av_log_set_level(AV_LOG_INFO);
	
	av_register_all();

	AVFormatContext *fmt_ctx = NULL;
	// para1:为上下文结构体分配空间；para2:指定要打开的视频文件；para3:输入文件格式
	// para3: 设置为 NULl，程序则根据文件后缀名推断程序类型; para4:设置命令行参数
	int ret = avformat_open_input(&fmt_ctx, "/home/annjeff/Video/dream.mp4",NULL, NULL );
	if( ret < 0){
		av_log(NULL, AV_LOG_ERROR, "Cann't open file:%s\n", av_err2str(ret));
		return -1;
	}
	// para2:流的索引值；para3:输入文件名字；para4：指定是输入流还是输出流（入0出1）
	av_dump_format(fmt_ctx, 0,"/home/annjeff/Video/dream.mp4",  0);
	avformat_close_input(&fmt_ctx);

	return 0;
}
```

### 5.6 [实战] 抽取音频数据

- `av_init_packet()`: 初始化一个**数据包结构体**，从多媒体文件读取的每一个数据包， 都可以放在**被初始化的包结构体里**。
- `av_find_best_stream()`：在多媒体文件中找到**最好的一路流**
- `av_read_frame()`: 将流中的一个个数据包获取到，获取到数据包就可以做一些处理。
- `av_packet_unref()`: 每次当我们通过 `av_read_frame()` 从我们想要获取的流中读取数据后，数据包就会被增加引用计数。当我们不使用时，调用`av_packet_unref()` 将引用数 -1 ，FFmpeg 观察到引用计数为 0 时，就会释放相应的资源。

```c
#include <stdio.h>
#include <libavutil/log.h>
#include <libavformat/avformat.h>
#include <libavcodec/avcodec.h>

int main(int argc, char* argv[])
{
	int audio_index = 0;
    // 初始 log 级别
    av_log_set_level(AV_LOG_INFO);
    av_register_all();
 
    AVFormatContext *fmt_ctx = NULL;
    
     
    // Step 1: 从命令行获取两个参数
    // argv: 第一个参数是文件名，第二及以后是程序真正的参数
     if (argc < 3){
	    av_log(NULL, AV_LOG_ERROR, "The number of input fileName less than three\n");
        return -1;
    }
     
	char *src = argv[1];
	char *dst = argv[2];

    if (!src || !dst){
        av_log(NULL, AV_LOG_ERROR, "src or dst is NULL! \n");
    }
 	// para1:为上下文结构体分配空间；para2:指定要打开的视频文件；para3:输入文件格式
    // para3: 设置为 NULl，程序则根据文件后缀名推断程序类型; para4:设置命令行参数
    int ret = avformat_open_input(&fmt_ctx, src, NULL, NULL );
	if (ret < 0){
        return -1;
    }
    av_dump_format(fmt_ctx, 0,"/home/annjeff/Video/dream.mp4",  0);
     
    // 打开多媒体成功后，创建一个新的二进制文件
    FILE* dst_fd = fopen(dst,"wb");
    if ( !dst_fd ){
        av_log(NULL, AV_LOG_ERROR, "Cann't open out file!\n");
        avformat_close_input(&fmt_ctx);
        return -1;
    }
    //Step 2: 得到我们想处理的流
    ret = av_find_best_stream(fmt_ctx,AVMEDIA_TYPE_AUDIO, -1,-1, NULL,0 );
   
    if(ret < 0 ){
        av_log(NULL, AV_LOG_ERROR, "Cann't find the best stream!\n");
        avformat_close_input(&fmt_ctx);
        fclose(dst_fd);
        return -1;
    }
    audio_index = ret;
                                                                                    
    AVPacket pkt;
    av_init_packet(&pkt);
    while(av_read_frame(fmt_ctx, &pkt) >= 0 ){
        if ( pkt.stream_index == audio_index){
			int len = fwrite(pkt.data, 1, pkt.size, dst_fd);
			//Step 3: 将获取的音频数据输出到 acc file
			if (len != pkt.size){
				av_log(NULL, AV_LOG_WARNING, "warning, length of data is not equal size of pkt!\n");
        }
    }

	av_packet_unref(&pkt); // 减引用计数
	 
	 }
	avformat_close_input(&fmt_ctx);	
	// 关闭目标文件
	if (dst_fd){
		fclose(dst_fd);
	}
	return 0;
}
```

### 5.7[实战] 抽取视频数据

- `Start code`: 视频抽取后，由一帧一帧的视频帧组成，如何区分每一帧呢？我们可以**在每一帧前加帧的长度信息**、或者在**每一帧前加关键字或特征码**，此处 `Start code`:属于**特征码**
- `SPS / PPS`: 作用是：**解码的视频参数,视频帧的宽高、帧率等存放于 SPS / PPS 中**
- `codec->extradata`: FFmpeg 从 `codec->extradata` 中获取 `SPS / PPS`

```c
#include <stdio.h>
#include <libavutil/log.h>
#include <libavformat/avio.h>
#include <libavformat/avformat.h>

#ifndef AV_WB32
#   define AV_WB32(p, val) do {                 \
        uint32_t d = (val);                     \
        ((uint8_t*)(p))[3] = (d);               \
        ((uint8_t*)(p))[2] = (d)>>8;            \
        ((uint8_t*)(p))[1] = (d)>>16;           \
        ((uint8_t*)(p))[0] = (d)>>24;           \
    } while(0)
#endif

#ifndef AV_RB16
#   define AV_RB16(x)                           \
    ((((const uint8_t*)(x))[0] << 8) |          \
      ((const uint8_t*)(x))[1])
#endif

// 增加特征码
// sps 和 pps 特征码前是 4 个字节，其中是：00 00 00 01
// 非 sps 和 pps 特征码是 3 个字节，其中内容是：00 00 01
static int alloc_and_copy(AVPacket *out,
                          const uint8_t *sps_pps, uint32_t sps_pps_size,
                          const uint8_t *in, uint32_t in_size)
{
    uint32_t offset         = out->size;
    // 区分是 3 字节 还是 4 字节
    uint8_t nal_header_size = offset ? 3 : 4;
    int err;
	
    // 空间扩容
    err = av_grow_packet(out, sps_pps_size + in_size + nal_header_size);
    if (err < 0)
        return err;
	
    // 如果有 sps 或 pps 就先把 sps、pps拷贝过去；再将数据帧拷贝过去
    if (sps_pps)
        memcpy(out->data + offset, sps_pps, sps_pps_size);
    memcpy(out->data + sps_pps_size + nal_header_size + offset, in, in_size);
    // 没有偏移量，即 没sps、pps
    if (!offset) {
        AV_WB32(out->data + sps_pps_size, 1);
    } else {
        (out->data + offset + sps_pps_size)[0] =
        (out->data + offset + sps_pps_size)[1] = 0;
        (out->data + offset + sps_pps_size)[2] = 1;
    }

    return 0;
}

// 读取 sps、pps
int h264_extradata_to_annexb(const uint8_t *codec_extradata, const int codec_extradata_size, AVPacket *out_extradata, int padding)
{
    uint16_t unit_size;
    uint64_t total_size                 = 0;
    uint8_t *out                        = NULL, unit_nb, sps_done = 0,
             sps_seen                   = 0, pps_seen = 0, sps_offset = 0, pps_offset = 0;
    const uint8_t *extradata            = codec_extradata + 4;
    static const uint8_t nalu_header[4] = { 0, 0, 0, 1 };
    int length_size = (*extradata++ & 0x3) + 1; // retrieve length coded size, 用于指示表示编码数据长度所需字节数

    sps_offset = pps_offset = -1;

    /* retrieve sps and pps unit(s) */
    // 后 5 位
    unit_nb = *extradata++ & 0x1f; /* number of sps unit(s) */
    if (!unit_nb) {
        goto pps;
    }else {
        sps_offset = 0;
        sps_seen = 1;
    }

    while (unit_nb--) {
        int err;
	
        unit_size   = AV_RB16(extradata);
        total_size += unit_size + 4;
        if (total_size > INT_MAX - padding) {
            av_log(NULL, AV_LOG_ERROR,
                   "Too big extradata size, corrupted stream or invalid MP4/AVCC bitstream\n");
            av_free(out);
            return AVERROR(EINVAL);
        }
        if (extradata + 2 + unit_size > codec_extradata + codec_extradata_size) {
            av_log(NULL, AV_LOG_ERROR, "Packet header is not contained in global extradata, "
                   "corrupted stream or invalid MP4/AVCC bitstream\n");
            av_free(out);
            return AVERROR(EINVAL);
        }
        if ((err = av_reallocp(&out, total_size + padding)) < 0)
            return err;
        memcpy(out + total_size - unit_size - 4, nalu_header, 4);
        memcpy(out + total_size - unit_size, extradata + 2, unit_size);
        extradata += 2 + unit_size;
pps:
        if (!unit_nb && !sps_done++) {
            unit_nb = *extradata++; /* number of pps unit(s) */
            if (unit_nb) {
                pps_offset = total_size;
                pps_seen = 1;
            }
        }
    }

    if (out)
        memset(out + total_size, 0, padding);

    if (!sps_seen)
        av_log(NULL, AV_LOG_WARNING,
               "Warning: SPS NALU missing or invalid. "
               "The resulting stream may not play.\n");

    if (!pps_seen)
        av_log(NULL, AV_LOG_WARNING,
               "Warning: PPS NALU missing or invalid. "
               "The resulting stream may not play.\n");

    out_extradata->data      = out;
    out_extradata->size      = total_size;

    return length_size;
}

int h264_mp4toannexb(AVFormatContext *fmt_ctx, AVPacket *in, FILE *dst_fd)
{

    AVPacket *out = NULL;
    AVPacket spspps_pkt;

    int len;
    uint8_t unit_type;
    int32_t nal_size;
    uint32_t cumul_size    = 0;
    const uint8_t *buf;
    const uint8_t *buf_end;
    int            buf_size;
    int ret = 0, i;

    out = av_packet_alloc();

    buf      = in->data;
    buf_size = in->size;
    buf_end  = in->data + in->size;

    do {
        ret= AVERROR(EINVAL);
        if (buf + 4 /*s->length_size*/ > buf_end)
            goto fail;

        for (nal_size = 0, i = 0; i<4/*s->length_size*/; i++)
            nal_size = (nal_size << 8) | buf[i];

        buf += 4; /*s->length_size;*/
        unit_type = *buf & 0x1f;

        if (nal_size > buf_end - buf || nal_size < 0)
            goto fail;

        /*
        if (unit_type == 7)
            s->idr_sps_seen = s->new_idr = 1;
        else if (unit_type == 8) {
            s->idr_pps_seen = s->new_idr = 1;
            */
            /* if SPS has not been seen yet, prepend the AVCC one to PPS */
            /*
            if (!s->idr_sps_seen) {
                if (s->sps_offset == -1)
                    av_log(ctx, AV_LOG_WARNING, "SPS not present in the stream, nor in AVCC, stream may be unreadable\n");
                else {
                    if ((ret = alloc_and_copy(out,
                                         ctx->par_out->extradata + s->sps_offset,
                                         s->pps_offset != -1 ? s->pps_offset : ctx->par_out->extradata_size - s->sps_offset,
                                         buf, nal_size)) < 0)
                        goto fail;
                    s->idr_sps_seen = 1;
                    goto next_nal;
                }
            }
        }
        */

        /* if this is a new IDR picture following an IDR picture, reset the idr flag.
         * Just check first_mb_in_slice to be 0 as this is the simplest solution.
         * This could be checking idr_pic_id instead, but would complexify the parsing. */
        /*
        if (!s->new_idr && unit_type == 5 && (buf[1] & 0x80))
            s->new_idr = 1;

        */
        /* prepend only to the first type 5 NAL unit of an IDR picture, if no sps/pps are already present */
        if (/*s->new_idr && */unit_type == 5 /*&& !s->idr_sps_seen && !s->idr_pps_seen*/) {

            h264_extradata_to_annexb( fmt_ctx->streams[in->stream_index]->codec->extradata,
                                      fmt_ctx->streams[in->stream_index]->codec->extradata_size,
                                      &spspps_pkt,
                                      AV_INPUT_BUFFER_PADDING_SIZE);

            if ((ret=alloc_and_copy(out,
                               spspps_pkt.data, spspps_pkt.size,
                               buf, nal_size)) < 0)
                goto fail;
            /*s->new_idr = 0;*/
        /* if only SPS has been seen, also insert PPS */
        }
        /*else if (s->new_idr && unit_type == 5 && s->idr_sps_seen && !s->idr_pps_seen) {
            if (s->pps_offset == -1) {
                av_log(ctx, AV_LOG_WARNING, "PPS not present in the stream, nor in AVCC, stream may be unreadable\n");
                if ((ret = alloc_and_copy(out, NULL, 0, buf, nal_size)) < 0)
                    goto fail;
            } else if ((ret = alloc_and_copy(out,
                                        ctx->par_out->extradata + s->pps_offset, ctx->par_out->extradata_size - s->pps_offset,
                                        buf, nal_size)) < 0)
                goto fail;
        }*/ else {
            if ((ret=alloc_and_copy(out, NULL, 0, buf, nal_size)) < 0)
                goto fail;
            /*
            if (!s->new_idr && unit_type == 1) {
                s->new_idr = 1;
                s->idr_sps_seen = 0;
                s->idr_pps_seen = 0;
            }
            */
        }


        len = fwrite( out->data, 1, out->size, dst_fd);
        if(len != out->size){
            av_log(NULL, AV_LOG_DEBUG, "warning, length of writed data isn't equal pkt.size(%d, %d)\n",
                    len,
                    out->size);
        }
        fflush(dst_fd);

next_nal:
        buf        += nal_size;
        cumul_size += nal_size + 4;//s->length_size;
    } while (cumul_size < buf_size);

    /*
    ret = av_packet_copy_props(out, in);
    if (ret < 0)
        goto fail;

    */
fail:
    av_packet_free(&out);

    return ret;
}

int main(int argc, char *argv[])
{
    int err_code;
    char errors[1024];

    char *src_filename = NULL;
    char *dst_filename = NULL;

    FILE *dst_fd = NULL;

    int video_stream_index = -1;

    //AVFormatContext *ofmt_ctx = NULL;
    //AVOutputFormat *output_fmt = NULL;
    //AVStream *out_stream = NULL;

    AVFormatContext *fmt_ctx = NULL;
    AVPacket pkt;

    //AVFrame *frame = NULL;

    av_log_set_level(AV_LOG_DEBUG);

    if(argc < 3){
        av_log(NULL, AV_LOG_DEBUG, "the count of parameters should be more than three!\n");
        return -1;
    }

    src_filename = argv[1];
    dst_filename = argv[2];

    if(src_filename == NULL || dst_filename == NULL){
        av_log(NULL, AV_LOG_ERROR, "src or dts file is null, plz check them!\n");
        return -1;
    }

    /*register all formats and codec*/
    av_register_all();

    dst_fd = fopen(dst_filename, "wb");
    if (!dst_fd) {
        av_log(NULL, AV_LOG_DEBUG, "Could not open destination file %s\n", dst_filename);
        return -1;
    }

    /*open input media file, and allocate format context*/
    if((err_code = avformat_open_input(&fmt_ctx, src_filename, NULL, NULL)) < 0){
        av_strerror(err_code, errors, 1024);
        av_log(NULL, AV_LOG_DEBUG, "Could not open source file: %s, %d(%s)\n",
               src_filename,
               err_code,
               errors);
        return -1;
    }

    /*dump input information*/
    av_dump_format(fmt_ctx, 0, src_filename, 0);

    /*initialize packet*/
    av_init_packet(&pkt);
    pkt.data = NULL;
    pkt.size = 0;

    /*find best video stream*/
    video_stream_index = av_find_best_stream(fmt_ctx, AVMEDIA_TYPE_VIDEO, -1, -1, NULL, 0);
    if(video_stream_index < 0){
        av_log(NULL, AV_LOG_DEBUG, "Could not find %s stream in input file %s\n",
               av_get_media_type_string(AVMEDIA_TYPE_VIDEO),
               src_filename);
        return AVERROR(EINVAL);
    }

    /*
    if (avformat_write_header(ofmt_ctx, NULL) < 0) {
        av_log(NULL, AV_LOG_DEBUG, "Error occurred when opening output file");
        exit(1);
    }
    */

    /*read frames from media file*/
    while(av_read_frame(fmt_ctx, &pkt) >=0 ){
        if(pkt.stream_index == video_stream_index){
            /*
            pkt.stream_index = 0;
            av_write_frame(ofmt_ctx, &pkt);
            av_free_packet(&pkt);
            */

            h264_mp4toannexb(fmt_ctx, &pkt, dst_fd);

        }

        //release pkt->data
        av_packet_unref(&pkt);
    }

    //av_write_trailer(ofmt_ctx);

    /*close input media file*/
    avformat_close_input(&fmt_ctx);
    if(dst_fd) {
        fclose(dst_fd);
    }

    //avio_close(ofmt_ctx->pb);

    return 0;
}
```

### 5.8 [实战]将 MP4 转成 FLV 格式

- `avformat_alloc_output_context2()`: 分配一个输出文件的上下文空间
- `avformat_free_context()`: 释放分配的输出上下文，防止内存泄漏
- `avformat_new_stream()`： 生成的新多媒体文件也有很多轨，来保存轨
- `avcodec_parameters_copy()`: 在拷贝 流 的同时，还要将其参数（SPS、PPS）等拷贝过去，我们只是换了外壳，里面数据没变，所以需要需要拷贝参数。
- `avformat_write_header()`：所有多媒体格式，都有多美体文件头，本函数就负责生产多媒体文件头。
- `av_write_frame()`：写入数据
- `av_interleaved_write_frame()`：写入数据（用的比较常见）
- `av_write_trailer()`：写入多媒体尾部信息

```c
#include <libavutil/timestamp.h>
#include <libavformat/avformat.h>

static void log_packet(const AVFormatContext *fmt_ctx, const AVPacket *pkt, const char *tag)
{
    AVRational *time_base = &fmt_ctx->streams[pkt->stream_index]->time_base;

    printf("%s: pts:%s pts_time:%s dts:%s dts_time:%s duration:%s duration_time:%s stream_index:%d\n",
           tag,
           av_ts2str(pkt->pts), av_ts2timestr(pkt->pts, time_base),
           av_ts2str(pkt->dts), av_ts2timestr(pkt->dts, time_base),
           av_ts2str(pkt->duration), av_ts2timestr(pkt->duration, time_base),
           pkt->stream_index);
}

int main(int argc, char **argv)
{
    AVOutputFormat *ofmt = NULL;
    AVFormatContext *ifmt_ctx = NULL, *ofmt_ctx = NULL;
    AVPacket pkt;
    const char *in_filename, *out_filename;
    int ret, i;
    int stream_index = 0;
    int *stream_mapping = NULL;
    int stream_mapping_size = 0;

    if (argc < 3) {
        printf("usage: %s input output\n"
               "API example program to remux a media file with libavformat and libavcodec.\n"
               "The output format is guessed according to the file extension.\n"
               "\n", argv[0]);
        return 1;
    }

    in_filename  = argv[1];
    out_filename = argv[2];

    av_register_all();
	// 打开输入文件，生产输入文件的多媒体上下文
    if ((ret = avformat_open_input(&ifmt_ctx, in_filename, 0, 0)) < 0) {
        fprintf(stderr, "Could not open input file '%s'", in_filename);
        goto end;
    }
	// 
    if ((ret = avformat_find_stream_info(ifmt_ctx, 0)) < 0) {
        fprintf(stderr, "Failed to retrieve input stream information");
        goto end;
    }

    av_dump_format(ifmt_ctx, 0, in_filename, 0);
	
    // 创建输出文件的上下文
    avformat_alloc_output_context2(&ofmt_ctx, NULL, NULL, out_filename);
    if (!ofmt_ctx) {
        fprintf(stderr, "Could not create output context\n");
        ret = AVERROR_UNKNOWN;
        goto end;
    }

    stream_mapping_size = ifmt_ctx->nb_streams;
    stream_mapping = av_mallocz_array(stream_mapping_size, sizeof(*stream_mapping));
    if (!stream_mapping) {
        ret = AVERROR(ENOMEM);
        goto end;
    }

    ofmt = ofmt_ctx->oformat;

    for (i = 0; i < ifmt_ctx->nb_streams; i++) {
        AVStream *out_stream;
        AVStream *in_stream = ifmt_ctx->streams[i];
        AVCodecParameters *in_codecpar = in_stream->codecpar;
		// 只保留音频、视频、字母流
        if (in_codecpar->codec_type != AVMEDIA_TYPE_AUDIO &&
            in_codecpar->codec_type != AVMEDIA_TYPE_VIDEO &&
            in_codecpar->codec_type != AVMEDIA_TYPE_SUBTITLE) {
            stream_mapping[i] = -1;
            continue;
        }

        stream_mapping[i] = stream_index++;

        out_stream = avformat_new_stream(ofmt_ctx, NULL);
        if (!out_stream) {
            fprintf(stderr, "Failed allocating output stream\n");
            ret = AVERROR_UNKNOWN;
            goto end;
        }
		// 拷贝参数
        ret = avcodec_parameters_copy(out_stream->codecpar, in_codecpar);
        if (ret < 0) {
            fprintf(stderr, "Failed to copy codec parameters\n");
            goto end;
        }
        out_stream->codecpar->codec_tag = 0;
    }
    av_dump_format(ofmt_ctx, 0, out_filename, 1);

    if (!(ofmt->flags & AVFMT_NOFILE)) {
        ret = avio_open(&ofmt_ctx->pb, out_filename, AVIO_FLAG_WRITE);
        if (ret < 0) {
            fprintf(stderr, "Could not open output file '%s'", out_filename);
            goto end;
        }
    }

    ret = avformat_write_header(ofmt_ctx, NULL);
    if (ret < 0) {
        fprintf(stderr, "Error occurred when opening output file\n");
        goto end;
    }

    while (1) {
        AVStream *in_stream, *out_stream;
		
        // 拿到输入文件的包
        ret = av_read_frame(ifmt_ctx, &pkt);
        if (ret < 0)
            break;

        in_stream  = ifmt_ctx->streams[pkt.stream_index];
        if (pkt.stream_index >= stream_mapping_size ||
            stream_mapping[pkt.stream_index] < 0) {
            av_packet_unref(&pkt);
            continue;
        }

        pkt.stream_index = stream_mapping[pkt.stream_index];
        out_stream = ofmt_ctx->streams[pkt.stream_index];
        log_packet(ifmt_ctx, &pkt, "in");

        /* copy packet */ // 音视频分别进行刻度转换
        pkt.pts = av_rescale_q_rnd(pkt.pts, in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
        pkt.dts = av_rescale_q_rnd(pkt.dts, in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
        pkt.duration = av_rescale_q(pkt.duration, in_stream->time_base, out_stream->time_base);
        pkt.pos = -1;
        log_packet(ofmt_ctx, &pkt, "out");
		
        // 将包写入文件
        ret = av_interleaved_write_frame(ofmt_ctx, &pkt);
        if (ret < 0) {
            fprintf(stderr, "Error muxing packet\n");
            break;
        }
        av_packet_unref(&pkt);
    }

    av_write_trailer(ofmt_ctx);
end:

    avformat_close_input(&ifmt_ctx);

    /* close output */
    if (ofmt_ctx && !(ofmt->flags & AVFMT_NOFILE))
        avio_closep(&ofmt_ctx->pb);
    avformat_free_context(ofmt_ctx);

    av_freep(&stream_mapping);

    if (ret < 0 && ret != AVERROR_EOF) {
        fprintf(stderr, "Error occurred: %s\n", av_err2str(ret));
        return 1;
    }

    return 0;
}

```

### 5.9 [实战] 从 MP4 截取一段视频

- `av_seek_frame()`: 向后拖动一段时间

```c
#include <stdlib.h>
#include <libavutil/timestamp.h>
#include <libavformat/avformat.h>

static void log_packet(const AVFormatContext *fmt_ctx, const AVPacket *pkt, const char *tag)
{
    AVRational *time_base = &fmt_ctx->streams[pkt->stream_index]->time_base;

    printf("%s: pts:%s pts_time:%s dts:%s dts_time:%s duration:%s duration_time:%s stream_index:%d\n",
           tag,
           av_ts2str(pkt->pts), av_ts2timestr(pkt->pts, time_base),
           av_ts2str(pkt->dts), av_ts2timestr(pkt->dts, time_base),
           av_ts2str(pkt->duration), av_ts2timestr(pkt->duration, time_base),
           pkt->stream_index);
}

int cut_video(double from_seconds, double end_seconds, const char* in_filename, const char* out_filename) {
    AVOutputFormat *ofmt = NULL;
    AVFormatContext *ifmt_ctx = NULL, *ofmt_ctx = NULL;
    AVPacket pkt;
    int ret, i;
	
    // 注册所有编解码器
    av_register_all();
	
    // 打开多媒体文件，获得输入文件上下文
    if ((ret = avformat_open_input(&ifmt_ctx, in_filename, 0, 0)) < 0) {
        fprintf(stderr, "Could not open input file '%s'", in_filename);
        goto end;
    }

    if ((ret = avformat_find_stream_info(ifmt_ctx, 0)) < 0) {
        fprintf(stderr, "Failed to retrieve input stream information");
        goto end;
    }

    av_dump_format(ifmt_ctx, 0, in_filename, 0);
	
    // 输出文件，拿到输出文件的上下文
    avformat_alloc_output_context2(&ofmt_ctx, NULL, NULL, out_filename);
    if (!ofmt_ctx) {
        fprintf(stderr, "Could not create output context\n");
        ret = AVERROR_UNKNOWN;
        goto end;
    }

    ofmt = ofmt_ctx->oformat;
	
    // 根据输入文件的上下文，可以得到输入文件有几路流，输出文件创建同样多的流
    // 拷贝每一路流
    for (i = 0; i < ifmt_ctx->nb_streams; i++) {
        AVStream *in_stream = ifmt_ctx->streams[i];
        AVStream *out_stream = avformat_new_stream(ofmt_ctx, in_stream->codec->codec);
        if (!out_stream) {
            fprintf(stderr, "Failed allocating output stream\n");
            ret = AVERROR_UNKNOWN;
            goto end;
        }

        ret = avcodec_copy_context(out_stream->codec, in_stream->codec);
        if (ret < 0) {
            fprintf(stderr, "Failed to copy context from input to output stream codec context\n");
            goto end;
        }
        out_stream->codec->codec_tag = 0;
        if (ofmt_ctx->oformat->flags & AVFMT_GLOBALHEADER)
            out_stream->codec->flags |= AV_CODEC_FLAG_GLOBAL_HEADER;
    }
    av_dump_format(ofmt_ctx, 0, out_filename, 1);

    if (!(ofmt->flags & AVFMT_NOFILE)) {
        ret = avio_open(&ofmt_ctx->pb, out_filename, AVIO_FLAG_WRITE);
        if (ret < 0) {
            fprintf(stderr, "Could not open output file '%s'", out_filename);
            goto end;
        }
    }

    ret = avformat_write_header(ofmt_ctx, NULL);
    if (ret < 0) {
        fprintf(stderr, "Error occurred when opening output file\n");
        goto end;
    }

    //    int indexs[8] = {0};


    //    int64_t start_from = 8*AV_TIME_BASE;
    // 调到指定截取 秒 的地方，还要乘该视频的时间机
    ret = av_seek_frame(ifmt_ctx, -1, from_seconds*AV_TIME_BASE, AVSEEK_FLAG_ANY);
    if (ret < 0) {
        fprintf(stderr, "Error seek\n");
        goto end;
    }

    int64_t *dts_start_from = malloc(sizeof(int64_t) * ifmt_ctx->nb_streams);
    memset(dts_start_from, 0, sizeof(int64_t) * ifmt_ctx->nb_streams);
    int64_t *pts_start_from = malloc(sizeof(int64_t) * ifmt_ctx->nb_streams);
    memset(pts_start_from, 0, sizeof(int64_t) * ifmt_ctx->nb_streams);

    while (1) {
        AVStream *in_stream, *out_stream;

        ret = av_read_frame(ifmt_ctx, &pkt);
        if (ret < 0)
            break;

        in_stream  = ifmt_ctx->streams[pkt.stream_index];
        out_stream = ofmt_ctx->streams[pkt.stream_index];

        log_packet(ifmt_ctx, &pkt, "in");

        if (av_q2d(in_stream->time_base) * pkt.pts > end_seconds) {
            av_free_packet(&pkt);
            break;
        }

        if (dts_start_from[pkt.stream_index] == 0) {
            dts_start_from[pkt.stream_index] = pkt.dts;
            printf("dts_start_from: %s\n", av_ts2str(dts_start_from[pkt.stream_index]));
        }
        if (pts_start_from[pkt.stream_index] == 0) {
            pts_start_from[pkt.stream_index] = pkt.pts;
            printf("pts_start_from: %s\n", av_ts2str(pts_start_from[pkt.stream_index]));
        }

        /* copy packet */
        // 对时间机进行转换
        pkt.pts = av_rescale_q_rnd(pkt.pts - pts_start_from[pkt.stream_index], in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
        pkt.dts = av_rescale_q_rnd(pkt.dts - dts_start_from[pkt.stream_index], in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
        if (pkt.pts < 0) {
            pkt.pts = 0;
        }
        if (pkt.dts < 0) {
            pkt.dts = 0;
        }
        pkt.duration = (int)av_rescale_q((int64_t)pkt.duration, in_stream->time_base, out_stream->time_base);
        pkt.pos = -1;
        log_packet(ofmt_ctx, &pkt, "out");
        printf("\n");

        ret = av_interleaved_write_frame(ofmt_ctx, &pkt);
        if (ret < 0) {
            fprintf(stderr, "Error muxing packet\n");
            break;
        }
        av_free_packet(&pkt);
    }
    free(dts_start_from);
    free(pts_start_from);

    av_write_trailer(ofmt_ctx);
end:

    avformat_close_input(&ifmt_ctx);

    /* close output */
    if (ofmt_ctx && !(ofmt->flags & AVFMT_NOFILE))
        avio_closep(&ofmt_ctx->pb);
    avformat_free_context(ofmt_ctx);

    if (ret < 0 && ret != AVERROR_EOF) {
        fprintf(stderr, "Error occurred: %s\n", av_err2str(ret));
        return 1;
    }

    return 0;
}

int main(int argc, char *argv[]){
    if(argc < 5){
        fprintf(stderr, "Usage: \
                command startime, endtime, srcfile, outfile");
        return -1;
    }

    double startime = atoi(argv[1]);
    double endtime = atoi(argv[2]);
    cut_video(startime, endtime, argv[3], argv[4]);

    return 0;
}

```

### 5.10 [实战] 一个简单的小咖秀

- 将两个多媒体文件中分别抽取音频与视频轨
- 将音频与视频轨合并成一个新文件
- 对音频与视频轨进行裁剪

## 六、FFmpeg 编解码实战

### 6.1 FFmpeg H264 解码

- 添加头文件：`libavcodec/avcodec.h` 
- 常用的数据结构
    - `AVCodec`: 编码器结构体，是 `H264` 还是 `H265`, 是 `AAC` 编码器
    - `AVCodecContext`: 编码器上下文，方便其他函数得知使用哪个**编码器**
    - `AVFrame`: 解码后的帧
- 结构体内存的分配与释放
    - `av_frame_alloc()`: 为 `AVFrame` 分配空间
    - `av_frame_free()`：释放 `AVFrame` 分配的空间
    - `avcodec_alloc_context3()`: 为 `AVCodec`分配上下文
    - `avcodec_free_context()`：释放 `AVCodec` 所占用的空间
- 解码步骤
    - 查找解码器（`avcodec_find_decoder` ）
    - 打开解码器（`avcodec_open2`）
    - 解码（`avcodec_decode_video2`）

### 6.2 H264 编码流程

- 查找编码器（`avcodec_find_encoder_by_name`）
- 设置编码参数，并打开编码器（`avcodec_open2`）
- 编码（`avcodec_encode_video2`）

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <libavcodec/avcodec.h>

#include <libavutil/opt.h>
#include <libavutil/imgutils.h>

int main(int argc, char **argv)
{
    const char *filename, *codec_name;
    const AVCodec *codec;
    AVCodecContext *c= NULL;
    int i, ret, x, y, got_output;
    FILE *f;
    AVFrame *frame;
    AVPacket pkt;
    uint8_t endcode[] = { 0, 0, 1, 0xb7 };

    if (argc <= 2) {
        fprintf(stderr, "Usage: %s <output file> <codec name>\n", argv[0]);
        exit(0);
    }
    filename = argv[1];
    codec_name = argv[2];
	
    // 只注册 avcodec 相关的库，其他的库不加载入程序
    avcodec_register_all();

    /* find the mpeg1video encoder */
    // 拿到具体的编解码器
    codec = avcodec_find_encoder_by_name(codec_name);
    if (!codec) {
        fprintf(stderr, "Codec not found\n");
        exit(1);
    }
	
    // 为编码器申请上下文空间
    c = avcodec_alloc_context3(codec);
    if (!c) {
        fprintf(stderr, "Could not allocate video codec context\n");
        exit(1);
    }

    /* put sample parameters */
    c->bit_rate = 400000;
    /* resolution must be a multiple of two */
    c->width = 352;
    c->height = 288;
    /* frames per second */
    // 设置时间机
    c->time_base = (AVRational){1, 25};
    // 帧率 1s 25 帧
    c->framerate = (AVRational){25, 1};

    /* emit one intra frame every ten frames
     * check frame pict_type before passing frame
     * to encoder, if frame->pict_type is AV_PICTURE_TYPE_I
     * then gop_size is ignored and the output of encoder
     * will always be I frame irrespective to gop_size
     */
    // 一组帧是多少，或者多少帧需要设置一个 关键帧
    c->gop_size = 10;
    // 设置 B 帧 前后参考帧
    c->max_b_frames = 1;
    // 所要编码数据的格式
    c->pix_fmt = AV_PIX_FMT_YUV420P;
	
    // 如果编码器是 H264,设置编码速度为慢，可以提高编码质量
    if (codec->id == AV_CODEC_ID_H264)
        av_opt_set(c->priv_data, "preset", "slow", 0);

    /* open it */
    // 打开编码器
    if (avcodec_open2(c, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        exit(1);
    }

    f = fopen(filename, "wb");
    if (!f) {
        fprintf(stderr, "Could not open %s\n", filename);
        exit(1);
    }

    frame = av_frame_alloc();
    if (!frame) {
        fprintf(stderr, "Could not allocate video frame\n");
        exit(1);
    }
    frame->format = c->pix_fmt;
    frame->width  = c->width;
    frame->height = c->height;

    ret = av_frame_get_buffer(frame, 32);
    if (ret < 0) {
        fprintf(stderr, "Could not allocate the video frame data\n");
        exit(1);
    }

    /* encode 1 second of video */
    for (i = 0; i < 25; i++) {
        av_init_packet(&pkt);
        pkt.data = NULL;    // packet data will be allocated by the encoder
        pkt.size = 0;

        fflush(stdout);

        /* make sure the frame data is writable */
        ret = av_frame_make_writable(frame);
        if (ret < 0)
            exit(1);

        /* prepare a dummy image */
        /* Y */
        for (y = 0; y < c->height; y++) {
            for (x = 0; x < c->width; x++) {
                frame->data[0][y * frame->linesize[0] + x] = x + y + i * 3;
            }
        }
		// 本例题是程序造的，不是来自 Camera
        /* Cb and Cr */
        for (y = 0; y < c->height/2; y++) {
            for (x = 0; x < c->width/2; x++) {
                frame->data[1][y * frame->linesize[1] + x] = 128 + y + i * 2;
                frame->data[2][y * frame->linesize[2] + x] = 64 + x + i * 5;
            }
        }

        frame->pts = i;

        /* encode the image */
        // got_output 可以判断压缩是否成功
        ret = avcodec_encode_video2(c, &pkt, frame, &got_output);
        if (ret < 0) {
            fprintf(stderr, "Error encoding frame\n");
            exit(1);
        }

        if (got_output) {
            printf("Write frame %3d (size=%5d)\n", i, pkt.size);
            fwrite(pkt.data, 1, pkt.size, f);
            av_packet_unref(&pkt);
        }
    }

    /* get the delayed frames */
    for (got_output = 1; got_output; i++) {
        fflush(stdout);

        ret = avcodec_encode_video2(c, &pkt, NULL, &got_output);
        if (ret < 0) {
            fprintf(stderr, "Error encoding frame\n");
            exit(1);
        }

        if (got_output) {
            printf("Write frame %3d (size=%5d)\n", i, pkt.size);
            fwrite(pkt.data, 1, pkt.size, f);
            av_packet_unref(&pkt);
        }
    }

    /* add sequence end code to have a real MPEG file */
    fwrite(endcode, 1, sizeof(endcode), f);
    fclose(f);

    avcodec_free_context(&c);
    av_frame_free(&frame);

    return 0;
}
```

### 6.3 [实战] 视频转图片

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 多媒体文件的相关的 api
#include <libavformat/avformat.h>
// 编解码相关 api
#include <libavcodec/avcodec.h>
#include <libswscale/swscale.h>

#define INBUF_SIZE 4096

#define WORD uint16_t
#define DWORD uint32_t
#define LONG int32_t

#pragma pack(2)
typedef struct tagBITMAPFILEHEADER {
  WORD  bfType;
  DWORD bfSize;
  WORD  bfReserved1;
  WORD  bfReserved2;
  DWORD bfOffBits;
} BITMAPFILEHEADER, *PBITMAPFILEHEADER;


typedef struct tagBITMAPINFOHEADER {
  DWORD biSize;
  LONG  biWidth;
  LONG  biHeight;
  WORD  biPlanes;
  WORD  biBitCount;
  DWORD biCompression;
  DWORD biSizeImage;
  LONG  biXPelsPerMeter;
  LONG  biYPelsPerMeter;
  DWORD biClrUsed;
  DWORD biClrImportant;
} BITMAPINFOHEADER, *PBITMAPINFOHEADER;

void saveBMP(struct SwsContext *img_convert_ctx, AVFrame *frame, char *filename)
{
    //1 先进行转换,  YUV420=>RGB24:
    int w = frame->width;
    int h = frame->height;


    int numBytes=avpicture_get_size(AV_PIX_FMT_BGR24, w, h);
    uint8_t *buffer=(uint8_t *)av_malloc(numBytes*sizeof(uint8_t));


    AVFrame *pFrameRGB = av_frame_alloc();
     /* buffer is going to be written to rawvideo file, no alignment */
    /*
    if (av_image_alloc(pFrameRGB->data, pFrameRGB->linesize,
                              w, h, AV_PIX_FMT_BGR24, pix_fmt, 1) < 0) {
        fprintf(stderr, "Could not allocate destination image\n");
        exit(1);
    }
    */
    avpicture_fill((AVPicture *)pFrameRGB, buffer, AV_PIX_FMT_BGR24, w, h);

    sws_scale(img_convert_ctx, frame->data, frame->linesize,
              0, h, pFrameRGB->data, pFrameRGB->linesize);

    //2 构造 BITMAPINFOHEADER
    BITMAPINFOHEADER header;
    header.biSize = sizeof(BITMAPINFOHEADER);


    header.biWidth = w;
    header.biHeight = h*(-1);
    header.biBitCount = 24;
    header.biCompression = 0;
    header.biSizeImage = 0;
    header.biClrImportant = 0;
    header.biClrUsed = 0;
    header.biXPelsPerMeter = 0;
    header.biYPelsPerMeter = 0;
    header.biPlanes = 1;

    //3 构造文件头
    BITMAPFILEHEADER bmpFileHeader = {0,};
    //HANDLE hFile = NULL;
    DWORD dwTotalWriten = 0;
    DWORD dwWriten;

    bmpFileHeader.bfType = 0x4d42; //'BM';
    bmpFileHeader.bfSize = sizeof(BITMAPFILEHEADER) + sizeof(BITMAPINFOHEADER)+ numBytes;
    bmpFileHeader.bfOffBits=sizeof(BITMAPFILEHEADER)+sizeof(BITMAPINFOHEADER);

    FILE* pf = fopen(filename, "wb");
    fwrite(&bmpFileHeader, sizeof(BITMAPFILEHEADER), 1, pf);
    fwrite(&header, sizeof(BITMAPINFOHEADER), 1, pf);
    fwrite(pFrameRGB->data[0], 1, numBytes, pf);
    fclose(pf);


    //释放资源
    //av_free(buffer);
    av_freep(&pFrameRGB[0]);
    av_free(pFrameRGB);
}

static void pgm_save(unsigned char *buf, int wrap, int xsize, int ysize,
                     char *filename)
{
    FILE *f;
    int i;

    f = fopen(filename,"w");
    fprintf(f, "P5\n%d %d\n%d\n", xsize, ysize, 255);
    for (i = 0; i < ysize; i++)
        fwrite(buf + i * wrap, 1, xsize, f);
    fclose(f);
}

static int decode_write_frame(const char *outfilename, AVCodecContext *avctx,
                              struct SwsContext *img_convert_ctx, AVFrame *frame, int *frame_count, AVPacket *pkt, int last)
{
    int len, got_frame;
    char buf[1024];

    len = avcodec_decode_video2(avctx, frame, &got_frame, pkt);
    if (len < 0) {
        fprintf(stderr, "Error while decoding frame %d\n", *frame_count);
        return len;
    }
    if (got_frame) {
        printf("Saving %sframe %3d\n", last ? "last " : "", *frame_count);
        fflush(stdout);

        /* the picture is allocated by the decoder, no need to free it */
        snprintf(buf, sizeof(buf), "%s-%d.bmp", outfilename, *frame_count);
        
        /*
        pgm_save(frame->data[0], frame->linesize[0],
                 frame->width, frame->height, buf);
        */
        
        saveBMP(img_convert_ctx, frame, buf);
        
        (*frame_count)++;
    }
    if (pkt->data) {
        pkt->size -= len;
        pkt->data += len;
    }
    return 0;
}

int main(int argc, char **argv)
{
    int ret;

    FILE *f;

    const char *filename, *outfilename;

    AVFormatContext *fmt_ctx = NULL;

    const AVCodec *codec;
    AVCodecContext *c= NULL;

    AVStream *st = NULL;
    int stream_index;

    int frame_count;
    AVFrame *frame;

    struct SwsContext *img_convert_ctx;

    //uint8_t inbuf[INBUF_SIZE + AV_INPUT_BUFFER_PADDING_SIZE];
    AVPacket avpkt;

    if (argc <= 2) {
        fprintf(stderr, "Usage: %s <input file> <output file>\n", argv[0]);
        exit(0);
    }
    filename    = argv[1];
    outfilename = argv[2];

    /* register all formats and codecs */
    av_register_all();

    /* open input file, and allocate format context */
    if (avformat_open_input(&fmt_ctx, filename, NULL, NULL) < 0) {
        fprintf(stderr, "Could not open source file %s\n", filename);
        exit(1);
    }

    /* retrieve stream information */
    if (avformat_find_stream_info(fmt_ctx, NULL) < 0) {
        fprintf(stderr, "Could not find stream information\n");
        exit(1);
    }

    /* dump input information to stderr */
    // 打印各种流的信息
    av_dump_format(fmt_ctx, 0, filename, 0);

    av_init_packet(&avpkt);

    /* set end of buffer to 0 (this ensures that no overreading happens for damaged MPEG streams) */
    //memset(inbuf + INBUF_SIZE, 0, AV_INPUT_BUFFER_PADDING_SIZE);
    //
	
    // 将多媒体文件中，想处理的流获取出来
    // 返回值，多媒体流的序列号
    ret = av_find_best_stream(fmt_ctx, AVMEDIA_TYPE_VIDEO, -1, -1, NULL, 0);
    if (ret < 0) {
        fprintf(stderr, "Could not find %s stream in input file '%s'\n",
                av_get_media_type_string(AVMEDIA_TYPE_VIDEO), filename);
        return ret;
    }

    stream_index = ret;
    // 拿到具体流的实例
    st = fmt_ctx->streams[stream_index];

    /* find decoder for the stream */
    codec = avcodec_find_decoder(st->codecpar->codec_id);
    if (!codec) {
        fprintf(stderr, "Failed to find %s codec\n",
                av_get_media_type_string(AVMEDIA_TYPE_VIDEO));
        return AVERROR(EINVAL);
    }


    /* find the MPEG-1 video decoder */
    /*
    codec = avcodec_find_decoder(AV_CODEC_ID_MPEG1VIDEO);
    if (!codec) {
        fprintf(stderr, "Codec not found\n");
        exit(1);
    }
    */
	
    // 分配一个输出文件的编解码器实例
    c = avcodec_alloc_context3(NULL);
    if (!c) {
        fprintf(stderr, "Could not allocate video codec context\n");
        exit(1);
    }

    /* Copy codec parameters from input stream to output codec context */
    if ((ret = avcodec_parameters_to_context(c, st->codecpar)) < 0) {
        fprintf(stderr, "Failed to copy %s codec parameters to decoder context\n",
                av_get_media_type_string(AVMEDIA_TYPE_VIDEO));
        return ret;
    }


    /*
    if (codec->capabilities & AV_CODEC_CAP_TRUNCATED)
        c->flags |= AV_CODEC_FLAG_TRUNCATED; // we do not send complete frames
    */

    /* For some codecs, such as msmpeg4 and mpeg4, width and height
       MUST be initialized there because this information is not
       available in the bitstream. */

    /* open it */
    // 打开编码器
    if (avcodec_open2(c, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        exit(1);
    }

    /*
    f = fopen(filename, "rb");
    if (!f) {
        fprintf(stderr, "Could not open %s\n", filename);
        exit(1);
    }
    */
	
    img_convert_ctx = sws_getContext(c->width, c->height,
                                     c->pix_fmt,
                                     c->width, c->height,
                                     AV_PIX_FMT_RGB24,
                                     SWS_BICUBIC, NULL, NULL, NULL);

    if (img_convert_ctx == NULL)
    {
        fprintf(stderr, "Cannot initialize the conversion context\n");
        exit(1);
    }

    frame = av_frame_alloc();
    if (!frame) {
        fprintf(stderr, "Could not allocate video frame\n");
        exit(1);
    }

    frame_count = 0;
    while (av_read_frame(fmt_ctx, &avpkt) >= 0) {
        /*
        avpkt.size = fread(inbuf, 1, INBUF_SIZE, f);
        if (avpkt.size == 0)
            break;
        */

        /* NOTE1: some codecs are stream based (mpegvideo, mpegaudio)
           and this is the only method to use them because you cannot
           know the compressed data size before analysing it.

           BUT some other codecs (msmpeg4, mpeg4) are inherently frame
           based, so you must call them with all the data for one
           frame exactly. You must also initialize 'width' and
           'height' before initializing them. */

        /* NOTE2: some codecs allow the raw parameters (frame size,
           sample rate) to be changed at any frame. We handle this, so
           you should also take care of it */

        /* here, we use a stream based decoder (mpeg1video), so we
           feed decoder and see if it could decode a frame */
        //avpkt.data = inbuf;
        //while (avpkt.size > 0)
        if(avpkt.stream_index == stream_index){
            if (decode_write_frame(outfilename, c, img_convert_ctx, frame, &frame_count, &avpkt, 0) < 0)
                exit(1);
        }

        av_packet_unref(&avpkt);
    }

    /* Some codecs, such as MPEG, transmit the I- and P-frame with a
       latency of one frame. You must do the following to have a
       chance to get the last frame of the video. */
    avpkt.data = NULL;
    avpkt.size = 0;
    decode_write_frame(outfilename, c, img_convert_ctx, frame, &frame_count, &avpkt, 1);

    fclose(f);

    avformat_close_input(&fmt_ctx);

    sws_freeContext(img_convert_ctx);
    avcodec_free_context(&c);
    av_frame_free(&frame);

    return 0;
}
```

### 6.4 FFmpeg AAC 编码

- 编码流程与视频相同
    - 找到编码器
    - 设置相应参数，打开编码器
    - 进行音频编码
- 编码函数
    - `avcodec_encodec_audio2`: 

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

#include <libavcodec/avcodec.h>

#include <libavutil/channel_layout.h>
#include <libavutil/common.h>
#include <libavutil/frame.h>
#include <libavutil/samplefmt.h>

/* check that a given sample format is supported by the encoder */
static int check_sample_fmt(const AVCodec *codec, enum AVSampleFormat sample_fmt)
{
    const enum AVSampleFormat *p = codec->sample_fmts;

    while (*p != AV_SAMPLE_FMT_NONE) {
        if (*p == sample_fmt)
            return 1;
        p++;
    }
    return 0;
}

/* just pick the highest supported samplerate */
static int select_sample_rate(const AVCodec *codec)
{
    const int *p;
    int best_samplerate = 0;

    if (!codec->supported_samplerates)
        return 44100;

    p = codec->supported_samplerates;
    while (*p) {
        if (!best_samplerate || abs(44100 - *p) < abs(44100 - best_samplerate))
            best_samplerate = *p;
        p++;
    }
    return best_samplerate;
}

/* select layout with the highest channel count */
static int select_channel_layout(const AVCodec *codec)
{
    const uint64_t *p;
    uint64_t best_ch_layout = 0;
    int best_nb_channels   = 0;

    if (!codec->channel_layouts)
        return AV_CH_LAYOUT_STEREO;

    p = codec->channel_layouts;
    while (*p) {
        int nb_channels = av_get_channel_layout_nb_channels(*p);

        if (nb_channels > best_nb_channels) {
            best_ch_layout    = *p;
            best_nb_channels = nb_channels;
        }
        p++;
    }
    return best_ch_layout;
}

int main(int argc, char **argv)
{
    const char *filename;
    const AVCodec *codec;
    AVCodecContext *c= NULL;
    AVFrame *frame;
    AVPacket pkt;
    int i, j, k, ret, got_output;
    FILE *f;
    uint16_t *samples;
    float t, tincr;

    if (argc <= 1) {
        fprintf(stderr, "Usage: %s <output file>\n", argv[0]);
        return 0;
    }
    filename = argv[1];

    /* register all the codecs */
    avcodec_register_all();

    /* find the MP2 encoder */
    //codec = avcodec_find_encoder(AV_CODEC_ID_MP2);
    codec = avcodec_find_encoder_by_name("libfdk_aac");
    if (!codec) {
        fprintf(stderr, "Codec not found\n");
        exit(1);
    }
	
    // 拿到编解码器上下文
    c = avcodec_alloc_context3(codec);
    if (!c) {
        fprintf(stderr, "Could not allocate audio codec context\n");
        exit(1);
    }

    /* put sample parameters */
    c->bit_rate = 64000;

    /* check that the encoder supports s16 pcm input */
    c->sample_fmt = AV_SAMPLE_FMT_S16;
    if (!check_sample_fmt(codec, c->sample_fmt)) {
        fprintf(stderr, "Encoder does not support sample format %s",
                av_get_sample_fmt_name(c->sample_fmt));
        exit(1);
    }

    /* select other audio parameters supported by the encoder */
    // 设置采样率
    c->sample_rate    = select_sample_rate(codec);
    // 设置声道数
    c->channel_layout = select_channel_layout(codec);
    c->channels       = av_get_channel_layout_nb_channels(c->channel_layout);

    /* open it */
    if (avcodec_open2(c, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        exit(1);
    }

    f = fopen(filename, "wb");
    if (!f) {
        fprintf(stderr, "Could not open %s\n", filename);
        exit(1);
    }

    /* frame containing input raw audio */
    frame = av_frame_alloc();
    if (!frame) {
        fprintf(stderr, "Could not allocate audio frame\n");
        exit(1);
    }

    frame->nb_samples     = c->frame_size;
    frame->format         = c->sample_fmt;
    frame->channel_layout = c->channel_layout;

    /* allocate the data buffers */
    ret = av_frame_get_buffer(frame, 0);
    if (ret < 0) {
        fprintf(stderr, "Could not allocate audio data buffers\n");
        exit(1);
    }

    /* encode a single tone sound */
    t = 0;
    tincr = 2 * M_PI * 440.0 / c->sample_rate;
    for (i = 0; i < 200; i++) {
        av_init_packet(&pkt);
        pkt.data = NULL; // packet data will be allocated by the encoder
        pkt.size = 0;

        /* make sure the frame is writable -- makes a copy if the encoder
         * kept a reference internally */
        ret = av_frame_make_writable(frame);
        if (ret < 0)
            exit(1);
        samples = (uint16_t*)frame->data[0];

        for (j = 0; j < c->frame_size; j++) {
            samples[2*j] = (int)(sin(t) * 10000);

            for (k = 1; k < c->channels; k++)
                samples[2*j + k] = samples[2*j];
            t += tincr;
        }
        /* encode the samples */
        ret = avcodec_encode_audio2(c, &pkt, frame, &got_output);
        if (ret < 0) {
            fprintf(stderr, "Error encoding audio frame\n");
            exit(1);
        }
        if (got_output) {
            fwrite(pkt.data, 1, pkt.size, f);
            av_packet_unref(&pkt);
        }
    }

    /* get the delayed frames */
    for (got_output = 1; got_output; i++) {
        ret = avcodec_encode_audio2(c, &pkt, NULL, &got_output);
        if (ret < 0) {
            fprintf(stderr, "Error encoding frame\n");
            exit(1);
        }

        if (got_output) {
            fwrite(pkt.data, 1, pkt.size, f);
            av_packet_unref(&pkt);
        }
    }
    fclose(f);

    av_frame_free(&frame);
    avcodec_free_context(&c);

    return 0;
}
```

---

## 七、SDL 音视频渲染

### 7.1 SDL 介绍、编译安装

- `SDL` （Simple DirectMedia Layer）
- 由 C 语言实现的跨平台的**媒体开源库**
- 多用于**游戏开发**、**模拟器**、**媒体播放器**等多媒体领域
- SDL 网址：`http://www.libsdl.org/`
- 编译安装
    - 生成 Makefile: `configure --prefix=/usr/local`
    - 安装：`sudo make -j 8 && make install` `-j 8`: **8 线程**

### 7.2 使用 SDL 基本步骤

- 添加头文件：`#include <SDL.h`
- 初始化 SDL
- 退出 SDL（销毁 SDL）
- SDL 用于渲染窗口
    - `SDL_Init()`: 初始化 SDL；`SDL_Quit()`: 退出 SDL
    - `SDL_CreateWindow()`: 创建窗口；`SDL_DestroyWindow()`:程序退出时销毁窗口
    - `SDL_CreateRender()`: 创建一个渲染器，将图片，图像，视频帧绘制到窗口里

```c
#include <SDL.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
    // 初始化 SDL 也可以是图片
    SDL_Init(SDL_INIT_VIDEO);
    // 创建一个窗口 
    // 窗口标题；起始点x,y;宽，高；让窗口显示
    SDL_Window *window = NULL;
    window = SDL_CreateWindow("SDL2 Window",200,200,640,480,SDL_WINDOW_SHOWN)
;                                                                             
    if (!window){
        printf("failed to Create window!");
        goto __EXIT;
    }   
                                                                             
    // 销毁窗口
    SDL_DestroyWindow(window);

__EXIT:
    SDL_Quit();

    return 0;
}
```

> gcc firstSDL.c -o firstSDL `pkg-config --libs sdl2`
>
> 
>
> > 报错信息:firstSDL.c:1:10: fatal error: SDL.h: 没有那个文件或目录
> >  #include <SDL.h>
>
> > 报错原因：gcc 没找到 SDL 头文件路径
>
> > 解决办法：`pkg-config --cflags --libs sdl2`
> >
> > > 加上**--cflags 参数**可以得到当前库的头文件路径 
> > >
> > > -D_REENTRANT -I/usr/include/SDL2 -lSDL2
>
> gcc firstSDL.c -o firstSDL `pkg-config --cflags --libs sdl2`
>
> 窗口没有显示，因为我们没有放入渲染器中。

### 7.3 SDL 渲染窗口

我们创建的窗口实际是在**内存中分配的一片空间**，我们真正要在屏幕上显示出来，实际要将内容**推到显卡驱动上**，显卡通过驱动程序将其显示在屏幕上。

- `SDL_CreateRender()`: 创建渲染器；`SDL_DestroyRenderer()`： 销毁渲染器
- `SDL_RenderClear()`： 我们创建了渲染器，将窗口输出到**渲染驱动**，先将**Render清空一下，防止上次遗留残余数据被输出出去**
- `SDL_RenderPresent()`： 真正将数据推送到驱动去，驱动将数据推送到显示器上。

```c
#include <SDL.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
	// 初始化 SDL 也可以是图片
	SDL_Init(SDL_INIT_VIDEO);
	// 创建一个窗口 
	// 窗口标题；起始点x,y;宽，高；让窗口显示
	SDL_Window *window = NULL;
	window = SDL_CreateWindow("SDL2 Window",200,200,640,480,SDL_WINDOW_SHOWN);
	if (!window){
		printf("failed to Create window!");
		goto __EXIT;
	}

	// 创建 Render
	SDL_Renderer *render = NULL;
	render = SDL_CreateRenderer(window, -1, 0);
	SDL_RenderClear(render);
	// 将数据推动到引擎
	SDL_RenderPresent(render);

	// 做个延迟,不然窗口显示会迅速被销毁
	SDL_Delay(30000);

	// 销毁窗口
	SDL_DestroyWindow(window);

__EXIT:
	SDL_Quit();

	return 0;
}
```

### 7.4 SDL 处理事件

- SDL 事件基本原理
    - SDL 将**所有事件都放在一个队列中**
    - 所有对事件的操作，**其实就是对队列的操作**
- SDL 事件种类
    - `SDL_WindowEvent`: 窗口事件
    - `SDL_KeyboardEvent`: 键盘事件
    - `SDL_MouseMotionEvent`: 鼠标事件
- SDL 事件处理
    - `SDL_PollEvent`: 轮寻，等一段时间扫描一次
    - `SDL_WaitEvent`: 事件触发机制，事件来了，发送信号

```c
#include <SDL.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
	// 初始化 SDL 也可以是图片
	SDL_Init(SDL_INIT_VIDEO);
	// 创建一个窗口 
	// 窗口标题；起始点x,y;宽，高；让窗口显示
	SDL_Window *window = NULL;
	window = SDL_CreateWindow("SDL2 Window",200,200,640,480,SDL_WINDOW_SHOWN);
	if (!window){
		printf("failed to Create window!");
		goto __EXIT;
	}

	// 创建 Render
	SDL_Renderer *render = NULL;
	render = SDL_CreateRenderer(window, -1, 0);
	SDL_RenderClear(render);
	// 将数据推动到引擎
	SDL_RenderPresent(render);

	// 处理事件
	int quit = 1;
	SDL_Event event;
	do{
		SDL_WaitEvent(&event);
		switch(event.type){
			case SDL_QUIT:
				quit = 0;
				break;
			default:
				SDL_Log("event type is %d",event.type);
		}
	}while(quit);

	// 销毁窗口
	SDL_DestroyWindow(window);

__EXIT:
	SDL_Quit();

	return 0;
}
// gcc event_SDL.c -o ./build/event_SDL `pkg-config --cflags --libs sdl2`
```

### 7.5 纹理渲染

- SDL 渲染基本原理

    <img src="assets/7.5 纹理渲染.png" style="zoom:61%;" />

- 纹理相关 API
    - `SDL_CreateTexture()`: 创建纹理
        - format: YUV 或 RGB
        - access: Texture 类型、Target , Stream
    - `SDL_DestoryTexture()`: 销毁纹理
- SDL 渲染相关的 API
    - `SDL_SetRenderTarget()`: 交给渲染器去渲染，渲染器目标点（默认是向创建的窗口渲染），因为使用纹理，告诉渲染器，向纹理上渲染
    - `SDL_RenderClear()`: 一种是清屏，另一种是特殊的刷子，设置什么颜色，就用什么颜色刷干净。
    - `SDL_RednerCopy()`: 将纹理拷贝到显卡，显卡拿到纹理数据后计算
    - `SDL_RenderPresent()`：将渲染好的图像显示

```c
#include <SDL.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
	// 初始化 SDL 也可以是图片
	SDL_Init(SDL_INIT_VIDEO);
	// 创建一个窗口 
	// 窗口标题；起始点x,y;宽，高；让窗口显示
	SDL_Window *window = NULL;
	window = SDL_CreateWindow("SDL2 Window",200,200,640,480,SDL_WINDOW_SHOWN);
	if (!window){
		printf("failed to Create window!");
		goto __EXIT;
	}

	// 创建 Render
	SDL_Renderer *render = NULL;
	render = SDL_CreateRenderer(window, -1, 0);
	SDL_RenderClear(render);
	// 将数据推动到引擎
	SDL_RenderPresent(render);

	// 处理事件
	int quit = 1;
	SDL_Event event;

	// 创建纹理
	SDL_Texture *texture = SDL_CreateTexture(render, 
											SDL_PIXELFORMAT_RGBA8888, 
										    SDL_TEXTUREACCESS_TARGET,
											640,
								    		480);
	if (!texture){
		SDL_Log("Failed to Create Texture!");
		goto __RENDER;
	}

	do{
		//SDL_WaitEvent(&event);
		SDL_PollEvent(&event);
		switch(event.type){
			case SDL_QUIT:
				quit = 0;
				break;
			default:
				SDL_Log("event type is %d",event.type);
		}
		SDL_Rect rect;
		rect.w = 30;
		rect.h = 30;
		rect.x = rand() % 600;
		rect.y = rand() % 450;

		// 对纹理填充颜色
		SDL_SetRenderTarget(render, texture);
		SDL_SetRenderDrawColor(render,0 ,0, 0, 0);
		SDL_RenderClear(render);
		// 绘制方块
		SDL_RenderDrawRect(render, &rect);
		SDL_SetRenderDrawColor(render, 255 ,0 , 0,0);
		SDL_RenderFillRect(render, &rect);

		// 将纹理输出到窗口
		// 恢复到默认渲染目标(显卡)
		SDL_SetRenderTarget(render, NULL);
		SDL_RenderCopy(render, texture, NULL, NULL);

		SDL_RenderPresent(render);

	}while(quit);


	// 销毁窗口
__RENDER:
	SDL_DestroyWindow(window);

__EXIT:
	SDL_Quit();

	return 0;
}
// gcc texture_SDL.c -o ./build/texture_SDL `pkg-config --cflags --libs sdl2`
```

### 7.6 实现 YUV 播放器

- SDL 库创建线程
    - `SDL_CreateThread()`：创建线程
        - @param1: fn，线程执行函数
        - @param2: 设置线程名
        - @param3:执行函数参数
- SDL 更新纹理
    - `SDL_UpdateTexture()`:只要告诉它，图片的起始地址和宽度即可
    - `SDL_UpdateYUVTexture()`: 要传递Y、U、V分量地址，虽然参数多，但是这个效率高

```c
#include <stdio.h>
#include <string.h>

#include <SDL.h>

const int bpp=12;

int screen_w=500,screen_h=500;

#define BLOCK_SIZE 4096000

//event message
#define REFRESH_EVENT  (SDL_USEREVENT + 1)
#define QUIT_EVENT  (SDL_USEREVENT + 2)

int thread_exit=0;

int refresh_video_timer(void *udata){

    thread_exit=0;

    while (!thread_exit) {
        SDL_Event event;
        event.type = REFRESH_EVENT;
        SDL_PushEvent(&event);
        SDL_Delay(40);
    }

    thread_exit=0;

    //push quit event
    SDL_Event event;
    event.type = QUIT_EVENT;
    SDL_PushEvent(&event);

    return 0;
}

int main(int argc, char* argv[])
{

    FILE *video_fd = NULL;

    SDL_Event event;
    SDL_Rect rect;

    Uint32 pixformat = 0;

    SDL_Window *win = NULL;
    SDL_Renderer *renderer = NULL;
    SDL_Texture *texture = NULL;

    SDL_Thread *timer_thread = NULL;

    int w_width = 640; w_height = 480;
    const int video_width = 320, video_height = 180;

    Uint8 *video_pos = NULL;
    Uint8 *video_end = NULL;

    unsigned int remain_len = 0;
    unsigned int video_buff_len = 0;
    unsigned int blank space_len = 0;
    Uint8 *video_buf[BLOCK_SIZE];

    const char *path = "test_yuv420p_320x180.yuv";

    const unsigned int yuv_frame_len = video_width * video_height * 12 / 8;

    //initialize SDL
    if(SDL_Init(SDL_INIT_VIDEO)) {
        fprintf( stderr, "Could not initialize SDL - %s\n", SDL_GetError());
        return -1;
    }

    //creat window from SDL
    win = SDL_CreateWindow("YUV Player",
                           SDL_WINDOWPOS_UNDEFINED,
                           SDL_WINDOWPOS_UNDEFINED,
                           w_width, w_height,
                           SDL_WINDOW_OPENGL|SDL_WINDOW_RESIZABLE);
    if(!win) {
        fprintf(stderr, "Failed to create window, %s\n",SDL_GetError());
        goto __FAIL;
    }

    renderer = SDL_CreateRenderer(screen, -1, 0);

    //IYUV: Y + U + V  (3 planes)
    //YV12: Y + V + U  (3 planes)
    pixformat= SDL_PIXELFORMAT_IYUV;

    //create texture for render
    texture = SDL_CreateTexture(renderer,
                                pixformat,
                                SDL_TEXTUREACCESS_STREAMING,
                                video_width,
                                video_height);

    //open yuv file
    video_fd = fopen(path, "r");
    if( !video_fd ){
        fprintf(stderr, "Failed to open yuv file\n");
        goto __FAIL;
    }

    //read block data
    if(video_buff_len = fread(video_buf, 1, BLOCK_SIZE, video_fd) <= 0){
        fprintf(stderr, "Failed to read data from yuv file!\n");
        goto __FAIL;
    }

    //set video positon
    video_pos = video_buf;
    video_end = video_buf + video_buff_len;
    blank_space_len = BLOCK_SIZE - video_buff_len;

    timer_thread = SDL_CreateThread(refresh_video_timer,
                                    NULL,
                                    NULL);

    do {
        //Wait
        SDL_WaitEvent(&event);
        if(event.type==REFRESH_EVENT){
            //not enought data to render
            if((video_pos + yuv_frame_len) > video_end){

                //have remain data, but there isn't space
                remain_len = video_end - video_pos;
                if(remain_len && !black_space_len) {
                    //copy data to header of buffer
                    memcpy(video_buf, video_pos, remain_len);

                    blank_space_len = BLOCK_SIZE - remain_len;
                    video_pos = video_buf;
                    video_end = video_buf + remain_len;
                }

                //at the end of buffer, so rotate to header of buffer
                if(video_end == (video_buf + BLOCK_SIZE)){
                    video_pos = video_buf;
                    video_end = video_buf;
                    blank_space_len = BLOCK_SIZE;
                }

                //read data from yuv file to buffer
                if(video_buff_len = fread(video_end, 1, blank_space_len, video_fd) <= 0){
                    fprintf(stderr, "eof, exit thread!");
                    thread_exit = 1;
                    continue;// to wait event for exiting
                }

                //reset video_end
                video_end += video_buff_len;
            }

            SDL_UpdateTexture( texture, NULL, video_pos, video_width);

            //FIX: If window is resize
            rect.x = 0;
            rect.y = 0;
            rect.w = w_width;
            rect.h = w_height;

            SDL_RenderClear( renderer );
            SDL_RenderCopy( renderer, texture, NULL, &rect);
            SDL_RenderPresent( renderer );

        }else if(event.type==SDL_WINDOWEVENT){
            //If Resize
            SDL_GetWindowSize(win, &w_width, &w_height);
        }else if(event.type==SDL_QUIT){
            thread_exit=1;
        }else if(event.type==QUIT_EVENT){
            break;
        }
    }while ( 1 );

__FAIL:

    //close file
    if(video_fd){
        fclose(video_fd);
    }

    SDL_Quit();

    return 0;
}
```

