
### 命令行基本语法
```shell
ffmpeg [global opetions] [input file options] -i input_file [output file options] putput_file
```

可以使用ffplay命令播放视频, 例如
```shell
ffplay -i input.mp4
```
推出播放界面使用q键

ffmpeg的滤镜是一个处理视频的函数, 可以对视频进行各种操作, 例如
```shell
ffmpeg -i input.mp4 -vf "scale=320:240" output.mp4
```
滤镜 filterchains 和 filtergraphs
filterchains: 多个滤镜组成的链, 例如
```shell
# 将input.mp4的视频进行hqdn3d处理, 然后比较处理前后的视频,使用filterchains需要两条命令
ffmpeg -i input.mp4 -vf hqdn3d,pad=2*iw output.mp4
ffmpeg -i output.mp4 -i input.mp4 -filter_complex overlay=w compare.mp4
```
filtergraphs: 多个filterchains组成的图, 例如
```shell
# 将input.mp4的视频进行hqdn3d处理, 然后比较处理前后的视频，使用filtergraphs需要一条命令
ffplay -i input.mp4 -vf split[a][b];[a]pad=2*iw[A];[b]hqdn[B];[A][B]overlay=w compare.mp4
```

### 设置视频的帧率
在输出文件前使用 -r
```shell
ffmpeg -i input.mp4 -r 30 output.mp4
```
使用fps filter
```shell
ffmpeg -i input.mp4 -vf fps=fps=30 output.mp4
```

还有一些预定义的帧率值

### bit rate
决定整个视频和音频的质量，它指定每个时间单位处理的位数，在 FFmpeg 中，比特率以每秒位数表示。
存储1秒编码流所使用的总位数，称为比特率。
比特率越高，视频的质量就越好，但是文件的大小也会更大。
比特率越低，视频的质量就越差，但是文件的大小也会更小。
比特率的单位是 bps（比特每秒），也可以用 kbps（千比特每秒）或 Mbps（兆比特每秒）来表示。
```shell
# 设置每秒1.5Mbit 的比特率
ffmpeg -i input.mp4 -b:v 1.5M output.mp4
```

![type_of_bit_rate](ffmpeg_imgs/type_of_bit_rate.png)


```shell
# 设定恒定比特率
ffmpeg -i input.mp4 -b 0.5M -minrate 0.5M -maxrate 0.5M -bufsize 1M output.mp4
```

```shell
# 设定输出文件的最大大小
ffmpeg -i input.mp4 -fs 10M output.mp4
```
### 计算文件大小
![file_size_calculation](ffmpeg_imgs/file_size_calculation.png)

### ffmpeg主要结构体
https://blog.csdn.net/weixin_53223301/article/details/144544863

### AVFormatContext结构体
```c
typedef struct AVFormatContext {
    const AVClass *av_class; // 用于日志记录和选项的类
    const struct AVInputFormat *iformat; // 输入容器格式
    const struct AVOutputFormat *oformat; // 输出容器格式
    void *priv_data; // 格式私有数据
    AVIOContext *pb; // I/O 上下文
    int ctx_flags; // 标志信号流属性
    unsigned int nb_streams; // 流的数量
    AVStream **streams; // 所有流的列表
    unsigned int nb_stream_groups; // 流组的数量
    AVStreamGroup **stream_groups; // 所有流组的列表
    unsigned int nb_chapters; // 章节的数量
    AVChapter **chapters; // 章节的列表
    char *url; // 输入或输出 URL
    int64_t start_time; // 组件的第一帧的位置
    int64_t duration; // 流的持续时间
    int64_t bit_rate; // 总流比特率
    unsigned int packet_size; // 包大小
    int max_delay; // 最大延迟
    int flags; // 修改 (de)muxer 行为的标志
    int64_t probesize; // 用于确定流属性的最大字节数
    int64_t max_analyze_duration; // 用于读取输入数据的最大持续时间
    const uint8_t *key; // 密钥
    int keylen; // 密钥长度
    unsigned int nb_programs; // 节目的数量
    AVProgram **programs; // 节目的列表
    enum AVCodecID video_codec_id; // 强制视频 codec_id
    enum AVCodecID audio_codec_id; // 强制音频 codec_id
    enum AVCodecID subtitle_codec_id; // 强制字幕 codec_id
    enum AVCodecID data_codec_id; // 强制数据 codec_id
    AVDictionary *metadata; // 应用于整个文件的元数据
    int64_t start_time_realtime; // 流的实际开始时间
    int fps_probe_size; // 用于确定帧速率的帧数
    int error_recognition; // 错误识别
    AVIOInterruptCB interrupt_callback; // 自定义中断回调
    int debug; // 调试标志
    int max_streams; // 最大流数量
    unsigned int max_index_size; // 用于每个流索引的最大内存量
    unsigned int max_picture_buffer; // 用于缓冲帧的最大内存量
    int64_t max_interleave_delta; // 最大缓冲持续时间
    int max_ts_probe; // 等待第一个时间戳时读取的最大包数
    int max_chunk_duration; // 最大块时间
    int max_chunk_size; // 最大块大小
    int max_probe_packets; // 最大探测包数
    int strict_std_compliance; // 允许非标准和实验扩展
    int event_flags; // 文件上发生的事件标志
    int avoid_negative_ts; // 避免负时间戳
    int audio_preload; // 音频预加载
    int use_wallclock_as_timestamps; // 使用挂钟时间作为时间戳
    int skip_estimate_duration_from_pts; // 跳过从 PTS 估算持续时间
    int avio_flags; // 用于强制 AVIO_FLAG_DIRECT 的标志
    enum AVDurationEstimationMethod duration_estimation_method; // 持续时间估算方法
    int64_t skip_initial_bytes; // 打开流时跳过的初始字节
    unsigned int correct_ts_overflow; // 校正单个时间戳溢出
    int seek2any; // 强制寻找到任何帧
    int flush_packets; // 每个包后刷新 I/O 上下文
    int probe_score; // 格式探测分数
    int format_probesize; // 用于识别输入格式的最大字节数
    char *codec_whitelist; // 允许的解码器列表
    char *format_whitelist; // 允许的解复用器列表
    char *protocol_whitelist; // 允许的协议列表
    char *protocol_blacklist; // 禁止的协议列表
    int io_repositioned; // IO 重新定位标志
    const struct AVCodec *video_codec; // 强制视频解码器
    const struct AVCodec *audio_codec; // 强制音频解码器
    const struct AVCodec *subtitle_codec; // 强制字幕解码器
    const struct AVCodec *data_codec; // 强制数据解码器
    int metadata_header_padding; // 元数据头的填充字节数
    void *opaque; // 用户数据
    av_format_control_message control_message_cb; // 设备与应用程序通信的回调
    int64_t output_ts_offset; // 输出时间戳偏移
    uint8_t *dump_separator; // 转储格式分隔符
    int (*io_open)(struct AVFormatContext *s, AVIOContext **pb, const char *url, int flags, AVDictionary **options); // 打开新 IO 流的回调
    int (*io_close2)(struct AVFormatContext *s, AVIOContext *pb); // 关闭 IO 流的回调
} AVFormatContext;
``` 

