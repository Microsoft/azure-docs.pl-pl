---
title: Wysyłanie powiadomień wypychanych do aplikacji Windows Phone przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Silverlight dla systemu Windows Phone 8 lub Windows Phone 8.1 przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: windows
keywords: powiadomienie wypychane, powiadomienia wypychane, wypychanie w systemie windows phone
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e91d250b8cc9b80f2c97910c7fa972af32fa9104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998343"
---
# <a name="tutorial-send-push-notifications-to-windows-phone-apps-using-notification-hubs"></a>Samouczek: wysyłanie powiadomień wypychanych do aplikacji Windows Phone przy użyciu Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do aplikacji platformy Silverlight dla systemu Windows Phone 8 lub Windows Phone 8.1 przy użyciu usługi Azure Notification Hubs. Jeśli aplikacja ma być przeznaczona dla systemu Windows Phone 8.1 (bez platformy Silverlight), dodatkowe informacje zawiera wersja dotycząca [aplikacji uniwersalnych systemu Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tego samouczka.

Korzystając z tego samouczka, utworzysz pustą aplikację dla systemu Windows Phone 8, która odbiera powiadomienia wypychane przy użyciu usługi powiadomień wypychanych firmy Microsoft (MPNS, Microsoft Push Notification Service). Po utworzeniu aplikacji będzie można za pomocą centrum powiadomień wysyłać powiadomienia push do wszystkich urządzeń z tą aplikacją.

