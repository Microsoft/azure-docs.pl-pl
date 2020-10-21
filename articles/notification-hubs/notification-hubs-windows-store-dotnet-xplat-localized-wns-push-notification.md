---
title: Wysyłanie zlokalizowanych powiadomień do aplikacji systemu Windows przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak używać usługi Azure Notification Hubs do wysyłania zlokalizowanych powiadomień z ważnymi wiadomościami.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: d91320d8f78942ca916084cbf29a88818d95f531
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314622"
---
# <a name="tutorial-send-localized-push-notifications-to-windows-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie zlokalizowanych powiadomień wypychanych do aplikacji systemu Windows przy użyciu usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Sklep Windows C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Omówienie

W tym samouczku przedstawiono sposób wysyłania zlokalizowanych powiadomień push do urządzeń przenośnych zarejestrowanych w usłudze Notification Hubs. W samouczku zaktualizujesz aplikacje utworzone w [samouczku: wysyłanie powiadomień do konkretnych urządzeń (platforma uniwersalna systemu Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md), aby obsługiwać następujące scenariusze:

- Aplikacja dla Sklepu Windows umożliwia urządzeniom klienckim określenie języka oraz subskrybowanie różnych kategorii ważnych wiadomości.
- Aplikacja zaplecza emituje powiadomienia, korzystając z funkcji **tag** i **szablon** usługi Azure Notification Hubs.

Po ukończeniu samouczka aplikacja mobilna będzie umożliwiać zarejestrowanie kategorii zgodnie z preferencjami użytkownika, a także określenie języka, w którym będą odbierane powiadomienia. Aplikacja zaplecza wysyła powiadomienia, które są zlokalizowane według języka i urządzenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji systemu Windows o obsługę informacji o ustawieniach regionalnych
> * Aktualizowanie aplikacji zaplecza w celu wysyłania zlokalizowanych powiadomień
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Ukończenie [samouczka: wysyłanie powiadomień do konkretnych urządzeń (platforma uniwersalna systemu Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

W [samouczku: wysyłanie powiadomień do konkretnych urządzeń (platforma uniwersalna systemu Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) utworzono aplikację, która używała **tagów** do subskrybowania powiadomień w różnych **kategoriach** wiadomości. W tym samouczku użyjesz funkcji **szablon** usługi Notification Hubs, aby jeszcze łatwiej dostarczać **zlokalizowane** powiadomienia z ważnymi wiadomościami.

Na wysokim poziomie szablony są sposobem określania formatu, w którym konkretne urządzenie powinno odebrać powiadomienie. Szablon określa dokładny format ładunku, odwołując się do właściwości stanowiących część komunikatu wysyłanego przez zaplecze aplikacji. W tym samouczku aplikacja zaplecza wysyła komunikat niezależny od ustawień regionalnych zawierający wszystkie obsługiwane języki:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Urządzenie rejestruje się przy użyciu szablonu odwołującego się do odpowiedniej właściwości. Na przykład aplikacja dla Sklepu Windows, która chce otrzymywać wiadomości wyskakujące w języku angielskim, zarejestruje się w następującym szablonie z odpowiednimi tagami:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Aby dowiedzieć się więcej o szablonach, zobacz artykuł [Push Templates (Szablony wypychania)](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="update-windows-app-to-support-locale-information"></a>Aktualizowanie aplikacji systemu Windows o obsługę informacji o ustawieniach regionalnych

1. Otwórz rozwiązanie programu Visual Studio utworzone w ramach [samouczka: wysyłanie powiadomień do konkretnych urządzeń (platforma uniwersalna systemu Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).
2. Zaktualizuj plik `MainPage.xaml` rozwiązania, aby uwzględnić pole kombi ustawień regionalnych:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. W klasie `Notifications` dodaj parametr ustawień regionalnych do metod `StoreCategoriesAndSubscribe` i `SubscribeToCategories`.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Zamiast wywoływać metodę `RegisterNativeAsync`, wywołaj metodę `RegisterTemplateAsync`. Możesz zarejestrować określony format powiadomienia, w którym szablon będzie zależeć od ustawień regionalnych. Musisz również podać nazwę szablonu („localizedWNSTemplateExample”), ponieważ może być konieczne zarejestrowanie więcej niż jednego szablonu (np. jednego szablonu dla powiadomień wyskakujących i jednego dla kafelków). Ponadto musisz nazwać szablony, aby je aktualizować lub usuwać.

    Jeśli urządzenie zarejestruje wiele szablonów z tym samym tagiem, przychodzący komunikat nakierowany na ten tag będzie skutkować dostarczeniem wielu powiadomień do urządzenia (jednego dla każdego szablonu). To zachowanie jest przydatne, jeśli ten sam komunikat logiczny ma powodować wiele powiadomień wizualnych, np. wyświetlać wskaźnik i powiadomienie wyskakujące w aplikacji dla Sklepu Windows.
4. Dodaj następującą metodę, aby pobrać przechowywane ustawienia regionalne:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. W pliku `MainPage.xaml.cs` zaktualizuj procedurę obsługi kliknięcia przycisku, aby pobrać bieżącą wartość pola kombi ustawień regionalnych i przekazać ją do wywołania klasy `Notifications`:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. Na koniec w pliku `App.xaml.cs` zaktualizuj metodę `InitNotificationsAsync` w celu pobrania ustawień regionalnych i używania jej podczas subskrybowania:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>Uruchamianie aplikacji platformy UWP

1. Uruchom aplikację platforma uniwersalna systemu Windows. Poczekaj na wyświetlenie komunikatu **Rejestracja zakończona powodzeniem**.

    ![Aplikacja mobilna i rejestracja](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Wybierz **kategorie** i **ustawienia regionalne**, a następnie kliknij pozycję **Subscribe** (Subskrybuj). Aplikacja konwertuje wybrane kategorie na tagi i żąda nowej rejestracji urządzenia dla wybranych tagów z centrum powiadomień.

    ![Aplikacja mobilna](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. Zostanie wyświetlony komunikat z **potwierdzeniem** dotyczący **subskrypcji**.

    ![Komunikat dotyczący subskrypcji](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>Aktualizowanie aplikacji konsolowej w celu wysyłania zlokalizowanych powiadomień

W przypadku wysyłania powiadomień szablonowych musisz podać tylko zestaw właściwości. W tym samouczku aplikacja zaplecza wysyła zestaw właściwości zawierający zlokalizowaną wersję bieżących wiadomości, na przykład:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

W tej sekcji zaktualizujesz projekt aplikacji konsoli w rozwiązaniu. Zmodyfikuj metodę `SendTemplateNotificationAsync` w poprzednio utworzonej aplikacji konsoli, korzystając z następującego kodu:

> [!IMPORTANT]
> Określ nazwę i parametry połączenia z pełnym dostępem dla centrum powiadomień w kodzie.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

To proste wywołanie dostarcza zlokalizowaną partię wiadomości do **wszystkich** urządzeń, niezależnie od platformy, natomiast centrum powiadomień tworzy i dostarcza prawidłowy, natywny ładunek do wszystkich urządzeń, które zasubskrybowały określony tag.

## <a name="run-console-app-to-send-localized-notification"></a>Uruchom aplikację konsolową, aby wysłać zlokalizowane powiadomienie
Uruchom **aplikację konsolową** , aby wysyłać powiadomienia dla każdej kategorii i w każdym obsługiwanym języku. Upewnij się, że otrzymujesz wyłącznie powiadomienia dla subskrybowanych kategorii oraz że wiadomość jest zgodna z wybranymi ustawieniami regionalnymi.

![Powiadomienia](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wysyłania zlokalizowanych powiadomień push do konkretnych urządzeń, które mają tagi skojarzone z rejestracjami. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych użytkowników, którzy mogą używać więcej niż jednego urządzenia, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)