### AVStream结构体
```c
typedef struct AVStream {
    const AVClass *av_class; // 用于日志记录和选项的类
    int index; // 流在 AVFormatContext 中的索引
    int id; // 格式特定的流 ID，解码时由 libavformat 设置，编码时由用户设置
    AVCodecParameters *codecpar; // 与此流关联的编解码器参数
    void *priv_data; // 私有数据
    AVRational time_base; // 表示帧时间戳的基本时间单位
    int64_t start_time; // 流的第一帧的 PTS（显示顺序），以流时间基为单位
    int64_t duration; // 流的持续时间，以流时间基为单位
    int64_t nb_frames; // 流中的帧数，如果已知，否则为 0
    int disposition; // 流的处置标志，AV_DISPOSITION_* 标志的组合
    enum AVDiscard discard; // 选择可以随意丢弃的包，不需要解复用
    AVRational sample_aspect_ratio; // 采样纵横比（如果未知则为 0）
    AVDictionary *metadata; // 元数据
    AVRational avg_frame_rate; // 平均帧率
    AVPacket attached_pic; // 对于带有 AV_DISPOSITION_ATTACHED_PIC 处置的流，此包将包含附加图片
#if FF_API_AVSTREAM_SIDE_DATA
    AVPacketSideData *side_data; // 应用于整个流的附加数据数组
    int nb_side_data; // AVStream.side_data 数组中的元素数量
#endif
    int event_flags; // 指示流上发生的事件的标志，AVSTREAM_EVENT_FLAG_* 的组合
    AVRational r_frame_rate; // 流的实际基本帧率
    int pts_wrap_bits; // 时间戳中的位数，用于包装控制
} AVStream;
```

### AVCodecParameters结构体
```c
typedef struct AVCodecParameters {
    enum AVMediaType codec_type; // 编码数据的一般类型（视频、音频、字幕等）
    enum AVCodecID codec_id; // 编码数据的具体类型（使用的编解码器）
    uint32_t codec_tag; // 编解码器的附加信息（对应于 AVI FOURCC）
    uint8_t *extradata; // 初始化解码器所需的额外二进制数据，依赖于编解码器
    int extradata_size; // extradata 内容的大小（以字节为单位）
    AVPacketSideData *coded_side_data; // 与整个流相关的附加数据
    int nb_coded_side_data; // coded_side_data 中的条目数量
    int format; // 视频：像素格式，对应于 enum AVPixelFormat；音频：采样格式，对应于 enum AVSampleFormat
    int64_t bit_rate; // 编码数据的平均比特率（以比特每秒为单位）
    int bits_per_coded_sample; // 编码字中每个样本的位数，表示每个样本的比特率
    int bits_per_raw_sample; // 每个输出样本的有效位数，如果样本格式有更多位，则最不重要的位是额外的填充位
    int profile; // 编解码器特定的比特流限制，表示流符合的配置文件
    int level; // 编解码器特定的比特流限制，表示流符合的级别
    int width; // 视频帧的宽度（以像素为单位）
    int height; // 视频帧的高度（以像素为单位）
    AVRational sample_aspect_ratio; // 视频帧的像素宽高比（宽度/高度）
    AVRational framerate; // 视频帧率，对于具有恒定帧持续时间的流
    enum AVFieldOrder field_order; // 交错视频中字段的顺序
    enum AVColorRange color_range; // 颜色范围
    enum AVColorPrimaries color_primaries; // 颜色原色
    enum AVColorTransferCharacteristic color_trc; // 颜色传输特性
    enum AVColorSpace color_space; // 颜色空间
    enum AVChromaLocation chroma_location; // 色度位置
    int video_delay; // 视频延迟帧的数量
    AVChannelLayout ch_layout; // 音频的通道布局和通道数量
    int sample_rate; // 每秒音频样本数
    int block_align; // 每个编码音频帧的字节数
    int frame_size; // 音频帧大小，如果已知
    int initial_padding; // 编码器在音频开头插入的填充量（以样本为单位）
    int trailing_padding; // 编码器在音频末尾附加的填充量（以样本为单位）
    int seek_preroll; // 不连续后要跳过的样本数
} AVCodecParameters;
```



### AVCodec
```c
typedef struct AVCodec {
    const char *name;                     // 编解码器的名称，唯一标识符。例如："h264" 表示 H.264 编解码器。
    const char *long_name;                // 编解码器的描述性名称，便于人类阅读。例如："H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"。
    enum AVMediaType type;                // 编解码器的媒体类型。可能的值包括：
                                          // - AVMEDIA_TYPE_VIDEO（视频）
                                          // - AVMEDIA_TYPE_AUDIO（音频）
                                          // - AVMEDIA_TYPE_SUBTITLE（字幕）
    enum AVCodecID id;                    // 编解码器的唯一标识符。例如：AV_CODEC_ID_H264 表示 H.264。
    int capabilities;                     // 编解码器的能力标志，表示支持的功能。可能的标志包括：
                                          // - AV_CODEC_CAP_DRAW_HORIZ_BAND: 支持水平带绘制。
                                          // - AV_CODEC_CAP_DR1: 支持自定义缓冲区分配。
                                          // - AV_CODEC_CAP_DELAY: 支持延迟帧处理。
                                          // - AV_CODEC_CAP_FRAME_THREADS: 支持帧级多线程。
                                          // - AV_CODEC_CAP_SLICE_THREADS: 支持切片级多线程。
    uint8_t max_lowres;                   // 解码器支持的最大低分辨率值。
    const AVRational *supported_framerates; // 支持的帧率数组，终止于 {0, 0}。例如：{30, 1} 表示 30 FPS。
    const enum AVPixelFormat *pix_fmts;   // 支持的像素格式数组，终止于 -1。例如：AV_PIX_FMT_YUV420P 表示 YUV420 格式。
    const int *supported_samplerates;     // 支持的音频采样率数组，终止于 0。例如：44100 表示 44.1 kHz。
    const enum AVSampleFormat *sample_fmts; // 支持的音频采样格式数组，终止于 -1。例如：AV_SAMPLE_FMT_FLTP 表示浮点平面格式。
    const AVClass *priv_class;            // 私有上下文的 AVClass，用于日志记录和选项管理。
    const AVProfile *profiles;            // 支持的配置文件数组，终止于 {AV_PROFILE_UNKNOWN}。例如：H.264 支持 Baseline、Main 和 High 配置文件。
    const char *wrapper_name;             // 如果编解码器基于外部库实现，则此字段表示外部库的名称。例如："libx264" 表示基于 x264 的 H.264 编码器。
    const AVChannelLayout *ch_layouts;    // 支持的音频通道布局数组，终止于零值布局。例如：AV_CHANNEL_LAYOUT_STEREO 表示立体声布局。
} AVCodec;

```




