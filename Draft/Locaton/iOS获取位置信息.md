# 在iOS设备中获取用户位置信息

在iOS应用开发中获取位置，减少用户操作从而优化体验，是个常用操作。本篇博文介绍如果接入CoreLocation获取设备位置。

## 接入流程

**准备工作**

1. 导入CoreLocation库，`import CoreLocation`
2. 在info.plist文件中添加获取位置权限信息，Apple提供了以下两个级别的位置权限

* Privacy - Location When In Use Usage Description: 当应用处于前台时请求获取位置信息
* Privacy - Location Always Usage Description: 前后台都可以请求位置信息

**请求定位权限**




