---
  layout: post
  title: Having fun with multiple environments
  subtitle: Part 1
  tags: xcode
  categories: 
  lang: en
  ref: multiple-environments-part1
---

If you are a software developer, you will agree that having multiple environments is really beneficial for gathering good feedback about new functionalities and finding those last minute errors and details to fix.

In mobile development, I'd say this is a 'must' because it's possible to deliver beta versions to different groups to continue testing and QA stages whilst you and your team continue developing new functionalities.

There is a problem about this, however. As you may have noticed, you can't install two apps with the same bundle id/package name in the same device, which means you can only have one of them at the same time, making testing on different environments tedious. For someone who only cares about testing beta version, it may not mean a lot, but it is quite upsetting for us, developers, because we are constantly testing in dev, beta, release...

To top it all, after finishing that *small problem*, you realise that all your installed versions share the same icon and display name. I know you are with me on this, it is quite annoying when you try to launch the beta version but end up launching the last version deployed on the store...

Furthermore, if your app connects to a *CMS* (content management system), probabilities your CMS is deployed in several environments is high. So, just like that, configuration becomes even more difficult. 

We have to admit it, we all have created a constant in the code that contains a server URL, with some commented lines with the rest of the environments. I have to say it's a good start, it could be even worse if we would have hardcoded in every http request, but still, we know is not a good solution. It is highly error prone and, of course, not very elegant...

If what you have read so far looks familiar and every time you have to release a new version, you are scare, you will find this articule really useful. However, if you have already fought this problem and solved it like a ninja, I would love to read your comments about your implementation and see if we could improve by taking the best of our solutions!

This article is the first of a serie of two (one for platform). Because it has a higher level of complexity, I will start talking about configuring xCode for iOS projects. I will publish an equivalent version for AndroidStudio shortly.

#### Where is my Beta

By default, any project created in xCode contains debug and release configurations, so you would only need to create the beta configuration to complete our project.

To do that, you would need to click your project, and again, in the left hand panel, your project (first item in the list, above your target). Now on *Info* tab, click on *"+"* you will find inside *"Configurations"* and press *"Duplicate ‘Release’ Configuration”*. Once you have done it, you can rename it as you wish. We will call it *"Beta"* for ease of understanding.

![Creating beta config](../../../assets/images/multiple-env/part1/duplicating-release-configuration.png "Cloning build configuration on xCode")

#### A name for every *Config*

Next, select your *target* and click on *"Build Settings"* tab.

Inside *"Packaging"* section, you will find *"Product Name*". For ease of use, most of the time I use the search bar on the top right-corner, but this is totally up to you. Expand it and you will see our three configurations. Name them as you wish. Quite often I leave the dev version as it is, because it will have a different icon than the release version, and just add suffix *Beta* so every tester who needs both Beta and Release can differentiate them.

![A new Product Name](../../../assets/images/multiple-env/part1/changing-product-name.png "Adding Beta suffix to beta build config")

#### I want them all!

As I said in the introduction, both developers and testers are interested in having any available version installed on the same device, saving us time and unexpected problems. The next step is key to achieve it, because, even though you have already created a *Beta* configuration and changed the names, every time you install a new version, the OS will overwrite the one you have installed (even if it comes from the AppStore).

You need creating a user-define configuration parameter. You can name it as you prefer, but you wil find many references on internet as *BUNDLE_ID_SUFFIX*. Once created, open the new *dropdown* and add the values for every configuration, leaving *"Release*" blank.

![Bundle ID suffix ](../../../assets/images/multiple-env/part1/bundle-id-suffix.png "Adding Bundle ID suffix")

