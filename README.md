# Godot Appodeal Android Plugin

A Godot plugin for integrating Appodeal SDK 3.3.2 with Godot 3.5.2

[![Android](https://img.shields.io/badge/Platform-Android-brightgreen.svg)](https://developer.android.com)
[![Godot](https://img.shields.io/badge/Godot%20Engine-3.5.2-3e8ecc.svg)](https://github.com/godotengine/godot/)
[![Appodeal](https://img.shields.io/badge/Appodeal%20SDK%20-3.3.2-e84039.svg)](https://docs.appodeal.com/android/get-started)
[![MIT license](https://img.shields.io/badge/License-MIT-yellowgreen.svg)](https://github.com/201949/GodotAppodeal/blob/main/LICENSE)

## Supported Features

- **Fully integrated with the Appodeal SDK Full Package**
- **Meta and Firebase analytics connectivity options** (needed for the [Appodeal Accelerator Program](https://docs.appodeal.com/accelerator/introduction)) 
- **Verbose Logging**

## Disclaimer

If you want me to continue developing the plugin and keeping it up-to-date, please support me by:

[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://buymeacoffee.com/magikelle)

Please also consider giving a star :star: to the plugin repository if you found it useful.

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
    Also, add the following permission under "Custom Permissions": `com.google.android.gms.permission.AD_ID` (For apps that use the IMA SDK version 3.24.0 or lower and are targeting Android 13, required the com.google.android.gms.permission.AD_ID permission in the AndroidManifest.xml).

## Example of Usage:

:warning: Description is under development :warning:
