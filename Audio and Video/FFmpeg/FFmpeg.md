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
> pkg-config --list libavformat 
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
            AVIODirContext *ctx = NULL;
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

        

