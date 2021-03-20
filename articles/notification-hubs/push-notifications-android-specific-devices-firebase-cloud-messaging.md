---
title: Wysyłanie powiadomień wypychanych do określonych urządzeń przy użyciu usługi Azure Notification Hubs i Google Firebase Cloud Messaging | Microsoft Docs
description: Dowiedz się, jak za pomocą usługi Notification Hubs wysyłać powiadomienia wypychane do określonych urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging (FCM).
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 04/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: be83471681948cbb9fe85f0d135343ab73bd72bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92315084"
---
# <a name="tutorial-send-notifications-to-specific-devices-using-notification-hubs-and-google-firebase-cloud-messaging"></a>Samouczek: wysyłanie powiadomień do określonych urządzeń przy użyciu usług Notification Hubs i Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Omówienie

Korzystając z tego samouczka, dowiesz się, jak rozgłaszać powiadomienia z najważniejszymi informacjami do aplikacji systemu Android przy użyciu usługi Azure Notification Hubs. Po zakończeniu pracy z samouczkiem będziesz wiedzieć, jak rejestrować kategorie ważnych wiadomości, które Cię interesują, oraz otrzymywać tylko powiadomienia push dla tych kategorii. Ten scenariusz jest typowym wzorcem dla wielu aplikacji wymagających wysyłania powiadomień do grup użytkowników, które wcześniej zadeklarowały zainteresowanie nimi, na przykład czytników danych RSS, aplikacji dla fanów muzyki itp.

