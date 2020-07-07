---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060501"
---
### <a name="configure-required-android-packages"></a>Konfigurowanie wymaganych pakietów systemu Android

Pakiet jest [automatycznie łączony](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) podczas kompilowania aplikacji. Poniżej przedstawiono kilka dodatkowych kroków, które należy wykonać, aby zakończyć proces konfiguracji.

### <a name="configure-android-manifest"></a>Konfigurowanie manifestu systemu Android

W "Android/App/src/Main/AndroidManifest.xml" Sprawdź nazwę pakietu, uprawnienia i wymagane usługi. Upewnij się, że zarejestrowane `RNPushNotificationPublisher` i `RNPushNotificationBootEventReceiver` odbierane i zarejestrowano `RNPushNotificationListenerService` usługę. Metadane powiadomień mogą służyć do dostosowywania wyglądu powiadomień wypychanych.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Konfigurowanie usług Google Services

W obszarze "Android/App/Build. Gradle" Zarejestruj usługi Google:

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

Skopiuj plik "google-services.json" pobrany podczas instalacji FCM do folderu projektu "Android/App/".

### <a name="handle-push-notifications-for-android"></a>Obsługa powiadomień wypychanych dla systemu Android

Istniejąca usługa została skonfigurowana `RNPushNotificationListenerService` do obsługi przychodzących powiadomień wypychanych systemu Android. Ta usługa została zarejestrowana wcześniej w manifeście aplikacji. Przetwarza przychodzące powiadomienia i serwery proxy do wieloplatformowej części strony. Nie są wymagane żadne dodatkowe czynności.