### AvCodecContext
```c
typedef struct AVCodecContext {
    const AVClass *av_class;          // 用于日志记录的类信息，由 avcodec_alloc_context3 设置。
    int log_level_offset;             // 日志级别偏移量。

    enum AVMediaType codec_type;      // 媒体类型（如视频、音频、字幕）。
    const struct AVCodec *codec;      // 指向编解码器的指针。
    enum AVCodecID codec_id;          // 编解码器的 ID（如 H.264、AAC）。

    unsigned int codec_tag;           // 编解码器的四字符代码（FourCC）。

    void *priv_data;                  // 私有数据，特定于编解码器。
    struct AVCodecInternal *internal; // 内部数据，供 libavcodec 使用。
    void *opaque;                     // 用户私有数据，可用于存储应用程序特定信息。

    int64_t bit_rate;                 // 平均比特率（单位：bps）。
    int flags;                        // 编解码器标志（AV_CODEC_FLAG_*）。
    int flags2;                       // 扩展标志（AV_CODEC_FLAG2_*）。

    uint8_t *extradata;               // 额外数据（如 SPS/PPS）。
    int extradata_size;               // 额外数据的大小。

    AVRational time_base;             // 时间基准，用于时间戳计算。
    AVRational pkt_timebase;          // 数据包时间基准。
    AVRational framerate;             // 帧率。

    int delay;                        // 编解码器的延迟帧数。

    // 视频相关字段
    int width, height;                // 视频宽度和高度（像素）。
    int coded_width, coded_height;    // 编码宽度和高度（可能包含填充）。
    AVRational sample_aspect_ratio;   // 像素的宽高比。
    enum AVPixelFormat pix_fmt;       // 像素格式（如 YUV420P）。
    enum AVPixelFormat sw_pix_fmt;    // 软件加速的像素格式。
    enum AVColorPrimaries color_primaries; // 色彩原色。
    enum AVColorTransferCharacteristic color_trc; // 色彩传输特性。
    enum AVColorSpace colorspace;     // 色彩空间。
    enum AVColorRange color_range;    // 色彩范围（如 MPEG、JPEG）。
    enum AVChromaLocation chroma_sample_location; // 色度采样位置。
    enum AVFieldOrder field_order;    // 场顺序（如顶场优先）。
    int refs;                         // 参考帧数量。
    int has_b_frames;                 // B 帧的数量。

    // 音频相关字段
    int sample_rate;                  // 音频采样率（如 44100 Hz）。
    enum AVSampleFormat sample_fmt;   // 音频采样格式（如 AV_SAMPLE_FMT_FLTP）。
    AVChannelLayout ch_layout;        // 音频通道布局（如立体声）。

    int frame_size;                   // 每帧的样本数。
    int block_align;                  // 每个音频帧的字节数。
    int cutoff;                       // 音频截止频率。

    // 硬件加速相关字段
    AVBufferRef *hw_frames_ctx;       // 硬件加速帧上下文。
    AVBufferRef *hw_device_ctx;       // 硬件设备上下文。
    int hwaccel_flags;                // 硬件加速标志。

    // 多线程相关字段
    int thread_count;                 // 线程数量。
    int thread_type;                  // 多线程类型（如帧级或切片级）。
    int active_thread_type;           // 当前使用的多线程类型。

    // 其他字段
    int profile;                      // 编解码器配置文件（如 H.264 的 Baseline、Main）。
    int level;                        // 编解码器级别。
    int skip_loop_filter;             // 跳过环路滤波。
    int skip_idct;                    // 跳过 IDCT。
    int skip_frame;                   // 跳过帧解码。
    int apply_cropping;               // 是否应用裁剪。

    // 调试和错误处理
    int debug;                        // 调试标志。
    int err_recognition;              // 错误识别标志。
    uint64_t error[AV_NUM_DATA_POINTERS]; // 错误信息。

    // 其他字段省略...
} AVCodecContext;
```

### AVCodec 和 AVCodecContext 的区别
在 FFmpeg 中，AVCodecContext 和 AVCodec 是两个密切相关但职责不同的结构体：

AVCodec：

表示编解码器本身的描述信息

包含编解码器的名称、类型(音频/视频/字幕)、ID、能力等信息

是静态的、只读的，通常由 FFmpeg 在初始化时注册

示例：H.264 解码器、MP3 编码器等

AVCodecContext：

表示编解码器的运行时上下文

包含编解码器的配置参数、状态信息等

是动态的、可配置的，每个编解码实例都有独立的上下文

示例：特定视频流的宽度/高度、比特率等参数

关系总结：AVCodec 描述编解码器是什么，而 AVCodecContext 描述如何使用这个编解码器。

avcodec_alloc_context3() 函数用于为指定的编解码器分配并初始化一个 AVCodecContext 上下文。

### ffmpeg非常重要和基础的结构体
1.	AVFormatContext：  
•	代表一个多媒体文件的格式上下文，包含文件的全局信息。    
•	包含一个指向 AVStream 结构体的指针数组，表示文件中的所有流（视频流、音频流、字幕流等）。 
2.	AVStream：
•	代表一个多媒体文件中的单个流（例如，一个视频流或一个音频流）。  
•	包含一个指向 AVCodecParameters 结构体的指针，表示该流的编解码器参数。  
3.	AVCodecParameters：   
•	包含与流的编解码器相关的参数信息，如编解码器类型、比特率、分辨率、采样率等。  
4.	AVCodecContext：  
•	包含编解码器的上下文信息，用于实际的编码和解码操作。    
•	包含编解码器的状态、配置参数和一些缓冲区。  
5.	AVFrame：   
•	代表一个解码后的原始数据帧，可以是视频帧或音频帧。  
•	包含帧的数据、格式、时间戳等信息。  
6.	AVPacket：  
•	代表一个编码后的数据包，可以是视频包或音频包。  
•	包含包的数据、大小、时间戳等信息。  
7.	AVIOContext：   
•	代表输入输出上下文，用于读写多媒体文件。    
•	包含缓冲区、读写函数指针等信息。    
8.	AVFilterContext：   
•	代表一个滤镜上下文，用于处理音视频数据的滤镜操作。  
•	包含滤镜的状态、配置参数和一些缓冲区。  

```
AVFormatContext
├── AVStream* streams[]  // 指向多个 AVStream 的指针数组
│   ├── AVCodecParameters* codecpar  // 每个 AVStream 包含一个指向 AVCodecParameters 的指针
│   └── ...  // 其他流相关信息
└── ...  // 其他全局信息

AVCodecContext
├── AVCodec* codec  // 指向编解码器的指针
├── AVFrame* frame  // 指向解码后的帧的指针
├── AVPacket* packet  // 指向编码后的包的指针
└── ...  // 其他编解码器相关信息

AVFrame
├── uint8_t* data[]  // 帧数据
├── int linesize[]  // 每行数据的大小
└── ...  // 其他帧相关信息

AVPacket
├── uint8_t* data  // 包数据
├── int size  // 包大小
└── ...  // 其他包相关信息

AVIOContext
├── unsigned char* buffer  // 缓冲区
├── int buffer_size  // 缓冲区大小
└── ...  // 其他 I/O 相关信息

AVFilterContext
├── AVFilter* filter  // 指向滤镜的指针
├── AVFrame* frame  // 指向滤镜处理后的帧的指针
└── ...  // 其他滤镜相关信息
```

### resize 和 scale 命令
在 FFmpeg 中调整视频大小意味着使用选项更改其宽度和高度，而缩放是指使用缩放过滤器更改帧大小，该过滤器提供高级功能
```shell
# 调整输出视频大小为 320x240
ffmpeg -i input.mp4 -s 320x240 output.mp4
# ffmepg还提供预定义的视频大小，下面两条命令的效果是一样的
ffmpeg -i input.mp4 -s 640x480 output.mp4
ffmpeg -i input.mp4 -s hd720 output.mp4
```
![resizing](ffmpeg_imgs/resizing.png)

### av_read_frame



### sample aspect ration 和 displey aspect ration
SAP: 单个像素的高宽比，就是像素本身的形状，SAP=1:1 表示像素是正方形的，SAP=2:1 表示像素是宽矩形的   
DAR: 视频最终显示的高宽比，比如说我们的屏幕是 16:9 的，那么 DAP=16:9

DAR和SAR的关系: $DAR=\frac{Width\times{SAR_宽}}{Height\times{SAR_高}}$


基本cropping语法
```shell
# 裁剪视频的左边1/3部分
ffmpeg -i input.mp4 -vf crop=iw/3:ih:0:0 left_third.mp4
# 裁剪视频的中间1/3部分
ffmpeg -i input.mp4 -vf crop=iw/3:ih:iw/3:0 center_third.mp4
# 裁剪视频的右边1/3部分
ffmpeg -i input.mp4 -vf crop=iw/3:ih:iw/3*2:0 right_third.mp4

# 剪裁中心帧
ffmpeg -i input.mp4 -vf crop=iw/2:ih/2 center_frame.mp4
```
自动剪裁剪裁区域，分辨率在4:3和16:9之间变换时，可能会产生黑边，可以自动检测黑边并裁剪
```shell
# 剪裁非黑色输出
ffmpeg -i input.mp4 -vf crop=limit=0 output.mp4
```


