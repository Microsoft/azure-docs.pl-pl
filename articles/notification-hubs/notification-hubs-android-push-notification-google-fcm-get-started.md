---
title: Wysyłanie powiadomień wypychanych do systemu Android przy użyciu usługi Azure Notification Hubs i zestawu SDK Firebase w wersji 0,6 | Microsoft Docs
description: W tym samouczku dowiesz się, jak wysyłać powiadomienia wypychane do 0,6 urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging.
services: notification-hubs
documentationcenter: android
keywords: powiadomienia wypychane, powiadomienie wypychane, powiadomienia wypychane w systemie android, firebase cloud messaging
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c5485dacc4d9e3210ad69819caf4e36f96c626da
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428387"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-06"></a>Samouczek: wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu zestawu Firebase SDK w wersji 0,6

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

W tym samouczku pokazano, jak używać usługi Azure Notification Hubs i zestawu SDK Firebase Cloud Messaging (FCM) w wersji 0,6 do wysyłania powiadomień wypychanych do aplikacji systemu Android. W tym samouczku tworzysz pustą aplikację dla systemu Android służącą do odbierania powiadomień push przy użyciu usługi Firebase Cloud Messaging (FCM).

Ukończony kod dla tego samouczka można pobrać [z witryny GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu programu Android Studio.
> * Tworzenie projektu Firebase obsługującego usługę Firebase Cloud Messaging.
> * Utwórz centrum.
> * Połącz aplikację z centrum.
> * Testowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/free/). 

Potrzebne są również następujące elementy: 

