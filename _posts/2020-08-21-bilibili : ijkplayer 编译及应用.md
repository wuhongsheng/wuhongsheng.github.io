---
layout: post
title:  "bilibili / ijkplayer 编译及应用"
date:   2020-08-21 08:14:54
categories: 音视频
tags: 音视频 ijkplayer
author: WHS
---

* content
{:toc}

[ijkplayer](https://github.com/bilibili/ijkplayer)是B站开源的基于ffplay实现的视频播放器





### 项目目录
android - android平台上的上层接口封装以及平台相关方法

config - 编译ffmpeg使用的配置文件

extra - 存放编译ijkplayer所需的依赖源文件, 如ffmpeg、openssl等

ijkmedia - 核心代码

ijkj4a - android平台下使用，用来实现c代码调用java层代码，这个文件夹是通过bilibili的另一个开源项目jni4android自动生成的。

* ijkplayer - 播放器数据下载及解码相关

* ijksdl - 音视频数据渲染相关

ios - iOS平台上的上层接口封装以及平台相关方法

tools - 初始化项目工程脚本

### 编译环境
* android-ndk-r10e 
* Mac OS X 10.15.4
* Android Studio 4.0
### 编译步骤
```shell
# 下载源码
git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android 
cd ijkplayer-android
# 切换到最新release分支
git checkout -B latest k0.8.8        
# 下载ffmpeg等第三方依赖 ffmpeg libyuv soundtouch
./init-android.sh                    
cd android/contrib
# 清除ffmpeg编译
./compile-ffmpeg.sh clean      
# 编译ffmpeg；all是编译所有平台，一般Android选择编译armv7a，arm64即可(./compile-ffmpeg.sh armv7a|arm64)
./compile-ffmpeg.sh all              
cd ..
# 编译ijkplayer；all是编译所有平台，一般Android选择编译armv7a，arm64即可(./compile-ijk.sh armv7a|arm64)
./compile-ijk.sh all               
```

### 扩展支持
```
增加rtsp协议支持
export COMMON_FF_CFG_FLAGS="$COMMON_FF_CFG_FLAGS --enable-demuxer=rtsp"
export COMMON_FF_CFG_FLAGS="$COMMON_FF_CFG_FLAGS --enable-demuxer=sdp"
export COMMON_FF_CFG_FLAGS="$COMMON_FF_CFG_FLAGS --enable-demuxer=rtp"

#默认的ijkplayer 的 ffmpeg 不支持pcm格式的音频，需要重新编译ffmpeg，添加对pcm的支持
export COMMON_FF_CFG_FLAGS="$COMMON_FF_CFG_FLAGS --enable-decoder=pcm_alaw"
export COMMON_FF_CFG_FLAGS="$COMMON_FF_CFG_FLAGS --enable-decoder=pcm_ulaw"
export COMMON_FF_CFG_FLAGS="$COMMON_FF_CFG_FLAGS --enable-decoder=pcm_mulaw"
```


### 日志过滤
1.上层播放组件封装
WtVideoView
2.IjkPlayer层
IJKMEDIA

### 常见问题

1.Could not find tag for codec pcm_alaw in stream #1, codec not currently supported
 
[codec not currently supported](https://stackoverflow.com/questions/47495713/could-not-find-tag-for-codec-pcm-alaw-in-stream-1-codec-not-currently-supporte)

ffmepg mp4容器不支持pcm_alaw 修改存储容器为mov

2.SDL_Android_NativeWindow_display_l: ANativeWindow_lock: failed -22

软解情况下，界面卡住
```java
private void setVideoURI(Uri uri, Map<String, String> headers) {
        mUri = uri;
        mHeaders = headers;
        mSeekWhenPrepared = 0;
        //重新设置渲染层
        setRender();
        openVideo();
        requestLayout();
        invalidate();
    }
```

播放Flv无声音
```
Stream #0: not enough frames to estimate rate; consider increasing probesize

IJKMEDIA: Stream
```
[IjkMediaPlayer 播放无声音Bug](https://blog.csdn.net/qq_34420120/article/details/91415061)


因为需要探测视频和音频的格式, 你没声音是在probesize的大小内没获取到音频包, 你可以做的是:

1.增大probesize和analyzeduration
2.修改ffmpeg源码, 在达到probesize大小但还没获取到视频或音频格式的时候自动增大probesize再继续探测
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "probesize", 1024L * 80);


不支持pcm_alaw 
```
Could not find codec parameters for stream 1 (Audio: pcm_alaw, 8000 Hz, 1 channels, 64 kb/s): unspecified sample format

```
在module-lite.s中增加支持

手机锁屏后网络中断导致视频中断

开启 前台服务 或者 申请后台服务白名单

### 扩展截屏、录像方法
1、通过在ffplay层扩至录像及录屏方法，但是不支持硬解下截屏
2、可以在java层使用TextureRenderView获取Bitmap的形式进行截图

### 性能优化
总延时=采集+编码(缓冲)+发送+传输+接收+解码(缓冲)+播放，你先确认下采集编码端有多少延时


```java
 //rtmp秒开配置
 //设置播放前的最大探测时间 
 ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "analyzemaxduration", 1);
 //播放前的探测Size，默认是1M, 改小一点会出画面更快
 ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "probesize", 512L);
 //每处理一个packet之后刷新io上下文
 ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "flush_packets", 1L);
            //是否开启预缓冲，一般直播项目会开启，达到秒开的效果，不过带来了播放丢帧卡顿的体验
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "packet-buffering", 0L);
            //跳帧处理,放CPU处理较慢时，进行跳帧处理，保证播放流程，画面和声音同步
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "framedrop", 1L);
            //rtmp秒开配置 end
            
            
//播放rtst流图像延时550ms左右, 这个延迟是否还能有所提升，目前使用的参数配置项如下：TCP 连接
mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "rtsp_transport", "tcp"); 
//码流分析时间设置，单位为微秒
mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "analyzeduration",500000L); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "fast", 1); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_CODEC, "threads", "auto"); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "infbuf", 1); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "flush_packets", 1L); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "packet-buffering", 0); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_CODEC,"skip_loop_filter",48L); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER,"framedrop",1); 
//减少缓冲
mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "fflags", "nobuffer"); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "probesize", 10240L); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER,"max-fps",25); mediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER,"reconnect",5);




//RTSP优化 
//最大帧率
ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER,"max-fps",30);
//不额外优化        
ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "fast", 1);
//探测大小 默认10240          
ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "probesize", 200);
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "flush_packets", 1);
            //pause output until enough packets have been read after stalling
//是否开启缓冲 
  ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "packet-buffering", 0);
//drop frames when cpu is too slow：0-120   
//丢帧,默认是1
ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "framedrop", 1);
            //automatically start playing on prepared
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "start-on-prepared", 1);
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_CODEC, "skip_loop_filter", 48);//默认值48
            //max buffer size should be pre-read：默认为15*1024*1024
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "max-buffer-size", 0);//最大缓存数
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "min-frames", 2);//默认最小帧数2
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "max_cached_duration", 30);//最大缓存时长
            //input buffer:don't limit the input buffer size (useful with realtime streams)
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "infbuf", 1);//是否限制输入缓存数
            ijkMediaPlayer.setOption(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "fflags", "nobuffer");
```

### 公网直播流测试地址

RTMP协议直播源
香港卫视：rtmp://live.hkstv.hk.lxdns.com/live/hks
RTSP协议直播源
* 大熊兔（VOD）：rtsp://184.72.239.149/vod/mp4://BigBuckBunny_175k.mov
* 国外电视台：rtsp://rtsp-v3-spbtv.msk.spbtv.com/spbtv_v3_1/214_110.sdp

HTTP协议直播源
http://live.hkstv.hk.lxdns.com/live/hks/playlist.m3u8

### 参考链接

[ijkplayer官方地址](https://github.com/Bilibili/ijkplayer)

[ijkplayer中遇到的问题汇总](https://www.jianshu.com/p/496257563f69)

[ijkplayer增加截屏和录像方法](https://github.com/momoxiaoray/ijkplayer)

[音频帧、视频帧及其同步](https://juejin.cn/post/6844904135314128903)




