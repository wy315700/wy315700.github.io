---
layout: post
title:  "OSX Yosemite (10.10) 手动开启Trim"
date:   2014-10-18 10:56:12
categories: Mac
---
<font color="red">11月18号更新，以下方法对于OSX 10.10.1 照样有效，把步骤完整的走一遍即可。</font>

<font color="red">2015年1月28号更新，以下方法对于OSX 10.10.2 照样有效，把步骤完整的走一遍即可。</font>

 

今日，苹果发布了OSX Yosemite。对于博主这种使用第三方SSD的人来说，开启Trim又成为一个艰难的问题，经过长时间的研究和搜寻，终于找到了一个解决方案。

相比较 [OSX 10.9开启Trim]({% post_url 2014-07-02-mac-osx-10-9-4-trim %})的步骤来说，差不多，但是要重启两步，下面见代码


{% highlight Bash shell scripts %}
#
# Original version by Grant Parnell is offline (http://digitaldj.net/2011/07/21/trim-enabler-for-lion/)
# Update July 2014: no longer offline, see https://digitaldj.net/blog/2011/11/17/trim-enabler-for-os-x-lion-mountain-lion-mavericks/
#
# Looks for "Apple" string in HD kext, changes it to a wildcard match for anything
#
# Alternative to http://www.groths.org/trim-enabler-3-0-released/
# Method behind this madness described: http://forums.macrumors.com/showthread.php?t=1409151&page=4
# See discussion in comments here: https://www.macupdate.com/app/mac/39654/lion-tweaks
# And here: http://forums.macrumors.com/showthread.php?t=1410459
# And here: http://forums.macrumors.com/showthread.php?t=1480302
#
# Yosemite: for recovering from stop sign on boot screen, please see http://www.cindori.org/update-on-trim-in-yosemite/
 
# backup patched file
sudo cp /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage.original
 
# For Yosemite you have to disable driver signature check 
sudo nvram boot-args="kext-dev-mode=1"
 
# !for Yosemite only! please reboot after settings the boot-args!
sudo shutdown -r now
 
# for Yosemite, Mavericks 10.9.5 and 10.9.4 (thanks Tobi)
sudo perl -pi -e 's|(^\x00{1,20})[^\x00]{9}(\x00{1,20}\x54)|$1\x00\x00\x00\x00\x00\x00\x00\x00\x00$2|sg' /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage
 
# !for Yosemite only! rebuild kext cache manually (could take a while)
sudo kextcache -m /System/Library/Caches/com.apple.kext.caches/Startup/Extensions.mkext /System/Library/Extensions
 
# for Mavericks and Mountain Lion from 10.8.3 to 10.9.3 
#sudo perl -pi -e 's|(\x52\x6F\x74\x61\x74\x69\x6F\x6E\x61\x6C\x00{1,20})[^\x00]{9}(\x00{1,20}\x54)|$1\x00\x00\x00\x00\x00\x00\x00\x00\x00$2|sg' /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage
 
# for Mountain Lion from 10.8.1 to 10.8.2 and Lion 10.7.5
#sudo perl -pi -e 's|(\x52\x6F\x74\x61\x74\x69\x6F\x6E\x61\x6C\x00{1,20})[^\x00]{9}(\x00{1,20}\x4D)|$1\x00\x00\x00\x00\x00\x00\x00\x00\x00$2|sg' /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage
 
# for Mountain Lion 10.8.0 and Lion 10.7.4 BELOW
#sudo perl -pi -e 's|(\x52\x6F\x74\x61\x74\x69\x6F\x6E\x61\x6C\x00{1,20})[^\x00]{9}(\x00{1,20}\x51)|$1\x00\x00\x00\x00\x00\x00\x00\x00\x00$2|sg' /System/Library/Extensions/IOAHCIFamily.kext/Contents/PlugIns/IOAHCIBlockStorage.kext/Contents/MacOS/IOAHCIBlockStorage
 
sudo touch /System/Library/Extensions/
 
# now reboot!
sudo shutdown -r now
{% endhighlight %}




![10.10.1 完美开启TRIM]({{ site.url }}/uploads/2014/10/QQ20141118-1.png)

同样，如果是苹果自己的SSD，完全不需要这么折腾