# use_dee_encode_dv8_4
使用Dolby Encoding Engine及一些三方工具实现在非macOS上制作Dolby Vision 8.4视频
*本文复制自https://www.cnblogs.com/izwb003/archive/2022/09/20/use_dee_encode_dv8_4.html
杜比视界作为一种新兴的、效果颇佳的HDR技术，正随着设备和软件等的更新换代走进千家万户，让越来越多的人体验到了HDR效果的魅力。目前，随着B站等视频平台开始接受杜比视界内容的UGC投稿，杜比视界8.4制作一时也成为了制作高质量视频的选择。而可惜的是，杜比视界8.4内容的制作至今依然过于依赖macOS或iOS平台，在Windows等平台制作Dolby Vision Profile 8.4内容依然还有很高门槛。截至本文发稿，Windows平台下能完成杜比视界8.4内容创作的软件仍只有Davinci Resolve 18，且它必须要求计算机有杜比视界解码能力才能出片，否则则需要杜比视界Trim License，而这玩意并不容易搞到。（然并卵，因为此处我们要用的DEE也要去找Dolby买License... - 所以如果您搞不到这两个License的任意一个的话，您可以关闭这标签页了。。。而且我觉得有功夫去联系Dolby买License，还不如去买个支持杜比视界编码的电脑然后用达芬奇实在0.0）——链接：杜比视界 UGC 视频制作 （四）｜ 使用达芬奇软件制作杜比视界 UGC 内容https://www.bilibili.com/video/BV1aa411g7MT/
注：该方法原先是B站用户@nilaoda https://space.bilibili.com/4743331写在他专栏里的教程。不过后来不清楚什么原因，他删除了其专栏及讲解视频。因此，我想在此备份一下该解决方案，旨在想到万一可能有人会用到，仅此而已。我在此除了稍微修改了部分内容外，完全照搬了大佬的原文，可以说全部研究成果都归@nilaoda大佬所有。在此也备注给原作者和Dolby，若您认为本文的发表有任何问题，可在B站私信我@izwb003 https://space.bilibili.com/36937211，我会在看到消息后第一时间按您要求处理本文。谢谢理解！
据原作者所述，该方法在研究过程中得到了B站用户@翼格小和奏 https://space.bilibili.com/669921?spm_id_from=333.337.0.0的帮助。两人都花费了很长时间完成了相关研究，也走过不少弯路，在此对两位大佬的努力表示感谢！
也欢迎您去看看大佬的博客。大佬还在博客里写过好多其他的骚操作。

工具准备
-杜比视界视频流验证工具
https://customer.dolby.com/content-creation-and-delivery/dolby-vision-professional-verification-toolkit-lite-v410
-杜比视界编码引擎（DEE）
https://customer.dolby.com/content-creation-and-delivery/dolby-encoding-engine-with-dolby-ac-4-v521
-FFmpeg
https://www.gyan.dev/ffmpeg/builds/ffmpeg-git-full.7z
-dovi_tool
https://github.com/quietvoid/dovi_tool/releases
-mp4muxer
https://github.com/DolbyLaboratories/dlb_mp4base/tree/master/bin
-mp4box（建议使用2022年1月份之前的版本）
https://www.videohelp.com/software/MP4Box/old-versions
-至少16G内存的PC（要不它撑不住RAW数据交换处理）
最后，您手上应该有这些文件：
dee_dv_mdgen.exe
dee_dv5_preproc.exe
dovi_tool.exe
dvesverifier.exe
ffmpeg.exe
license.lic
mp4box.exe
mp4demuxer.exe
mp4muxer.exe
制作步骤
素材要求
PQ/HLG视频素材，色彩原色bt2020。（但其实基本上什么文件都可以被libplacebo正确转换到HLG，包括杜比视界Profile5或SDR素材）

比如下图素材即为一合格素材：（图源：bilibili @nilaoda）
 
STEP1.预压制
很多时候，你的原始素材直接嵌入RPU并不能通过杜比视频验证工具，也未必能过B站的转码。为了更大的普适性，这里将无差别进行预压制。

假设您的视频文件叫footage.mp4。请在保存这个footage.mp4的目录下新建一个文件夹Tools，把上述文件放进去。