> [!NOTE]
> Zestaw SDK usługi Notification Hubs dla systemu Windows Phone nie obsługuje używania usługi WNS (Windows Push Notification Service) z aplikacjami platformy Silverlight dla systemu Windows Phone 8.1. Aby używać usługi WNS (zamiast usługi MPNS) z aplikacjami platformy Silverlight dla systemu Windows Phone 8.1, postępuj zgodnie z instrukcjami w [samouczku dotyczącym usługi Notification Hubs dla aplikacji platformy Silverlight dla systemu Windows Phone], w którym zastosowano interfejsy API REST.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie centrum powiadomień
> * Tworzenie aplikacji systemu Windows Phone
> * Wysyłanie testowe powiadomienia

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Visual Studio 2015 Express ze składnikami programowania aplikacji mobilnych](https://www.visualstudio.com/vs/older-downloads/)

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Notification Hubs dotyczących aplikacji dla systemu Windows Phone 8.

## <a name="create-your-notification-hub"></a>Tworzenie centrum powiadomień

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Konfigurowanie ustawień systemu Windows Phone (MPNS)

1. Wybierz pozycję **Windows Phone (MPNS)** w obszarze **USTAWIENIA POWIADOMIEŃ**.
2. Wybierz pozycję **Włącz wypychanie uwierzytelniania**.
3. Wybierz pozycję **Zapisz** na pasku narzędzi.

    ![Witryna Azure Portal — włączanie nieuwierzytelnionych powiadomień push](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    Centrum zostało utworzone i jest skonfigurowane do wysyłania nieuwierzytelnionych powiadomień do urządzeń z systemem Windows Phone.

    > [!NOTE]
    > W tym samouczku używana jest usługa MPNS w trybie nieuwierzytelnionym. W trybie nieuwierzytelnionym usługi MPNS występują ograniczenia dotyczące powiadomień, które można wysyłać do poszczególnych kanałów. Usługa Notification Hubs obsługuje [tryb uwierzytelniony usługi MPNS](/previous-versions/windows/apps/ff941099(v=vs.105)), umożliwiając przekazanie certyfikatu.

## <a name="create-a-windows-phone-application"></a>Tworzenie aplikacji systemu Windows Phone

W tej sekcji utworzysz aplikację systemu Windows Phone, która będzie rejestrować się w centrum powiadomień.

1. W programie Visual Studio utwórz nową aplikację dla systemu Windows Phone 8.

    ![Visual Studio — Nowy projekt — Aplikacja dla systemu Windows Phone][13]

    W programie Visual Studio 2013 Update 2 lub nowszym zamiast tego utwórz aplikację platformy Silverlight dla systemu Windows Phone.

    ![Visual Studio — Nowy projekt — Pusta aplikacja — Windows Phone Silverlight][11]
2. W programie Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij pozycję **Zarządzaj pakietami NuGet**.
3. Wyszukaj pakiet `WindowsAzure.Messaging.Managed` i kliknij pozycję **Zainstaluj**, a następnie zaakceptuj warunki użytkowania.

    ![Visual Studio — menedżer pakietów NuGet][20]
4. Otwórz plik App.xaml.cs i dodaj następujące instrukcje `using`:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    ```

5. Dodaj następujący kod na początku metody `Application_Launching` w pliku `App.xaml.cs`:

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
    }
    ```

   > [!NOTE]
   > wartość `MyPushChannel` jest indeksem służącym do wyszukiwania istniejącego kanału w kolekcji [HttpNotificationChannel](/previous-versions/ff402781(v=vs.110)). Jeśli nie istnieje, utwórz nowy wpis o tej nazwie.

    Wstaw nazwę centrum i parametry połączenia o nazwie `DefaultListenSharedAccessSignature` zanotowane w poprzedniej sekcji.
    Ten kod pobiera identyfikator URI kanału dla aplikacji z usługi MPNS, a następnie rejestruje ten identyfikator URI kanału w centrum powiadomień. Gwarantuje również to, że identyfikator URI kanału jest rejestrowany w centrum powiadomień przy każdym uruchomieniu aplikacji.

   > [!NOTE]
   > W tym samouczku do urządzenia wysyłane jest wyskakujące powiadomienie. W przypadku wysyłania powiadomienia na kafelku należy zamiast tego wywołać metodę `BindToShellTile` na kanale. W celu obsługi wyskakujących powiadomień oraz powiadomień na kafelku należy wywołać metodę `BindToShellTile` i `BindToShellToast`.

6. W Eksploratorze rozwiązań rozwiń węzeł **Właściwości**, otwórz plik `WMAppManifest.xml`, kliknij kartę **Możliwości** i upewnij się, że możliwość **ID_CAP_PUSH_NOTIFICATION** jest zaznaczona. Aplikacja może teraz odbierać powiadomienia push.

    ![Visual Studio — możliwości aplikacji dla systemu Windows Phone][14]
7. Naciśnij klawisz `F5`, aby uruchomić aplikację. W aplikacji zostanie wyświetlony komunikat dotyczący rejestracji.
8. Zamknij aplikację lub przejdź do strony głównej.

   > [!NOTE]
   > Aby otrzymywać wyskakujące powiadomienia wypychane, aplikacja nie może być uruchomiona na pierwszym planie.

## <a name="test-send-a-notification"></a>Wysyłanie testowe powiadomienia

1. W witrynie Azure Portal przełącz się na kartę Przegląd.
2. Wybierz opcję **Wysyłanie testowe**.

    ![Przycisk Wysyłanie testowe](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. W oknie **Wysyłanie testowe** wykonaj następujące kroki:

    1. W obszarze **Platformy** wybierz pozycję **Windows Phone**.
    2. W obszarze **Typ powiadomienia** wybierz pozycję **Wyskakujące**.
    3. Wybierz pozycję **Wyślij**
    4. Zobacz **wynik** na liście w dolnej części okna.

        ![Okno wysyłania testowego](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. W emulatorze systemu Windows Phone lub na telefonie z systemem Windows Phone upewnij się, że widzisz komunikat z powiadomieniem.

    ![Powiadomienie w systemie Windows Phone](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Następne kroki

W tym prostym przykładzie wysłano powiadomienia wypychane do wszystkich urządzeń z systemem Windows Phone 8. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych urządzeń](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[MPNS authenticated mode]: /previous-versions/windows/apps/ff941099(v=vs.105)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: /previous-versions/windows/apps/jj662938(v=vs.105)
[tile catalog]: /previous-versions/windows/apps/hh202948(v=vs.105)
[samouczku dotyczącym usługi Notification Hubs dla aplikacji platformy Silverlight dla systemu Windows Phone]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari
