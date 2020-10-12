---
title: Wysyłanie powiadomień wypychanych do określonych aplikacji systemu Android przy użyciu usługi Azure Notification Hubs
description: Dowiedz się, jak wysyłać powiadomienia wypychane do określonych aplikacji systemu Android przy użyciu usługi Azure Notification Hubs.
documentationcenter: android
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ae0e17a8-9d2b-496e-afd2-baa151370c25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: 870842074bb4a0012a48115586a8ad571275d470
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91281035"
---
# <a name="tutorial-send-push-notifications-to-specific-android-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień wypychanych do określonych aplikacji systemu Android przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

W tym samouczku pokazano, jak wysyłać powiadomienia push do użytkownika konkretnej aplikacji na konkretnym urządzeniu za pomocą usługi Azure Notification Hubs. Zaplecze ASP.NET interfejsu WebAPI jest używane do uwierzytelniania klientów oraz generowania powiadomień, jak pokazano w artykule ze wskazówkami [Rejestrowanie z poziomu zaplecza aplikacji](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). Ten samouczek dotyczy centrum powiadomień utworzonego w [samouczku: powiadomienia wypychane do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs i obsługi komunikatów w chmurze Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu zaplecza interfejsu WebAPI, które będzie uwierzytelniać użytkowników.  
> * Aktualizowanie aplikacji systemu Android.
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ [Samouczek: powiadomienia wypychane do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs i obsługi komunikatów w chmurze Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md) przed wykonaniem tego samouczka.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Tworzenie projektu systemu Android

Następnym krokiem jest zaktualizowanie aplikacji systemu Android utworzonej w [samouczku: powiadomienia wypychane do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs i Firebase w chmurze](notification-hubs-android-push-notification-google-fcm-get-started.md).

1. Otwórz plik `res/layout/activity_main.xml` i zastąp następujące definicje zawartości:

    Spowoduje to dodanie kontrolek EditText na potrzeby logowania się jako użytkownik. Ponadto zostanie dodane pole na potrzeby tagu nazwy użytkownika, które będzie częścią wysyłanych powiadomień:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/usernameText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/usernameHint"
        android:layout_above="@+id/passwordText"
        android:layout_alignParentEnd="true" />
    <EditText
        android:id="@+id/passwordText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/passwordHint"
        android:inputType="textPassword"
        android:layout_above="@+id/buttonLogin"
        android:layout_alignParentEnd="true" />
    <Button
        android:id="@+id/buttonLogin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/loginButton"
        android:onClick="login"
        android:layout_above="@+id/toggleButtonFCM"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="24dp" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="WNS on"
        android:textOff="WNS off"
        android:id="@+id/toggleButtonWNS"
        android:layout_toLeftOf="@id/toggleButtonFCM"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="FCM on"
        android:textOff="FCM off"
        android:id="@+id/toggleButtonFCM"
        android:checked="true"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="APNS on"
        android:textOff="APNS off"
        android:id="@+id/toggleButtonAPNS"
        android:layout_toRightOf="@id/toggleButtonFCM"
        android:layout_centerVertical="true" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessageTag"
        android:layout_below="@id/toggleButtonFCM"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_tag_hint" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_below="@+id/editTextNotificationMessageTag"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_hint" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:onClick="sendNotificationButtonOnClick"
        android:layout_below="@+id/editTextNotificationMessage"
        android:layout_centerHorizontal="true" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:id="@+id/text_hello"
        />
    </RelativeLayout>
    ```
2. Otwórz plik `res/values/strings.xml` i zastąp definicję kontrolki `send_button` następującymi wierszami, które ponownie definiują ciąg kontrolki `send_button` i dodają ciągi dla innych kontrolek:

    ```xml
    <string name="usernameHint">Username</string>
    <string name="passwordHint">Password</string>
    <string name="loginButton">1. Sign in</string>
    <string name="send_button">2. Send Notification</string>
    <string name="notification_message_hint">Notification message</string>
    <string name="notification_message_tag_hint">Recipient username</string>
    ```

    Twój układ graficzny pliku `main_activity.xml` powinien wyglądać teraz podobnie do poniższego:

    ![Zrzut ekranu przedstawiający emulator przedstawiający, jak będzie wyglądać główny układ graficzny X M L.][A1]
3. Utwórz nową klasę o nazwie `RegisterClient` w tym samym pakiecie, w którym znajduje się klasa `MainActivity`. Użyj poniższego kodu dla nowego pliku klasy.

    ```java
  
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.util.Set;
    
    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.methods.HttpPut;
    import org.apache.http.client.methods.HttpUriRequest;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONException;
    import org.json.JSONObject;
    
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.util.Log;
    
    public class RegisterClient {
        private static final String PREFS_NAME = "ANHSettings";
        private static final String REGID_SETTING_NAME = "ANHRegistrationId";
        private String Backend_Endpoint;
        SharedPreferences settings;
        protected HttpClient httpClient;
        private String authorizationHeader;
    
        public RegisterClient(Context context, String backendEndpoint) {
            super();
            this.settings = context.getSharedPreferences(PREFS_NAME, 0);
            httpClient =  new DefaultHttpClient();
            Backend_Endpoint = backendEndpoint + "/api/register";
        }
    
        public String getAuthorizationHeader() {
            return authorizationHeader;
        }
    
        public void setAuthorizationHeader(String authorizationHeader) {
            this.authorizationHeader = authorizationHeader;
        }
    
        public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
            String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
    
            JSONObject deviceInfo = new JSONObject();
            deviceInfo.put("Platform", "fcm");
            deviceInfo.put("Handle", handle);
            deviceInfo.put("Tags", new JSONArray(tags));
    
            int statusCode = upsertRegistration(registrationId, deviceInfo);
    
            if (statusCode == HttpStatus.SC_OK) {
                return;
            } else if (statusCode == HttpStatus.SC_GONE){
                settings.edit().remove(REGID_SETTING_NAME).commit();
                registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                statusCode = upsertRegistration(registrationId, deviceInfo);
                if (statusCode != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            } else {
                Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                throw new RuntimeException("Error upserting registration");
            }
        }
    
        private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                throws UnsupportedEncodingException, IOException,
                ClientProtocolException {
            HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
            request.setEntity(new StringEntity(deviceInfo.toString()));
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            request.addHeader("Content-Type", "application/json");
            HttpResponse response = httpClient.execute(request);
            int statusCode = response.getStatusLine().getStatusCode();
            return statusCode;
        }
    
        private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
            if (settings.contains(REGID_SETTING_NAME))
                return settings.getString(REGID_SETTING_NAME, null);
    
            HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            HttpResponse response = httpClient.execute(request);
            if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                throw new RuntimeException("Error creating Notification Hubs registrationId");
            }
            String registrationId = EntityUtils.toString(response.getEntity());
            registrationId = registrationId.substring(1, registrationId.length()-1);
    
            settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();
    
            return registrationId;
        }
    }
    ```

    Ten składnik implementuje wywołania REST wymagane do skontaktowania się z zapleczem aplikacji w celu zarejestrowania powiadomień wypychanych. Ponadto zapisuje lokalnie identyfikatory *registrationId* utworzone przez centrum powiadomień zgodnie z opisem w sekcji [Rejestrowanie z poziomu zaplecza aplikacji](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). Używa tokenu autoryzacji przechowywanego w magazynie lokalnym po kliknięciu przycisku **Zaloguj** .
4. W `MainActivity` klasie i Dodaj pole dla `RegisterClient` klasy i ciąg dla punktu końcowego zaplecza usługi ASP.NET. Pamiętaj, aby zastąpić ciąg `<Enter Your Backend Endpoint>` wcześniej pozyskanym, faktycznym punktem końcowym zaplecza. Na przykład `http://mybackend.azurewebsites.net`.

    ```java
    private RegisterClient registerClient;
    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    FirebaseInstanceId fcm;
    String FCM_token = null;
    ```

