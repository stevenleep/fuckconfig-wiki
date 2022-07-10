源项目暂时不提供对外支持, 这个参仓库只是其中一些的实现原理 [点击查看](./fuckconfig-designIdeas.md)
# FuckConfig 是什么?
我的生产力产品大部分属于Apple和Linux家族下(应该说是基本)，在使用过程中经常会需要对各种工具进行配置，举几个简单的例子来说：
> 1、当我工作在Github上时候，我需要一个为Github单独的创建一个SSH Key，一些时候需要临时使用自己的笔记本处理公司事物（帮Review/MR一下），我需要一个新的SSH Key提供给Gitlab；当我管理不同的服务是否还会需要不同SSH Key；<br/>
> 2、一些时候我在开发类似NPM工具包和在编写项目的时候，对与各种工具的版本要求有不一样，有时候需要配置gitconfig、有时候有需要配置npm源、还需要配置node版本、以及hosts;

类似于上面的配置文件，很多工具都会存在属于自己的；
- 当多了以后我往往记不清楚他们的位置
- 以及他们分别是做什么的（你不得不承认有些配置你是真的不能通过名称分别出来）<br/>

我经常这些分布在各地的配置文件感到头疼，所以有了FuckConfig这个Desktop App；

## 如何管理配置文件
> 由于前期只是我个人的需求，实现方式不一定对所有人适用（比如：其他同学不一定存在我指定的记事本工具打开）
- 基础版本提供配置管理的功能，直接扫描当前用户系统下具备的配置文件，在预设的配置文件中进行映射和关联，分组展示在窗口中；
- 使用NSWorkspace服务去看当前runingAppcations中找一个正在启动的编辑器打开；
- 没有任何正在激活的编辑器, 会直接运行Vi编辑器打开（VSCode存在会在VSCode中打开）
> 通过link一件来安装应用的情况看下面👇的介绍

## MACOS 如何在线安装 APP?

FuckConfig还具备通过链接来安装开发环境的功能，具体的实现原理参考了[MacAppsLink](https://macapps.link/) , 通过 cur 来执行 shell 脚本程序安装;

```bash
clear && rm -rf ~/macapps && mkdir ~/macapps > /dev/null && cd ~/macapps

installApp() {
    if [ $1 == "dmg" ]; then curl -s -L -o "$2.dmg" $4; yes | hdiutil mount -nobrowse "$2.dmg" -mountpoint "/Volumes/$2" > /dev/null;
        if [[ $(appStatus "$3" "$2" "" "dmg" "$7") == "uninstalled" ]]; then cp -R "/Volumes/$2/$3" /Applications; fi;
    hdiutil unmount "/Volumes/$2" > /dev/null && rm "$2.dmg"
    elif [ $1 == "zip" ]; then curl -s -L -o "$2.zip" $4; unzip -qq "$2.zip";
        [[ $(appStatus "$3" "" "$5" "zip" "$7") == "uninstalled" ]]; then mv "$5$3" /Applications;
    rm -rf "$2.zip" && rm -rf "$5" && rm -rf "$3"
    elif [ $1 == "tar" ]; then curl -s -L -o "$2.tar.bz2" $4; tar -zxf "$2.tar.bz2" > /dev/null;
        [[ $(appStatus "$3" "" "$5" "tar" "$7") == "uninstalled" ]]; then mv "$5$3" /Applications;
    rm -rf "$2.tar.bz2" && rm -rf "$3"; fi
}
```
是否有其他方式：

- [setapp](https://setapp.com/)
  > 暂时还不清楚 setapp 背后如何实现的第三方应用安装的，不过 setapp 会在 Applications 下创建一个 SetApp 文件夹， 存放由 SetApp 提供的应用。
  >
  > 官方表示快速安装正在努力中: [quickly-install](https://support.setapp.com/hc/en-us/articles/360008560299-How-do-I-quickly-install-my-Setapp-apps-on-another-Mac-)

## 为什么 MacOS 不支持第三方安装？

- Gatekeeper:检查是否存在 Apple 颁发的证书
- XProtect：阻止任何的恶意代码执行


## IOS 如何在线安装 APP?

[Apple Developer Enterprise Program](https://developer.apple.com/programs/enterprise/) 走 items-services 协议

```bash
itms-services://?action=download-manifest&url=https://xxx/xxxx.plist
```

- plist 地址必须是：公网 SSL(不支持自签名和面试 ssl)的 https
- safari 打开 plist，会自动下载/提示安装应用
  > NOTES:
  > items-services 这种方式只支持：
  >
  > 1. 个人开发使用或内测使用
  > 2. OTA 企业应用程序

