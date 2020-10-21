---
title: Wysyłanie powiadomień wypychanych do aplikacji platformy Xamarin. Android przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin Android przy użyciu usługi Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: 51a827b0bc80d7eec5b480d3e5efc49e5e1cc974
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318270"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Samouczek: wysyłanie powiadomień wypychanych do aplikacji platformy Xamarin. Android przy użyciu Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Omówienie

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację dla platformy Xamarin.Android służącą do odbierania powiadomień push przy użyciu usługi Firebase Cloud Messaging (FCM). Za pomocą centrum powiadomień będzie można wysyłać powiadomienia push do wszystkich urządzeń, na których działa ta aplikacja. Gotowy kod jest dostępny w przykładowej aplikacji [NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu Firebase i włączanie usługi Firebase Cloud Messaging
> * Tworzenie centrum powiadomień
> * Tworzenie aplikacji platformy Xamarin.Android i łączenie jej z centrum powiadomień
> * Wysyłanie powiadomień testowych z witryny Azure Portal

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Program Visual Studio z platformą Xamarin] w systemie Windows lub [program Visual Studio dla komputerów Mac] w systemie OS X.
* Aktywne konto Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Tworzenie projektu Firebase i włączanie usługi Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Konfigurowanie ustawień GCM/FCM dla centrum powiadomień

