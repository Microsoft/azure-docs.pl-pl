---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147803"
---
### <a name="add-the-google-services-json-file"></a>Dodaj plik JSON usług Google Services

1. **Kontrolka**  +  **Kliknij** folder **Android** , a następnie wybierz **Otwórz w Android Studio**. Następnie przejdź do widoku **projektu** (jeśli nie jest jeszcze).

1. Znajdź *google-services.jsna* pobranym wcześniej pliku podczas konfigurowania projektu **PushDemo** w [konsoli Firebase](https://console.firebase.google.com). Następnie przeciągnij go do katalogu głównego modułu **aplikacji** (aplikacja**Android**  >  **Android**  >  **app**).

### <a name="configure-build-settings-and-permissions"></a>Konfigurowanie ustawień i uprawnień kompilacji

1. Przełącz widok **projektu** do **systemu Android**.

1. Otwórz **AndroidManifest.xml**, a następnie Dodaj uprawnienia **internetowe** i **READ_PHONE_STATE** po elemencie **aplikacji** przed tagiem zamykającym **</manifest>** .

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Dodawanie zestawów SDK Firebase

1. W **Android Studio**Otwórz plik **Build. Gradle** na poziomie projektu (**Gradle scripts**  >  **Build. Gradle (Project: Android)**). Upewnij się, że masz ścieżkę "com. Google. GMS: Google-Services" w ``buildscript``  >  węźle **zależności** .

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Upewnij się, że masz odwołanie do najnowszej wersji zgodnie z instrukcjami podanymi w [konsoli Firebase](https://console.firebase.google.com) podczas tworzenia **projektu systemu Android**.

1. W pliku **Build. Gradle kompilacji** na poziomie aplikacji (**Gradle scripts**  >  **Build. Gradle (module: App)**) Zastosuj **wtyczkę Gradle usług Google Services**. Zastosuj wtyczkę bezpośrednio nad węzłem **systemu Android** .

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. W tym samym pliku, w węźle **zależności** , Dodaj zależność dla biblioteki systemu Android dla usługi **Cloud Messaging** .

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Upewnij się, że korzystasz z najnowszej wersji zgodnie z [dokumentacją klienta systemu Android dla usługi Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/android/client).

1. Zapisz zmiany, a następnie kliknij przycisk **Synchronizuj teraz** (w wierszu paska narzędzi) lub **zsynchronizuj projekt z plikami Gradle**.

### <a name="handle-push-notifications-for-android"></a>Obsługa powiadomień wypychanych dla systemu Android

1. W **Android Studio**kliknij **formant**  +  **Click** **com. <your_organization>. pushdemo** (**App**  >  **src**  >  **Main**  >  **Kotlin**), wybierz pozycję **pakiet** z menu **Nowy** . Wprowadź **usługi** jako nazwę, a następnie naciśnij klawisz **Return**.

1. **Kontrolka**  +  **Kliknij** folder **usługi** , wybierz pozycję **Kotlin plik/Klasa** z menu **Nowy** . Wprowadź **DeviceInstallationService** jako nazwę, a następnie naciśnij klawisz **Return**.

1. Zaimplementuj **DeviceInstallationService** przy użyciu następującego kodu.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Ta klasa implementuje odpowiedni dla danej platformy odpowiednik dla `com.<your_organization>.pushdemo/deviceinstallation` kanału. Zostało to zdefiniowane w części Flutter aplikacji w **DeviceInstallationService. dart**. W takim przypadku wywołania są wykonywane ze wspólnego kodu do macierzystego hosta. Pamiętaj, aby zastąpić **<your_organization>** własną organizacją, wszędzie tam, gdzie jest to używane.
    >
    > Ta klasa udostępnia unikatowy identyfikator (przy użyciu [bezpiecznego. AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) w ramach ładunku rejestracji centrum powiadomień.

1. Dodaj inny **plik/klasę Kotlin** do folderu **Services** o nazwie *NotificationRegistrationService*, a następnie Dodaj następujący kod.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Ta klasa implementuje odpowiedni dla danej platformy odpowiednik dla `com.<your_organization>.pushdemo/notificationregistration` kanału. Zostało to zdefiniowane w części Flutter aplikacji w **NotificationRegistrationService. dart**. W takim przypadku wywołania są nawiązywane z macierzystego hosta do wspólnego kodu. Należy również zastanowić się nad zastępowaniem **<your_organization>** własną organizacją, wszędzie tam, gdzie jest to używane.

1. Dodaj inny **plik/klasę Kotlin** do folderu **Services** o nazwie *NotificationActionService*, a następnie Dodaj następujący kod.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Ta klasa implementuje odpowiedni dla danej platformy odpowiednik dla `com.<your_organization>.pushdemo/notificationaction` kanału. To zostało zdefiniowane w części Flutter aplikacji w **NotificationActionService. dart**. W takim przypadku wywołania mogą być wykonywane w obu kierunkach. Pamiętaj, aby zastąpić **<your_organization>** własną organizacją, wszędzie tam, gdzie jest to używane.

1. Dodaj nowy **plik/klasę Kotlin** do **modelu com. <your_organization>. pushdemo** pakiet o nazwie *PushNotificationsFirebaseMessagingService*, a następnie Zaimplementuj go przy użyciu następującego kodu.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Ta klasa jest odpowiedzialna za obsługę powiadomień, gdy aplikacja jest uruchomiona na pierwszym planie. Warunkowo wywoła **triggerAction** w **NotificationActionService** , jeśli w ładunku powiadomienia zostanie uwzględniona akcja, która została odebrana w **onMessageReceived**. Spowoduje to również wywołanie **refreshRegistration** w **NotificationRegistrationService** , gdy token **Firebase** zostanie wygenerowany ponownie przez zastąpienie funkcji **onNewToken** .
    >
    > Jeszcze raz zapoznaj się z tematem Zastąp **<your_organization>** własną organizacją, wszędzie tam, gdzie jest używana.

1. W **AndroidManifest.xml** (**app**  >  **src**  >  **główny**src aplikacji) Dodaj **PushNotificationsFirebaseMessagingService** do dolnej części elementu **aplikacji** z `com.google.firebase.MESSAGING_EVENT` filtrem intencje.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. W **DeviceInstallationService**upewnij się, że następujące Importy znajdują się u góry pliku.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Zastąp **<your_organization>** własną wartością swojej organizacji.

1. Zaktualizuj tekst zastępczy *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* , aby uzyskać wartość tokenu z **PushNotificationFirebaseMessagingService**.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. W obszarze **głównym**upewnij się, że następujące Importy znajdują się u góry pliku.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Zastąp **<your_organization>** własną wartością swojej organizacji.

1. Dodaj zmienną do przechowywania odwołania do **DeviceInstallationService**.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Dodaj funkcję o nazwie **processNotificationActions** , aby sprawdzić, czy **cel** ma dodatkową wartość o nazwie **Action**. Warunkowego wyzwalania tej akcji lub zapisywania jej do użycia później, jeśli akcja jest przetwarzana podczas uruchamiania aplikacji.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Zastąp funkcję **onNewIntent** , aby wywołać **processNotificationActions**.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Ponieważ wartość parametru **launchmode** dla operacji **Main** jest ustawiona **na SingleTop**, **zamiar** zostanie wysłany do istniejącego wystąpienia **działania** za pośrednictwem funkcji **onNewIntent** , a nie za pomocą funkcji **OnCreate** , a więc musisz obsługiwać przychodzący **cel** w funkcjach **OnCreate** i **onNewIntent** .

1. Zastąp funkcję **OnCreate** , ustaw wartość **deviceInstallationService** na nowe wystąpienie elementu **deviceInstallationService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Ustaw właściwości **notificationActionService** i **notificationRegistrationService** na **PushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. W tej samej funkcji warunkowo Wywołaj **FirebaseInstanceId. GetInstance (). InstanceId**. Zaimplementuj **OnCompleteListener** , aby ustawić wartość **tokenu** " **PushNotificationFirebaseMessagingService** " przed wywołaniem **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Nadal w obszarze **OnCreate**Wywołaj **processNotificationActions** na końcu funkcji. Użyj *wartości true* dla argumentu *launchAction* , aby wskazać, że ta akcja jest przetwarzana podczas uruchamiania aplikacji.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Należy ponownie zarejestrować aplikację przy każdym uruchomieniu i zatrzymać ją z sesji debugowania, aby kontynuować otrzymywanie powiadomień wypychanych.