### padding的基本语法
```shell
# 原图像1600 x 9000,周围填充30像素pink，pad参数的顺序是：输出宽度，输出高度，原图像相对于新图像的x坐标和y坐标便宜量，填充颜色
ffmepg -i input.jpg -vf pad=1630:930:30:30:pink output.jpg

# padding video from 4:3 to 16:9
ffmpeg -i 4_3.mp4 -vf "pad=iw*16/9:ih:(ow -iw)/2:0:color" 16_9.mp4

# padding video from 16:9 to 4:3
ffmpeg -i 16_9.mp4 -vf "pad=iw:iw*3/4:0:(oh -ih)/2:color" 4_3.mp4

# 水平填充，增加高宽比，常用操作，新的aspect ratin:ra
ffmpeg -i input.mp4 -vf "pad=iw*ra:iw:(ow-iw)/2:0:color" output.mp4

# 垂直填充，减小高宽比，常用操作，新的aspect ratin:ra
ffmpeg -i input.mp4 -vf "pad=iw:iw*ra:0:(oh-ih)/2:color" output.mp4
```

### 翻转
```shell
# 水平翻转
ffmpeg -i input.mp4 -vf hflip output.mp4
# 垂直翻转
ffmpeg -i input.mp4 -vf vflip output.mp4
```

### 旋转
![rotating](ffmpeg_imgs/rotating.png)

```shell
# 逆时针旋转90°并且垂直翻转
ffplay -f lavfi -i smptebars -vf transpose=0
ffmpeg -i input.mp4 -vf transpose=0 output.mp4
ffmpeg -i input.mp4 -vf transpose=2, vflip output.mp4

# 顺时针旋转90°
ffmpeg -i input.mp4 -vf transpose=1 output.mp4

# 逆时针旋转90°并且垂直翻转
ffmpeg -i input.mp4 -vf transpose=3 output.mp4
```

### blur sharpen and Other denosing

**boxblur**
![boxblur](ffmpeg_imgs/boxblur.png)
boxblur=亮度半径:亮度强度[:色度半径:色度强度[:透明度半径:透明度强度]]   
其中：
* luma_r（亮度半径）：控制亮度（Y）分量的模糊半径，数值越大越模糊（默认 2）。
* luma_p（亮度强度）：控制亮度模糊的迭代次数（默认 2），次数越多越模糊。
* chroma_r（色度半径）：控制色度（UV）分量的模糊半径（默认 0，即不模糊）。
* chroma_p（色度强度）：控制色度模糊的迭代次数（默认 0）。
* alpha_r（透明度半径）：控制透明度（Alpha）通道的模糊半径（默认 0）。
* alpha_p（透明度强度）：控制透明度模糊的迭代次数（默认 0）。
如果省略 chroma_r 和 chroma_p，则默认不对色度进行模糊；同理，alpha_r 和 alpha_p 也可省略。
```shell
# 模糊亮度，半径1.5，强度1
ffmpeg -i input.mp4 -vf boxblur=1.5:1 output.mp4
```

**smartblur**
![smartblur](ffmpeg_imgs/smartblur.png)
smartblur=luma_r:luma_s:luma_t[:chroma_r:chroma_s:chroma_t]     
其中：
* luma_r：亮度（Y）模糊半径（默认 1.0，范围 ≥0.1）
* luma_s：亮度平滑强度（默认 1.0，范围 ≥0.1）
* luma_t：亮度阈值（默认 0.0，范围 0~100，控制边缘保留程度）
* chroma_r：色度（UV）模糊半径（默认 luma_r）
* chroma_s：色度平滑强度（默认 luma_s）
* chroma_t：色度阈值（默认 luma_t）
如果只提供 luma_r、luma_s、luma_t，则色度参数会自动采用相同的值。
```shell
# 模糊亮度，半径最大为5，强度0.8，阈值为0
ffmpeg -i input.mp4 -vf smartblur=5:0.8:0 output.mp4
```
**sharpen**
![unsharp](ffmpeg_imgs/unsharp.png)
可以根据参数锐化和模糊帧
```shell
# 默认参数锐化
ffmpeg -i input.mp4 -vf unsharp output.mp4

# 高斯模糊
ffmpeg -i input.mp4 -vf unshar=6:6:-2 output.mp4
```

**使用 denoise3d 降噪**
```shell
# 降噪
ffmpeg -i input.mp4 -vf mp=denoise3d output.mp4
```
**使用 hqdn3d 降噪**
```shell
# 降噪
ffmpeg -i input.mp4 -vf hqdn3d output.mp4
```
**使用 nr 选项进行降噪**
```shell
# 降噪
ffmpeg -i input.mp4 -vf nr output.mp4
```

### overlay Picture in Picture
视频叠加是一种在（通常较大）背景视频或图像上显示前景视频或图像的技术
```shell
# 基础语法，因为这里的输入时两个，这里使用-filter_complex选项代替-vf
ffmpeg -i input1 -i input -filter_complex overay=x:y output

# 使用filtergraph的方法
ffmpeg -i unput1 -vf movie=input2[logo];[in][logo]pverlay=x:y output

# 在视频的左上角添加一个logo
ffmpeg -i input.mp4 -i logo.png -filter_compelx overlay pair_left_top output.mp4

# 在视频的右上角添加一个logo
ffmpeg -i input.mp4 -i logo.png -filter_complex overlay=W-w:0 pair_right_top output.mp4

# 在视频的左下角添加一个logo
ffmpeg -i input.mp4 -i logo.png -filter_complex overlay=0:H-h pair_left_bottom output.mp4

# 在视频的右下角添加一个logo
ffmpeg -i input.mp4 -i logo.png -filter_complex overlay=W-w:H-h pair_right_bottom output.mp4

# 在特定时间添加一个logo
ffmpeg -i input.mp4 -itsoffset 5 -i logo.png -filter_complex overlay timer_with_logo.mp4
```

### Adding text on video
从文本文件或字符串在视频上添加文本，并使用各种参数进行修改。文本是从 textfile 参数指定的文件中加载的，或者直接使用 text 参数输入的。其他必需参数是指定所选字体的 fontfile。文本位置由 x 和 y 参数设置

```shell
# 从文本文件添加文本
ffmpeg -i input.mp4 -vf drawtext=fontfile=arial.ttf:text='Hello world' output.mp4

# 文本位置
ffmpeg -f lavfi -i color=c=whilt -vf drawtext="frontfile=arial.ttf:text='Good day':x=(w-tw)/2:y=(h-th)/2"

# 文本颜色和大小
ffmpeg -f lavfi -i color=c=whilt -vf drawtext="frontfile=arial.ttf:text='Good day':x=(w-tw)/2:y=(h-th)/2:fontcolor=red:fontsize=24"

# 水平动态文本
ffmpeg -f lavfi -i color=c=whilt -vf drawtext="frontfile=arial.ttf:text='Good day':x=w-t*20:y=(h-th)/2:fontcolor=red:fontsize=24"

# 水平底部附近滚动一行文本
 ffmpeg -i test.mp4 -vf drawtext="fontfile=arial.ttf:textfile=info.txt:x=w-t*50:y=h-th:fontcolor=red:fontsize=32" add_text_dy1.mp4

# 垂直动态文本
ffmpeg -i test.mp4 -vf drawtext="fontfile=arial.ttf:textfile=info.txt:x=(w-tw)/2:y=h-t*100:fontcolor=red:fontsize=32" add_text_dy1_v.mp4
```

### conversion between formats
media containers
![container](ffmpeg_imgs/container.png)
media formats and codecs in conversion
![conversion](ffmpeg_imgs/conversion.png)
常见媒体文件的默认编解码器
![default_codec](ffmpeg_imgs/default_codec.png)

```shell
# 默认覆盖旧输出文件
ffmpeg -y -i input.mp4 output.mp4
# 不覆盖旧输出文件
ffmpeg -n -i input.mp4 output.mp4
```


### 时间操作

