---
layout: post
title: Cocos2d-x lua binding.
---

{{ page.title }}
================

<p class="meta">5 June 2017 - Cocos2d-x lua binding.</p>


我使用的Cocos2d-x版本是3.10的。绑定的过程中使用了这位兄弟的现成品。
[github](https://github.com/TshineZheng/Dragonbones-Cocos2dx)
可以直接下载下来，然后进行替换，一般情况下就可以正常使用了。


如果你对绑定的原理还不理解的话，可以看下[这里](https://segmentfault.com/a/1190000000631630)，我也是搜索到的，感觉看完还是有作用的。


如果要打包安卓的话需要配置这三个工程的Android.mk文件。

frameworks-runtime-src-proj.android-jni  下的[Android.mk](https://github.com/MENGJIANGTAO/mengjiangtao.github.io/blob/master/_Resources/frameworks-runtime-src-proj.android-jni.mk)



framework-cocos2d-x-cocos-scripting-lua-bindings-proj.android 下的[Android.mk](https://github.com/MENGJIANGTAO/mengjiangtao.github.io/blob/master/_Resources/framework-cocos2d-x-cocos-scripting-lua-bindings-proj.android-Android.mk)


framework-cocos2d-x-cocos-editor-support-dragonbones-render-cocos2d-x-3.x-proj.android下的[Android.mk](https://github.com/MENGJIANGTAO/mengjiangtao.github.io/blob/master/_Resources/framework-cocos2d-x-cocos-editor-support-dragonbones-render-cocos2d-x-3.x-proj.android-Android.mk)


代码中需要添加这个，我是写在了CCLuaStack.cpp init()函数中的。
```
register_all_dragonbones(_state);

```

这些都配置完成后，就可以使用cocos compile -p android打包了。
