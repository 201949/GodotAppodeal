# Godot Appodeal Android Plugin

A Godot plugin for integrating Appodeal SDK 3.3.2 with Godot 3.5.2

[![Android](https://img.shields.io/badge/Platform-Android-brightgreen.svg)](https://developer.android.com)
[![Godot](https://img.shields.io/badge/Godot%20Engine-3.5.2-3e8ecc.svg)](https://github.com/godotengine/godot/)
[![Appodeal](https://img.shields.io/badge/Appodeal%20SDK%20-3.3.2-e84039.svg)](https://docs.appodeal.com/android/get-started)
[![MIT license](https://img.shields.io/badge/License-MIT-yellowgreen.svg)](https://github.com/201949/GodotAppodeal/blob/main/LICENSE)

## Supported Features

- **Fully integrated with the Appodeal SDK Full Package**
- **Meta and Firebase analytics connectivity options** (needed for the [Appodeal Accelerator Program](https://docs.appodeal.com/accelerator/introduction)) 
- **Verbose Logging** (needed for the [Appodeal Accelerator Program](https://docs.appodeal.com/accelerator/introduction)) 

## Disclaimer

If you want me to continue developing the plugin and keeping it up-to-date, please support me by:

[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://buymeacoffee.com/magikelle)

Please also consider giving a star :star: to the plugin repository if you found it useful.

Stay tuned for updates, and feel free to [open an issue](https://github.com/201949/GodotAppodeal/issues) or [contribute](https://github.com/201949/GodotAppodeal/pulls) if you have any suggestions or feedback!

## Supporters

Become a supporter!

## Appodeal Help Center and Developer Documentation

For official information on version Android SDK Change Log, visit: [Changelog Android](https://docs.appodeal.com/android/changelog)

For official information about the integration process, visit: [Appodeal Help Center](https://docs.appodeal.com)

## Generating the Plugin .aar File

If there is no release for your Godot version, you will need to generate a new plugin .aar file.  
Follow these instructions: [Official Documentation](https://docs.godotengine.org/en/stable/tutorials/plugins/android/android_plugin.html "documentation").

**Alternatively, you can download the precompiled plugin files for Godot 3.5.2 from the [releases page](https://github.com/201949/GodotAppodeal/releases/tag/v.3.3.2).**

To compile the project yourself:

1. Open a command window and *cd* into the `GodotAppodeal` directory, then run the appropriate command:

    * Windows:
    
        ```bash
        gradlew.bat build
        ```
        
    * Linux:
    
        ```bash
        ./gradlew build
        ```
    
2. Copy the newly created `.aar` and `.gdap` files to your plugin directory:

    `app/build/outputs/aar/Appodeal-3.3.2-release.aar` to `[your godot project]/android/plugins/`
    
    `AppodealPlugin.gdap` to `[your godot project]/android/plugins/`

## Preparing the Editor and Project for Plugin Use

1. Check your Android export template settings. You need to specify a minimum SDK version of 21 and a target SDK version of 34 to meet the Google Play target platform requirements -- [Target API level requirements for Google Play apps](https://support.google.com/googleplay/android-developer/answer/11926878).

    ![Pic 01](https://raw.githubusercontent.com/201949/GodotAppodeal/main/pic_01.png)

2. Check the `android/build/config.gradle` file and make any necessary changes to the SDK version specification.

    ![Pic 02](https://raw.githubusercontent.com/201949/GodotAppodeal/main/pic_02.png)

3. In the Android export template "Options" section under "Permissions", set "Access Network State" and "Internet" to "On".
    Also, add the following permission under "Custom Permissions": `com.google.android.gms.permission.AD_ID` (For apps that use the IMA SDK version 3.24.0 or lower and are targeting Android 13, required the com.google.android.gms.permission.AD_ID permission in the AndroidManifest.xml)

4. In `android/build/build.gradle` you need to make some changes for use with Firebase analytics connectivity (**marked by green-olive color**):

   ![Pic 03](https://raw.githubusercontent.com/201949/GodotAppodeal/main/pic_03.png)

   And you need to get google-services.json from the FireBase "Project Overview >> Project Settings" section of your FireBase project and place it in the "/android/build" folder of your Godot project.

5. Also you need to make some xml changes in case to use Meta event tracking (needed for the [Appodeal Accelerator Program](https://docs.appodeal.com/accelerator/introduction)).
   
   You need to create a `strings.xml` file in `/android/build/res/values` folder with contents like below:
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="facebook_app_id">Your FaceBook App ID</string>
  <string name="facebook_client_token">Your FaceBook Client Token</string>
</resources>
```

And in the main `AndroidManifest.xml` (located in `/android/build` of your project) you need to add metadata for use with AdMob and meta after <!--CHUNK_APPLICATION_END-->:
```xml
	<meta-data
		android:name="com.google.android.gms.ads.APPLICATION_ID"
		android:value="Your AdMob Application ID"/>
		
	<meta-data
		android:name="com.facebook.sdk.ApplicationId"
		android:value="@string/facebook_app_id" />
	
	<meta-data
		android:name="com.facebook.sdk.ClientToken"
		android:value="@string/facebook_client_token" />
```

## Example of Usage:

:warning: ***Description is under development*** :warning:

Get `appodeal.gd` gdscript from `/distrib` or from [releases page](https://github.com/201949/GodotAppodeal/releases/tag/v.3.3.2) and place it in a script subfolder or somewhere in your project.

Add `appodeal.gd` to the AutoLoad section of your Project Settings (make it a singleton with the name **Appodeal**).

Create your own customization script in the main scene or a singleton script with the following parameters:

```gdscript
extends Node

# Appodeal settings
const _ANDROID_APP_KEY: String = "Your Appodeal App Key"
var appodeal_test = false
var appodeal_log_level = 2 # 0 -Verbose, 1 - Debug, 2 - None
var appodeal_fb = true

func _ready():
	if OS.get_name() == "Android":
		var ad_types = Appodeal.AdType.BANNER | Appodeal.AdType.INTERSTITIAL | Appodeal.AdType.MREC | Appodeal.AdType.REWARDED_VIDEO
		Appodeal.set_smart_banners(false)
		Appodeal.set_testing(appodeal_test)
		Appodeal.set_log_level(appodeal_log_level)
		Appodeal.initialize(_ANDROID_APP_KEY, ad_types)

```
This script will to initialize Appodeal plugin with some parameters.

***For example:***

   `appodeal_test = false` -- settings for Ads mode: `true` - testing, `false` - production
   
   `appodeal_log_level = 2` -- settings for log level: 0 - Verbose, 1 - Debug, 2 - None

Next, in your project, in scripts, create calls to the Appodeal singleton methods where you want to display Ads.

***For example:***
```gdscript
# for Banner
Appodeal.show(Appodeal.ShowStyle.BANNER_TOP) # See official documentation for parameters

# for Interstitial
if Appodeal.is_loaded(Appodeal.AdType.INTERSTITIAL):
		Appodeal.show(Appodeal.ShowStyle.INTERSTITIAL)

# for Rewarded
if Appodeal.is_loaded(Appodeal.AdType.REWARDED_VIDEO):
		Appodeal.show(Appodeal.ShowStyle.REWARDED_VIDEO)
		print("Appodeal Show Reward!")
```
You can then process the signals to create some events for your needs.

***For example:***
```gdscript
func _ready():
	Appodeal.connect("on_interstitial_failed_to_load",self,"_on_Appodeal_interstitial_failed_to_load")
	Appodeal.connect("on_interstitial_shown",self,"_on_Appodeal_interstitial_shown")
	Appodeal.connect("on_interstitial_show_failed",self,"_on_Appodeal_interstitial_show_failed")
	Appodeal.connect("on_interstitial_closed",self,"_on_Appodeal_interstitial_closed")
	Appodeal.connect("on_rewarded_video_loaded", self, "_on_Appodeal_rewarded_video_loaded")
	Appodeal.connect("on_rewarded_video_failed_to_load", self, "_on_Appodeal_rewarded_video_failed_to_load")
	Appodeal.connect("on_rewarded_video_shown", self, "_on_Appodeal_rewarded_video_shown")
	Appodeal.connect("on_rewarded_video_show_failed", self, "_on_Appodeal_rewarded_video_show_failed")
	Appodeal.connect("on_rewarded_video_clicked", self, "_on_Appodeal_rewarded_video_clicked")
	Appodeal.connect("on_rewarded_video_finished", self, "_on_Appodeal_rewarded_video_finished")
	Appodeal.connect("on_rewarded_video_closed", self, "_on_Appodeal_rewarded_video_closed")
	Appodeal.connect("on_rewarded_video_expired", self, "_on_Appodeal_rewarded_video_expired")

func _on_Appodeal_interstitial_failed_to_load():
	print("Appodeal Interstitial Failed to Load!")

func _on_Appodeal_interstitial_shown():
	print("Appodeal Interstitial Shown!")

func _on_Appodeal_interstitial_show_failed():
	print("Appodeal Interstitial Failed!")

func _on_Appodeal_interstitial_closed():
	print("Appodeal Interstitial Closed!")

func _on_Appodeal_rewarded_video_loaded():
	print("Appodeal Rewarded Video Loaded!")

func _on_Appodeal_rewarded_video_shown():
	print("Appodeal Rewarded Video Shown!")

func _on_Appodeal_rewarded_video_finished(_amount, _name):
	print("Appodeal Rewarded Video Finished!")

func _on_Appodeal_rewarded_video_closed(finished):
	print("Appodeal Rewarded Video Closed!")

func _on_Appodeal_rewarded_video_show_failed():
	print("Appodeal Rewarded Video Show Failed!")

func _on_Appodeal_rewarded_video_failed_to_load():
	print("Appodeal Rewarded Video Failed to Load!")

func _on_Appodeal_rewarded_video_expired():
	print("Appodeal Rewarded Video Expired!")
```

You can also get some statistics by calling `log_event` method with your own parameters.

***For example:***
```gdscript
Appodeal.log_event("gb_main_menu_open", {"screen":name})
```
**See official documentation for [Event Tracking](https://docs.appodeal.com/ru/android/advanced/event-tracking)**
