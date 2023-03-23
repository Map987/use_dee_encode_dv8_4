* __注__：如果有任何链接已经失效，或者您知道其他更有用的指南，请通过[联系我](//yukisubs.wordpress.com/about)提出您的建议。
* 最后更新时间：2020年9月28日
* Markdown 和白色版本[在此提供](//gist.github.com/YukinoAi/acea024631a2585aa592b16b4bde959f)，[替代方案](//gist.github.com/zeriyu/e6c171f6224d6134d79996c11de02995)，点击“ Raw ”。


#### __粉丝字幕组制作指南索引__：

* [粉丝字幕组概述](#fansubbing-overview)
* [视频采集](#capping)
* [视频编码](#encoding)
    * 音频编码
    * 播放（计算机/家庭影院）
* [视频复用和解复用](#muxing-and-demuxing)
* [视频过滤](#filtering)
    * 情景过滤
* [翻译（TL）](#translating-tl--translate-check-tlc)
* [编辑和质量检查（QC）](#editing--quality-check-qc)
* [时间轴](#timing)
* [字幕排版](#typesetting)
* [发布（Distro）](#distribute-distro)
    * 种子
    * HTTP/S
    * IRC
    * [各种发布工具](#random-distro-tools)
    * [团队博客](#the-group-blog)
    * [为自助制作（DIY）的人发布](#distro-for-do-it-yourself-diy-people)
* [重新复用](#remux)
* [模拟信号处理](#in-service-of-chaos-analog)


#### __粉丝字幕制作概述__
* __理论__：[动漫粉丝字幕制作的历史](//en.wikipedia.org/wiki/Fansub)和[粉丝字幕制作的现状：已死亡](http://www.crymore.net/2015/05/15/the-state-of-fansubbing-its-dead)。
* __各种指南索引或概述__：
    * 这个指南索引。备选方案：
    * Doki的[粉丝字幕制作流程概述](//doki.co/support/the-fansubbing-process)，[便携文档格式（PDF）](//yukisubs.files.wordpress.com/2016/10/the_fansubbing_process_doki_fansubs.pdf)。
    * unanimated的[指南索引](//unanimated.github.io/guides.htm)
        * 排版指南[Guide](//unanimated.github.io/ts/index.htm)，[PDF-白色](//yukisubs.files.wordpress.com/2020/04/typesetting_in_aegisub_by_unanimated_2018_white.pdf)，[PDF-黑色](//yukisubs.files.wordpress.com/2020/04/typesetting_in_aegisub_by_unanimated_2018_black.pdf)，[old-PDF](//yukisubs.files.wordpress.com/2016/10/unanimated_fansub_guides.pdf)。
    * SubsByRock [索引](//subsbyrock.wordpress.com/links)。
    * tekeremata的[不知名的东西](http://tekeremata.org/category/articulos/taller-de-fansub/)（西班牙语，请向下滚动）。
    * 还有这个随意的[白痴入门指南](//fansubbing.blogspot.com/2007/03/what-goes-into-fansub

*Aegisub*：粉丝字幕制作的核心工具，可从[二进制文件](http://plorkyeran.com/aegisub)处获取。
* Aegisub的[主要文档](http://docs.aegisub.org/manual/Overview)、[PDF](//yukisubs.files.wordpress.com/2017/05/aegisub-3-2-manual.pdf)、[路径说明符](http://docs.aegisub.org/3.2/Aegisub_path_specifiers/)，以及这个[ASS Override标签](http://docs.aegisub.org/manual/ASS_Tags)。
* [随机粉丝字幕制作技巧](http://mod16.org/hurfdurf/?p=128)（面向团队）。
* 《粉丝字幕组的一天》：[博客文章](http://mod16.org/hurfdurf/?p=144)、[采访](//uniquec.shinsen-radio.org)和[YouTube播放列表](//www.youtube.com/playlist?list=PLtwIkfU56fRSZgaPHFt79ei1_Kf4cDaFY)，以及曾经的情况: `web.archive.org/web/20021013081748/http://pepper.idge.net:80/digisub.html`，[PDF](//yukisubs.files.wordpress.com/2018/02/digisub_guide_onceuponatimein200
