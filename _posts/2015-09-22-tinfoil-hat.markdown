---
layout: post
title:  "Keeping Your Users Safe With Tinfoil Hats"
subtitle: "keeping your tools secure"
date:   2015-09-27
tags: security 
---

Recently a large number of apps were compromised in the app store.  It's believed that an infected version of xcode([xcodeghost]) was used to develop the applications which compromised them when they were compiled. Shipping compromised code is irresponsible and a good way to lose users trust, and potentially lose them as customers forever. So let's put on our [tinfoil hats] and talk about how we can be responsible developers.

##Trusted Sources

Security is really about trust.  If I download xCode from Apple it's fairly reasonable to believe that they aren't compromising my computer with malicious software.  If I download it from a third party share site or BitTorrent I'm no longer trusting Apple, I'm trusting 1..N entities haven't compromised my software.  Those entities could be the hosting sites themselves, the original uploader, etc.  In general if you are using any kind of software you're best off obtaining it from the original creator, from their official source control (ie a github repo), or a trusted software repository (Windows store, Apple Store, official Linux repositories) 

##Code Signing and SHA Verification

The recent hack in the Apple App Store came from compromised xCode installs, more specifically the hackers repackaged the compiler so it would inject their malicious code and put it up on an easy to download from server in China. This hack could have been prevented by verifying the code signature (via [gatekeeper]) or running a SHA1 check.  

To check your install with gatekeeper manually run:

```
spctl --assess --verbose /Applications/Xcode.app
```

If your application is legitimate the output will be something like:

```
/Applications/Xcode.app: accepted
source=Mac App Store
```

Gatekeeper is Apple specific, if you find yourself on a Linux or Windows machine you can use SHA1 hash to verify the software you installed. For example on my Fedora install I can run:

```
sha1sum ~/Downloads/go1.5.linux-amd64.tar.gz 
```

which produces 5817fa4b2252afdb02e11e8b9dc1d9173ef3bd5a.  That hash matches the one Google publishes on their [go download] page so I know my go install was not compromised or corrupted during download.

##Audit Source

I've left this section to last because it's the least reasonable thing you can do for software safety.  Auditing source can give you peace of mind IF you know what to look for.  The majority of people, even developers, won't be able to see the truly sneaky things happening unless they gain intimate knowledge of the entire code base.  That being said, source open to the scrutiny of the public can be harder to compromise.

##Weak Hat

Think I can improve my security? Let me know in the comments section!

[go download]:https://golang.org/dl/
[hacked]:http://9to5mac.com/2015/09/20/xcode-ghost-app-store-malware-malicious-apps/
[xcodeghost]:http://arstechnica.com/security/2015/09/apple-scrambles-after-40-malicious-xcodeghost-apps-haunt-app-store/
[gatekeeper]:https://developer.apple.com/news/?id=09222015a
[tinfoil hats]:https://en.wikipedia.org/wiki/Tin_foil_hat