Scenariusze rozgłaszania są włączone w przypadku co najmniej jednego *tagu* podczas tworzenia rejestracji w centrum powiadomień. W przypadku wysłania powiadomień do tagu wszystkie urządzenia zarejestrowane dla tego tagu otrzymają powiadomienie. Tagi to po prostu ciągi, dlatego nie muszą być aprowizowane z wyprzedzeniem. Aby uzyskać więcej informacji na temat tagów, zobacz [Notification Hubs Routing and Tag Expressions (Wyrażenia routingu i tagów w usłudze Notification Hubs)](notification-hubs-tags-segment-push-message.md).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Dodawanie wyboru kategorii do aplikacji mobilnej.
> * Rejestrowanie w celu otrzymywania powiadomień z tagami.
> * Wysyłanie powiadomień z tagami.
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono aplikację utworzoną w [samouczku: powiadomienia wypychane do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs i obsługi komunikatów w chmurze Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md). Przed rozpoczęciem tego samouczka Ukończ [Samouczek: powiadomienia wypychane do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs i obsługi komunikatów w chmurze Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="add-category-selection-to-the-app"></a>Dodawanie wyboru kategorii do aplikacji

Pierwszym krokiem jest dodanie elementów interfejsu użytkownika do istniejącej strony głównej, co umożliwi użytkownikowi wybranie kategorii do zarejestrowania. Kategorie wybrane przez użytkownika są przechowywane na urządzeniu. Po uruchomieniu aplikacji w centrum powiadomień zostanie utworzona rejestracja urządzenia z wybranymi kategoriami w formie tagów.

1. Otwórz plik `res/layout/activity_main.xml file` i zastąp jego zawartość następującą zawartością:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Otwórz plik `res/values/strings.xml` i dodaj następujące wiersze:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    Układ graficzny pliku `main_activity.xml` powinien wyglądać mniej więcej tak:

    ![Zrzut ekranu przedstawiający emulator przedstawiający, jak będzie wyglądać główny układ graficzny X M L.][A1]
3. Utwórz klasę `Notifications` w tym samym pakiecie co klasa `MainActivity`.

    ```java
    import java.util.HashSet;
    import java.util.Set;
    import java.util.concurrent.TimeUnit;
    
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    
    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private FirebaseInstanceId fcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;
        public static String FCM_token = "";
        private static final String TAG = "Notifications";
    
        public Notifications(Context context, String hubName, String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;
    
            fcm = FirebaseInstanceId.getInstance();
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }
    
        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }
    
        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }
    
        public void subscribeToCategories(final Set<String> categories) {
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
    
                        String templateBodyFCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    
                        hub.registerTemplate(FCM_token,"simpleFCMTemplate", templateBodyFCM,
                                categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
    
                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }
    
    }
    ```

    Ta klasa używa magazynu lokalnego do przechowywania kategorii wiadomości, które mają być odbierane na tym urządzeniu. Zawiera również metody rejestracji w tych kategoriach.
4. W `MainActivity` klasie Dodaj pole dla `Notifications` :

    ```java
    private Notifications notifications;
    ```
5. Następnie zaktualizuj metodę, `onCreate` jak pokazano w poniższym kodzie. Rejestrujesz się w Notification Hubs w metodzie **subscribeToCategories** klasy **Notifications** . 

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;

        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
        notifications = new Notifications(this, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);
        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Upewnij się, że nazwa centrum i parametry połączenia są poprawnie ustawione w klasie NotificationSettings.

    > [!NOTE]
    > Ponieważ poświadczenia dystrybuowane przy użyciu aplikacji klienckiej nie są zazwyczaj bezpieczne, należy przy użyciu aplikacji klienckiej dystrybuować wyłącznie klucz dostępu do nasłuchiwania. Dostęp do nasłuchiwania umożliwia aplikacji rejestrowanie powiadomień, ale nie może ona modyfikować istniejących rejestracji ani wysyłać powiadomień. Klucz pełnego dostępu jest używany w zabezpieczonej usłudze zaplecza do wysyłania powiadomień oraz zmiany istniejących rejestracji.

6. Następnie dodaj następujące instrukcje importu:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    import android.view.View;
    ```
7. Dodaj następującą metodę `subscribe` w celu obsługi zdarzenia kliknięcia przycisku subskrybowania:

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    Ta metoda tworzy listę kategorii i używa klasy `Notifications` do przechowywania listy w magazynie lokalnym oraz rejestrowania odpowiednich tagów w centrum powiadomień. Jeśli kategorie zostaną zmienione, rejestracja zostanie ponownie utworzona przy użyciu nowych kategorii.

Aplikacja może teraz przechowywać zestaw kategorii w magazynie lokalnym na urządzeniu i dokonywać rejestracji przy użyciu centrum powiadomień za każdym razem, gdy użytkownik zmieni wybór kategorii.

## <a name="register-for-notifications"></a>Rejestrowanie do otrzymywania powiadomień

Te kroki umożliwiają zarejestrowanie przy użyciu centrum powiadomień przy uruchamianiu przy użyciu kategorii przechowywanych w magazynie lokalnym.

1. Upewnij się, że następujący kod znajduje się na końcu `onCreate` metody w `MainActivity` klasie:

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Ten kod gwarantuje, że podczas każdego uruchomienia aplikacja pobiera kategorie z magazynu lokalnego oraz żąda rejestracji dla tych kategorii.
2. Następnie zaktualizuj metodę `onStart()` klasy `MainActivity` w następujący sposób:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    Ten kod aktualizuje stronę główną w oparciu o stan poprzednio zapisanych kategorii.

Aplikacja jest teraz zakończona i może przechowywać w magazynie lokalnym na urządzeniu zestaw kategorii używanych do rejestracji w centrum powiadomień za każdym razem, gdy użytkownik zmieni wybór kategorii. Następnie zdefiniuj zaplecze, które może wysyłać powiadomienia dla kategorii do tej aplikacji.

## <a name="send-tagged-notifications"></a>Wysyłanie powiadomień z tagami

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testowanie aplikacji

1. W programie Android Studio uruchom aplikację w urządzeniu z systemem Android lub emulatorze systemu Android. Interfejs użytkownika aplikacji udostępnia zestaw przełączników pozwalających wybrać kategorie do zasubskrybowania.
2. Włącz co najmniej jeden przełącznik kategorii, a następnie kliknij przycisk **Subscribe** (Subskrybuj). Aplikacja konwertuje wybrane kategorie na tagi i żąda nowej rejestracji urządzenia dla wybranych tagów z centrum powiadomień. Zarejestrowane kategorie są zwracane i wyświetlane w wyskakującym powiadomieniu.

    ![Subskrybowanie kategorii](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Uruchom aplikację konsolową .NET, która wysyła powiadomienia dla każdej kategorii. Powiadomienia dla wybranych kategorii będą wyświetlane jako powiadomienia wyskakujące.

    ![Powiadomienia dotyczące wiadomości technologicznych](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano rozgłoszone powiadomienia do określonych urządzeń z systemem Android zarejestrowanych dla kategorii. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych użytkowników, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych użytkowników](push-notifications-android-specific-users-firebase-cloud-messaging.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)