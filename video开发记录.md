# 记录video开发兼容问题

- **兼容问题（Android，iOS）**
    
    安卓上可以自定义controls，iOS不全屏可以使用自定义controls，全屏会使用iOS系统视频播放器（所有app），想要在iOS app中不全屏播放，需要iOS app进行参数设置webview.allowsInlineMediaPlayback = YES;
    
- **iOS10以下系统 只有点击播放后会触发onload()、canplay()事件**读取文件加载

        var reader = new FileReader();
        reader.onload = function (e) {}
        reader.readAsDataURL(input.files[0]);

- **全屏和退出全屏方法 需要做判断兼容处理**

- **部分浏览器对取消静音操作增加暂停视频功能**

- **部分浏览器，取消全屏会暂停播放**

- **微信无法静音播放**
