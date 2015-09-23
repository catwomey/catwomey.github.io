---
layout: post
title:  "Putting on the tinfoil hat"
subtitle: "keeping your tools secure"
date:   2015-09-20
tags: security 
---

Recently a large number of apps were compromised in the app store.  It's believed that an infected version of xcode([xcodeghost]) was used to develop the applications which compromised them when they were compiled. As our world gets more connected security is only going to matter more and as developers we should be setting the best example we can.  

##Trusted Sources

Security is really about trust in the software development world.  If I download xCode from Apple it's fairly reasonable to believe that they aren't compromising my computer with malicious software.  If I download it from a thirdparty share site or bittorrent I'm no longer trusting Apple, I'm trusting 1..N entities haven't compromised my software.  Those entities could be the hosting sites themselves, the original uploader, etc.  

##SHA1 Hash Verification

The recent hack in the Apple App Store came from compromised xCode installs, more specifically the hackers repackaged the compiler so it would inject their malicious code and put it up on an easy to download from server in China. This hack could have been prevented by running a SHA1 check or using gatekeeper in Mac OS.  To check your install with gatekeeper manually run:

```
spctl --assess --verbose /Applications/Xcode.app
```

The output will be something like:

```
/Applications/Xcode.app: accepted
source=Mac App Store
```

Gatekeeper is Apple specific, if you find yourself on a Linux or Windows machine you can use SHA1 hash to verify the software you installed. For example on my Fedora install I can run:

```
sha1sum ~/Downloads/go1.5.linux-amd64.tar.gz 
```

which produces 5817fa4b2252afdb02e11e8b9dc1d9173ef3bd5a.  That hash matches the one Google publishes on their [go download] page so I know my go install was not compromised or corrupted during download.


##Permissions

Limiting software access to your system can be tricky, sometimes you get crashes or system instability but the gains can outweigh the pains quite quickly. My favorite approach is creating users and groups for specific tasks.  Such as creating a tomcat user that only has access to the directories it needs, or i'll create a group that grants read and write to specific development directories.  

##Audit Source

I've left this section to last because it's the least reasonable thing you can do for software safety.  Auditing source can give you peace of mind IF you know what to look for.  The majority of people, even developers, won't be able to see the truly sneaky things happening unless they gain intimate knowledge of the entire code base.  That being said, if the source is available for your favorite tool the ability to preemptively patch 
[go download]:https://golang.org/dl/
[hacked]:http://9to5mac.com/2015/09/20/xcode-ghost-app-store-malware-malicious-apps/
[xcodeghost]:http://arstechnica.com/security/2015/09/apple-scrambles-after-40-malicious-xcodeghost-apps-haunt-app-store/