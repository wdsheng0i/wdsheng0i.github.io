---
layout: post
title: WVP + ZLMediaKit + MediaServerUI实现摄像头GB28181推流播放录制
category: acide
tags: [acide]
---

## 参考资料
- [基于GB28181-2016标准实现的开箱即用的网络视频平台: wvp-GB28181-pro](https://github.com/648540858/wvp-GB28181-pro)
- [流媒体服务ZLMediaKit](https://github.com/ZLMediaKit/ZLMediaKit)
- [播放器essibuca](https://github.com/langhuihui/jessibuca/tree/v3)
- [前端页面基于MediaServerUI](https://gitee.com/kkkkk5G/MediaServerUI)
- [WVP + ZLMediaKit + MediaServerUI实现摄像头GB28181推流播放录制](https://notemi.cn/wvp---zlmedia-kit---mediaserverui-to-realize-streaming-playback-and-recording-of-camera-gb28181.html)
- [WVPPro+ZLMediaKit+大华相机推流+安卓模拟GB28181设备推流](https://blog.csdn.net/qq_22310551/article/details/122087930)
- [国标GB28181介绍](https://blog.csdn.net/weixin_38746576/article/details/106780001)
- 视频来源于BILIBILI：https://b23.tv/FrqIFe

## wvp介绍
WEB VIDEO PLATFORM是一个基于GB28181-2016标准，依托优秀的开源流媒体服务框架ZLMediaKit，实现的开箱即用的网络视频平台。  
- 负责实现核心信令与设备管理后台部分，支持NAT穿透，支持海康、大华、宇视等品牌的IPC、NVR接入。    
- 支持国标级联，
- 支持将不带国标功能的摄像机/直播流/直播推流转发到其他国标平台。  

## 一、环境搭建
- [wvp平台部署](https://github.com/648540858/wvp-GB28181-pro/wiki)
- [ZLMediaKit流媒体服务部署](https://github.com/ZLMediaKit/ZLMediaKit/wiki/%E5%BF%AB%E9%80%9F%E5%BC%80%E5%A7%8B)
- [wvp与ZLMediaKit联调](https://github.com/648540858/wvp-GB28181-pro/wiki/%E4%B8%8EZLMediaKit%E8%81%94%E8%B0%83#zlm%E9%85%8D%E7%BD%AE)

## 二、设备接入
- [WVP + ZLMediaKit + MediaServerUI实现摄像头GB28181推流播放录制](https://notemi.cn/wvp---zlmedia-kit---mediaserverui-to-realize-streaming-playback-and-recording-of-camera-gb28181.html)
- [WVPPro+ZLMediaKit+大华相机推流+安卓模拟GB28181设备推流](https://blog.csdn.net/qq_22310551/article/details/122087930)

## 三、推流拉流测试
- [WVP + ZLMediaKit + MediaServerUI实现摄像头GB28181推流播放录制](https://notemi.cn/wvp---zlmedia-kit---mediaserverui-to-realize-streaming-playback-and-recording-of-camera-gb28181.html)
- [WVPPro+ZLMediaKit+大华相机推流+安卓模拟GB28181设备推流](https://blog.csdn.net/qq_22310551/article/details/122087930)

## 四、概念了解
- H.264与H.265  
	都是视频压缩格式，由于视频本身的码流太大，所以需要经过压缩然后再通过网络进行传输。H.264是目前比较主流的压缩算法，像视频会议设备一般都采用这个编码格式。基础的H.264可以支持在1M带宽下传输720P30帧/秒的图像；H.264 HIGH PROFILE支持在512K带宽下传输720P30帧/秒的图像。  
- H.265  
	是比较新的压缩算法，可以更一步提高压缩比，随着我们现在生活中出现的视频格式越来越大（比如现在基本都是1080P甚至4K的显示器，4K片源将来也会越来越多），就需要像H.265这样的新压缩算法，提高效率、节约带宽或存储空间。H.265支持在384K带宽下传输720P30帧/秒。   
- AAC（Advanced Audio Coding）：高级音频编码  
- DVR：Digital Video Recorder，  
	即数字视频记录器，通常称为数字硬盘录像机，因为采用硬盘作为存储载体已经是最主流的模式。DVR最主要的特点是：可以单独工作的监控设备，可以在本地监控、回放及报警处理，当然，现在的DVR也基本具备网络功能，可以实现网络传输  
- NVR：Network Video Recorder,  
	即网络视频记录器，或者叫网络硬盘录像机，其实还有另一个名字：Hybrid DVR，即混合型DVR，实际上，是在DVR的基础上，增加了对视频服务器/网络摄像机的接入，即除了自身具备硬盘录像机的功能外，还可以存储一些视频服务器/网络摄像机的视频数据。   
- DVS：Digital Video Server，  
	即数字视/频服/务器，简称视/频服/务器，其实，视/频服/务器是从视频编/码/器发展而来，视/频服务器与DVR的最大区别是，视/频服务器必须要在网络上才能有用，无法单独使用，一旦网络失效，视/频服务器就失去作用。视/频服务器主要用来解决远程监控的问题   
- IPC 即IP-CAMERA，  
	是集成视频服务器和摄像机的功能为一体的数字视频设备；IP-CAMERA网络摄像机一般有内置Web服务的数字摄像机和录音设备，直接与以太网（有线、无线）相连。用户可通过标准Web浏览器观看和收听网络摄像机传送过来的视频和声音  
- RTP 协议Real-time Transport Protocol  
	详细说明了在互联网上传递音频和视频的标准数据包格式。它一开始被设计为一个多播协议，但后来被用在很多单播应用中。RTP协议常用于流媒体系统（配合RTSP协议），视频会议和一键通（Push to Talk）系统（配合H.323或SIP），使它成为IP电话产业的技术基础。RTP协议和RTP控制协议RTCP一起使用，而且它是创建在UDP协议上的  
- SIP（Session initialization Protocol，会话初始协议）  
	是由IETF（Internet Engineering Task Force，因特网工程任务组）制定的多媒体通信协议。)是一个应用层的信令控制协议。用于创建、修改和释放一个或多个参与者的会话。这些会话可以是Internet多媒体会议、IP电话或多媒体分发。会话的参与者可以通过组播（multicast）、网状单播（unicast）或两者的混合体进行通信。  
- GB28181协议：https://blog.csdn.net/fanyun_01/article/details/120536834  
	全称为GB/T28181《安全防范视频监控联网系统信息传输、交换、控制技术要求》，是由公安部科技信息化局提出，由全国安全防范报警系统标准化技术委员会(SAC/TC100)归口，公安部一所等多家单位共同起草的一部国家标准（以下简称28181）。 GB28181协议在全国平安城市、交通、道路等监控中广泛采用，若想做统一的大监控平台，则支持28181协议接入是必不可少的   
- gat1400协议  
	中华人民共和国 公共安全行业标准 -GA/T1400.3－2017 公安视频图像信息应用系统 标准及说明  
	GA/T 1400是于2017年首次发布关于图片、视频片段、文件等属性对象的传输协议  
	GA/T 1400《公安视频图像信息应用系统》分成4个部分： 第一部分--通用技术要求； 第二部分--应用平台技术要求； 第三部分--数据库技术要求； 第四部分--接口协议部分  
- [国标级联概述](https://cloud.baidu.com/doc/EVS/s/Lkunxdngi)

## 问题记录
环境部署问题记录：wvp-GB28181-pro + ZLMediaKit 
````
1.yum install openssl openssl-devel

2.CentOS 安装 转码软件FFMPeg：https://blog.csdn.net/weixin_44037416/article/details/121290131
  	先sudo yum update -y
	后sudo yum install epel-release -y

3.CMake 3.1.3 or higher is required. You are running version 2.8.12.2
	升级cmake: https://blog.csdn.net/qq_34935373/article/details/90266958
	CMake 运行错误could not find CMAKE_ROOT！！！  https://blog.csdn.net/lichen18848950451/article/details/79912265
	清除缓存即可，只需一行命令：hash -r

4.Host is not allowed to connect to this MySQL server解决方法
	https://blog.csdn.net/weixin_43989637/article/details/112009123

5.dev.yml: 缺少配置项 hook-ip: 192.168.145.132

6.The encoder ‘acc‘ is experimental codecs are not enabled ,add ‘-strict -2‘ if you want to use it.
	ffmpeg -re -i "/opt/oceans.mp4" -vcodec h264 -acodec aac -f rtsp -rtsp_transport tcp -strict -2  rtsp://127.0.0.1/live/test
	ffmpeg -re -i "/opt/oceans.mp4" -vcodec h264 -acodec aac -f flv -strict -2  rtmp://127.0.0.1/live/test

7.WebRTC（网页实时通信技术）https://webrtc.org.cn/webrtc-tutorial-datachannel/
	是一系列为了建立端到端文本或者随机数据的规范，标准，API和概念的统称。这些对等端通常是由两个浏览器组成，但是WebRTC也可以被用于在客户端和服务器之间建立通信连接，或者在任何其他可以实施WebRTC标准的设备之间进行通信建立。

8.[未注册的zlm] 拒接接入：jzRQbcHF1c7mIdQj来自192.168.145.132：80
	2022-04-27 01:52:14.274 [wvp-1] WARN  c.g.iot.vmp.service.impl.MediaServerServiceImpl:358 - 请检查ZLM的<general.mediaServerId>配置是否与WVP的<media.id>一致
````