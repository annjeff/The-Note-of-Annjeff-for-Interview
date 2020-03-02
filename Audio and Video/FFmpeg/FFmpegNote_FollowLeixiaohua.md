# 跟雷神学 FFmpeg

## 一、FFmpeg 的使用

### 1.1 FFmpeg 的功能

> FFmpeg 用于食品的转码

## 1.2 最简单的命令

```
ffmpeg -i input.avi -b:v 640k output.ts
# 上述命令将 当前文件夹下的 input.avi 文件转换为 output.ts 文件，并将 output.ts 文件视频的码率设置为 640kbps。
```

### 1.3 命令格式

```
ffmpeg -i {输入文件路径} -b:v {输出视频码率} {输出文件路径}
```

说明：

- 所有的参数都是以**键值对的形式指定的**。

例如：

- 输入文件参数是 `-i` ，而参数值是`文件路径`
- 输出视频码率参数是 `-b:v` ，而参数值是`视频的码率值`

注意：**位于最后面的输出文件路径前面不包含参数名称 ！**

### 1.4 常用命令行参数

|     参数     |                        说明                         |
| :----------: | :-------------------------------------------------: |
|      -h      |                        帮助                         |
| -i filename  |                      输入文件                       |
| -t duration  |           设置处理时间，格式为：hh:mm:ss            |
| -ss position | 设置起始时间（指定从 pos开始操作)，格式为：hh:mm:ss |
| -b:v bitrate |                    设置视频码率                     |
| -b:a bitrate |                    设置音频码率                     |
|    -r fps    |                      设置帧率                       |
|    -s w*h    |      设置帧格式大小，格式为 WxH 例如：1280*720      |
|  -c:v codec  |                   设置视频编码器                    |
|  -c:a codec  |                   设置音频编码器                    |
|   -ar freq   |                   设置音频采样率                    |

### 1.5 练习题 使用 FFmpeg 将 input.xx(任意)转为要求格式

- MKV文件，起始时间为第 10s ,持续时间为 10s。

    ```
    ffmpeg  -i input.xx -ss 10 -t 10 out.mkv
    ```

- MP4 文件，视频编码器为 `libx264`,音频编码器为`libmp3lame`

    ```
    ffmpeg -i cuc_ieschool.mkv -c:v libx264 -c:a libmp3lame out.mp4
    ```

- AVI 文件，分辨率为 640x360，帧率为 25fps，视频码率为 600kbps，音频采样率为 64kbps。

    ```
    ffmpeg -i cuc_ieschool.mkv -s 640x360 -r 25 -b:v 600k out.avi
    ```

- FLV 文件，视频编码器为`libx264`，音频编码器为`libmp3lame`, 音频采样率为 44100Hz。

    ```
    ffmpeg -i cuc_ieschool.mkv -c:v libx264 -c:a libmp3lame -ar 44100 out.flv
    ```

- YUV420P 格式像素数据，分辨率为 640x360。

    ```
    ffmpeg -i cuc_ieschool.mkv -s 640x360 -c:v yuv4 out.yuv 
    ```

- H.264 码流文件，分辨率为 640x360.

    ```
    
    ```

- WAVE 格式音频采样数据。

    ```
    
    ```

- MP3 码流文件

    ```
    
    ```

### 二、ffplay

### 2.1 功能

> ffplay 用于视频的播放

### 2.2 命令格式

```
ffplay {输入文件路径}
ffplay input.avi
```

### 2.3 快捷键

|    快捷键    |      说明      |
| :----------: | :------------: |
|    q，ESC    |      退出      |
|      f       |      全屏      |
|   p, 空格    |      暂停      |
| 鼠标点击屏幕 | 跳转到指定位置 |

---



## 三、FFmpeg 视频解码器

### 3.1 视频解码知识

- 纯净的视频解码流程
    - 压缩编码数据 ----> 像素数据
    - 例如：解码 H.264 就是：H.264码流 ----> YUV
- 一般的视频解码流程
    - 视频码流一般存储在**一定封装格式**(例如：MP4、AVI)格式中通常还包含音频码流等内容。
    - 对于**封装格式中的视频，徐亚先从封装格式中提取视频码流，然后再进行解码。**
    - 例如解码 MKV 格式的视频文件，就是 MKV----> H.264码流---->YUV

### 3.2 