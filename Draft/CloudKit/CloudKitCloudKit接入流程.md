# CloudKit接入流程

介绍为App接入CloudKit的整体流程，以及一些基本功能的使用示例。是一篇翻译向文章[查看原文CloudKit](https://developer.apple.com/documentation/cloudkit)

## 根据内容选择存储方式

我们存储的内容可能有以下三种形式：文件，键值对，关系数据库。

对于文件存储并且希望通过iCloud在用户不同设备间共享,我们可以通过FileManager实现，具体方法如下:
```Swift
func url(forUbiquityContainerIdentifier containerIdentifier: String?) -> URL?
```
更多文件存储内容可以参考[Designing for Documents in iCloud](https://developer.apple.com/library/archive/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForDocumentsIniCloud.html#//apple_ref/doc/uid/TP40012094-CH2-SW1)

如果你需要存储的内容是简单的键值对可以使用[NSUbiquitousKeyValueStore](https://developer.apple.com/documentation/foundation/nsubiquitouskeyvaluestore)，当然证对你存储的数据类型有些要求，一般需要以字符串为Key, Value值可以是布尔值，数字，字符串(String),日期（Date），二进制数据（Data）以及

如果你要存储的是关系型数据库，你可以使用CoreData+iCloudKit或者直接使用CloudKit，直接是用CloudKit繁琐些，也是我们接下来主要介绍的内容。


## 在应用中开启CloudKit功能

首先你必须是苹果公司的注册开发者（交了99刀保护费），然后就可以在你的项目中按照下图开启iCloud功能。

![add cloudkit](https://docs-assets.developer.apple.com/published/86388fde1a/rendered2x-1604920522.png)

创建container，同时开启推送通知，苹果是通过静默推送的方式来监听iCloud数据信息的变化的。

![创建container](https://docs-assets.developer.apple.com/published/34c76f3917/rendered2x-1604920526.png)

登录测试设备并开启iCloud(后面测试回用到)

![](https://docs-assets.developer.apple.com/published/f43a3fae52/rendered2x-1604486161.png)

登录iCloud dashboard [icloud.developer.apple.com](https://icloud.developer.apple.com) 使用Safari浏览器，其他的可能会卡在loading界面。

现你已经为你的App接入的iCloud, 接下就可以创建和同步数据了。
