# 微信热补丁 `tinker` 官方示例, 持续更新


## v1.0.1 基础包

+ 该版本作为基础包

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

配置基础包, tinkerid, dexMode等，详见gradle配置: [tinker task 配置](/app/build.gradle#140)

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

这里需要注意, 命令在linux和mac下最好是`./gradlew`, 意思是当前项目的gradlew, 如果写成`gradlew`可以会去下载gradle等, 因为那是全局的...
而windows就可以是`gradlew`

**注意** debug和release配置的基包不同, 和他们一一对应, 另外, release还需要配置mapping文件.

2 双击对应task

就是去gradle projects里找到对应task, 双击执行就可以, 如下图:

![]()

比如, 打debug补丁, 双击`tinkerPatchDebug`就可以了

下一次打补丁时就可以从快捷栏选择，然后点击右侧运行, 如下图:

![]()

