---
title: Wysyłanie powiadomień wypychanych do systemu Android przy użyciu usługi Azure Notification Hubs i Google Cloud Messaging | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak za pomocą usług Azure Notification Hubs i Google Firebase Cloud Messaging wysyłać powiadomienia push do urządzeń z systemem Android.
services: notification-hubs
documentationcenter: android
keywords: powiadomienia wypychane, powiadomienie wypychane, powiadomienia wypychane w systemie android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c97710202a448c613ab685932cb335bbaed4953
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87832658"
---
# <a name="tutorial-send-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Samouczek: wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs i Google Cloud Messaging (przestarzałe)

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!WARNING]
> Od 10 kwietnia 2018 firma Google ma przestarzałe Google Cloud Messaging (GCM). Serwer GCM i interfejsy API klienta są przestarzałe i zostaną usunięte od razu do 29 maja 2019. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące GCM i FCM](https://developers.google.com/cloud-messaging/faq).

## <a name="overview"></a>Omówienie

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla systemu Android przy użyciu usługi Azure Notification Hubs.
Utworzysz pustą aplikację dla systemu Android służącą do odbierania powiadomień push przy użyciu usługi Google Cloud Messaging (GCM).

> [!IMPORTANT]
> Google Cloud Messaging (GCM) jest przestarzała i zostanie [wkrótce](https://developers.google.com/cloud-messaging/faq)usunięta.

> [!IMPORTANT]
> W tym temacie przedstawiono powiadomienia wypychane za pomocą usługi Google Cloud Messaging (GCM). Jeśli używasz usługi Google Firebase Cloud Messaging (FCM), zobacz [Wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

Kompletny kod dla tego samouczka można pobrać z witryny GitHub [tutaj](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie projektu obsługującego usługę Google Cloud Messaging.
> * Tworzenie centrum powiadomień
> * Łączenie aplikacji z centrum powiadomień
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Tworzenie projektu obsługującego usługę Google Cloud Messaging

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>Konfigurowanie ustawienia usługi GCM dla centrum powiadomień

1. Wybierz pozycję **Google (GCM)** w obszarze **USTAWIENIA POWIADOMIEŃ**.
2. Wprowadź **klucz interfejsu API** uzyskany z konsoli Google Cloud Console.
3. Wybierz pozycję **Zapisz** na pasku narzędzi.

    ![Azure Notification Hubs — Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Twoje centrum powiadomień jest teraz skonfigurowane do pracy z usługą GCM i uzyskano parametry połączenia do rejestrowania aplikacji w celu odbierania i wysyłania powiadomień wypychanych.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Łączenie aplikacji z centrum powiadomień

### <a name="create-a-new-android-project"></a>Tworzenie nowego projektu dla systemu Android

1. W programie Android Studio utwórz nowy projekt programu Android Studio.

   ![Android Studio — nowy projekt][13]
2. Wybierz kształt **Phone and Tablet** (Telefon i tablet) oraz odpowiednią wersję z listy **Minimum SDK** (Minimalna wersja zestawu SDK), która ma być obsługiwana. Następnie kliknij przycisk **Dalej**.

   ![Android Studio — przepływ pracy tworzenia projektu][14]
3. Wybierz pozycję **Empty Activity** (Puste działanie) dla głównego działania i kliknij przycisk **Next** (Dalej), a następnie kliknij przycisk **Finish** (Zakończ).

### <a name="add-google-play-services-to-the-project"></a>Dodawanie usług Google Play do projektu

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Dodawanie bibliotek usługi Azure Notification Hubs

1. W pliku `Build.Gradle` dla **aplikacji** dodaj następujące wiersze w sekcji **dependencies**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Dodaj następujące repozytorium po sekcji **dependencies**.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="updating-the-projects-androidmanifestxml"></a>Aktualizowanie pliku AndroidManifest.xml projektu

1. W celu zapewnienia obsługi usługi GCM zaimplementuj w kodzie wystąpienie usługi odbiornika interfejsu Instance ID, który służy do [uzyskiwania tokenów rejestracji](https://developers.google.com/cloud-messaging/) przy użyciu [interfejsu API Instance ID firmy Google](https://developers.google.com/instance-id/). W tym samouczku nazwa klasy to `MyInstanceIDService`.

    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`. Zastąp symbol zastępczy `<your package>` rzeczywistą nazwą pakietu wyświetlaną u góry pliku `AndroidManifest.xml`.
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```

2. Po odebraniu przez aplikację tokenu rejestracji usługi GCM z interfejsu API Instance ID token zostanie użyty do [rejestracji w usłudze Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Rejestracja w tle jest przeprowadzana przy użyciu klasy `IntentService` o nazwie `RegistrationIntentService`. Ta usługa odpowiada za [odświeżanie tokenu rejestracji usługi GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`. Zastąp symbol zastępczy `<your package>` rzeczywistą nazwą pakietu wyświetlaną u góry pliku `AndroidManifest.xml`.

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Zdefiniujemy odbiornik, aby otrzymywać powiadomienia. Dodaj następującą definicję odbiornika do pliku AndroidManifest.xml wewnątrz tagu `<application>`. Zastąp symbol zastępczy `<your package>` rzeczywistą nazwą pakietu wyświetlaną u góry pliku `AndroidManifest.xml`.

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

4. Dodaj następujące wymagane uprawnienia usługi GCM poniżej tagu `<application>`. Zastąp wartość `<your package>` nazwą pakietu wyświetlaną u góry pliku `AndroidManifest.xml`.

    Aby uzyskać więcej informacji o tych uprawnieniach, zobacz [Konfigurowanie aplikacji klienckiej GCM dla systemu Android](https://developers.google.com/cloud-messaging/).

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>Dodawanie kodu

1. W widoku projektu rozwiń węzeł **App**  >  **src**  >  **Main**  >  **Java**. Kliknij prawym przyciskiem folder pakietu w węźle **java**, kliknij pozycję **New** (Nowy), a następnie kliknij pozycję **Java Class** (Klasa Java). Dodaj nową klasę o nazwie `NotificationSettings`.

    ![Android Studio – nowa klasa Java][6]

    Zaktualizuj trzy symbole zastępcze w poniższym kodzie dla klasy `NotificationSettings`:

   * `SenderId`: Numer projektu uzyskany wcześniej w [konsoli Google Cloud Console](https://cloud.google.com/console).
   * `HubListenConnectionString`: Parametry połączenia **DefaultListenAccessSignature** dla centrum. Możesz skopiować te parametry połączenia, klikając pozycję **Zasady dostępu** na stronie **Ustawienia** centrum w witrynie [Azure Portal].
   * `HubName`: Użyj nazwy centrum powiadomień, która jest wyświetlana na stronie Centrum w [Azure Portal].

     `NotificationSettings` — kod:

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```

2. Dodaj kolejną nową klasę o nazwie `MyInstanceIDService`. Ta klasa to Twoja implementacja usługi odbiornika interfejsu Instance ID.

    Kod dla tej klasy będzie wywoływać klasę `IntentService` w celu [odświeżenia tokenu usługi GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) w tle.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Dodaj kolejną nową klasę do projektu o nazwie `RegistrationIntentService` . Ta klasa zawiera implementację interfejsu `IntentService`, który obsługuje [odświeżanie tokenu usługi GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i [rejestrowanie w centrum powiadomień](notification-hubs-push-notification-registration-management.md).

    Użyj następującego kodu dla tej klasy.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. W klasie `MainActivity` dodaj następujące instrukcje `import` na początku klasy.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```

5. Dodaj następujące prywatne składowe u góry klasy. Ten kod umożliwia [sprawdzanie dostępności usług Google Play zgodnie z zaleceniami firmy Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```

6. W klasie `MainActivity` dodaj następującą metodę do sprawdzania dostępności usług Google Play.

    ```java
    /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. W klasie `MainActivity` dodaj następujący kod, który sprawdza dostępność usług Google Play przed wywołaniem klasy `IntentService` w celu pobrania tokenu rejestracji GCM i rejestracji w centrum powiadomień.

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. W metodzie `OnCreate` klasy `MainActivity` dodaj następujący kod w celu uruchomienia procesu rejestracji po utworzeniu działania.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```

9. Dodaj te dodatkowe metody do klasy `MainActivity`, aby zweryfikować stan aplikacji i zgłosić stan w aplikacji.

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. Metoda `ToastNotify` używa kontrolki *"Hello World"* `TextView` do trwałego zgłaszania stanu i powiadomień w aplikacji. W układzie activity_main.xml dodaj następujący identyfikator dla tej kontrolki.

    ```xml
    android:id="@+id/text_hello"
    ```

11. Dodaj podklasę dla odbiornika zdefiniowanego w pliku AndroidManifest.xml. Dodaj kolejną nową klasę o nazwie `MyHandler` do projektu.

12. Dodaj następujące instrukcje import u góry pliku `MyHandler.java`:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```

13. Dodaj następujący kod dla `MyHandler` klasy, tworząc podklasę `com.microsoft.windowsazure.notifications.NotificationsHandler` .

    Ten kod zastępuje metodę `OnReceive`, dlatego program obsługi zgłasza odbierane powiadomienia. Program obsługi wysyła również powiadomienie wypychane do menedżera powiadomień systemu Android za pomocą metody `sendNotification()`. Metoda `sendNotification()` powinna być wykonywana, gdy aplikacja nie jest uruchomiona i otrzymano powiadomienie.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }

        private void sendNotification(String msg) {

            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);

            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```

14. W Android Studio na pasku menu kliknij kolejno pozycje **Kompiluj** Kompiluj  >  **ponownie projekt** , aby upewnić się, że w kodzie nie występują żadne błędy.

## <a name="testing-your-app"></a>Testowanie aplikacji

### <a name="run-the-mobile-application"></a>Uruchamianie aplikacji mobilnej

1. Uruchom aplikację i zwróć uwagę, czy identyfikator rejestracji jest zgłaszany, co oznacza pomyślną rejestrację.

      ![Testowanie w systemie Android — rejestracja kanału][18]
2. Wprowadź komunikat powiadomienia, który ma zostać wysłany do wszystkich urządzeń z systemem Android zarejestrowanych w centrum.

      ![Testowanie w systemie Android — wysyłanie komunikatu][19]

3. Naciśnij przycisk **Send Notification** (Wyślij powiadomienie). Na wszystkich urządzeniach, na których jest uruchomiona aplikacja, jest wyświetlane wystąpienie kontrolki `AlertDialog` z komunikatem powiadomienia push. Urządzenia, na których aplikacja nie jest uruchomiona, ale zostały wcześniej zarejestrowane do otrzymywania powiadomień push, otrzymują powiadomienie w menedżerze powiadomień systemu Android. Komunikaty z powiadomieniami można wyświetlić, szybko przesuwając w dół od lewego górnego rogu.

      ![Testowanie w systemie Android — powiadomienia][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Wysyłanie testowych powiadomień push z poziomu witryny Azure Portal

Możesz przetestować odbieranie powiadomień push w aplikacji, wysyłając je w witrynie [Azure Portal].

1. W sekcji **Rozwiązywanie problemów** wybierz pozycję **Wysyłanie testowe**.
2. W pozycji **Platformy** wybierz opcję **Android**.
3. Wybierz przycisk **Wyślij**, aby wysłać powiadomienie testowe.
4. Upewnij się, został wyświetlony komunikat powiadomienia na urządzeniu z systemem Android.

    ![Azure Notification Hubs — Wysyłanie testowe](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>Powiadomienia wypychane w emulatorze

Aby przetestować powiadomienia wypychane w emulatorze, upewnij się, że obraz emulatora obsługuje poziom interfejsu API Google wybrany dla aplikacji. Jeśli obraz nie obsługuje natywnych interfejsów API Google, wystąpi wyjątek **SERVICE\_NOT\_AVAILABLE** (usługa niedostępna).

Ponadto upewnij się, że dodano konto Google do działającego emulatora w obszarze **Ustawienia**  >  **konta**. W przeciwnym razie próby rejestracji w usłudze GCM mogą spowodować wystąpienie wyjątku **AUTHENTICATION\_FAILED** (uwierzytelnianie nie powiodło się).

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Opcjonalnie) Wysyłanie powiadomień wypychanych bezpośrednio z poziomu aplikacji

Zwykle powiadomienia są wysyłane przy użyciu serwera zaplecza. W niektórych przypadkach warto wysyłać powiadomienia bezpośrednio z aplikacji klienta. W tej sekcji wyjaśniono sposób wysyłania powiadomień z klienta przy użyciu [interfejsu API REST usługi Azure Notification Hubs](/previous-versions/azure/reference/dn223264(v=azure.100)).

1. W widoku projektu Android Studio rozwiń węzeł **App**  >  **src**  >  **Main**  >  **res**  >  **layout**. Otwórz plik układu `activity_main.xml` i kliknij kartę **Text** (Tekst), aby zaktualizować zawartość tekstową pliku. Zaktualizuj go przy użyciu poniższego kodu, który dodaje nowe kontrolki `Button` i `EditText` służące do wysyłania komunikatów powiadomień wypychanych do centrum powiadomień. Dodaj ten kod w dolnej części bezpośrednio przed elementem `</RelativeLayout>`.

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```

2. W widoku projektu Android Studio rozwiń węzeł **App**  >  **src**  >  **Main**  >  **res**  >  **Values**. Otwórz plik `strings.xml` i dodaj wartości ciągu, do których odwołują się nowe kontrolki `Button` i `EditText`. Dodaj poniższe wiersze w dolnej części pliku, bezpośrednio przed elementem `</resources>`.

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```

3. W pliku `NotificationSetting.java` dodaj następujące ustawienie do klasy `NotificationSettings`.

    Zaktualizuj element `HubFullAccess` przy użyciu parametrów połączenia **DefaultFullSharedAccessSignature** dla swojego centrum. Parametry połączenia można skopiować z witryny [Azure Portal], klikając pozycję **Zasady dostępu** na stronie **Ustawienia** centrum powiadomień.

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```

4. W pliku `MainActivity.java` dodaj następujące instrukcje `import` na początku pliku.

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```

5. W pliku `MainActivity.java` dodaj następujące składowe u góry klasy `MainActivity`.

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```

6. Utwórz token sygnatury dostępu współdzielonego (SaS), aby uwierzytelnić żądanie POST w celu wysyłania komunikatów do centrum powiadomień. Przeanalizuj dane klucza z parametrów połączenia, a następnie utwórz token sygnatury dostępu współdzielonego, jak określono w dokumentacji interfejsu API REST [Common Concepts](/previous-versions/azure/reference/dn495627(v=azure.100)) (Wspólne pojęcia). Poniższy kod jest przykładem implementacji.

    W pliku `MainActivity.java` dodaj następującą metodę do klasy `MainActivity`, aby przeanalizować parametry połączenia.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```

7. W pliku `MainActivity.java` dodaj następującą metodę do klasy `MainActivity`, aby utworzyć token uwierzytelniania sygnatury dostępu współdzielonego.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```

8. W pliku `MainActivity.java` dodaj następującą metodę do klasy `MainActivity` w celu obsługi kliknięcia przycisku **Send Notification** (Wyślij powiadomienie) i wysłania komunikatu do centrum przy użyciu wbudowanego interfejsu API REST.

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano wyemitowane powiadomienia do wszystkich urządzeń z systemem Android zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem Android, przejdź do następującego samouczka:  

 > [!div class="nextstepaction"]
 > [Wypychanie powiadomień do konkretnych urządzeń](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Witryna Azure Portal]: https://portal.azure.com
