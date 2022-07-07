# FuckConfig
源项目暂时不提供对外支持, 这个参仓库只是其中一些实现方式/原理

[点击查看FuckConfig设计思路](./fuckconfig-designIdeas.md)

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

## MACOS 如何在线安装 APP?

[MacAppsLink](https://macapps.link/) 和 FuckConfig 实现原理, 通知 cur 来执行 shell 脚本程序安装;

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