```shell
ffmpeg -i input.mp3 -t 30 output.mp3

# 从20秒开始截取
ffmpeg -i input.mp3 -ss 20 output.mp3
# 从30秒开始截取30秒
ffmpeg -i input.mp3 -ss 30 -t 30 output.mp3

# 音频延后5秒
ffmpeg -i test.mp4 -itsoffset 3 -i test.mp4 -map 0:v -map 1:a -c:a copy -c:v copy delay_audio.mp4
```
timestamps
![timestamps](ffmpeg_imgs/timestamps.png)

每个视频帧都包含一个带有时间戳值的 Header，序列 2 帧的差值为 1/fps，例如如果 fps 为 25，则差值为 0.04 秒
```shell
# 视频3倍速播放
ffplay -i input.mp4 -vf setpts=PTS/3

# 音频3倍速播放
ffplay -i input.mp3 -af atempo=3

# 视频和音频同时3倍速播放
 ffplay -i test.mp4 -af atempo=3 -vf setpts=PTS/3
```
### metadata
![metadata](ffmpeg_imgs/metadata.png)

构建metadata
```shell
ffmpeg -i test_0.mp3 -metadata artist=ffmpeg -metadata tital="Test 1" title.mp3
```

### Image Processing
![image_processing](ffmpeg_imgs/image_processing.png)

```shell
# 将video转换为每张图像
ffmpeg -i ..\test.mp4 frame%4d.jpg

# 一张图像制作成视频
ffmpeg -loop 1 -i frame0263.jpg -t 10 photo.mp4

# 多张图像转为视频
ffmpeg -f image2 -i frame%4d.jpg -r 30 video.mp4
```

### pulsecode modulation 
常见的音频位深
![audio_bit_depths](ffmpeg_imgs/audio_bit_depths.png)
常见的音频采样频率
![audio_sample_rates](ffmpeg_imgs/audio_smaple_rates.png)

```shell
# 要在左通道中创建 C4 音调，在右通道中创建 C5 音调，我们可以使用命令
ffplay -f lavfi -i "aevalsrc=exprs='sin(261.63*2*PI*t)|cos(523.25*2*PI*t)':c=FL+FR"

# 使用 volume 滤镜来调整音量
ffmpeg -i test_0.mp3 -af volume=1/2 middle_loud.mp3

# 音量增加10分贝
ffplay -i input.mp3 -af volume=10dB 

```
### 编解码器预设
了解，需要使用的时候在细看

### 隔行扫描视频
可以去掉隔行扫描视频的隔行扫描

调整隔行扫描的顺序

### 使用ffmepg推流
```shell
ffmpeg -i rtsp://192.168.0.103:25544/live/34020000001320000001_34020000001320000001?expired=20250629192431 -vcodec copy -acodec copy -f mepgts udp://127.0.0.1:12345
```

### I、P、B帧
https://zhuanlan.zhihu.com/p/359621409

关键帧指的是，在构成一段动画的若干帧中，起到决定性作用的2-3帧   
关键帧：相当于二维动画中的原画，指角色或者物体运动或变化中的关键动作所处的那一帧，它包含了图像的所有信息，后来帧仅包含了改变了的信息。
ffmpeg中的关键帧是指I帧，I帧是视频编码中的一种帧类型，它包含完整的图像数据，而其他类型的帧（如P帧和B帧）则依赖于I帧进行解码。关键帧通常用于视频流的随机访问和编辑，因为它们提供了完整的图像信息。    
简单地说，I帧是关键帧，属于帧内压缩，解码时单独的该帧便可完成解码；P帧为向前预测编码帧，即P帧解码时需要参考前面相关帧的信息才能解码；B帧为双向预测编码帧，解码时既需要参考前面已有的帧又需要参考后面待解码的帧；他们都是基于I帧来压缩数据。

I帧表示关键帧，又称intra picture，I帧画面完整保留，解码时只需要本帧数据就可以完成（因为包含完整画面）。

P帧前向预测编码帧 又称predictive-frame，表示的是这一帧跟之前的一个关键帧（或P帧）的差别，解码时需要用之前缓存的画面叠加上本帧定义的差别，生成最终画面。（也就是差别帧，P帧没有完整画面数据，只有与前一帧的画面差别的数据）

B帧双向预测内插编码帧 又称bi-directional interpolated prediction frame，是双向差别帧，也就是B帧记录的是本帧与前后帧的差别，换言之，要解码B帧，不仅要取得之前的缓存画面，还要解码之后的画面，通过前后画面的与本帧数据的叠加取得最终的画面。B帧压缩率高，但是解码时CPU会比较累。

因此，I帧和P帧的解码算法比较简单，资源占用也比较少，I帧只要自己完成就行了，至于P帧，也只需要解码器把前一个画面缓存一下，遇到P帧时就使用之前缓存的画面就行。如果视频流只有I和P，解码器可以不管后面的数据，边读边解码，线性前进。如果视频流还有B帧，则需要缓存前面和当前的视频帧，待后面视频帧获得后，再解码。

I帧特点:

1）.它是一个全帧压缩编码帧。它将全帧图像信息进行JPEG压缩编码及传输;

2）.解码时仅用I帧的数据就可重构完整图像;

3）.I帧描述了图像背景和运动主体的详情;

4）.I帧不需要参考其他画面而生成;

5）.I帧是P帧和B帧的参考帧(其质量直接影响到同组中以后各帧的质量);

6）.I帧是帧组GOP的基础帧(第一帧),在一组中只有一个I帧;

7）.I帧不需要考虑运动矢量;

8）.I帧所占数据的信息量比较大。

P帧特点:

1）.P帧是I帧后面相隔1~2帧的编码帧;

2）.P帧采用运动补偿的方法传送它与前面的I或P帧的差值及运动矢量(预测误差);

3）.解码时必须将I帧中的预测值与预测误差求和后才能重构完整的P帧图像;

4）.P帧属于前向预测的帧间编码。它只参考前面最靠近它的I帧或P帧;

5）.P帧可以是其后面P帧的参考帧,也可以是其前后的B帧的参考帧;

6）.由于P帧是参考帧,它可能造成解码错误的扩散;

7）.由于是差值传送,P帧的压缩比较高。

B帧特点

1）.B帧是由前面的I或P帧和后面的P帧来进行预测的;

2）.B帧传送的是它与前面的I或P帧和后面的P帧之间的预测误差及运动矢量;

3）.B帧是双向预测编码帧;

4）.B帧压缩比最高,因为它只反映丙参考帧间运动主体的变化情况,预测比较准确;

5）.B帧不是参考帧,不会造成解码错误的扩散。


### DTS和PTS
PTS（Presentation Time Stamp）表示帧在播放时的时间戳，指示该帧应该在何时被显示。PTS 是基于时间基准（time_base）计算的，通常用于视频和音频流的同步。     
DTS（Decoding Time Stamp）表示帧在解码时的时间戳，指示该帧应该在何时被解码。DTS 通常用于 B 帧，因为 B 帧需要依赖于前后帧进行解码。

在没有B frame的情况下，DTS和PTS的输出顺序是一样的

GOP：两个I frame之间形成一个GOP，在x264中同时可以通过参数来设定bf的大小，即：I 和p或者两个P之间B的数量。

如果有B frame 存在的情况下一个GOP的最后一个frame一定是P.

![PTS_DTS](./ffmpeg_imgs/PTS_DTS.png)

**采集顺序**指图像传感器采集原始信号得到图像帧的顺序。  
**编码顺序**指编码器编码后图像帧的顺序。存储到磁盘的本地视频文件中图像帧的顺序与编码顺序相同。    
**传输顺序**指编码后的流在网络中传输过程中图像帧的顺序。    
**解码顺序**指解码器解码图像帧的顺序。    
**显示顺序**指图像帧在显示器上显示的顺序。       
**采集顺序与显示顺序相同。编码顺序、传输顺序和解码顺序相同。**

