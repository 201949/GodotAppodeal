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

4. In `android/build/build.gradle` you need to make some changes for use with Meta and Firebase analytics connectivity as below marked as `//Changes for analytics connectivity`:
```build.gradle
// Gradle build config for Godot Engine's Android port.
//
// Do not remove/modify comments ending with BEGIN/END, they are used to inject
// addon-specific configuration.
buildscript {
    apply from: 'config.gradle'

    repositories {
        google()
        mavenCentral()
//CHUNK_BUILDSCRIPT_REPOSITORIES_BEGIN
//CHUNK_BUILDSCRIPT_REPOSITORIES_END
    }
    dependencies {
        classpath libraries.androidGradlePlugin
        classpath libraries.kotlinGradlePlugin
        classpath 'com.google.gms:google-services:4.3.15' //Changes for analytics connectivity
//CHUNK_BUILDSCRIPT_DEPENDENCIES_BEGIN
//CHUNK_BUILDSCRIPT_DEPENDENCIES_END
    }
}

plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

apply plugin: 'com.google.gms.google-services' //Changes for analytics connectivity

apply from: 'config.gradle'

allprojects {
    repositories {
        google()
        mavenCentral()
		maven { url "https://jitpack.io" }
//CHUNK_ALLPROJECTS_REPOSITORIES_BEGIN
//CHUNK_ALLPROJECTS_REPOSITORIES_END

        // Godot user plugins custom maven repos
        String[] mavenRepos = getGodotPluginsMavenRepos()
        if (mavenRepos != null && mavenRepos.size() > 0) {
            for (String repoUrl : mavenRepos) {
                maven {
                    url repoUrl
                }
            }
        }
    }
}

configurations {
    // Initializes a placeholder for the devImplementation dependency configuration.
    devImplementation {}
}

dependencies {
    implementation libraries.kotlinStdLib
    implementation libraries.androidxFragment
    implementation (platform('com.google.firebase:firebase-bom:32.7.0')) //Changes for analytics connectivity
    implementation 'com.google.firebase:firebase-analytics' //Changes for analytics connectivity
    implementation 'com.google.firebase:firebase-messaging' //Changes for analytics connectivity

    if (rootProject.findProject(":lib")) {
        implementation project(":lib")
    } else if (rootProject.findProject(":godot:lib")) {
        implementation project(":godot:lib")
    } else {
        // Custom build mode. In this scenario this project is the only one around and the Godot
        // library is available through the pre-generated godot-lib.*.aar android archive files.
        debugImplementation fileTree(dir: 'libs/debug', include: ['*.jar', '*.aar'])
        devImplementation fileTree(dir: 'libs/dev', include: ['*.jar', '*.aar'])
        releaseImplementation fileTree(dir: 'libs/release', include: ['*.jar', '*.aar'])
    }

    // Godot user plugins remote dependencies
    String[] remoteDeps = getGodotPluginsRemoteBinaries()
    if (remoteDeps != null && remoteDeps.size() > 0) {
        for (String dep : remoteDeps) {
            implementation dep
        }
    }

    // Godot user plugins local dependencies
    String[] pluginsBinaries = getGodotPluginsLocalBinaries()
    if (pluginsBinaries != null && pluginsBinaries.size() > 0) {
        implementation files(pluginsBinaries)
    }

//CHUNK_DEPENDENCIES_BEGIN
//CHUNK_DEPENDENCIES_END
}

android {
    compileSdkVersion versions.compileSdk
    buildToolsVersion versions.buildTools
    ndkVersion versions.ndkVersion

    compileOptions {
        sourceCompatibility versions.javaVersion
        targetCompatibility versions.javaVersion
    }

    kotlinOptions {
        jvmTarget = versions.javaVersion
    }

    assetPacks = [":assetPacks:installTime"]

    defaultConfig {
        // The default ignore pattern for the 'assets' directory includes hidden files and directories which are used by Godot projects.
        aaptOptions {
            ignoreAssetsPattern "!.svn:!.git:!.gitignore:!.ds_store:!*.scc:<dir>_*:!CVS:!thumbs.db:!picasa.ini:!*~"
        }

        ndk {
            String[] export_abi_list = getExportEnabledABIs()
            abiFilters export_abi_list
        }

        manifestPlaceholders = [godotEditorVersion: getGodotEditorVersion()]

        // Feel free to modify the application id to your own.
        applicationId getExportPackageName()
        versionCode getExportVersionCode()
        versionName getExportVersionName()
        minSdkVersion getExportMinSdkVersion()
        targetSdkVersion getExportTargetSdkVersion()

        missingDimensionStrategy 'products', 'template'
//CHUNK_ANDROID_DEFAULTCONFIG_BEGIN
//CHUNK_ANDROID_DEFAULTCONFIG_END
    }

    lintOptions {
        abortOnError false
        disable 'MissingTranslation', 'UnusedResources'
    }

    ndkVersion versions.ndkVersion

    packagingOptions {
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/NOTICE'

        // 'doNotStrip' is enabled for development within Android Studio
        if (shouldNotStrip()) {
            doNotStrip '**/*.so'
        }
    }

    signingConfigs {
        debug {
            if (hasCustomDebugKeystore()) {
                storeFile new File(getDebugKeystoreFile())
                storePassword getDebugKeystorePassword()
                keyAlias getDebugKeyAlias()
                keyPassword getDebugKeystorePassword()
            }
        }

        release {
            File keystoreFile = new File(getReleaseKeystoreFile())
            if (keystoreFile.isFile()) {
                storeFile keystoreFile
                storePassword getReleaseKeystorePassword()
                keyAlias getReleaseKeyAlias()
                keyPassword getReleaseKeystorePassword()
            }
        }
    }

    buildTypes {

        debug {
            // Signing and zip-aligning are skipped for prebuilt builds, but
            // performed for custom builds.
            zipAlignEnabled shouldZipAlign()
            if (shouldSign()) {
                signingConfig signingConfigs.debug
            } else {
                signingConfig null
            }
        }

        dev {
            initWith debug
            // Signing and zip-aligning are skipped for prebuilt builds, but
            // performed for custom builds.
            zipAlignEnabled shouldZipAlign()
            if (shouldSign()) {
                signingConfig signingConfigs.debug
            } else {
                signingConfig null
            }
        }

        release {
            // Signing and zip-aligning are skipped for prebuilt builds, but
            // performed for custom builds.
            zipAlignEnabled shouldZipAlign()
            if (shouldSign()) {
                signingConfig signingConfigs.release
            } else {
                signingConfig null
            }
        }
    }

    sourceSets {
        main {
            manifest.srcFile 'AndroidManifest.xml'
            java.srcDirs = [
                'src'
//DIR_SRC_BEGIN
//DIR_SRC_END
            ]
            res.srcDirs = [
                'res'
//DIR_RES_BEGIN
//DIR_RES_END
            ]
            aidl.srcDirs = [
                'aidl'
//DIR_AIDL_BEGIN
//DIR_AIDL_END
            ]
            assets.srcDirs = [
                'assets'
//DIR_ASSETS_BEGIN
//DIR_ASSETS_END
            ]
        }
        debug.jniLibs.srcDirs = [
            'libs/debug'
//DIR_JNI_DEBUG_BEGIN
//DIR_JNI_DEBUG_END
        ]
        dev.jniLibs.srcDirs = [
            'libs/dev'
//DIR_JNI_DEV_BEGIN
//DIR_JNI_DEV_END
        ]
        release.jniLibs.srcDirs = [
            'libs/release'
//DIR_JNI_RELEASE_BEGIN
//DIR_JNI_RELEASE_END
        ]
    }

    applicationVariants.all { variant ->
        variant.outputs.all { output ->
            output.outputFileName = "android_${variant.name}.apk"
        }
    }
}

task copyAndRenameDebugApk(type: Copy) {
    from "$buildDir/outputs/apk/debug/android_debug.apk"
    into getExportPath()
    rename "android_debug.apk", getExportFilename()
}

task copyAndRenameDevApk(type: Copy) {
    from "$buildDir/outputs/apk/dev/android_dev.apk"
    into getExportPath()
    rename "android_dev.apk", getExportFilename()
}

task copyAndRenameReleaseApk(type: Copy) {
    from "$buildDir/outputs/apk/release/android_release.apk"
    into getExportPath()
    rename "android_release.apk", getExportFilename()
}

task copyAndRenameDebugAab(type: Copy) {
    from "$buildDir/outputs/bundle/debug/build-debug.aab"
    into getExportPath()
    rename "build-debug.aab", getExportFilename()
}

task copyAndRenameDevAab(type: Copy) {
    from "$buildDir/outputs/bundle/dev/build-dev.aab"
    into getExportPath()
    rename "build-dev.aab", getExportFilename()
}

task copyAndRenameReleaseAab(type: Copy) {
    from "$buildDir/outputs/bundle/release/build-release.aab"
    into getExportPath()
    rename "build-release.aab", getExportFilename()
}

//CHUNK_GLOBAL_BEGIN
//CHUNK_GLOBAL_END

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

*For example:*

   `appodeal_test = false` -- settings for Ads mode: `true` - testing, `false` - production
   
   `appodeal_log_level = 2` -- settings for log level: 0 - Verbose, 1 - Debug, 2 - None

Next, in your project, in scripts, create calls to the Appodeal singleton methods where you want to display Ads.

*For example:*

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

*For example:*
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

*For example:*
```gdscript
Appodeal.log_event("gb_main_menu_open", {"screen":name})
```
**See official documentation for [Event Tracking](https://docs.appodeal.com/ru/android/advanced/event-tracking)**
