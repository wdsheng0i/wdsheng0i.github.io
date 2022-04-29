---
layout: post
title: WVP + ZLMediaKit + MediaServerUI实现摄像头GB28181推流播放录制
category: iot
tags: [iot]
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