### 时间基与时间戳的概念
https://www.cnblogs.com/leisure_chn/p/10584910.html

在 FFmpeg 中，时间基(time_base)是时间戳(timestamp)的单位，时间戳值乘以时间基，可以得到实际的时刻值(以秒等为单位)。例如，如果一个视频帧的 dts 是 40，pts 是 160，其 time_base 是 1/1000 秒，那么可以计算出此视频帧的解码时刻是 40 毫秒(40/1000)，显示时刻是 160 毫秒(160/1000)。FFmpeg 中时间戳(pts/dts)的类型是 int64_t 类型，把一个 time_base 看作一个时钟脉冲，则可把 dts/pts 看作时钟脉冲的计数。

三种时间基 tbr、tbn 和 tbc   
tbn：对应容器中的时间基。值是 AVStream.time_base 的倒数   
tbc：对应编解码器中的时间基。值是 AVCodecContext.time_base 的倒数   
tbr：从视频流中猜算得到，可能是帧率或场率(帧率的 2 倍)   
除以上三种时间基外，FFmpeg 还有一个内部时间基 AV_TIME_BASE(以及分数形式的 AV_TIME_BASE_Q)   
```cpp
// Internal time base represented as integer
#define AV_TIME_BASE            1000000

// Internal time base represented as fractional value
#define AV_TIME_BASE_Q          (AVRational){1, AV_TIME_BASE}
```
av_q2d()将时间从 AVRational 形式转换为 double 形式。AVRational 是分数类型，double 是双精度浮点数类型，转换的结果单位是秒。转换前后的值基于同一时间基，仅仅是数值的表现形式不同而已。    

av_rescale_q()用于不同时间基的转换，用于将时间值从一种时间基转换为另一种时间基。

av_packet_rescale_ts()用于将 AVPacket 中各种时间值从一种时间基转换为另一种时间基。

AVStream.time_base 是 AVPacket 中 pts 和 dts 的时间单位，输入流与输出流中 time_base 按如下方式确定：   
对于输入流：打开输入文件后，调用 avformat_find_stream_info()可获取到每个流中的 time_base    
对于输出流：打开输出文件后，调用 avformat_write_header()可根据输出文件封装格式确定每个流的 time_base 并写入输出文件中   

不同封装格式具有不同的时间基，在转封装(将一种封装格式转换为另一种封装格式)过程中，时间基转换相关代码如下：
```cpp
av_read_frame(ifmt_ctx, &pkt);
pkt.pts = av_rescale_q_rnd(pkt.pts, in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
pkt.dts = av_rescale_q_rnd(pkt.dts, in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
pkt.duration = av_rescale_q(pkt.duration, in_stream->time_base, out_stream->time_base);

// 下面的代码具有和上面代码相同的效果：
// 从输入文件中读取 packet
av_read_frame(ifmt_ctx, &pkt);
// 将 packet 中的各时间值从输入流封装格式时间基转换到输出流封装格式时间基
av_packet_rescale_ts(&pkt, in_stream->time_base, out_stream->time_base);

// 这里流里的时间基in_stream->time_base和out_stream->time_base，是容器中的时间基，就是 tbn
```

### 像素格式 yuv和RGB

RGB色彩空间是由红色、绿色和蓝色三种颜色组成的，RGB24表示每个像素用24位来表示颜色信息，其中8位表示红色，8位表示绿色，8位表示蓝色。RGB24的优点是颜色还原度高，缺点是文件体积大。

由于human visual system (HVS)通常对亮度要比色度更敏感，对于后续需要进行有损压缩过程以及编码器来说，那在给我输入原始图像之前，既然色度信息不敏感，那是不是可以直接去掉图像中部分色度信息，也无伤大雅。

所以首先可以考虑将图片中**亮度和色度信息分开**，然后让色度分量的分辨率(像素点)比亮度分辨率低一些，即对色度分量进行降采样处理，丢掉一些色度信息。

而YUV(YCbCr)就是比较常用的满足这种需求的颜色空间形式。其中Y表示luminance亮度分量，它的值可以通过原始RGB的三个颜色分量加权得到。   
$Y = {k_r}R + {k_g}G + {k_b}B$    

其中$k_r$、$k_g$、$k_b$分别是红色、绿色和蓝色的权重系数。一般来说，$k_r$=0.299，$k_g$=0.587，$k_b$=0.114。

而颜色信息（chrominance or chroma）可以用原始RGB颜色分量和亮度Y的差值来表示，因为RGB有三个颜色分量，所以理论上我们会得到Cr，Cb，Cg三个颜色分量，
$C_r = R - Y$   
$C_b = B - Y$      
$C_g = G - Y$   
所以，对于亮度和色度拆开的颜色空间来说，一个有色图像完整表示（相对于RGB）应当是Y分量加上Cr，Cb，Cg分量。

但是我们又会发现，Cr+Cb+Cg其实是一个常数，所以我们只需要在三个里面选2个颜色分量即可，第三个分量可以通过这两个推导得到   
亮度：即人眼对光的明亮程度的感受。  
色调：人眼能看到的颜色种类，与光的波长有关  
饱和度：颜色深浅程度。与各种颜色混入白光的比例有关。    
以上 色调 + 饱和度 = 色度
![rgb](ffmpeg_imgs/rgb.png)
![yuv](ffmpeg_imgs/yuv.png)
![yuv_space](ffmpeg_imgs/yuv_space.png)  
YUV 到 RGB的转换公式如下：
![rgb2yuv](ffmpeg_imgs/rgb2yuv.png)


YUV数据存储分为平面模式和交错模式。  
平面模式：Y、U、V分量分别存储在不同的平面中。每个平面都是一个单独的数组，Y分量存储在一个数组中，U分量存储在另一个数组中，V分量存储在第三个数组中。   
交错模式：Y、U、V分量交错存储在同一个平面中。每个像素的Y、U、V分量交替存储在同一个数组中。   
宽度为6高度为4的YUV420格式图像，平面模式存储方式如下：
```
Y0 Y1 Y2 Y3 Y4 Y5 Y6 Y7 Y8 Y9 Y10 Y11 Y12 Y13 Y14 Y15
Y16 Y17 Y18 Y19 Y20 Y21 Y22 Y23 U0 U1 U2 U3 U4 U5 V0 V1 V2 V3 V4 V5
```
宽度为6高度为4的YUV420格式图像，交错模式存储方式如下：
```
YU Y YU Y YU Y
YV Y YV Y YV Y
YU Y YU Y YU Y
YV Y YV Y YV Y
```
ffmpeg中AV_PIC_FMT_YUV420P表示平面存储，AV_PIC_FMT_YUV420表示交错存储。

参考：   
https://zhuanlan.zhihu.com/p/698541085   
https://zhuanlan.zhihu.com/p/248116694   
https://zhuanlan.zhihu.com/p/452676366   
https://blog.csdn.net/the_sangzi_home/article/details/105311494     
YUV444、 YUV422、YUV420  看下面链接      
https://blog.csdn.net/rjszcb/article/details/118728264   

### jpeg
JPEG（Joint Photographic Experts Group）是一种有损压缩的图像格式，广泛用于存储和传输数字图像。JPEG 压缩算法通过去除人眼不敏感的高频信息来减小图像文件的大小。JPEG 图像通常以 .jpg 或 .jpeg 扩展名保存。

颜色空间采用YUVJ

了解标准JPEG、渐进JPEG和JPEF2000。  
标准JPEG从上到下，从左到右依次显示图像
渐进JPEG从模糊到清晰逐步显示图像


### ffmpeg 麦克风
使用ffmpeg -list_devices true -f dshow -i dummy命令列出所有可用的音频和视频设备。

