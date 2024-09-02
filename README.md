# flutter_test_update

A new Flutter project to demonstrate how to use ota_update library for upgrading your APK from ouwn server.

## 1-- Install dependencies

Run this command to add library:

    flutter pub add ota_update

Once it is added you can start use library into your Dart code:

    import 'package:ota_update/ota_update.dart';


## 2-- How to adjsut project in Android

These steps you have to provide manually for your project to let be used with ota_update:

### 2--1-- Add permissions ot AndroidManifest.xml:

~~~xml
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES"/>
~~~

### 2--2-- Add provider

Add following provider referrence to AndroidManifest.xml inside `<application>` node.

~~~xml
    <provider
        android:name="sk.fourq.otaupdate.OtaUpdateFileProvider"
        android:authorities="${applicationId}.ota_update_provider"
        android:exported="false"
        android:grantUriPermissions="true">
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/filepaths" />
    </provider>    
~~~

### 2--3-- Point download folder

Create the file `android/src/main/res/xml/filepaths.xml` with following contents. This will allow plugin to access the downloads folder to start the update.

~~~xml
    <?xml version="1.0" encoding="utf-8"?>
    <paths xmlns:android="http://schemas.android.com/apk/res/android">
    <files-path name="internal_apk_storage" path="ota_update/"/>
    </paths>
~~~

### 2--4-- Optional: Non-https traffic

Cleartext traffic is disabled by Android download manager by default for security reasons. To allow it you need to create file `res/xml/network_security_config.xml`

~~~xml
    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config>
        <base-config cleartextTrafficPermitted="true" />
    </network-security-config>
~~~

and reference it in `AndroidManifest.xml` in `<applicaiton>` tag:

~~~xml
    android:networkSecurityConfig="@xml/network_security_config"
~~~

### 2--5-- Creating APK

This application has two environments (flavors) with anmes: 'dev' and 'prod'.
it was achieved by this code in `/android/app/build.graddle` under `android` object:

        flavorDimensions += "app"

        productFlavors {
            create("dev") {
                dimension = "app"
                resValue "string", "app_name", "flutter_test_name_dev"
            }
            create("prod") {
                dimension = "app"
                resValue "string", "app_name", "flutter_test_name"
            }
        }

        applicationVariants.all { variant ->
            variant.outputs.all {
                def productFlavorsName = productFlavors.name.toString()
                if (productFlavorsName.contains('prod')) {
                    outputFileName = "flutter_test_update.apk"
                } else {
                    outputFileName = "flutter_test_update_dev.apk"
                }
                
            }
        }


You can use two separate applications by generating two separate APKs ( this logic is added to  build.gradle of android/app).

Commands to generate APKs:

- Apk `flutter_test_update.apk` for prod:

        flutter build apk --release --flavor prod

- Apk `flutter_test_update_dev.apk` for prod:

        flutter build apk --release --flavor dev

Using terminal you can generate using these commands APK files.

These files will be by this relative paths:

    flutter_test_update\build\app\outputs\apk\dev\release
    flutter_test_update\build\app\outputs\apk\prod\release