---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: de961aa36d690cf03e42707758bc70e5495f692e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448750"
---
### <a name="validate-package-name-and-permissions"></a>Weryfikuj nazwę i uprawnienia pakietu

1. W **PushDemo. Android**Otwórz **Opcje projektu** , a następnie **aplikację dla systemu Android** z sekcji **kompilacja** .

1. Sprawdź, czy **Nazwa pakietu** jest zgodna z wartością użytą w projekcie [Firebase konsoli](https://console.firebase.google.com) **PushDemo** . **Nazwa pakietu** jest w formacie ``com.<organization>.pushdemo`` .

1. Ustaw **minimalną wersję systemu Android** na **Android 8,0 (poziom interfejsu API 26)** i **docelową wersję systemu Android** do najnowszego **poziomu interfejsu API**.

    > [!NOTE]
    > Na potrzeby tego samouczka są obsługiwane tylko te urządzenia, na których jest uruchomiony **poziom interfejsu API w wersji 26 lub nowszej** . można jednak go zwiększyć, aby obsługiwać urządzenia z starszymi wersjami.

1. Upewnij się, że uprawnienia **Internetu** i **READ_PHONE_STATE** są włączone w obszarze **wymagane uprawnienia**.

1. Kliknij przycisk **OK** .

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Dodawanie pakietów Xamarin Usługi Google Play Base i Xamarin. Firebase. Messaging

1. W **PushDemo. Android**, **Control**  +  **kliknij przycisk** w folderze **Packages** , a następnie wybierz **Zarządzaj pakietami NuGet..**..

1. Wyszukaj ciąg **Xamarin. GooglePlayServices. Base** (nie **Basement**) i upewnij się, że jest on sprawdzony.

1. Wyszukaj element **Xamarin. Firebase. Messaging** i upewnij się, że jest on sprawdzony.

1. Kliknij przycisk **Dodaj pakiety**, a następnie kliknij przycisk **Akceptuj** po wyświetleniu monitu, aby zaakceptować **postanowienia licencyjne**.

### <a name="add-the-google-services-json-file"></a>Dodaj plik JSON usług Google Services

1. **Kontrolka**  +  **Kliknij** `PushDemo.Android` projekt, a następnie wybierz **istniejący plik...** z menu **Dodaj** .

1. Wybierz *google-services.js* pobrany wcześniej plik podczas konfigurowania projektu **PushDemo** w [konsoli Firebase](https://console.firebase.google.com) , a następnie kliknij przycisk **Otwórz**.

1. Po wyświetleniu monitu wybierz opcję **kopiowania pliku do katalogu**.

1. **Kontrolka**  +  **Kliknij** *google-services.jsw* pliku z `PushDemo.Android` projektu, a następnie upewnij się, że **GoogleServicesJson** jest ustawiony jako **Akcja kompilacji**.

### <a name="handle-push-notifications-for-android"></a>Obsługa powiadomień wypychanych dla systemu Android

1. **Kontrolka**  +  **Kliknij** `PushDemo.Android` projekt, wybierz pozycję **Nowy folder** z menu **Dodaj** , a następnie kliknij pozycję **Dodaj** przy użyciu *usług* jako **nazwę folderu**.

1. **Kontrolka**  +  **Kliknij** folder **usługi** , a następnie wybierz pozycję **nowy plik...** z menu **Dodaj** .

1. Wybierz pozycję **Ogólne**  >  **pusta Klasa**, wprowadź *DeviceInstallationService.cs* jako **nazwę**, a następnie kliknij pozycję **Nowy** Dodaj następującą implementację.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for FCM");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Ta klasa udostępnia unikatowy identyfikator (przy użyciu [bezpiecznego. AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) w ramach ładunku rejestracji centrum powiadomień.

1. Dodaj kolejną **pustą klasę** do folderu **Services** o nazwie *PushNotificationFirebaseMessagingService.cs*, a następnie Dodaj następującą implementację.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. W programie **MainActivity.cs**upewnij się, że w górnej części pliku dodano następujące obszary nazw.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. W **MainActivity.cs**ustaw wartość **SingleTopmode** **, aby nie** była **ponownie** tworzona w momencie otwarcia.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Dodaj prywatne właściwości i odpowiadające im pola zapasowe, aby przechowywać odwołanie do implementacji **IPushNotificationActionService** i **IDeviceInstallationService** .

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Zaimplementuj Interfejs **IOnSuccessListener** , aby pobrać i zapisać token **Firebase** .

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Dodaj nową metodę o nazwie **ProcessNotificationActions** , która sprawdzi, czy dany **cel** ma dodatkową wartość o nazwie *Action*. Warunkowego wyzwalania tej akcji przy użyciu implementacji **IPushDemoNotificationActionService** .

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Zastąp metodę **OnNewIntent** , aby wywołać metodę **ProcessNotificationActions** .

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Ponieważ wartość parametru **launchmode** dla **działania** jest ustawiona na **SingleTop**, **cel** zostanie wysłany do istniejącego wystąpienia **działania** za pośrednictwem metody **OnNewIntent** zamiast metody **OnCreate** i dlatego musi obsługiwać przychodzące zamiar w metodach **OnCreate** i **OnNewIntent** .

1. Zaktualizuj metodę **OnCreate** , aby wywołać `Bootstrap.Begin` ją bezpośrednio po wywołaniu metody `base.OnCreate` **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. W tej samej metodzie warunkowo Wywołaj metodę **getinstanceid** w wystąpieniu **FirebaseApp** , bezpośrednio po wywołaniu metody `Bootstrap.Begin` , dodając wartość **Main** jako **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Nadal w trybie **OnCreate**Wywołaj **ProcessNotificationActions** natychmiast po wywołaniu metody Pass `LoadApplication` w bieżącym **zamiarie**.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Należy ponownie zarejestrować aplikację przy każdym uruchomieniu i zatrzymać ją z sesji debugowania, aby kontynuować otrzymywanie powiadomień wypychanych.
