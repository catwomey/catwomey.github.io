---
layout: post
title:  "Swifty Configuration for iOS"
subtitle: ""
date:   2016-01-30
tags: ios
---

For the last several months I've had the opportunity to jump head first into Swift development for iOS.  Something I immediately noticed was how cumbersome it felt to configure an application for multiple environments.  Some solutions I found required including different .plist files based on environment you were deploying to.  This required that you duplicate configuration if a property was different in only 1 environment (ie analytics).  Another form of configuration was conditional compilation, I didn't really like this either as it was more restrictive than objective c's conditional compilation. I have quite a bit of experience with Java Spring webapps and have come to admire Java configuration as a convenient way to contain configuration.  So armed with this I attempted the same in Swift.

##Injecting Build Configuration into .plist

The first thing I needed was a way to flag what configuration I wanted the application to load, which can determined by the variable CONFIGURATION that XCode provides during build. The value of CONFIGURATION is equal to the configuration value your build scheme is using. The easiest place to read it from was a global Info.plist file that had some general properties. So I injected the following into my Info.plist

```
	<key>environment</key>
	<string>${CONFIGURATION}</string>
```
With this property set I could now reliably determine what configuration the application should use.

##Creating the Swift Configuration

Now that we can get a solid hold on the environment we built for, we can create a Singleton Swift class to provide the configuration for each environment. Inside of the Swift class I embed an enum of type string to map to expected values. I'm using the enum to contain the possible configuration values, but you can modify to your preference.

Here's a sample of what I have created:

```swift
class MySwiftConfig {
	static let sharedInstance = MySwiftConfig()
	private var env: environment?
    private init() {
        if let path = NSBundle.mainBundle().pathForResource("Info", ofType: "plist"), let dict = NSDictionary(contentsOfFile: path), let envString =   dict["environment"] as? String {
        	env = environment(rawValue:envString)
        }
        else {
        	//panic! or provide defaults or bail out
        }
    }

    func useAnalytics() -> bool {
    	return env == envs.Release
    }

    func apiUrl() -> String {
    	if env == envs.Release {
    		return "https://myapi.com"
    	} else {
    		return "https://dev.myapi.com"
    	}
    }

    func myBlog() -> String {
    	return "http://codingvelocity.com"
    }

    enum environment:String {
    	case Debug = "Debug"
    	case Release = "Release"
    }

}
```

As you can see in the sample properties that are static just return the raw value, but things that differ per environment like apiUrls are conditional. You can also provide defaults for properties so you don't need to reconfigure the whole app per environment. Another benefit is you can test your configuration.

To retrieve any one of these properties you simply need to call MySwiftConfig.sharedInstance.**function-name**

##Conclusion

I'm still fairly new to swift and iOS development so maybe there's a better way to configure the application, but so far i've found this has worked quite well.