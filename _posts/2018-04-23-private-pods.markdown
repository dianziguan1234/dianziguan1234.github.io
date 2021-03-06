---
layout:     post
title:      "在iOS项目中使用CocoaPods私有库"
subtitle:   "组件化"
date:       2018-04-23 23:00:00
author:     "管维诚"
header-img: "img/gwc_sea.jpg"
---

### 1.在Git上创建Spec仓库

#### Spec是Pods索引库，所有公开的Pods都在这个里面，他实际是一个Git仓库remote端 在GitHub上，但是当你使用了Cocoapods后他会被clone到本地的~/.cocoapods/repos目录下，可以进入到这个目录看到master文件夹就是这个官方的Spec Repo了。这个master目录的结构是这个样子的:
![](http://p2bzzkn05.bkt.clouddn.com/18-4-23/1469452.jpg)      
每次我们在执行pod install或者是pod update的时候都会自动更新这个spec里面的内容，拉取最新的数据回来。然后我们使用pod search的时候就是直接从这个里面进行查询的。

### 2.向CocoaPods添加该仓库

```pod repo add 仓库名 仓库地址```

示例如下:

```pod repo add Specs git@gitee.com:he11oworld/Specs.git```

添加成功之后可以通过pod repo list的命令查看，结果如下所示：
![](http://p2bzzkn05.bkt.clouddn.com/18-4-23/16271526.jpg)

### 编写podspec文件

#### 1.搭建项目的结构，创建podspec文件

podspec是一个配置文件，该文件描述了一个库是怎样被添加到工程中的。在这个配置文件里面配置了：库文件的名称、源码存放路径、版本号、依赖库和编译条件等。为了避免写错，建议使用命令行的方式生成。

在终端执行pod spec create Foundation-pd，CocoaPods会自动帮我们生成Foundation-pd.podspec的模板文件。推荐搭建项目的文件组织结构如下所示：
![](http://p2bzzkn05.bkt.clouddn.com/18-4-23/99797113.jpg)
#### 2.提交代码到git服务器

```
git add .

git commit -m "first commit"

git push origin master

git tag -a 1.0.0 -m 'v1.0.0'

git push origin tag 1.0.0

```

### 3.编写podspec文件，示例podspec内容如下，建议参考CocoaPods上面的来做，照着那些第三库的podspec文件写就行了。
参照例子
<https://github.com/we11cheng/pods_test/blob/master/gwc_spec.podspec>

### 提交podspec文件到Spec仓库

#### 1.检查podspec文件是否正确

```pod spec lint Foundation-pd.podspec --verbose --sources='git@gitee.com:he11oworld/Specs.git,https://github.com/CocoaPods/Specs' --use-libraries```

#### 2.提交podspec文件到Spec库

```
pod repo push Specs Foundation-pd.podspec --verbose --sources='git@gitee.com:he11oworld/Specs.git,https://github.com/CocoaPods/Specs' --use-libraries --allow-warnings

```
```Foundation-pd``` 自行替换

提交完成之后可以通过```pod search Foundation-pd```检验是否成功：


说明：

(1) --verbose:表示显示全部的日志信息，建议加上这个，方便判断错误信息。

(2) --sources:如果我们在podspec里面依赖到一些私有的库之后，直接进行校验是会报错的提示找不到，这里加上我们的Spec仓库的地址告诉CocoaPods找不到的时候去哪里找。

(3) --allow-warnings:表示允许警告.

(4) --use-libraries:表示使用静态库或者是framework，这里主要是解决当我们依赖一些framework库后校验提示找不到库的时候用到。


```source 'https://github.com/CocoaPods/Specs.git'```

```source 'git@gitee.com:he11oworld/Specs.git'```