1. Wybierz pozycję **Google (GCM/FCM)/** w sekcji **ustawień** w menu po lewej stronie.
2. Wprowadź **klucz serwera** zanotowany w konsoli usługi Google Firebase.
3. Wybierz pozycję **Zapisz** na pasku narzędzi.

    ![Zrzut ekranu przedstawiający centrum powiadomień w witrynie Azure Portal z opcją Google G C M języka C M i wyróżnioną kolorem czerwonym.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Centrum powiadomień jest skonfigurowane do pracy z usługą FCM i uzyskano parametry połączenia do rejestrowania aplikacji zarówno w celu odbierania, jak i wysyłania powiadomień wypychanych.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Tworzenie aplikacji platformy Xamarin.Android i łączenie jej z centrum powiadomień

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Tworzenie projektu programu Visual Studio i dodawanie pakietów NuGet

> [!NOTE]
> Kroki opisane w tym samouczku dotyczą programu Visual Studio 2017. 

1. W programie Visual Studio otwórz menu **Plik**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. W oknie **Nowy projekt** wykonaj następujące czynności:
    1. Rozwiń węzeł **zainstalowane**, **Visual C#**, a następnie kliknij pozycję **Android**.
    2. Z listy wybierz pozycję **aplikacja dla systemu Android (Xamarin)** .
    3. Wprowadź **nazwę** dla projektu.
    4. Wybierz **lokalizację** dla projektu.
    5. Wybierz **przycisk OK**

        ![Okno dialogowe Nowy projekt](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. W oknie dialogowym **Nowa aplikacja dla systemu Android** wybierz pozycję **pusta aplikacja**, a następnie wybierz **przycisk OK**.

    ![Okno dialogowe Nowy projekt](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. W oknie **Eksploratora rozwiązań** rozwiń węzeł **Właściwości** i kliknij pozycję **AndroidManifest.xml**. Zaktualizuj nazwę pakietu tak, aby pasowała do nazwy pakietu wprowadzonej podczas dodawania usługi Firebase Cloud Messaging do projektu w konsoli Google Firebase Console.

    ![Nazwa pakietu w usłudze GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Ustaw docelową wersję systemu Android dla projektu na **android 9,0 (kołowy)** , wykonując następujące czynności: 
    1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Właściwości**. 
    1. W przypadku **kompilacji przy użyciu systemu Android w wersji: (platforma docelowa)** wybierz pozycję **Android 9,0 (wykres kołowy)**. 
    1. Wybierz opcję **tak** w oknie komunikatu, aby kontynuować Zmienianie platformy docelowej.
1. Dodaj wymagane pakiety NuGet do projektu, wykonując następujące czynności:
    1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet..**..
    1. Przejdź do **zainstalowanej** karty, wybierz pozycję **Xamarin. Android. support. Design**, a następnie wybierz pozycję **Update (Aktualizuj** ) w okienku po prawej stronie, aby zaktualizować pakiet do najnowszej wersji.
    1. Przejdź do karty **przeglądanie** . Wyszukaj aplikację **Xamarin. GooglePlayServices. Base**. Na liście wyników wybierz pozycję **Xamarin.GooglePlayServices.Base**. Następnie wybierz pozycję **Zainstaluj**.

        ![Pakiet NuGet usług Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. W oknie **Menedżera pakietów NuGet** wyszukaj pozycję **Xamarin.Firebase.Messaging**. Na liście wyników wybierz pozycję **Xamarin.Firebase.Messaging**. Następnie wybierz pozycję **Zainstaluj**.
    7. Teraz wyszukaj pozycję **Xamarin.Azure.NotificationHubs.Android**. Na liście wyników wybierz pozycję **Xamarin.Azure.NotificationHubs.Android**. Następnie wybierz pozycję **Zainstaluj**.

### <a name="add-the-google-services-json-file"></a>Dodawanie pliku JSON usług Google

1. Skopiuj plik `google-services.json` pobrany z konsoli Google Firebase Console do folderu projektu.
2. Dodaj plik `google-services.json` do projektu.
3. Wybierz plik `google-services.json` w oknie **Eksploratora rozwiązań**.
4. W okienku **Właściwości** ustaw **GoogleServicesJson** jako akcję kompilacji. Jeśli nie widzisz opcji **GoogleServicesJson**, zamknij program Visual Studio, uruchom go ponownie, otwórz projekt i spróbuj ponownie.

    ![Akcja kompilacji GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Konfigurowanie centrów powiadomień w projekcie

#### <a name="registering-with-firebase-cloud-messaging"></a>Rejestrowanie w usłudze Firebase Cloud Messaging

1. Otwórz plik `AndroidManifest.xml` i wstaw następujące elementy `<receiver>` do elementu `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Dodaj następujące instrukcje **przed elementem aplikacji** .

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Zbierz następujące informacje dotyczące aplikacji dla systemu Android i centrum powiadomień:

   * **Listen connection string (Parametry połączenia nasłuchiwania)**: na pulpicie nawigacyjnym w [witrynie Azure Portal] kliknij pozycję **Wyświetl parametry połączeń**. Skopiuj parametry połączenia `DefaultListenSharedAccessSignature` dla tej wartości.
   * **Nazwa centrum**: nazwa centrum z witryny [Azure Portal]. Na przykład *moje_centrum_powiadomien_2*.
4. W oknie **Eksploratora rozwiązań** kliknij prawym przyciskiem myszy **projekt**, wskaż pozycję **Dodaj** i wybierz pozycję **Klasa**.
5. Utwórz klasę `Constants.cs` w projekcie Xamarin i zdefiniuj w tej klasie następujące wartości stałych. Zastąp tekst zastępczy własnymi wartościami.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Dodaj następujące instrukcje using w pliku `MainActivity.cs`:

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. Dodaj następujące właściwości do klasy MAINS. Zmienna TAG zostanie użyta do wyświetlenia okna dialogowego alertu, gdy aplikacja jest uruchomiona:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. Dodaj następującą metodę do klasy MAINS. Sprawdza on, czy **usługi Google Play** są dostępne na urządzeniu.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. Dodaj następującą metodę do klasy MAINS, która tworzy kanał powiadomień.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. W pliku `MainActivity.cs` dodaj następujący kod do elementu `OnCreate` po elemencie `base.OnCreate(savedInstanceState)`:

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. Dodaj klasę o nazwie `MyFirebaseMessagingService` do projektu. 
16. Dodaj następujące instrukcje using w pliku `MyFirebaseMessagingService.cs`.

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. Dodaj następujący kod nad deklaracją klasy i spraw, aby klasa dziedziczyła z klasy `FirebaseMessagingService`:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. Dodaj następujący kod wewnątrz `MyFirebaseMessagingService` klasy, aby przetworzyć odbierane komunikaty. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. Dodaj następujące metody do klasy MyFirebaseMessagingService (bezpośrednio poniżej kodu dodanego w poprzednim kroku), aby otrzymać token rejestracji FCM i wysłać go do wystąpienia Notification Hubs (centrum). 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. **Skompiluj** projekt.
1. **Uruchom** aplikację na urządzeniu lub załadowanym emulatorze

## <a name="send-test-notification-from-the-azure-portal"></a>Wysyłanie powiadomienia testowego z witryny Azure Portal

Odbieranie powiadomień w aplikacji możesz przetestować za pomocą opcji **Wysyłanie testowe** w witrynie [Azure Portal]. Powoduje to wysłanie testowego powiadomienia push na urządzenie.

![Witryna Azure Portal — wysyłanie testowe](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Powiadomienia wypychane są zwykle wysyłane w usłudze zaplecza, takiej jak Mobile Services czy ASP.NET, za pośrednictwem zgodnej biblioteki. Jeśli biblioteka nie jest dostępna dla zaplecza, można także użyć interfejsu API REST bezpośrednio do wysyłania komunikatów powiadomień.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano wyemitowane powiadomienia do wszystkich urządzeń z systemem Android zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem Android, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych urządzeń](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio z programem Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio dla komputerów Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Witryna Azure Portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android