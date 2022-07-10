[简体中文](./ZH_CN.md)

The source project does not provide external support at the moment, this reference repository is only some of the implementation principles [Click to view](./fuckconfig-designIdeas.md)
# What is FuckConfig?
Most of my productivity products fall under the Apple and Linux families (I should say basically), and I often need to configure various tools in the process of using them, to name a few simple examples：
> 1、When I work on Github, I need to create a separate SSH Key for Github, sometimes I need to use my laptop temporarily for company stuff (help Review/MR a bit), I need a new SSH Key for Gitlab; when I manage different services will I still need different SSH Key；<br/>
> 2、Sometimes I need to configure gitconfig, sometimes npm source, node version, and hosts when I'm developing NPM toolkits and writing projects.;

Similar to the configuration file above, many tools will have their own：
- When more I often do not remember their location
- and what they do respectively (you have to admit that some configurations you can't really tell by name)<br/>

I often have a headache with all these distributed configuration files, so there is FuckConfig, a Desktop App.

## How to manage profiles
> As the preliminary stage is only my personal needs, the implementation may not be applicable to all (for example: other students may not exist I specify the notepad tool to open)
- The basic version provides the function of configuration management, directly scanning the configuration files available under the current user's system, mapping and associating them in the preset configuration files, grouping them for display in the window.
- Use the NSWorkspace service to look at the current runningAppcations to find an editor that is starting and open it.
- Without any active editors, the Vi editor will be opened directly (VSCode presence will be opened in VSCode)
> See the following 👇 for installing applications via link

## How to install MACOS APP online?
FuckConfig also has a link to install the development environment, the specific implementation of the principle of reference [MacAppsLink](https://macapps.link/) , through cur to execute the shell script program to install;

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
Is there any other way：

- [setapp](https://setapp.com/)
  > It is not clear how the third-party application installation behind setapp is implemented, but setapp creates a SetApp folder under Applications, where the applications provided by SetApp are stored。
  >
  > Officials say quick installation is a work in progress: [quickly-install](https://support.setapp.com/hc/en-us/articles/360008560299-How-do-I-quickly-install-my-Setapp-apps-on-another-Mac-)

## Why MacOS does not support third-party installation？

- Gatekeeper: checks for the presence of Apple-issued certificates
- XProtect：Block any malicious code execution


## IOS How to install APP online?

[Apple Developer Enterprise Program](https://developer.apple.com/programs/enterprise/) 走 items-services 协议

```bash
itms-services://?action=download-manifest&url=https://xxx/xxxx.plist
```

- plist：The address must be: public SSL (self-signed and public ssl are not supported) https
- safari Open plist and it will automatically download/prompts to install the application
  > NOTES:
  > items-services approach only supports：
  >
  > 1. Personal development use or internal testing use
  > 2. OTA Enterprise Applications

