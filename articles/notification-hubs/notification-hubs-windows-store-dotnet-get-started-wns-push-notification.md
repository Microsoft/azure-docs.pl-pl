---
title: Wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Użyj usługi Azure Notification Hubs, aby wypchnąć powiadomienia wypychane do aplikacji platformy uniwersalnej systemu Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: 'mvc, ms.custom: devx-track-csharp'
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 4f55b6eafe230f722979d535111ce45aa35981f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93125041"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień do platforma uniwersalna systemu Windows aplikacji przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Korzystając z tego samouczka, utworzysz centrum powiadomień, aby wysyłać powiadomienia push do aplikacji platformy uniwersalnej systemu Windows (UWP, Universal Windows Platform). Utworzysz pustą aplikację dla Sklepu Windows, która odbiera powiadomienia push przy użyciu usługi powiadomień push systemu Windows (WNS). Następnie możesz wysyłać powiadomienia wypychane do wszystkich urządzeń z uruchomioną aplikacją przy użyciu Centrum powiadomień.

> [!NOTE]
> Kompletny kod dla tego samouczka można znaleźć [w witrynie GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Wykonaj następujące czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji w Sklepie Windows
> * Tworzenie centrum powiadomień
> * Tworzenie przykładowej aplikacji systemu Windows
> * Wysyłanie powiadomień testowych

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Microsoft Visual Studio 2017 lub nowszy. Przykład w tym samouczku używa [programu Visual Studio 2019](https://www.visualstudio.com/products).
- [Zainstalowane narzędzia do programowania aplikacji platformy UWP](/windows/uwp/get-started/get-set-up)
- Aktywne konto Sklepu Windows
- Upewnij się, że opcja **Pobierz powiadomienia z aplikacji i innych nadawców** jest włączona. 
    - Uruchom okno **Ustawienia** na komputerze.
    - Wybierz kafelek **system** .
    - Wybierz pozycję **powiadomienia & akcje** z menu po lewej stronie. 
    - Upewnij się, że ustawienie **Pobierz powiadomienia z aplikacji i innych nadawców** jest włączone. Jeśli nie jest włączona, włącz ją.

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Notification Hubs dotyczących aplikacji platformy UWP.

## <a name="create-an-app-in-windows-store"></a>Tworzenie aplikacji w Sklepie Windows

Aby wysyłać powiadomienia wypychane do aplikacji platformy UWP, skojarz aplikację ze Sklepem Windows. Następnie skonfiguruj integrację centrum powiadomień z usługą WNS.

1. Przejdź do [Centrum deweloperów systemu Windows](https://partner.microsoft.com/dashboard/windows/first-run-experience), zaloguj się przy użyciu konta Microsoft, a następnie wybierz pozycję **Utwórz nową aplikację**.

    ![Przycisk Nowa aplikacja](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Wpisz nazwę aplikacji i wybierz pozycję **Rezerwuj nazwę produktu**. Spowoduje to utworzenie nowej rejestracji aplikacji w Sklepie Windows.

    ![Nazwa aplikacji w Sklepie](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Rozwiń węzeł **Zarządzanie produktem**, wybierz pozycję **WNS/usługi MPNS**, a następnie wybierz pozycję **usługi Live Services**. Zaloguj się na konto Microsoft. Strona Rejestracja aplikacji zostanie otwarta na nowej karcie. Alternatywnie możesz przejść bezpośrednio do strony [Moje aplikacje](https://apps.dev.microsoft.com) i wybrać nazwę aplikacji, aby uzyskać dostęp do tej strony.

    ![Strona usługi WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Zanotuj hasło **tajne aplikacji** oraz **Identyfikator zabezpieczeń (SID) pakietu** i **tożsamość aplikacji** w sekcji Sklep Windows.

    >[!WARNING]
    >Klucz tajny aplikacji i identyfikator SID pakietu są ważnymi poświadczeniami zabezpieczeń. Nie udostępniaj nikomu tych wartości ani nie rozpowszechniaj ich razem z aplikacją.

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Konfigurowanie ustawień usługi WNS na potrzeby centrum

1. W kategorii **USTAWIENIA POWIADOMIEŃ** wybierz pozycję **Windows (WNS)**.
2. Wprowadź wartości w pozycjach **Identyfikator SID pakietu** oraz **Wpis tajny**, które zostały zanotowane w poprzedniej sekcji.
3. Kliknij przycisk **Zapisz** na pasku narzędzi.

    ![Pola Identyfikator SID pakietu i Klucz zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Twoje centrum powiadomień jest teraz skonfigurowane do pracy z usługą WNS. Masz parametry połączenia potrzebne do zarejestrowania aplikacji i wysyłania powiadomień.

## <a name="create-a-sample-windows-app"></a>Tworzenie przykładowej aplikacji systemu Windows

1. W programie Visual Studio otwórz menu **Plik**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**.
2. W oknie dialogowym **Tworzenie nowego projektu** wykonaj następujące czynności:

    1. W polu wyszukiwania u góry wpisz **uniwersalne systemu Windows**.
    2. W wynikach wyszukiwania wybierz pozycję **pusta aplikacja (platforma uniwersalna systemu Windows)**, a następnie wybierz przycisk **dalej**.

       ![Okno dialogowe Nowy projekt](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** i **lokalizację** plików projektu.
    4. Wybierz przycisk **Utwórz**.

3. Zaakceptuj wartości domyślne dla wersji platformy **docelowej** i **minimalnej**, a następnie wybierz pozycję **OK**.
4. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji ze sklepu Windows, wybierz pozycję **Publikuj**, a następnie wybierz pozycję **Skojarz aplikację ze sklepem**. Zostanie wyświetlony kreator **Kojarzenie aplikacji ze Sklepem Windows**.
5. W kreatorze zaloguj się za pomocą konta Microsoft.
6. Wybierz aplikację zarejestrowaną w kroku 2, wybierz przycisk **Dalej**, a następnie wybierz pozycję **Skojarz**. Spowoduje to dodanie wymaganych informacji dotyczących rejestracji w Sklepie Windows do manifestu aplikacji.
7. W programie Visual Studio kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Zostanie otwarte okno **Zarządzanie pakietami NuGet**.
8. W polu wyszukiwania wprowadź ciąg **WindowsAzure.Messaging.Managed**, wybierz pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.

    ![Okno Zarządzanie pakietami NuGet][20]

    Ta akcja spowoduje pobranie, zainstalowanie i dodanie odwołania do biblioteki usługi Azure Notification Hubs dla systemu Windows przy użyciu [pakietu NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Otwórz plik projektu `App.xaml.cs` i dodaj następujące instrukcje:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. W `App.xaml.cs` pliku projektu Znajdź `App` klasę i Dodaj następującą `InitNotificationsAsync` definicję metody. Zamień `<your hub name>` na nazwę centrum powiadomień utworzonego w Azure Portal i Zamień na `<Your DefaultListenSharedAccessSignature connection string>` `DefaultListenSharedAccessSignature` Parametry połączenia na stronie **zasady dostępu** w centrum powiadomień:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

    Ten kod pobiera identyfikator URI kanału dla aplikacji z usługi WNS, a następnie rejestruje ten identyfikator URI kanału w centrum powiadomień.

    >[!NOTE]
    > Zastąp tekst `hub name` nazwą centrum powiadomień wyświetlaną w witrynie Azure Portal. Zastąp również symbol zastępczy parametrów połączenia przy użyciu parametrów połączenia `DefaultListenSharedAccessSignature` uzyskanych ze strony **Zasady dostępu** Twojego centrum powiadomień w poprzedniej sekcji.

11. W górnej części programu obsługi zdarzeń `OnLaunched` w pliku `App.xaml.cs` dodaj następujące wywołanie do nowej metody `InitNotificationsAsync`:

    ```csharp
    InitNotificationsAsync();
    ```

    Ta akcja gwarantuje, że identyfikator URI kanału jest rejestrowany w centrum powiadomień przy każdym uruchomieniu aplikacji.

12. Kliknij prawym przyciskiem myszy `Package.appxmanifest` i wybierz pozycję Wyświetl kod (**F7**). Znajdź  `<Identity .../>` i Zastąp wartość **tożsamością aplikacji** utworzoną [wcześniej](#create-an-app-in-windows-store)WNS.

13. Aby uruchomić aplikację, naciśnij klawisz **F5** na klawiaturze. Zostanie wyświetlone okno dialogowe z kluczem rejestracji. Kliknij przycisk **OK**, aby zamknąć okno dialogowe.

    ![Rejestracja zakończona powodzeniem](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Aplikacja jest teraz gotowa do odbierania wyskakujących powiadomień.

## <a name="send-test-notifications"></a>Wysyłanie powiadomień testowych

Możesz szybko przetestować odbieranie powiadomień w aplikacji, wysyłając powiadomienia w witrynie [Azure Portal](https://portal.azure.com/).

1. W witrynie Azure Portal przełącz się na kartę Przegląd i wybierz pozycję **Wysyłanie testowe** na pasku narzędzi.

    ![Przycisk Wysyłanie testowe](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. W oknie **Wysyłanie testowe** wykonaj następujące czynności:
    1. W obszarze **Platformy** wybierz pozycję **Windows**.
    2. W obszarze **Typ powiadomienia** wybierz pozycję **Wyskakujące**.
    3. Wybierz pozycję **Wyślij**.

        ![Okienko Wysyłanie testowe](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Zobacz wynik operacji wysyłania na liście **Wynik** w dolnej części okna. Możesz też zobaczyć komunikat alertu.

    ![Wynik operacji wysyłania](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Zobaczysz komunikat z powiadomieniem: **Wiadomość testowa** na pulpicie.

    ![Komunikat z powiadomieniem](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Następne kroki
Wysłano powiadomienia emisji do wszystkich urządzeń z systemem Windows przy użyciu portalu lub aplikacji konsolowej. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych urządzeń](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: /previous-versions/windows/apps/hh761494(v=win.10)
[tile catalog]: /previous-versions/windows/apps/hh761491(v=win.10)
[badge overview]: /previous-versions/windows/apps/hh779719(v=win.10)