如果你的素材是PQ-HDR ，或DoVi(P5)，请使用如下命令将它转制到HLG ：
Tools\ffmpeg -y -init_hw_device vulkan=vulkan -filter_hw_device vulkan -i footage.mp4 -fps_mode cfr -vf "hwupload,libplacebo=iw:ih:format=yuv420p10le:colorspace=bt2020nc:color_primaries=bt2020:color_trc=arib-std-b67,hwdownload,format=yuv420p10le" -c:v libx265 _hlg.hevc
注：若卡了错或者只这样转制为HLG后成品过检测工具出Warning，抑或是嫌弃太慢，可以尝试先硬件加速转为一个非标的HLG的MP4
Tools\ffmpeg -y -hwaccel cuda -hwaccel_output_format cuda -extra_hw_frames 3 -i footage.mp4 -vf "hwupload=derive_device=vulkan,libplacebo=iw:ih:format=yuv420p10le:colorspace=bt2020nc:color_primaries=bt2020:color_trc=arib-std-b67,hwdownload,format=yuv420p10le" -c:v hevc_nvenc -c:a copy outputHLG.mp4
然后再压制这个HLG的MP4：
Tools\ffmpeg -i outputHLG.mp4 -fps_mode cfr -c:v libx265 -x265-params colorprim=bt2020:colormatrix=bt2020nc:transfer=arib-std-b67:annexb=1:aud=1:hash=1:repeat-headers=1:sar=1:level-idc=0:no-info=1 -bsf:v filter_units=remove_types=39 -b:v 35M -y _hlg.hevc
(从大佬的这篇博文https://github.com/nilaoda/Blog/discussions/56里了解更多)

如果你的素材本身就是HLG-HDR ，可直接使用如下命令压制：
Tools\ffmpeg -i footage.mp4 -fps_mode cfr -c:v libx265 -x265-params colorprim=bt2020:colormatrix=bt2020nc:transfer=arib-std-b67:annexb=1:aud=1:hash=1:repeat-headers=1:sar=1:level-idc=0:no-info=1 -bsf:v filter_units=remove_types=39 -b:v 35M -y _hlg.hevc
从ffmpeg的输出中记录关键信息：总帧数（最后一行输出的encoded xxx frames），帧率，分辨率（在上面的一行Stream里能看到）。

之后在cmd里输入如下内容，将这些信息创建成变量，方便下面多次使用。
set db_height=分辨率AxB的A值
set db_width=分辨率AxB的B值
set db_framerate=FPS值
set db_frame_count=总帧数
创建杜比视界metadata xml文件
由于dee工具接收的是raw视频流，而这种流的文件大小大到离谱会分分钟吃满硬盘，所以我们不输出成文件而采用命名管道推流。

执行以下命令，从命名管道ffmpeg_233读取数据，并设置格式为 raw 输入：
Tools\dee_dv_mdgen --input-format raw:pix_fmt=rgb48le:chroma=rec2020:width=%db_height%:height=%db_width%:framerate=%db_framerate%:eotf=hlg:streaming=1:frame_count=%db_frame_count% --mastering-display preset=bt2020_1000 --output-metadata _output.xml --progress 1 --overwrite 1 --input named_pipe://\\.\pipe\ffmpeg_233
执行后程序将等待推流。再开一个cmd窗口，使用ffmpeg向命名管道ffmpeg_233输出raw流数据：
Tools\ffmpeg -i _hlg.hevc -pix_fmt rgb48le -f rawvideo -y \\.\pipe\ffmpeg_233
等待完成后，会得到一个_output.xml元数据文件
生成杜比视界RPU文件
在第一个窗口继续输入：
Tools\dee_dv5_preproc --input-format raw:pix_fmt=rgb48le:width=%db_height%:height=%db_width%:framerate=%db_framerate%:streaming=1:frame_count=%db_frame_count% --input named_pipe://\\.\pipe\ffmpeg_233 --input-metadata _output.xml --output-rpu _output.rpu --output NULL --progress 1 --overwrite 1 --L11 4,0,0
另一个窗口还是输入命令输出raw流：
Tools\ffmpeg -i _hlg.hevc -pix_fmt rgb48le -f rawvideo -y \\.\pipe\ffmpeg_233
注：由于是分析过程，过程可能会非常缓慢且消耗性能。耐心一些。

使用dovi_tool嵌入rpu到ES流
执行以下命令，将output.rpu 嵌入_hlg.hevc 中：

Tools\dovi_tool inject-rpu -i _hlg.hevc --rpu-in _output.rpu -o _hlg_injected.hevc
转换至Profile 8.4
执行以下命令，将 RPU 转换为 8.4：

Tools\dovi_tool -m 4 convert _hlg_injected.hevc --discard -o _hlg_injected_converted.hevc
使用官方工具进行校验
执行以下命令，验证生成的_hlg_injected_converted.hevc ：

Tools\dvesverifier.exe -i _hlg_injected_converted.hevc --dv-profile 8.4 --progress --stop-on-error
正确情况下，不应有Error字样出现。各Test Case 应是PASS或 - 。若有Warning出现不一定代表失败，但是很有可能失败，比如上传B站无法转码等，所以应看一下输出，尝试调整。

封装
首先使用杜比官方工具进行封装：

Tools\mp4muxer -i _hlg_injected_converted.hevc -o _output_dolby.mp4 --hvc1flag 0 --dv-profile 8 --dv-bl-compatible-id 4 --mpeg4-comp-brand mp42,iso6,isom,msdh,dby1 --overwrite
后使用mp4box将原始音轨封装进去

Tools\mp4box -add _output_dolby.mp4#1:name= -add footage.mp4#2:name= -brand mp42isom -ab iso6 -ab msdh -ab dby1 -itags tool= -new _output_dolby_new.mp4
（大佬也有讲mp4box和Dolby Vision混流的相关知识。可以去参考大佬的博客https://github.com/nilaoda/Blog/discussions/43）
注：若有单独提取出来的音轨且是mp4muxer支持的格式，也可以直接在mp4muxer封装时就封进去，不使用mp4box亦可。

最终成品：（图源：bilibili @nilaoda）
 

这个文件就可以用于投稿了，并且能够被正确识别为杜比视界。

这个过程并不是最科学的，很多地方是多余的。不过暂时没有找到更好的方式了……
