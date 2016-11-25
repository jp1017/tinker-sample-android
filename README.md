# 微信热补丁 `tinker` 官方示例, 持续更新

[![Build Status](https://travis-ci.org/jp1017/tinker-sample-android.svg?branch=master)](https://travis-ci.org/jp1017/tinker-sample-android)

<img src="http://7xlah4.com1.z0.glb.clouddn.com/2016-11-25-10-52-25-294_tinker.sample..png" width="320"/> <img src="http://7xlah4.com1.z0.glb.clouddn.com/2016-11-25-10-51-57-743_tinker.sample..png" width="320"/>

# 版本说明

## v1.0.3 add CI

## v1.0.2 补丁测试

## v1.0.1 基础包

+ 该版本作为基础包, 分为debug和release

# tinker 接入指南

## 安装tinker gradle插件

1 在项目的build.gradle中, 添加tinker-patch-gradle-plugin的依赖

```
buildscript {
    dependencies {
        classpath ('com.tencent.tinker:tinker-patch-gradle-plugin:1.7.5')
    }
}
```

2 然后在app的gradle文件app/build.gradle，我们需要添加tinker的库依赖以及apply tinker的gradle插件.

//apply tinker插件
>apply plugin: 'com.tencent.tinker.patch'

```
dependencies {
    //可选，用于生成application类
    provided('com.tencent.tinker:tinker-android-anno:1.7.5')
    //tinker的核心库
    compile('com.tencent.tinker:tinker-android-lib:1.7.5')
}
```

## 配置tinker task

配置基础包, tinkerid, dexMode等，详见gradle配置: [tinker task 配置](https://github.com/jp1017/tinker-sample-android/blob/master/app/build.gradle)

我做了如下修改:

1 修改tinkerid为版本号, 跳过了需要commit一次的坑:smile:
```
def getTinkerIdValue() {
    //版本作为id
    return android.defaultConfig.versionName
}
```
2 移动备份文件到/tinker/bakApk/下, 防止clean掉基础包文件

3 重命名备份文件, 比如`base-app-debug-v1.0.1-2016-1125.apk`, 当然自动生成的是`app-debug-v1.0.1-2016-1125.apk`, 需要手动添加前缀作为基础包, 后面多次编译不会把基础包覆盖掉, 也不会像官方demo里那样以秒命名产生很多文件...

4 修改tinker message 为 `I am the patch apk-v版本号`



**注意** 里面有些修改的地方, 包名修改为你的包名等, 我用todo做了标记

## 生成 Application

如果你有Application类, 那么需要自定义一个DefaultApplicationLike, 让tinker帮你生成Application

正如项目里的`public class SampleApplicationLike extends DefaultApplicationLike {`

并对类添加注解, 比如添加如下注解:

```
@DefaultLifeCycle(
application = "tinker.sample.android.app.SampleApplication",             //application name to generate
flags = ShareConstants.TINKER_ENABLE_ALL)
```

编译后, 会生成一个SampleApplication, 用这个作为你的Application, 写入清单文件

好了, tinker到这里就配置好了, 下面开始打补丁

## 打补丁包

1 命令行

打debug补丁: `./gradlew tinkerPatchDebug`

打release补丁: `./gradlew tinkerReleaseDebug`

这里需要注意, 命令在linux和mac下最好是`./gradlew`, 意思是当前项目的gradlew, 如果写成`gradlew`可以会去下载gradle等, 因为那是全局的, 比如AS2.2.2带的版本是2.14.1
而我现在的是最新版本3.2.1, 可输入`./gradlew -v` 和 `gradlew -v`　查看
而windows就可以是`gradlew`

**注意** debug和release配置的基包不同, 和他们一一对应, 另外, release还需要配置mapping文件.

2 双击对应task

就是去gradle projects里找到对应task, 双击执行就可以, 如下图:

![gradle](http://7xlah4.com1.z0.glb.clouddn.com/20161125%20125123tinker%20gradle.png)

比如, 打debug补丁, 双击`tinkerPatchDebug`就可以了

下一次打补丁时就可以从快捷栏选择，然后点击右侧运行, 如下图:

![patch](http://7xlah4.com1.z0.glb.clouddn.com/20161125130855tinker1.png)

## 安装及卸载补丁

### 加载补丁
第二个参数是补丁包存放路径, 名称任意, 可以不以 `.bak` 结尾

>TinkerInstaller.onReceiveUpgradePatch(getApplicationContext(), patchPath);

还可以自定义加载成功等交互, 请参考 `SampleResultService`, 别忘记添加进清单

### 清除补丁

当补丁出现异常或者某些情况，我们可能希望清空全部补丁，调用方法为：

>Tinker.with(context).cleanPatch();

当然我们也可以选择卸载某个版本的补丁文件：

>Tinker.with(context).cleanPatchByVersion();

在升级版本时我们也无须手动去清除补丁，框架已经为我们做了这件事情。需要注意的是，在补丁已经加载的前提下清除补丁，可能会引起crash。这个时候更好重启一下所有的进程。

### 查看补丁是否加载

>boolean isPatched = tinker.isTinkerLoaded();

# 参考

更多使用及问题请参考官方文档:

[Tinker -- 微信Android热补丁方案](https://github.com/Tencent/tinker/wiki)

[Tinker 接入指南](https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97)

[Tinker API概览](https://github.com/Tencent/tinker/wiki/Tinker-API%E6%A6%82%E8%A7%88)

[Tinker 自定义扩展](https://github.com/Tencent/tinker/wiki/Tinker-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%89%A9%E5%B1%95)

[Tinker 常见问题](https://github.com/Tencent/tinker/wiki/Tinker-%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)
