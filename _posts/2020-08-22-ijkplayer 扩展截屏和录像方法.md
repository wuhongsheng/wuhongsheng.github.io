---
layout: post
title:  "ijkplayer 扩展截屏和录像方法"
date:   2020-08-22 08:14:54
categories: 音视频
tags: 音视频 ijkplayer
author: WHS
---

* content
{:toc}

[ijkplayer](https://github.com/bilibili/ijkplayer)是B站开源的基于ffplay实现的视频播放器，源码不支持截图和录像，因此需要从底层扩展






* 修改ff_ffplay.h 

```C
//申明录制视频、截图相关方法
int       ffp_start_record(FFPlayer *ffp, const char *file_name);
int       ffp_stop_record(FFPlayer *ffp);
int       ffp_record_file(FFPlayer *ffp, AVPacket *packet);
int       ffp_record_file2(FFPlayer *ffp, AVPacket *packet);
void      ffp_get_current_frame_l(FFPlayer *ffp, uint8_t *frame_buf);
AVStream            *m_vStream;
```

* 修改ff_ffplay.c

```c
void ffp_get_current_frame_l(FFPlayer *ffp, uint8_t *frame_buf)
        {
            ALOGD("=============>start snapshot\n");

            VideoState *is = ffp->is;
            Frame *vp;
            int i = 0, linesize = 0, pixels = 0;
            uint8_t *src;

            vp = &is->pictq.queue[is->pictq.rindex];
            int height = vp->bmp->h;
            int width = vp->bmp->w;

            ALOGD("=============>%d X %d === %d\n", width, height, vp->bmp->pitches[0]);

            // copy data to bitmap in java code
            linesize = vp->bmp->pitches[0];
            src = vp->bmp->pixels[0];
            pixels = width * 4;
            for (i = 0; i < height; i++) {
                memcpy(frame_buf + i * pixels, src + i * linesize, pixels);
            }

            ALOGD("=============>end snapshot\n");
        }

int ffp_start_record(FFPlayer *ffp, const char *file_name)
        {
            assert(ffp);

            VideoState *is = ffp->is;
            //文件输出格式
            ffp->m_ofmt_ctx = NULL;
            ffp->m_ofmt = NULL;
            ffp->is_record = 0;
            ffp->record_error = 0;

            if (!file_name || !strlen(file_name)) {
                av_log(ffp, AV_LOG_ERROR, "filename is invalid");
                goto end;
            }

            if (!is || !is->ic|| is->paused || is->abort_request) {
                av_log(ffp, AV_LOG_ERROR, "is,is->ic,is->paused is invalid");
                goto end;
            }

            if (ffp->is_record) { // 已经在录制
                av_log(ffp, AV_LOG_ERROR, "recording has started");
                goto end;
            }
            // 初始化一个用于输出的AVFormatContext结构体 
            avformat_alloc_output_context2(&ffp->m_ofmt_ctx, NULL, NULL, file_name);
            if (!ffp->m_ofmt_ctx) {
                av_log(ffp, AV_LOG_ERROR, "Could not create output context filename is %s\n", file_name);
                goto end;
            }
            ffp->m_ofmt = ffp->m_ofmt_ctx->oformat;

            for (int i = 0; i < is->ic->nb_streams; i++) {
                AVStream *in_stream = is->ic->streams[i];

                //AVCodecParameters *in_codecpar = in_stream->codecpar;
                //if (in_codecpar->codec_type != AVMEDIA_TYPE_AUDIO &&
                //            in_codecpar->codec_type != AVMEDIA_TYPE_VIDEO &&
                //            in_codecpar->codec_type != AVMEDIA_TYPE_SUBTITLE) {
                //            continue;
                //        }
                AVStream *out_stream = avformat_new_stream(ffp->m_ofmt_ctx, avcodec_find_decoder(in_stream->codecpar->codec_id));
                if(in_stream->codecpar->codec_type == AVMEDIA_TYPE_VIDEO){
                    m_vStream = out_stream;
                }
                if (!out_stream) {
                    av_log(ffp, AV_LOG_ERROR, "Failed allocating output stream\n");
                    goto end;
                }
                if (avcodec_parameters_copy(out_stream->codecpar, in_stream->codecpar) < 0) {
                    av_log(ffp, AV_LOG_ERROR, "Failed to copy codec parameters\n");
                    goto end;
                }

                if(in_stream->codecpar->codec_type == AVMEDIA_TYPE_VIDEO){
                   in_stream->codec->time_base.den =25;
                   out_stream->codec->time_base.den =25;
                   av_log(ffp, AV_LOG_ERROR, "VIDEO time_base.den:%d time_base.num:%d\n",in_stream->codec->time_base.den,in_stream->codec->time_base.num);
                  //编码器中宽高为0时则从解析器中提取,修复摄像头录制时报dimensions not set的错误
                   if (out_stream->codecpar->width == 0 || out_stream->codecpar->height == 0){
                       if (in_stream->parser){
                           out_stream->codecpar->width = in_stream->parser->width;
                           out_stream->codecpar->height = in_stream->parser->height;
                           av_log(ffp, AV_LOG_DEBUG, "set width from parser：%d", out_stream->codecpar->width );
                           av_log(ffp, AV_LOG_DEBUG, "set height from parser:%d", out_stream->codecpar->height );
                       }
                   }
                }else if (in_stream->codecpar->codec_type == AVMEDIA_TYPE_AUDIO) {
                    av_log(ffp, AV_LOG_ERROR, "AUDIO time_base.den:%d time_base.num:%d\n",in_stream->codec->time_base.den,in_stream->codec->time_base.num);
                }
                out_stream->codec->codec_tag = 0;
                out_stream->codecpar->codec_tag = 0;
                if (ffp->m_ofmt_ctx->oformat->flags & AVFMT_GLOBALHEADER) {
                    out_stream->codec->flags |= CODEC_FLAG_GLOBAL_HEADER;
                }
            }
            //打印输出信息
            av_dump_format(ffp->m_ofmt_ctx, 0, file_name, 1);
            // 打开输出文件
            if (!(ffp->m_ofmt->flags & AVFMT_NOFILE)) {
                if (avio_open(&ffp->m_ofmt_ctx->pb, file_name, AVIO_FLAG_WRITE) < 0) {
                    av_log(ffp, AV_LOG_ERROR, "Could not open output file '%s'", file_name);
                    goto end;
                }
            }

            // 写视频文件头
            // 根据文件名的后缀写相应格式的文件头
            //s：用于输出的AVFormatContext。
            //options：额外的选项，目前没有深入研究过，一般为NULL。
            if (avformat_write_header(ffp->m_ofmt_ctx, NULL) < 0) {
                av_log(ffp, AV_LOG_ERROR, "Error occurred when opening output file\n");
                goto end;
            }

            ffp->is_record = 1;
            ffp->record_error = 0;
            //互斥锁
            pthread_mutex_init(&ffp->record_mutex, NULL);

            return 0;
        end:
            ffp->record_error = 1;
            return -1;
        }

int ffp_record_file(FFPlayer *ffp, AVPacket *packet)
        {
            assert(ffp);
            VideoState *is = ffp->is;
            int ret = 0;
            AVStream *in_stream;
            AVStream *out_stream;
            if (ffp->is_record) {
                if (packet == NULL) {
                    ffp->record_error = 1;
                    av_log(ffp, AV_LOG_ERROR, "packet == NULL");
                    return -1;
                }
                AVPacket *pkt = (AVPacket *)av_malloc(sizeof(AVPacket)); // 与看直播的 AVPacket分开，不然卡屏
                av_new_packet(pkt, 0);
               if (0 == av_packet_ref(pkt, packet)) {
                    pthread_mutex_lock(&ffp->record_mutex);
                     //录制的第一帧，时间从0开始
                    if (!ffp->is_first) {
                        ffp->is_first = 1;
                        pkt->pts = 0;
                        pkt->dts = 0;
                    } else {
                        //之后的每一帧都要减去，点击开始录制时的值，这样的时间才是正确的
                      if (pkt->stream_index == AVMEDIA_TYPE_VIDEO) {
                            pkt->pts = llabs(pkt->pts - ffp->start_v_pts);
                            pkt->dts = llabs(pkt->dts - ffp->start_v_dts);
                            printf("AVMEDIA_TYPE_VIDEO: %lld pkt->pts: %lld pkt->dts: %lld pkt->stream_index:%d pkt->duration:%lld\n",ffp->start_v_pts,pkt->pts,pkt->dts,pkt->stream_index,pkt->duration);
                        } else if (pkt->stream_index == AVMEDIA_TYPE_AUDIO) {
                            pkt->pts = llabs(pkt->pts - ffp->start_a_pts);
                            pkt->dts = llabs(pkt->dts - ffp->start_a_dts);
                            printf("AVMEDIA_TYPE_AUDIO: %lld pkt->pts: %lld pkt->dts: %lld pkt->stream_index:%d pkt->duration:%lld\n",ffp->start_a_pts,pkt->pts,pkt->dts,pkt->stream_index,pkt->duration);
                        }
                    }
                    in_stream  = is->ic->streams[pkt->stream_index];
                    out_stream = ffp->m_ofmt_ctx->streams[pkt->stream_index];
                    // 将packet中的各时间值从输入流封装格式时间基转换到输出流封装格式时间基,跟下面方法一样
                    av_packet_rescale_ts(pkt, in_stream->time_base, out_stream->time_base);
                    // 转换PTS/DTS
                    //pkt->pts = av_rescale_q_rnd(pkt->pts, in_stream->time_base, out_stream->time_base, (AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX));
                    //pkt->dts = av_rescale_q_rnd(pkt->dts, in_stream->time_base, out_stream->time_base, (AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX));
                    //pkt->duration = av_rescale_q(pkt->duration, in_stream->time_base, out_stream->time_base);
                    //pkt->pos = -1;
                    av_log(ffp, AV_LOG_ERROR, "last duration: %lld",pkt->duration);
                    if(pkt->stream_index == AVMEDIA_TYPE_AUDIO){
                        pkt->duration = 0;
                    }
                     // 写入一个AVPacket到输出文件,如果遇到报错的帧，那么直接跳过ret赋值0，跳过该帧
                    if ((ret = av_interleaved_write_frame(ffp->m_ofmt_ctx, pkt))< 0) {
                        av_log(ffp, AV_LOG_ERROR, "Error muxing packet %d",ret);
                        ret = 0;
                    }
                    av_packet_unref(pkt);
                    pthread_mutex_unlock(&ffp->record_mutex);
                } else {
                    av_log(ffp, AV_LOG_ERROR, "av_packet_ref == NULL");
                }
            }
            return ret;
        }
        

int ffp_stop_record(FFPlayer *ffp)
    {
        assert(ffp);
        if (ffp->is_record) {
            ffp->is_record = 0;
            pthread_mutex_lock(&ffp->record_mutex);
            if (ffp->m_ofmt_ctx != NULL) {
                ////写输出流（文件）的文件尾
                av_write_trailer(ffp->m_ofmt_ctx);
                if (ffp->m_ofmt_ctx && !(ffp->m_ofmt->flags & AVFMT_NOFILE)) {
                    avio_close(ffp->m_ofmt_ctx->pb);
                }
                //.关闭上下文
                avformat_free_context(ffp->m_ofmt_ctx);
                ffp->m_ofmt_ctx = NULL;
                ffp->is_first = 0;
            }
            pthread_mutex_unlock(&ffp->record_mutex);
            pthread_mutex_destroy(&ffp->record_mutex);
            av_log(ffp, AV_LOG_DEBUG, "stopRecord ok\n");
        } else {
            av_log(ffp, AV_LOG_ERROR, "don't need stopRecord\n");
        }
        return 0;
    }
```

* 修复ff_ffplay_def.h

FFPlayer 结构中增加如下属性

```c
  AVFormatContext *m_ofmt_ctx;        // 用于输出的AVFormatContext结构体
    AVOutputFormat *m_ofmt;
    pthread_mutex_t record_mutex;       // 锁
    int is_record;                      // 是否在录制
    int record_error;
    int is_first;                       // 第一帧数据
    int64_t start_pts;                  // 开始录制时pts
    int64_t start_dts;                  // 开始录制时dts

    int64_t start_v_pts;                // 开始录制时pts 视频
    int64_t start_v_dts;                // 开始录制时dts 视频
    int64_t start_a_pts;                // 开始录制时pts 音频
    int64_t start_a_dts;                // 开始录制时dts 音频
```

* 修改ijkplayer.c

```c
int ijkmp_start_record(IjkMediaPlayer *mp,const char *file_name)
{
    assert(mp);
    MPTRACE("ijkmp_startRecord()\n");
    pthread_mutex_lock(&mp->mutex);
    int retval = ffp_start_record(mp->ffplayer,file_name);
    pthread_mutex_unlock(&mp->mutex);
    MPTRACE("ijkmp_startRecord()=%d\n", retval);
    return retval;
}


int ijkmp_stop_record(IjkMediaPlayer *mp)
{
    assert(mp);
    MPTRACE("ijkmp_stopRecord()\n");
    pthread_mutex_lock(&mp->mutex);
    int retval = ffp_stop_record(mp->ffplayer);
    pthread_mutex_unlock(&mp->mutex);
    MPTRACE("ijkmp_stopRecord()=%d\n", retval);
    return retval;
}

static void ijkmp_get_current_frame_l(IjkMediaPlayer *mp, uint8_t *frame_buf)
{
    ffp_get_current_frame_l(mp->ffplayer, frame_buf);
}

void ijkmp_get_current_frame(IjkMediaPlayer *mp, uint8_t *frame_buf)
{
    assert(mp);
    pthread_mutex_lock(&mp->mutex);
    ijkmp_get_current_frame_l(mp, frame_buf);
    pthread_mutex_unlock(&mp->mutex);
}

```

* 修改 ijkplayer_jni.c

```c
  + #include <android/bitmap.h>

   static jboolean
IjkMediaPlayer_getCurrentFrame(JNIEnv *env, jobject thiz, jobject bitmap)
{
    jboolean retval = JNI_TRUE;
    IjkMediaPlayer *mp = jni_get_media_player(env, thiz);
    JNI_CHECK_GOTO(mp, env, NULL, "mpjni: getCurrentFrame: null mp", LABEL_RETURN);

    uint8_t *frame_buffer = NULL;

    if (0 > AndroidBitmap_lockPixels(env, bitmap, (void **)&frame_buffer)) {
        (*env)->ThrowNew(env, "java/io/IOException", "Unable to lock pixels.");
        return JNI_FALSE;
    }

    ijkmp_get_current_frame(mp, frame_buffer);

    if (0 > AndroidBitmap_unlockPixels(env, bitmap)) {
        (*env)->ThrowNew(env, "java/io/IOException", "Unable to unlock pixels.");
        return JNI_FALSE;
    }

LABEL_RETURN:
    ijkmp_dec_ref_p(&mp);
    return retval;
}


static jint
IjkMediaPlayer_startRecord(JNIEnv *env, jobject thiz, jstring file)
{
    jint retval = 0;
    IjkMediaPlayer *mp = jni_get_media_player(env, thiz);
    JNI_CHECK_GOTO(mp, env, NULL, "mpjni: startRecord: null mp", LABEL_RETURN);
    const char *nativeString = (*env)->GetStringUTFChars(env, file, 0);
    retval = ijkmp_start_record(mp, nativeString);

LABEL_RETURN:
    ijkmp_dec_ref_p(&mp);
    return retval;
}

static jint
IjkMediaPlayer_stopRecord(JNIEnv *env, jobject thiz)
{
    jint retval = 0;
    IjkMediaPlayer *mp = jni_get_media_player(env, thiz);
    JNI_CHECK_GOTO(mp, env, NULL, "mpjni: stopRecord: null mp", LABEL_RETURN);

    retval = ijkmp_stop_record(mp);

LABEL_RETURN:
    ijkmp_dec_ref_p(&mp);
    return retval;
}


  { "getCurrentFrame",        "(Landroid/graphics/Bitmap;)Z", (void *) IjkMediaPlayer_getCurrentFrame },
  { "startRecord",            "(Ljava/lang/String;)I", (void *) IjkMediaPlayer_startRecord },
  { "stopRecord",             "()I",      (void *) IjkMediaPlayer_stopRecord },
```

* 修改 android/ijkplayer/ijkplayer-java/src/main/java/tv/danmaku/ijk/media/player/IjkMediaPlayer.java

```java
  public native int startRecord(String var1);
  public native int stopRecord();
  public native boolean getCurrentFrame(Bitmap var1);
```


* 修改Android.mk

```
- LOCAL_LDLIBS += -llog -landroid
+ LOCAL_LDLIBS += -llog -landroid -ljnigraphics
```
### 参考链接

[ijkplayer增加截屏和录像方法](https://github.com/momoxiaoray/ijkplayer)