录制麦克风音频
```shell
ffmpeg -f dshow -i audio="麦克风 (Realtek High Definition Audio)" output.mp3
```
* -f dshow：指定使用 DirectShow 捕获音频（Windows 特有）。
* -i audio="设备名称"：指定麦克风设备。
* output.mp3：输出文件名（支持 .mp3、.wav、.aac 等格式）。
```shell
# 指定编码格式
ffmpeg -f dshow -i audio="麦克风名称" -c:a libmp3lame -b:a 192k output.mp3
# -c:a libmp3lame：使用 MP3 编码（需 FFmpeg 编译支持）。
# -b:a 192k：设置比特率为 192kbps（可调整）。

# 录制WAV（无损格式）
ffmpeg -f dshow -i audio="麦克风名称" output.wav

# 录制 AAC（高质量压缩）
ffmpeg -f dshow -i audio="麦克风名称" -c:a aac -b:a 256k output.m4a
```

## 第4章 封装与解封装 （复习知识点）
容器格式：定义 功能

二进制查看工具：HxD, Notepad++    
分析MP4的工具，mp4info, Elecard StreamEye

### 视频文件转MP4

相关概念：
* 元数据（moov）和音视频数据（mdat）
* box 和 atom 
* 容器（盒子、箱子）和子容器
* box的基本结构（header:size type, data），box和FullBox（largesize）的区别
* mp4的box列表
* moov至少包含的三种box：mvhd、trak、udta
* trak至少包含的三种box：tkhd、mdia、edts
* mdia至少包含的三种box：mdhd、hdlr、minf
* minf包含视频的vhmd和音频的smhd
* minf里面的dinf,stbl、stsd、stts、stsc、stsz、stco、co64
![mp4_box](ffmpeg_imgs/mp4_box.png)     
了解基本的box结构，知道重要的box的作用。    
moov box在前和moov box在后的区别。
直播场景中的Fragment MP4（fmp4）和 CMAF，基本原理
* mp4在ffmpep中demuxer的参数和muxer的参数（faststart, dash）

### 视频文件转FLV
分为FLV文件头和FLV文件内容
FLV主要格式：FLVTAG     
FLVTAG:header和body FVL文件分析器**flvAnalyser**    
ffmpeg中FVL的muxer和demuxer的基本参数   
生成带关键索引的FLV文件，根据索引快进，跳转

### 视频文件转MPEG-TS
TS文件格式分3层：ES层、PES层、TS/PS层  每层作用   
ffmpeg中TS的muxer的基本参数   
MPEG-TS格式分析工具：EasyICE和DVBinspector

### 视频文件转HLS
工作方式
M3U8文件的基本结构
* EXT-X-VERSION：版本号
* EXT-X-TARGETDURATION：最长的分片时长
* EXT-X-MEDIA-SEQUENCE：分片序列号
* EXT-X-ENDLIST：结束标志
* EXT-X-PLAYLIST-TYPE：播放类型
* EXT-X-MAP：初始化段
* EXT-X-STREAM-INF：流信息
* EXT-X-PLAYLIST：播放列表
* EXT-X-BYTERANGE：字节范围
ffmpeg中HLS的muxer的基本参数

### 视频文件切片
segment切片参数     
segment_format指定文件格式      
提取视频流，提取音频流


## 第5章 编码与转码

### 软编码H264
```shell
#  查看h264编码器的参数
ffmpeg -h encoder=libx254
```

h264编码器常用参数
* preset：编码速度和压缩率的平衡，值越小，速度越快，压缩率越低。常用值有 ultrafast、superfast、veryfast、faster、fast、medium、slow、veryslow。
```shell
# preset=ultrafast
ffmpeg -i Titanic.mkv -c:v libx264 -preset ultrafast -b:v 2000k ultrafast.mkv
# preset=medium
ffmpeg -i Tianic.mkv -c:v libx264 -preset mediun -b:v 2000k meduin.mkv
# 并排显示
ffmpeg -i .\ultrafast.mkv -i .\medium.mkv -filter_complex "[0:v][1:v]hstack=inputs=2[v]" -map "[v]" -map 0:a -c:v libx264 -c:a copy compare.mkv
```
* tune：编码器的优化目标，值有 film、animation、grain、stillimage、psnr、screencapture、fastdecode、zerolatency。
* profile：编码器的配置文件，值有 baseline、main、high、high10、high422、high444。
```shell
# profile=baseline (没有B帧)
ffmpeg -i Titanic.mkv -c:v libx264 -profile:v baseline  -level 3.1 baseline.ts
# profile=high
ffmpeg -i Titanic.mkv -c:v libx264 -profile:v high  -level 3.1 high.ts
# windows用ffprobe查看B帧数量
???
```
* level：编码器的级别，值有 1.0、1.1、1.2、1.3、2.0、2.1、2.2、3.0、3.1、3.2、4.0、4.1、4.2、5.0、5.1。
* -g: 控制以帧数为单位的 GOP 大小，值越大，GOP 越长，压缩率越高。使用sc_threshold关闭场景切换判定，不插入关键帧
```shell
# 设置gop大小为30，sc_threshold=0
ffmpeg -i Titanic.mkv -c:v libx264 -g 30 -sc_threshold 0 gop_th.mkv
```
设置h264的内部参数，如不出现b帧，控制I P B帧的出现频率和规律
```shell
# 通过-x264-params参数设置h264的b帧数量为0
ffmpeg -i .\Titanic.mkv -c:v libx264 -x264-params "bframes=0" -g 30 -sc_threshold 0 -y gop_th_nob.ts
# 通过-x264-params参数设置h264每两个P帧之间3个B帧
ffmpeg -i .\Titanic.mkv -c:v libx264 -x264-params "bframes=3:b-adapt=0" -g 30 -sc_threshold 0 -y gop_th_ibp.ts
```

码率设置
* VBR:可变码率
* CBR:恒定码率(平均恒定)
* CRF:恒定质量
```shell
# 下面命令 HRD变为CBR 平均恒定码率最大最小码率5Mbit/s
ffmpeg -i Titanic.mkv -an -c:v libx264 -x264opts "nal-hrd=cbr:force-cfr=1" -b:v 5M -maxrate 5M -minrate 5M -bufsize 5M -muxrate 5.5M cbr.ts
```    

### 硬件加速
先了解一下， NVIDIA GPU, intel QSV

### MP3
libmp3lame编码器的参数,了解编码质量参数
* q参数：质量参数，值越小，质量越高，范围为 0-9。
```shell
ffmpeg -i test.mp3 -c:a linmp3lame -q:a 9 out.mp3
# 恒定码率格式，流媒体使用
ffmpeg -i test.mp3 -c:a libmp3lame -b:a 64k out.mp3
```
* abr: 平均码率编码，编码速度比vbr高马，质量比cbr高
```shell
ffmpeg -i test.mp3 -c:a libmp3lame -b:a 64k -abr 1 out.mp3
```

### AAC
基本知识        
基本编码器操作
```shell
ffmpeg -i test.mp3-c:a aac -b:a 64k out.aac
```
* -b:a：设置音频比特率，单位为 kbps。
* -q:a：设置音频质量，范围为 0-5，值越小，质量越高。
FDK-AAC编码, ffmpeg中质量最高的AAC编码器
m4a是AAC的封装格式，AAC是MPEG-4的音频编码格式。
```shell
ffmpeg -i input.wav -c:a libfdk_aac -b:a 128k output.m4a
```
aac的vbr模式
```shell
ffmpeg -i input.wav -c:a libfdk_aac -vbr 5 output.m4a
# HE
ffmpeg -i input.wav -c:a libfdk_aac -profile:a aac_he -b:a 64k output.m4a
# HEv2
ffmpeg -i input.wav -c:a libfdk_aac -profile:a aac_he_v2 -b:a 64k output.m4a
```