Coming next, you will need to edit your *.plist* file (if you haven't change it, it is your *Info.plist*). Open it and find *"Bundle Identifier"* row. Doble click on it and add *"${BUNDLE_ID_SUFFIX}"* to the end of the existing string. By doing this, we will get every configuration running without having to hardcode every string -and regret it later.

![Updating plist file](../../../assets/images/multiple-env/part1/bundle-identifier.png "Updating plist file")

#### Icons, please

From this moment on, you CAN have every single version installed on your device, but they will still show the same icon.

To solve it, we will follow the same pattern we have implemented for saving us of writing the exact name for every icon/configuration.

Back to your *target* view, you need to search for "Asset Catalog App Icon" in the *"Build Settings*" tab. You will find it under *"Asset Catalog Compiler — Options* group"*, if you don't use the search bar. This time you don't need to expand anything, just adding our *${BUNDLE_ID_SUFFIX}* at the end of the current value (default: *AppIcon*) will do the work. After pressing *enter* you will see that the dropdown will be automatically expanded and you will see our three configurations, each on with its custom icon.

![App icon suffix](../../../assets/images/multiple-env/part1/asset-catalog-icon.png "Adding App icon id for every configuration")

Lastly, we need new icons for our project.

Open up your *Assets.xcassets* file and, at the left bottom corner, click on "+" and select *"App icons & Launch images > New iOS App Icon"*. Repeat this process and rename both new icons to *"AppIcon.dev"* and *"AppIcon.beta"*.

![Adding new icons](../../../assets/images/multiple-env/part1/creating-new-icons.png "Adding new icons")

Now it's your turn and you will need to fill in every screen size with the icons that your designer has created for you.

#### How do I install a specific version now?

It is time for *Schemes*. Here, we are able to set the configuration we'd like to use every time we run the app on debug, testing, generating the *ipa* file, etc.

*Schemes* are accessible from the menu *Product > Scheme > Manage Schemes*, or by clicking into the target selector, next to the Stop button, up in the left.

Once the scheme management screen is shown, the easiest way to proceed is by duplicating the first one on the list, which is the default one. If you see more schemes there, it may be some external library you have included.

![Duplicating xCode schemes](../../../assets/images/multiple-env/part1/duplicating-scheme.png "Duplicating  xCode schemes")

A *Beta version*, by definition, is the version that it's ready to be deployed and tested before releasing into the AppStore. This means that we are only interested on changing the behaviour when we need to export it, so select *"Archive"* in the left menu, and pick our new configuration on *"Build Configurations"*.

![Picking Beta build config](../../../assets/images/multiple-env/part1/setting-beta-archive.png "Picking Beta build config")

In case you would like to run this configuration during development, just follow the same steps but selecting *"Run"* this time.

#### Configuring multiple environments

To complete what I promised at the beginning of this post, we need to see how to change environment related parameters automatically by running one configuration or other.

There are different options to achieve this, personally, I like the idea of creating a new configuration *plist* file to group all those environment-dependant variables.

No matter how you do it, first step is common to any option, and it would be adding a new key into *Info.plist* with *"$(CONFIGURATION)"* as value. This way, the configuration used during building time will be accesible through a *Configuration* variable, making very easy the assertion of which configuration is running.

![Info.plist configuration](../../../assets/images/multiple-env/part1/configuration-key.png "Adding new config key-value to Info.plist")

If you would like to test it now, you could add the line below inside the *"didFinishLaunchingWithOptions"* method of *AppDelegate* and see what it prints:

{% highlight swift %}
    print(Bundle.main.object(forInfoDictionaryKey:"Configuration")!)
{% endhighlight %}

Here is where other solutions may diver. Our next step will be to create a new *plist* file called *"Config"* and fill it in with a dictionary containing the environment variables. You may want to use the following example as starting point:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Debug</key>
    <dict>
      <key>endpoint</key>
      <string>Development API Endpoint</string>
    </dict>
    <key>Release</key>
    <dict>
      <key>endpoint</key>
      <string>Release API Endpoint</string>
    </dict>
  </dict>
</plist>
{% endhighlight %}

Reading this file from code should be straight forward, but I think it makes sense to have a specific class in charge of loading the configuration and accessing the values. It would be something like the following:

{% highlight swift %}
import Foundation

class Config: NSObject {
    // Singleton instance
    static let sharedInstance = Config()
    
    var configs: NSDictionary!
    
    override init() {
        // (1) Finds the Configuration property from Info.plist
        let currentConfiguration = Bundle.main.object(forInfoDictionaryKey: "Configuration")!
        // (2) Reads the Config plist file
        let path = Bundle.main.path(forResource: "Config", ofType: "plist")!
        // (3) Find current configuration, rollout to Release if not found
        if let dictionary = NSDictionary(contentsOfFile: path)!.object(forKey: currentConfiguration) as? NSDictionary{
            configs = dictionary
        } else {
            configs = NSDictionary(contentsOfFile: path)!.object(forKey: "Release") as! NSDictionary
        }
    }
}

extension Config {
    func apiEndpoint() -> String {
        return configs.object(forKey: "endpoint") as! String
    }
}
{% endhighlight %}

Having created this class, now your environment-dependant configuration is accesible from any part of your app.

{% highlight swift %}
Config.sharedInstance.apiEndpoint()
{% endhighlight %} 

As an important note, the previous code contains some lines for checking if a file of the current configuration is available, rolling out to *Release* in case it does not.

#### Conclusion

Reducing the possibilities of making mistakes and get a clear knowledge of how to deploy into multiple environments is always a good idea. Imagine that instead of having three environments, we have four or even more, and each of them pointing to a different server...What I have explained here will allow you to create multiple schemes and configurations to adapt your app to all of them. Furthermore, by integrating **[Fastlane](https://docs.fastlane.tools)** you will save a lot of suffering and hours!

I really hope this post helps you developing awesome iOS apps. Shortly, the equivalent version for Android Studio.