* Najnowsza wersja [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* System Android 2,3 lub nowszy dla usługi Firebase Cloud Messaging
* Poprawka usługi Google Repository 27 lub nowsza dla usługi Firebase Cloud Messaging
* Usługi Google Play 9.0.2 lub nowsze dla obsługi komunikatów Firebase w chmurze

Wykonanie kroków tego samouczka jest wymaganiem wstępnym dla wszystkich innych samouczków Notification Hubs dla aplikacji systemu Android.

## <a name="create-an-android-studio-project"></a>Tworzenie projektu programu Android Studio

1. Uruchom program Android Studio.
2. Wybierz pozycję **plik**, wskaż polecenie **Nowy**, a następnie wybierz pozycję **Nowy projekt**. 
2. Na stronie **Wybierz projekt** wybierz pozycję **puste działanie**, a następnie wybierz pozycję **dalej**. 
3. Na stronie **Konfigurowanie projektu** wykonaj następujące czynności: 
    1. Wprowadź nazwę aplikacji.
    2. Określ lokalizację, w której mają zostać zapisane pliki projektu. 
    3. Wybierz pozycję **Zakończ**. 

        ![Konfigurowanie projektu](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Tworzenie projektu Firebase obsługującego usługę FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurowanie centrum

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurowanie ustawień usługi Firebase Cloud Messaging na potrzeby centrum

1. W lewym okienku w obszarze **Ustawienia** wybierz pozycję **Google (GCM/FCM)**. 
2. Wprowadź **klucz serwera** dla projektu FCM, który został wcześniej zapisany. 
3. Na pasku narzędzi wybierz pozycję **Zapisz**. 

    ![Centrum powiadomień Azure — Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Azure Portal wyświetla komunikat w obszarze alerty informujące o tym, że centrum zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony. 

Twoje centrum jest teraz skonfigurowane do pracy z usługą Firebase Cloud Messaging. Istnieją również parametry połączenia, które są niezbędne do wysyłania powiadomień do urządzenia i rejestrowania aplikacji w celu otrzymywania powiadomień.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Łączenie aplikacji z centrum powiadomień

### <a name="add-google-play-services-to-the-project"></a>Dodawanie usług Google Play do projektu

1. W Android Studio wybierz pozycję **Narzędzia** z menu, a następnie wybierz pozycję **Menedżer zestawów SDK**. 
2. Wybierz docelową wersję Android SDK, która jest używana w projekcie. Następnie wybierz pozycję **Pokaż szczegóły pakietu**. 

    ![Menedżer zestawów Android SDK — wybieranie wersji docelowej](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Wybierz pozycję **Google API**, jeśli nie została jeszcze zainstalowana.

    ![Menedżer zestawów Android SDK — wybrane interfejsy API firmy Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Przejdź do karty **SDK Tools** . Jeśli nie zainstalowano jeszcze Usługi Google Play, wybierz pozycję **usługi Google Play** , jak pokazano na poniższej ilustracji. Następnie wybierz pozycję **Zastosuj** do instalacji. Zanotuj ścieżkę zestawu SDK, która będzie potrzebna w kolejnym kroku.

    ![Menedżer zestawów Android SDK — wybrane usługi Google Play](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Jeśli zobaczysz okno dialogowe **Potwierdzanie zmiany**, wybierz przycisk **OK**. Instalator składników zainstaluje żądane składniki. Wybierz przycisk **Finish** (Zakończ) po zainstalowaniu składników.
4. Wybierz przycisk **OK**, aby zamknąć okno dialogowe **Settings for New Projects** (Ustawienia nowego projektu).  
1. Otwórz plik AndroidManifest.xml, a następnie Dodaj następujący tag do znacznika *aplikacji* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Dodawanie bibliotek Notification Hubs platformy Azure

1. W pliku Build. Gradle dla aplikacji Dodaj następujące wiersze w sekcji zależności.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. Dodaj następujące repozytorium po sekcji dependencies.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Dodawanie obsługi usługi Google Firebase

1. W pliku Build. Gradle dla aplikacji Dodaj następujące wiersze w sekcji **zależności** , jeśli jeszcze nie istnieją. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Dodaj następującą wtyczkę na końcu pliku, jeśli jeszcze jej nie ma. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Wybierz pozycję **Sync Now** (Synchronizuj teraz) na pasku narzędzi.

### <a name="update-the-androidmanifestxml-file"></a>Aktualizowanie pliku AndroidManifest.xml

1. Po otrzymaniu tokenu rejestracji usługi FCM można użyć go do [zarejestrowania się w usłudze Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Ta rejestracja jest obsługiwana w tle przy użyciu `IntentService` nazwy `RegistrationIntentService` . Ta usługa odświeża również token rejestracji FCM. Należy również utworzyć klasę o nazwie `FirebaseService` jako podklasę `FirebaseMessagingService` i zastąpić `onMessageReceived` metodę, aby otrzymywać i obsługiwać powiadomienia. 

    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Dodaj następujące wymagane uprawnienia związane z FCMem poniżej `</application>` znacznika.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Dodaj kod

1. W widoku projektu rozwiń węzeł **App**  >  **src**  >  **Main**  >  **Java**. Kliknij prawym przyciskiem myszy folder pakietu w obszarze **Java**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Klasa języka Java**. Wprowadź **NotificationSettings** jako nazwę, a następnie wybierz przycisk **OK**.

    Zaktualizuj te trzy symbole zastępcze w poniższym kodzie dla klasy `NotificationSettings`:

   * **HubListenConnectionString**: domyślne parametry połączenia **DefaultListenAccessSignature** dla centrum. Możesz skopiować te parametry połączenia, klikając pozycję **zasady dostępu** w centrum w [Azure Portal].
   * **HubName**: Użyj nazwy centrum, która pojawia się na stronie centrum w [Azure Portal].

     `NotificationSettings` — kod:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Aby kontynuować, wprowadź **nazwę** i **DefaultListenSharedAccessSignature** centrum. 

2. Dodaj kolejną nową klasę o nazwie `RegistrationIntentService` do projektu. Ta klasa implementuje `IntentService` interfejs. Obsługuje również [odświeżanie tokenu FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i [Rejestrowanie się w centrum powiadomień](notification-hubs-push-notification-registration-management.md).

    Użyj następującego kodu dla tej klasy.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
               else if (!(storedToken = sharedPreferences.getString("FCMtoken", "")).equals(FCM_token)) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. W `MainActivity` klasie Dodaj następujące `import` instrukcje powyżej deklaracji klasy.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Dodaj następujące składowe u góry klasy. Pola służą do [sprawdzania dostępności usług Google Play zgodnie z zaleceniami firmy Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. W `MainActivity` klasie Dodaj następującą metodę, aby sprawdzić dostępność usługi Google Play.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
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

6. W `MainActivity` klasie Dodaj następujący kod, który wyszukuje usługi Google Play przed wywołaniem w `IntentService` celu pobrania tokenu rejestracji FCM i zarejestrowania się w centrum:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. W `OnCreate` metodzie `MainActivity` klasy Dodaj następujący kod, aby rozpocząć proces rejestracji podczas tworzenia działania:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Aby zweryfikować stan aplikacji i raport w aplikacji, Dodaj następujące dodatkowe metody do `MainActivity` :

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

9. Metoda `ToastNotify` używa kontrolki *"Hello World"* `TextView` do trwałego zgłaszania stanu i powiadomień w aplikacji. W obszarze Układ **zasobów**  >  **layout**  >  **activity_main.xml** układ Dodaj następujący identyfikator dla tej kontrolki.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Zrzut ekranu pokazujący identyfikator Android: ID = "@ + ID/text_hello" zastosowany do formantu TextView.](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Następnie Dodaj podklasę dla odbiorcy zdefiniowanego w AndroidManifest.xml. Dodaj kolejną nową klasę o nazwie `FirebaseService` do projektu.

11. Dodaj następujące instrukcje import u góry pliku `FirebaseService.java`:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Dodaj następujący kod dla `FirebaseService` klasy, tworząc podklasę `FirebaseMessagingService` .

    Ten kod zastępuje `onMessageReceived` otrzymane powiadomienia. wysyła również Powiadomienie wypychane do Menedżera powiadomień systemu Android za pomocą `sendNotification()` metody. Wywołaj `sendNotification()` metodę, gdy aplikacja nie jest uruchomiona i otrzymasz powiadomienie.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. W Android Studio na pasku menu wybierz kolejno opcje **Kompiluj**  >  **Skompiluj projekt** , aby upewnić się, że w kodzie nie występują błędy. Jeśli zostanie wyświetlony komunikat o błędzie o `ic_launcher` ikonie, Usuń następujące instrukcje z pliku AndroidManifest.xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Upewnij się, że masz urządzenie wirtualne do uruchamiania aplikacji. Jeśli go nie masz, Dodaj go w następujący sposób:
    1. ![Otwórz Menedżera urządzeń](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Tworzenie urządzenia wirtualnego](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Uruchom aplikację na wybranym urządzeniu i sprawdź, czy została ona pomyślnie zarejestrowana w centrum.

    > [!NOTE]
    > Rejestracja może zakończyć się niepowodzeniem podczas początkowego uruchomienia, dopóki nie `onTokenRefresh()` zostanie wywołana metoda usługi identyfikatora wystąpienia. Odświeżenie powinno zainicjować pomyślną rejestrację w centrum powiadomień.

    ![Rejestrowanie urządzenia powiodło się.](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Wysyłanie testowego powiadomienia z centrum powiadomień

Powiadomienia wypychane można wysyłać z [Azure Portal] , wykonując następujące czynności:

1. W Azure Portal na stronie Centrum powiadomień centrum, wybierz pozycję **Testuj Wyślij** w sekcji **Rozwiązywanie problemów** .
3. W pozycji **Platformy** wybierz opcję **Android**.
4. Wybierz pozycję **Wyślij**.  Nie zobaczysz jeszcze powiadomienia na urządzeniu z systemem Android, ponieważ na nim nie uruchomiono aplikacji mobilnej. Po uruchomieniu aplikacji mobilnej ponownie wybierz przycisk **Wyślij** , aby zobaczyć komunikat powiadomienia.
5. Zobacz wynik operacji na liście u dołu.

    ![Azure Notification Hubs — Wysyłanie testowe](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Zobaczysz komunikat powiadomienia na urządzeniu. 

    ![Komunikat z powiadomieniem na urządzeniu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Uruchamianie aplikacji mobilnej na emulatorze

Przed przetestowaniem powiadomień wypychanych wewnątrz emulatora upewnij się, że obraz emulatora obsługuje poziom interfejsu API Google wybrany dla aplikacji. Jeśli obraz nie obsługuje natywnych interfejsów API Google, może wystąpić wyjątek **usługi \_ niedostępne \_ ** .

Upewnij się również, że konto Google zostało dodane do działającego emulatora w obszarze **Ustawienia**  >  **konta**. W przeciwnym razie próby zarejestrowania się w usłudze FCM mogą spowodować **wyjątek \_ niepowodzenia uwierzytelniania** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto usługi Firebase Cloud Messaging do rozgłaszania powiadomień do wszystkich urządzeń z systemem Android, które zostały zarejestrowane w usłudze. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Samouczek: powiadomienia wypychane do określonych urządzeń z systemem Android](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Witryna Azure Portal]: https://portal.azure.com
