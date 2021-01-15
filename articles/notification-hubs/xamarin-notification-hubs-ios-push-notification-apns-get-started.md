---
title: Wysyłanie powiadomień wypychanych do platformy Xamarin przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do aplikacji platformy Xamarin.iOS przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
keywords: powiadomienia wypychane w systemie ios, wiadomości wypychane, powiadomienia wypychane, wiadomość wypychana
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: ff1e5edad05ebd7157f71ad2e099ea88905be4f3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221140"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień wypychanych do aplikacji platformy Xamarin. iOS przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Omówienie

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację platformy Xamarin. iOS, która odbiera powiadomienia wypychane przy użyciu [usługi Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Po zakończeniu będzie można za pomocą centrum powiadomień wysyłać powiadomienia push do wszystkich urządzeń, na których działa ta aplikacja. Gotowy kod jest dostępny w przykładowej aplikacji [NotificationHubs][GitHub].

W tym samouczku utworzysz/zaktualizujesz kod, aby wykonać następujące zadania:

> [!div class="checklist"]
> * Generowanie pliku żądania podpisania certyfikatu
> * Rejestrowanie aplikacji dla usługi powiadomień wypychanych
> * Tworzenie profilu inicjowania obsługi dla aplikacji
> * Konfigurowanie centrum powiadomień dla powiadomień push systemu iOS
> * Wysyłanie testowych powiadomień wypychanych

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Najnowsza wersja środowiska [Xcode][Install Xcode]
* Urządzenie zgodne z systemem iOS 10 (lub nowszą wersją)
* Członkostwo w [programie dla deweloperów firmy Apple](https://developer.apple.com/programs/).
* [Visual Studio dla komputerów Mac]
  
  > [!NOTE]
  > Ze względu na wymagania dotyczące konfiguracji powiadomień wypychanych w systemie iOS należy wdrożyć i przetestować aplikację przykładową na fizycznym urządzeniu z systemem iOS (telefonie iPhone lub tablecie iPad), a nie w symulatorze.

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usług Notification Hubs dotyczących aplikacji platformy Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Łączenie aplikacji z centrum powiadomień

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. W programie Visual Studio utwórz nowy projekt dla systemu iOS i wybierz szablon **Single View Application** (Aplikacja z jednym widokiem), a następnie kliknij przycisk **Next** (Dalej).

     ![Visual Studio — wybieranie typu aplikacji][31]

2. Wprowadź nazwę aplikacji i identyfikator organizacji, kliknij przycisk **Dalej**, a następnie pozycję **Utwórz**

3. W widoku rozwiązania kliknij dwukrotnie plik *Info.plist* i w obszarze **Tożsamość** upewnij się, że identyfikator pakietu jest zgodny z użytym podczas tworzenia profilu aprowizacji. W obszarze **Podpisywanie** upewnij się, że konto dewelopera jest wybrane w obszarze **Zespół**, opcja „Automatically manage signing” (Automatycznie zarządzaj podpisywaniem) jest zaznaczona, a Twój certyfikat podpisywania i profil aprowizacji zostały automatycznie wybrane.

    ![Visual Studio — konfiguracja aplikacji systemu iOS][32]

4. W widoku rozwiązania kliknij dwukrotnie przycisk i upewnij się, że włączono opcję `Entitlements.plist` **Włącz powiadomienia wypychane** .

    ![Visual Studio — konfigurowanie uprawnień systemu iOS][33]

5. Dodaj pakiet składnika Azure Messaging. W widoku rozwiązania kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj**  >  **Dodaj pakiety NuGet**. Wyszukaj pakiet **Xamarin.Azure.NotificationHubs.iOS** i dodaj go do projektu.

6. Dodaj nowy plik do klasy, nadaj mu nazwę `Constants.cs`, dodaj następujące zmienne i zastąp symbole zastępcze literału ciągu przy użyciu zanotowanych wcześniej wartości `hubname` i `DefaultListenSharedAccessSignature`.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. W pliku `AppDelegate.cs` dodaj następującą instrukcję using:

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    using UserNotifications
    ```

8. Utwórz implementację `MSNotificationHubDelegate` w `AppDelegate.cs` :

    ```csharp
    public class AzureNotificationHubListener : MSNotificationHubDelegate
    {
        public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
        {

        }
    }
    ```

9. W pliku `AppDelegate.cs` zaktualizuj element `FinishedLaunching()`, aby był zgodny z następującym kodem:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // Set the Message listener
        MSNotificationHub.SetDelegate(new AzureNotificationHubListener());
        
        // Start the SDK
        MSNotificationHub.Start(ListenConnectionString, NotificationHubName);

        return true;
    }
    ```

10. W programie `AppDelegate.cs` Zaimplementuj `DidReceivePushNotification` metodę dla `AzureNotificationHubListener` klasy:

    ```csharp
    public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
    {
        // This sample assumes { aps: { alert: { title: "Hello", body: "World" } } }
        var alertTitle = message.Title ?? "Notification";
        var alertBody = message.Body;

        var myAlert = UIAlertController.Create(alertTitle, alertBody, UIAlertControllerStyle.Alert);
        myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
    }
    ```

11. Uruchom aplikację na urządzeniu.

## <a name="send-test-push-notifications"></a>Wysyłanie testowych powiadomień wypychanych

Odbieranie powiadomień w aplikacji możesz przetestować za pomocą opcji *Wysyłanie testowe* w witrynie [Azure Portal]. Powoduje to wysłanie testowego powiadomienia push na urządzenie.

![Witryna Azure Portal — wysyłanie testowe][30]

Powiadomienia wypychane są zwykle wysyłane za pośrednictwem usługi wewnętrznej bazy danych, takiej jak Mobile Apps czy ASP.NET, przy użyciu zgodnej biblioteki. Jeśli biblioteka nie jest dostępna w danym zapleczu, powiadomienia można również wysyłać bezpośrednio za pomocą interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano wyemitowane powiadomienia do wszystkich urządzeń z systemem iOS zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem iOS, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych urządzeń](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio dla komputerów Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Witryna Azure Portal]: https://portal.azure.com