5. W swojej klasie `MainActivity` w metodzie `onCreate` usuń lub oznacz jako komentarz inicjowanie pola `hub` oraz wywołanie metody `registerWithNotificationHubs`. Następnie dodaj kod, aby zainicjować wystąpienie klasy `RegisterClient`. Metoda powinna zawierać następujące wiersze:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mainActivity = this;
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
        fcm = FirebaseInstanceId.getInstance();
        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);
        setContentView(R.layout.activity_main);
    }
    ```
7. Dodaj następujące instrukcje `import` do pliku `MainActivity.java`:

    ```java
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    
    import android.widget.Button;
    import android.widget.ToggleButton;
    import java.io.UnsupportedEncodingException;
    import android.content.Context;
    import java.util.HashSet;
    import android.widget.Toast;
    import org.apache.http.client.ClientProtocolException;
    import java.io.IOException;
    import org.apache.http.HttpStatus;
    
    import android.os.AsyncTask;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    
    import android.app.AlertDialog;
    import android.content.DialogInterface;
    
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.google.android.gms.tasks.OnSuccessListener;
    import java.util.concurrent.TimeUnit;
    ```
8. Zastąp kod w metodzie onStart następującym:

    ```java
    super.onStart();
    Button sendPush = (Button) findViewById(R.id.sendbutton);
    sendPush.setEnabled(false);
    ```
9. Następnie Dodaj następujące metody, aby obsłużyć przycisk **Zaloguj się** i wysyłać powiadomienia wypychane.

    ```java
    public void login(View view) throws UnsupportedEncodingException {
        this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

        final Context context = this;
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() {
                        @Override
                        public void onSuccess(InstanceIdResult instanceIdResult) {
                            FCM_token = instanceIdResult.getToken();
                            Log.d(TAG, "FCM Registration Token: " + FCM_token);
                        }
                    });
                    TimeUnit.SECONDS.sleep(1);
                    registerClient.register(FCM_token, new HashSet<String>());
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to register", e.getMessage());
                    return e;
                }
                return null;
            }

            protected void onPostExecute(Object result) {
                Button sendPush = (Button) findViewById(R.id.sendbutton);
                sendPush.setEnabled(true);
                Toast.makeText(context, "Signed in and registered.",
                        Toast.LENGTH_LONG).show();
            }
        }.execute(null, null, null);
    }

    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
        return basicAuthHeader;
    }

    /**
        * This method calls the ASP.NET WebAPI backend to send the notification message
        * to the platform notification service based on the pns parameter.
        *
        * @param pns     The platform notification service to send the notification message to. Must
        *                be one of the following ("wns", "fcm", "apns").
        * @param userTag The tag for the user who will receive the notification message. This string
        *                must not contain spaces or special characters.
        * @param message The notification message string. This string must include the double quotes
        *                to be used as JSON content.
        */
    public void sendPush(final String pns, final String userTag, final String message)
            throws ClientProtocolException, IOException {
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    String uri = BACKEND_ENDPOINT + "/api/notifications";
                    uri += "?pns=" + pns;
                    uri += "&to_tag=" + userTag;

                    HttpPost request = new HttpPost(uri);
                    request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                    request.setEntity(new StringEntity(message));
                    request.addHeader("Content-Type", "application/json");

                    HttpResponse response = new DefaultHttpClient().execute(request);

                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        DialogNotify("MainActivity - Error sending " + pns + " notification",
                                response.getStatusLine().toString());
                        throw new RuntimeException("Error sending notification");
                    }
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                    return e;
                }

                return null;
            }
        }.execute(null, null, null);
    }
    ```

    `login`Program obsługi przycisku **logowania** generuje podstawowy token uwierzytelniania przy użyciu nazwy użytkownika i hasła wejściowego (reprezentuje dowolny token używany przez schemat uwierzytelniania), a następnie używa `RegisterClient` do wywoływania zaplecza do rejestracji.

    Metoda `sendPush` wywołuje zaplecze, aby wyzwolić bezpieczne powiadomienie do użytkownika w oparciu o tag użytkownika. Docelowa usługa powiadomień platformy dla metody `sendPush` zależy od przekazanego ciągu `pns`.

10. Dodaj następującą metodę `DialogNotify` do klasy `MainActivity`.

    ```java
    protected void DialogNotify(String title, String message)
    {
        AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
        alertDialog.setTitle(title);
        alertDialog.setMessage(message);
        alertDialog.setButton(AlertDialog.BUTTON_NEUTRAL, "OK",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {
                        dialog.dismiss();
                    }
                });
        alertDialog.show();
    }
    ```
11. W swojej klasie `MainActivity` zaktualizuj metodę `sendNotificationButtonOnClick`, aby wywołać metodę `sendPush` przy użyciu wybranych przez użytkownika usług powiadomień platformy, jak pokazano poniżej.

    ```java
    /**
    * Send Notification button click handler. This method sends the push notification
    * message to each platform selected.
    *
    * @param v The view
    */
    public void sendNotificationButtonOnClick(View v)
            throws ClientProtocolException, IOException {

        String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                .getText().toString();
        String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                .getText().toString();

        // JSON String
        nhMessage = "\"" + nhMessage + "\"";

        if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
        {
            sendPush("wns", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonFCM)).isChecked())
        {
            sendPush("fcm", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
        {
            sendPush("apns", nhMessageTag, nhMessage);
        }
    }
    ```
12. W pliku `build.gradle` dodaj następujący wiersz do sekcji `android` po sekcji `buildTypes`.

    ```java
    useLibrary 'org.apache.http.legacy'
    ```
13. Jeśli aplikacja jest ukierunkowana na poziom interfejsu API 28 (Android 9,0) lub nowszego, Dołącz następującą deklarację w obrębie `<application>` elementu `AndroidManifest.xml` .

    ```xml
    <uses-library
        android:name="org.apache.http.legacy"
        android:required="false" />
    ```
14. Skompiluj projekt.

## <a name="test-the-app"></a>Testowanie aplikacji

1. Uruchom aplikację na urządzeniu lub emulatorze przy użyciu programu Android Studio.
2. W aplikacji systemu Android wprowadź nazwę użytkownika i hasło. Oba muszą być taką samą wartością ciągu i nie mogą zawierać spacji ani znaków specjalnych.
3. W aplikacji systemu Android kliknij przycisk **Zaloguj**. Poczekaj na wyskakujący komunikat informujący o zarejestrowaniu **i zarejestrowaniu**. Spowoduje to włączenie przycisku **Send Notification** (Wyślij powiadomienie).

    ![Zrzut ekranu emulatora pokazujący, jak wygląda aplikacja powiadamiania użytkowników o Notification Hubs po zalogowaniu się.][A2]
4. Kliknij przyciski przełączania, aby włączyć wszystkie platformy, na których uruchomiono aplikację i zarejestrowano użytkownika.
5. Wprowadź nazwę użytkownika, który odbierze powiadomienie. Ten użytkownik musi być zarejestrowany na potrzeby otrzymywania powiadomień na urządzeniach docelowych.
6. Wpisz wiadomość dla użytkownika, która zostanie odebrana w formie powiadomienia push.
7. Kliknij przycisk **Send Notification** (Wyślij powiadomienie).  Każde urządzenie, które zostało zarejestrowane przy użyciu pasującego tagu nazwy użytkownika, otrzyma powiadomienie push.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wysyłania powiadomień push do konkretnych użytkowników, którzy mają tagi skojarzone ze swoimi rejestracjami. Aby dowiedzieć się, jak wypychać powiadomienia oparte na lokalizacji, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień na podstawie lokalizacji](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
