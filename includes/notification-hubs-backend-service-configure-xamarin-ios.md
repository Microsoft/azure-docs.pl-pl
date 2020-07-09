---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 1dc491084f65bc90397b0897de6b6cfe4f2fd410
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448705"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Skonfiguruj info. plist i uprawnienia. plist

1. Upewnij się, że zalogowano się na **koncie dewelopera firmy Apple** w preferencjach **programu Visual Studio**  >  **...**  >  **Publikowanie**  >  Pobrano **konta deweloperów firmy Apple** oraz odpowiedni *certyfikat* i *profil aprowizacji* . Te zasoby powinny zostać utworzone w ramach poprzednich kroków.

1. W **PushDemo. iOS**Otwórz pozycję **info. plist** i upewnij się, że **BundleIdentifier** jest zgodna z wartością używaną dla odpowiedniego profilu aprowizacji w [portalu dla deweloperów firmy Apple](https://developer.apple.com). **BundleIdentifier** było w formacie ``com.<organization>.PushDemo`` .

1. W tym samym pliku Ustaw **minimalną wersję systemu** na **13,0**.

    > [!NOTE]
    > Tylko urządzenia z systemem **iOS 13,0 lub nowszym** są obsługiwane na potrzeby tego samouczka, jednak można je zwiększyć, aby obsługiwać urządzenia z starszymi wersjami.

1. Otwórz **Opcje projektu** dla **PushDemo. iOS** (kliknij dwukrotnie projekt).

1. W obszarze **Opcje projektu**w obszarze **Build > podpisywanie pakietu systemu iOS**upewnij się, że konto dewelopera jest zaznaczone w obszarze **zespół**. Upewnij się, że wybrano opcję "automatycznie Zarządzaj podpisem", a certyfikat podpisywania i profil aprowizacji są automatycznie wybierane.

    > [!NOTE]
    > Jeśli *certyfikat podpisywania* i *profil aprowizacji* nie zostały wybrane automatycznie, wybierz pozycję **Ręczne inicjowanie obsługi administracyjnej**, a następnie kliknij pozycję **Opcje podpisywania pakietu**. Upewnij się, że Twój *zespół* został wybrany do **podpisywania tożsamości** i wybrano profil aprowizacji specyficzny dla usługi *PushDemo* **na potrzeby konfiguracji** **debugowania** i **wersji** , co gwarantuje, że dla danej **platformy** jest wybrany **telefon iPhone** .

1. W **PushDemo. iOS**, Otwórz **uprawnienia. plist** i upewnij się, że opcja **Włącz powiadomienia wypychane** jest zaznaczona, gdy jest wyświetlana na karcie **uprawnienia** . Następnie upewnij się, że ustawienie **środowisko APS** jest ustawione na **programowanie** , gdy jest wyświetlana na karcie **Źródło** .

### <a name="handle-push-notifications-for-ios"></a>Obsługa powiadomień wypychanych dla systemu iOS

1. **Kontrolka**  +  **Kliknij** projekt **PushDemo. iOS** , wybierz pozycję **Nowy folder** z menu **Dodaj** , a następnie kliknij pozycję **Dodaj** przy użyciu *usług* jako **nazwę folderu**.

1. **Kontrolka**  +  **Kliknij** folder **usługi** , a następnie wybierz pozycję **nowy plik...** z menu **Dodaj** .

1. Wybierz pozycję **Ogólne**  >  **pusta Klasa**, wprowadź *DeviceInstallationService.cs* jako **nazwę**, a następnie kliknij pozycję **Nowy** Dodaj następującą implementację.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Ta klasa zapewnia unikatowy identyfikator (przy użyciu wartości [UIDevice. IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) ) i ładunku rejestracji centrum powiadomień.

1. Dodaj nowy folder do projektu **PushDemo. iOS** o nazwie *Extensions* , a następnie Dodaj **pustą klasę** do tego folderu o nazwie *NSDataExtensions.cs* z następującą implementacją.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. W programie **AppDelegate.cs**upewnij się, że w górnej części pliku dodano następujące obszary nazw.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Dodaj właściwości prywatne i odpowiednie pola zapasowe, aby przechowywać odwołanie do implementacji **IPushDemoNotificationActionService**, **INotificationRegistrationService**i **IDeviceInstallationService** .

    ```csharp
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
    ```

1. Dodaj metodę **RegisterForRemoteNotifications** , aby zarejestrować ustawienia powiadomień użytkownika, a następnie w przypadku powiadomień zdalnych z usługą **APNs**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Dodaj metodę **CompleteRegistrationAsync** , aby ustawić `IDeviceInstallationService.Token` wartość właściwości. Odśwież rejestrację i w pamięci podręcznej token urządzenia, jeśli został on zaktualizowany od czasu ostatniego przechowywania.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. Dodaj metodę **ProcessNotificationActions** do przetwarzania danych powiadomień **nsdictionary zawierający** i warunkowego wywoływania **NotificationActionService. TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Zastąp metodę **RegisteredForRemoteNotifications** , przekazując argument **DeviceToken** do metody **CompleteRegistrationAsync** .

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Zastąp metodę **ReceivedRemoteNotification** , przekazując argument **UserInfo** do metody **ProcessNotificationActions** .

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Zastąp metodę **FailedToRegisterForRemoteNotifications** , aby zarejestrować błąd.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Jest to bardzo duży symbol zastępczy. Należy zaimplementować odpowiednie rejestrowanie i obsługę błędów dla scenariuszy produkcyjnych.

1. Zaktualizuj metodę **FinishedLaunching** , aby wywołać `Bootstrap.Begin` ją bezpośrednio po wywołaniu do `Forms.Init` przekazania w implementacji platformy **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. W tej samej metodzie warunkowo Żądaj autoryzacji i zarejestruj się na potrzeby powiadomień zdalnych bezpośrednio po `Bootstrap.Begin` .

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Nadal w **FinishedLaunching**, wywołaj **ProcessNotificationActions** natychmiast po wywołaniu metody, `LoadApplication` Jeśli argument **Options** zawiera **UIApplication. LaunchOptionsRemoteNotificationKey** przekazywanie w wyniku obiektu **userInfo** .

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
