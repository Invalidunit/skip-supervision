# skip supervision

__跳过 iPhone 监督锁__


目前方法测试可在 iOS 14.8 上工作，但不排除可以在更高版本工作。（也许用 Palera1n 也可以？）

反正大于或等于 A12 的都不用看了，自觉关掉网页

请先自行用 Checkra1n，将 iPhone 启动，然后使用 iproxy 之类的连接到手机，用来执行后续命令。

---

## -1 说点别的？

建议执行完之前先不连接 Wi-Fi，虽然没啥所谓

如果已经使用了其他工具捣鼓过，然后自己有洁癖的话可以使用下面命令将设备抹掉所有设置和内容，重新开始

    nvram oblit-inprogress=5
    kill 1

## 0 只是方便你复制，将 CloudConfigurationDetails.plist 路径赋值给变量 config

    config="/var/containers/Shared/SystemGroup/systemgroup.com.apple.configurationprofiles/Library/ConfigurationProfiles/CloudConfigurationDetails.plist"

## 1 创建 CloudConfigurationDetails.plist

### 1.0 防止你已经创建过文件（已经自己联网，并看见监督界面（远程管理））

    rm -f "${config}"

### 1.1 使用 plutil 创建空 plist 文件

    /binpack/usr/bin/plutil -create "${config}"

### 1.2 为文件设置权限

    chown mobile:nobody "${config}"
    chmod 0666 "${config}"

## 2 写入内容

    /binpack/usr/bin/plutil -key "AllowPairing" -type bool -value true "${config}"
    /binpack/usr/bin/plutil -key "CloudConfigurationUIComplete" -type bool -value true "${config}"
    /binpack/usr/bin/plutil -key "ConfigurationSource" -int "0" "${config}"
    /binpack/usr/bin/plutil -key "IsSupervised" -type bool -value false "${config}"
    /binpack/usr/bin/plutil -key "PostSetupProfileWasInstalled" -type bool -value false "${config}"

## 3 清理本地缓存

    rm -rf '/var/mobile/Library/Caches/'

## 4 重新启动欢迎程序（Setup）

### 若后续不想越狱

清理 Checkra1n 文件残留

    umount -f /private/var/binpack
    rm -rf /var/binpack
    rm -f /var/checkra1n.dmg
    rm -f /var/dropbear_rsa_host_key
    rm -f /var/dropbear_rsa_host_key

### 若后续想越狱

那就直接看后面

----

这里可以选择直接重启

    kill 1

或者重新加载欢迎程序（Setup）

    killall -9 Setup

## 5 走正常流程激活

> 不需要顾虑什么，正常的连接 Wi-Fi，激活手机，登录 Apple ID 与 iCloud，不会有监督页面（远程管理）弹出。