### AAC 和 MP3
每帧MP3音频包含1152个样本，每帧AAC音频包含1024个样本。   
1152/1024=1.125，MP3的帧长比AAC的帧长多了12.5%。AAC每帧持续时间更少，时间切片更小，理论上音频质量更高   
MP3每帧持续时间：1152 * 1000 / 44100 = 26.1ms   
AAC每帧持续时间：1024 * 1000 / 44100 = 23.2ms   
例子：在一个MP4文件中的音频流使用的AAC编码，把这个音频流直接解码再编码成MP3格式，出现了错误。
```
[libmp3lame @ 0x5555555aa680] frame_size (1152) was not respected for a non-last frame
```
错误的原因在于AAC编码器在编码时使用了AAC的ADTS封装格式，AAC的ADTS封装格式每帧音频数据的大小是1024个采样点，而MP3的帧大小是1152个采样点。   
MP3编码器要求每次编码传入的音频帧的样本数量必须等于1152个采样点，而AAC编码器传入的音频帧的样本数量是1024个采样点，所以MP3编码器报错了。
保存为MP3的正确做法应该是使用重采样。

### avcodec_send_packet 和 avcodec_receive_frame
在处理视频流的时候，由于B帧的存在，会在while循环中多次调用avcodec_receive_frame函数来获取解码后的帧数据。

音频帧是按顺序解码的，一次调用avcodec_receive_frame就行了。但是为了代码统一性，依然保留循环，同时ffmpeg官方也推荐使用循环。

### SwSContext
在 FFmpeg 中，SwsContext 是用于 图像缩放（resize）、像素格式转换（pixel format conversion）和色彩空间转换（color space conversion） 的核心结构。它通过 libswscale 库实现，常用于视频处理（如转码、滤镜、播放等场景）。
```c
SwsContext *sws_ctx = sws_getContext(
    src_width, src_height, src_pix_fmt,   // 输入参数：宽、高、像素格式
    dst_width, dst_height, dst_pix_fmt,   // 输出参数：宽、高、像素格式
    SWS_BILINEAR,                         // 缩放算法（如 SWS_BILINEAR）
    NULL, NULL, NULL                      // 可选参数（滤镜、色彩矩阵等）
);

...

// 分配输出内存（例如转换为 RGB24）
av_image_alloc(dst_data, dst_linesize, dst_width, dst_height, dst_pix_fmt, 1);

// 执行转换
int ret = sws_scale(
    sws_ctx,             // SwsContext 上下文
    src_data,            // 输入数据指针数组
    src_linesize,        // 输入行大小
    0,                   // 起始行（通常为0）
    src_height,          // 处理的行数（通常为原图高度）
    dst_data,            // 输出数据指针数组
    dst_linesize         // 输出行大小
);

sws_freeContext(sws_ctx);  // 释放 SwsContext
```

### AVFrame
AVPacket 是 FFmpeg 中用于存储编码数据的结构体，通常用于存储音频或视频流中的数据包。它包含了数据包的大小、时间戳、流索引等信息。AVPacket 主要用于在解码和编码过程中传递数据。
```c
typedef struct AVFrame {
    /**
     * 存储原始帧数据，未编码的原始图像或音频数据，作为解码器的输出或编码器的输入
     * data是一个指针数组，元素指向视频图像的某一plane或音频中某声道的某一plane
     * 视频:
     *   Packet: Y,U,V交织存储在一个plane中，如YUVYUV...data[0]指向这个plane
     *   Planar: Y,U,V存储在三个plane中，data[0]指向Y-plane，data[1]指向U-plane，data[2]指向V-plane
     * 音频:
     *   Packet: L,R交织存储在一个plane中，形如LRLRLR...data[0]指向这个plane
     *   Planar: L,R存储在两个plane中，data[0]指向L-plane，data[1]指向R-plane
     */
    uint8_t *data[AV_NUM_DATA_POINTERS];
    /**
     * linesize是一个数组
     * 视频:
     *   每个元素是一个图像plane中一行图像的大小(字节数)，注意有对齐要求linesize对应stride值
     *   packed: 只有一个plane，linesize[0]表示一行图像所占的存储空间大小     
     *   planar: 有多个plane，每个plane的linesize[i]表示一行图像在当前plane中所占的存储空间大小
     * 音频:
     *   每个元素是一个音频plane的大小(字节数)
     *   packed: 多声道音频只有一个plane，linesize[0]表示所有数据的存储空间大小
     *   planar: 有多个plane，但只使用一个linesize[0]，每个plane大小都是linesize[0]
     * 事实上，linesize可能因性能考虑而填充一些额外的数据，故可能比实际对应的音视频数据要大
     */
    int linesize[AV_NUM_DATA_POINTERS];
    /**
     * 在一个正常的AVFrame中，data与extended_data通常都会被设置
     * 但是对于一个plannar且有多个通道且data无法装下所有通道的数据的时候，extended_data必须被使用，用来存储多出来的通道的数据的指针
     */
    uint8_t **extended_data;
	// ...
} AVFrame;
```
linesize（或 stride）是 AVFrame 结构体中的一个重要参数，表示 每一行像素数据在内存中的对齐字节数。它直接影响图像数据的存储和访问方式。

访问YUV420P的像素
```cpp
AVFrame *frame = ...;  // 假设是 YUV420P 格式
int width = frame->width;
int height = frame->height;

// 访问 Y 分量（亮度）
for (int y = 0; y < height; y++) {
    uint8_t *y_line = frame->data[0] + y * frame->linesize[0];
    for (int x = 0; x < width; x++) {
        uint8_t pixel = y_line[x];  // 读取 Y 值
    }
}

// 访问 U 分量（色度）
for (int y = 0; y < height/2; y++) {
    uint8_t *u_line = frame->data[1] + y * frame->linesize[1];
    for (int x = 0; x < width/2; x++) {
        uint8_t pixel = u_line[x];  // 读取 U 值
    }
}
```
访问RGB24的像素
```cpp
AVFrame *frame = ...;  // 假设是 RGB24 格式
int width = frame->width;
int height = frame->height;

for (int y = 0; y < height; y++) {
    uint8_t *line = frame->data[0] + y * frame->linesize[0];
    for (int x = 0; x < width; x++) {
        uint8_t r = line[x * 3];     // R
        uint8_t g = line[x * 3 + 1]; // G
        uint8_t b = line[x * 3 + 2]; // B
    }
}
```

分析
```cpp
uint8_t *y_line = frame->data[0] + y * frame->linesize[0];
// frame->data[0]：指向图像 第一个分量（平面）的起始地址。
// 对于 YUV420P，这是 Y（亮度）分量 的起始指针。
// 对于 RGB24，这是所有像素数据的起始指针（因为 RGB 通常无分平面存储）。

// y：当前要访问的 行号（从 0 到 height-1）。

// frame->linesize[0]：第一个分量（平面）的 内存对齐后的行字节数（可能 ≥ 实际像素宽度 × 字节数）。

// 得到的第y行数据的指针
```
### FILE 
```cpp
FILE *fopen(const char *filename, const char *mode);
filename：文件名（如 "test.txt"）。

mode：打开模式：

"r"：只读（文件必须存在）。

"w"：只写（若文件存在则清空，否则创建）。

"a"：追加（若文件不存在则创建）。

"rb" / "wb"：二进制模式（用于非文本文件）。

"r+" / "w+"：读写模式

size_t fwrite(const void *ptr, size_t size, size_t count, FILE *stream);

ptr：要写入的数据指针（packet->data）。

size：每个数据块的大小（这里是 1 字节）。

count：要写入的块数（这里是 packet->size）。

stream：目标文件指针（fp_out）

int fclose(FILE *stream);
```

### ffmpeg滤镜原理
FFmpeg 的滤镜 API 主要通过滤镜名称（filter_name 或 filter_desc）在滤镜图（AVFilterGraph）中动态添加和连接滤镜实现滤镜链

https://zhuanlan.zhihu.com/p/675218673
