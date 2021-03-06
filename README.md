android-appversion-gradle-plugin
================================
[![Android Arsenal](http://img.shields.io/badge/Android%20Arsenal-appversion--plugin-brightgreen.svg)](https://android-arsenal.com/details/1/1129)

1. Easy way to add build number to your application version
2. Easy way to change APK name and add extra data to it.

## Supported version of gradle-android-plugin
- `0.12.+` -> [![Maven Central](http://img.shields.io/badge/maven-v.1.0.2-blue.svg)](http://search.maven.org/#artifactdetails%7Ccom.github.hamsterksu%7Candroid-appversion-gradle-plugin%7C1.0.2%7Cjar) - `com.github.hamsterksu:android-appversion-gradle-plugin:1.0.+`
- ` > 0.14.+` -> [![Maven Central](http://img.shields.io/badge/maven-v.1.2.0-blue.svg)](http://search.maven.org/#artifactdetails%7Ccom.github.hamsterksu%7Candroid-appversion-gradle-plugin%7C1.2.0%7Cjar) - `com.github.hamsterksu:android-appversion-gradle-plugin:1.2.+`
- `1.0.+` -> [![Maven Central](http://img.shields.io/badge/maven-v.1.2.0-blue.svg)](http://search.maven.org/#artifactdetails%7Ccom.github.hamsterksu%7Candroid-appversion-gradle-plugin%7C1.2.0%7Cjar) - `com.github.hamsterksu:android-appversion-gradle-plugin:1.2.+`
 
## Сontents
1. [How to use](#how-to-use)
  1. [Add plugin to dependencies](#add-plugin-to-dependencies)
  2. [Apply plugin](#apply-plugin)
  3. [Configure plugin](#configure-plugin)
  4. [Available options](#available-options)
2. [Typical usecases](#typical-usecases)
3. [Use brackets in some cases](#use-brackets-in-some-cases)
4. [What is *versions.properties* file?](#what-is-versionsproperties-file?)
5. [Configuration examples](#config-examples)

## Typical usecases

*Do you want to use package name, app version name and version code in file name?*

Just use the following `fileNameFormat`: `$appPkg.$versionName($versionCode)`

**Result** - `com.yourdomain.app.1.0.0.1(1).apk`

## Use brackets in some cases

*Why do you need to use brackets `{}` in some cases?*

`fileNameFormat` - is a Groovy string. Be careful to use something like `$appPkg.v_$versionName`. Groovy will try to find the `"v_"` property in the String object. And you will get `Error: > No such property: v_ for class: java.lang.String`.

In this case you need to use `{}`: `${appPkg}.v_$versionName`

## How to use

#### Add plugin to dependencies

```groovy
buildscript {
    repositories {
		mavenCentral()
	}

	dependencies{
		classpath 'com.github.hamsterksu:android-appversion-gradle-plugin:1.+'
	}
}
```

#### Apply plugin

```groovy
apply plugin: 'versionPlugin'
```
	
#### Configure plugin

```groovy
versionPlugin{
	buildTypesMatcher = 'release'

	supportBuildNumber = true
	buildNumberPrefix = 'b'
	
	fileNameFormat = '$appPkg-v_$versionName-c_$versionCode'
}
```
	
## Available options:##

* **buildTypesMatcher** - `regexp` value. the most useful case: if you want to apply changes for one build type use `buildTypesMatcher = 'release'` if for both `buildTypesMatcher = 'release|debug'`. Sure you can use any regexp value.
* **supportBuildNumber** - use `true` if you want to add `build number` to `appVersion`. `versions.properties file` will be generated in root project. you can add it to version control ignore list if you use CI or you are alone devloper in the team.
* **buildNumberPrefix** - `string` value, prefix before build number. example: `1.0.0.b1`
* **fileNameFormat** - `groovy template string`. you can specify your own format of APK file name. Available variables in the formatted string:
  * *appName* - name of main module
  * *projectName* - name of root project
  * *flavorName* - falvour name
  * *buildType* - build type
  * *versionName* - version name
  * *versionCode* - version code
  * *appPkg* - `application id`
  * *date* - build date, you can specify your own date format use `dateFormat` propery 
  * *time* - build time, you can specify your own date format use `timeFormat` propery 
  * *customName* - use this template to use string from `customNameMapping`
* **dateFormat** - format string for SimpleDateFormat
* **timeFormat** - format string for SimpleDateFormat
* **customNameMapping** - mapping for `$customName`. it's `groovy map` for `variantName` -> `text`

Example:
```groovy 
customNameMapping = [
    'debug':'MySuperApp',
    'releae':'MySuperApp'
]
```
```groovy 
customNameMapping = [
    'flavourDebug':'MySuperApp',
    'flavourRelease':'MySuperApp'
]
```
## What is versions.properties file?
If you turn on `supportBuildNumber` plugin will generate `versions.properties` file in root project. it store current build number for each flavour. You can add it to version control ignore list if you use CI or you are alone devloper in the team.
 
## Config examples
```groovy 
versionPlugin{
    buildTypesMatcher = 'release|debug'

    supportBuildNumber = true
    buildNumberPrefix = 'b'

    fileNameFormat = '$customName-$versionName($versionCode)-$buildType'

    customNameMapping = [
    	'debug':'mysuperapp',
        'release':'mysuperapp'
    ]
}
```
```groovy 
versionPlugin{
    buildTypesMatcher = 'release'
    supportBuildNumber = true
    
    fileNameFormat = '$appPkg-$versionName($versionCode)'
}
```
```groovy 
versionPlugin{
    buildTypesMatcher = 'release'
    supportBuildNumber = false
    
    fileNameFormat = '$projectName-$flavorName-$versionName($versionCode)-$date-$time'
    dateFormat = 'dd-MM-yyyy'
    timeFormat = 'HH:mm'
}
```
