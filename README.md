# Windows(PC) SDK 开发手册## 网易云信服务概要云信以提供客户端SDK（覆盖Android、iOS、Web、PC）和服务端OPEN API 的形式提供即时通讯云服务。开发者只需想好APP 的创意及UI 展现方案，根据APP 业务需求选择云信的相应功能接入即可。注：SDK 兼容的系统有 Windows xp(sp2以上）、Windows 7、Windows 8(8.1)、Windows 10。## 开发准备### 总体接口约定SDK 提供的所有API 都是**C接口**，根据模块划分为不同的API 文件，只提供动态加载dll 的方式获取API 并调用。App 开发者只需要引用SDK包里nim\_c\_sdk\\include 目录下的模块定义头文件（命名方式如nim\_xxx\_def.h）即可。关于API 的定义，可以查看API 文档或SDK 包里nim\_chatroom\_c\_sdk\\api 目录里的API 头文件（命名方式如nim\_xxx.h）。一般来说，每个模块都有对应的API 头文件和定义头文件。SDK 提供了2种类型的接口。第一种：注册回调和执行接口分开定义，这类接口需要提前注册好回调函数，然后执行接口时，调用相应的回调函数输出结果，App 上层需要在回调函数里处理结果。这类回调函数可能由调用执行接口触发，也有可能由SDK 主动触发，一般由SDK 主动触发回调函数（如接收消息等）。第二种：回调函数作为参数，传入执行接口，然后执行接口时，会触发传入的回调函数。注意，回调函数中如果涉及到跨线程资源的需要开发者切换线程操作，即使不涉及到资源问题，也要保证回调函数中不会处理耗时任务，以免堵塞SDK底层线程。**从版本2.7.0开始，服务器和客户端上线了频控策略，与服务器有交互的接口(接口命名结尾为_online的查询接口以及命令类接口，如同意群邀请等)增加频控控制，开发者关注错误码416。**### SDK内容* nim.dll： SDK核心功能模块；放在用户程序目录下* nim_audio.dll： 负责语音录制和播放；放在用户程序目录下* nim\_tools\_http.dll： 负责通用的http传输；nim.dll模块使用了此功能，C++封装层(需要依赖Demo工程)* nrtc.dll： 负责视频聊天功能；放在用户程序目录下* nim_conf： SDK版本相关；放在用户程序目录下* nim\_c\_sdk： IM C 接口说明以及SDK所有定义的头文件（头文件用户自己决定放在合适位置）。* nim\_chatroom\_c\_sdk: 聊天室 C 接口说明以及SDK所有定义的头文件（头文件用户自己决定放在合适位置）。

**SDK不提供debug版本的动态链接库供开发者调试，如果有问题请联系技术支持或在线客服。**### C++ 封装层为了方便桌面应用开发者更快的接入云信SDK，PC SDK下载包中还提供了官方的[C++ 封装层](https://github.com/netease-im/NIM_PC_SDK-CPP- "target=_blank")项目文件及源码，接入和使用方法请看[Windows(PC) SDK开发手册(C++ 封装层)](?doc=pc_cpp "target=_blank")，目前DEMO源码就是通过接入和调用该SDK完成IM功能的。### C# 封装层云信SDK还提供了C# 程序集，方便.net 开发人员接入,PC SDK下载包中包括官方的[C# 封装层](https://github.com/netease-im/NIM_PC_SDK-CSharp "target=_blank")项目文件及源码，接入和使用方法请看[Windows(PC) SDK开发手册(C# 封装层)](?doc=pc_csharp "target=_blank")，目前DEMO源码就是通过接入和调用该SDK完成IM功能的。**如果开发者在调用C接口过程中或者解析接口返回结果过程中出现疑问，可以参考和借鉴C++封装层。**### SDK数据目录当收到多媒体消息后，SDK 会负责下载这些多媒体文件，同时SDK 还要记录一些log，因此SDK 需要一个数据缓存目录。该目录由第三方App 通过nim\_client\_init 初始化接口传入，默认为存放到系统的AppData 目录下，App 传入一个目录名即可，SDK 会自动生成用户数据缓存目录。数据缓存目录默认为"{系统的AppData 目录}\\{App 传入的目录名}\\NIM\\{某个用户对应的用户数据目录}”，还可以由App 完全自定义用户数据目录，需要传入完整的路径，并确保读写权限正确。如果第三方App 需要清除缓存功能，可扫描该目录下的文件，按照你们的规则清理即可。具体某个用户对应的缓存目录下面包含如下子目录：- image：图片消息文件- audio：语音消息文件- video：视频消息文件- res：其他资源文件SDK 提供了接口nim\_tool\_get\_user\_specific\_appdata\_dir 获取某个用户对应的具体类型的App data 目录（如图片消息文件存放目录，语音消息文件存放目录等）（注意：需要调用nim\_free\_buf 接口释放其返回的内存）。 