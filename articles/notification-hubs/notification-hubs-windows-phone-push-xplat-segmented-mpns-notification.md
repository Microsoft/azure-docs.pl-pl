---
title: Wysyłanie powiadomień wypychanych do konkretnych telefonów z systemem Windows przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do konkretnych (nie wszystkich) urządzeń z systemem Windows Phone 8 lub Windows Phone 8.1 zarejestrowanych w zapleczu aplikacji.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
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
ms.openlocfilehash: 67700ec643a27f8ae5c581fe9de8e3295a1925e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998241"
---
# <a name="tutorial-send-push-notifications-to-specific-windows-phones-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień wypychanych do konkretnych telefonów z systemem Windows przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem Windows Phone 8 lub Windows Phone 8.1. Jeśli aplikacja ma być przeznaczona dla systemu Windows Phone 8.1 (bez platformy Silverlight), dodatkowe informacje zawiera wersja dotycząca [aplikacji uniwersalnych systemu Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tego samouczka.

Ten scenariusz możesz włączyć, uwzględniając co najmniej jeden *tag* podczas tworzenia rejestracji w centrum powiadomień. W przypadku wysłania powiadomień do tagu wszystkie urządzenia zarejestrowane dla tego tagu otrzymają powiadomienie. Aby uzyskać więcej informacji na temat tagów, zobacz [Tagi w rejestracjach](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Zestaw SDK usługi Notification Hubs dla systemu Windows Phone nie obsługuje używania usługi WNS (Windows Push Notification Service) z aplikacjami platformy Silverlight dla systemu Windows Phone 8.1. Aby używać usługi WNS (zamiast usługi MPNS) z aplikacjami platformy Silverlight dla systemu Windows Phone 8.1, postępuj zgodnie z instrukcjami w [samouczku dotyczącym usługi Notification Hubs dla aplikacji platformy Silverlight dla systemu Windows Phone], w którym zastosowano interfejsy API REST.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie wyboru kategorii do aplikacji mobilnej
> * Rejestrowanie do otrzymywania powiadomień za pomocą tagów
> * Wysyłanie powiadomień z tagami
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Zakończ [samouczek: wysyłanie powiadomień push do aplikacji systemu Windows Phone przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md). W tym samouczku zaktualizujesz aplikację mobilną, aby zyskać możliwość zarejestrowania się w kategoriach ważnych wiadomości, które Cię interesują, oraz otrzymywania tylko powiadomień push dla tych kategorii.

## <a name="add-category-selection-to-the-mobile-app"></a>Dodawanie wyboru kategorii do aplikacji mobilnej

Pierwszym krokiem jest dodanie elementów interfejsu użytkownika do istniejącej strony głównej, co umożliwi użytkownikowi wybranie kategorii do zarejestrowania się. Kategorie wybrane przez użytkownika są przechowywane na urządzeniu. Po uruchomieniu aplikacji w centrum powiadomień zostanie utworzona rejestracja urządzenia z wybranymi kategoriami w formie tagów.

1. Otwórz plik `MainPage.xaml`, a następnie zastąp elementy `Grid` o nazwie `TitlePanel` i `ContentPanel` następującym kodem:

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Dodaj klasę o nazwie `Notifications` do projektu. Dodaj modyfikator `public` do definicji klasy. Następnie dodaj następujące instrukcje `using` do nowego pliku:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Skopiuj następujący kod do klasy `Notifications`:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Ta klasa używa izolowanego magazynu do przechowywania kategorii wiadomości, które będą odbierane na tym urządzeniu. Zawiera również metody rejestrowania tych kategorii przy użyciu [szablonu](notification-hubs-templates-cross-platform-push-messages.md) rejestracji powiadomień.
4. W pliku projektu `App.xaml.cs` dodaj następującą właściwość do klasy `App`. Zastąp symbole zastępcze `<hub name>` i `<connection string with listen access>` uzyskanymi wcześniej wartościami: nazwą centrum powiadomień i parametrami połączenia *DefaultListenSharedAccessSignature*.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Ponieważ poświadczenia dystrybuowane przy użyciu aplikacji klienckiej nie są zazwyczaj bezpieczne, należy przy użyciu aplikacji klienckiej dystrybuować wyłącznie klucz dostępu do nasłuchiwania. Dostęp do nasłuchiwania umożliwia aplikacji rejestrowanie powiadomień, ale nie może ona modyfikować istniejących rejestracji ani wysyłać powiadomień. Klucz pełnego dostępu jest używany w zabezpieczonej usłudze zaplecza do wysyłania powiadomień oraz zmiany istniejących rejestracji.

5. W pliku `MainPage.xaml.cs` dodaj następujący wiersz:

    ```csharp
    using Windows.UI.Popups;
    ```
6. W pliku projektu MainPage.xaml.cs dodaj następującą metodę:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Ta metoda tworzy listę kategorii i używa klasy `Notifications` do przechowywania listy w magazynie lokalnym oraz rejestrowania odpowiednich tagów w centrum powiadomień. Jeśli kategorie zostaną zmienione, rejestracja zostanie ponownie utworzona przy użyciu nowych kategorii.

Aplikacja może teraz przechowywać zestaw kategorii w magazynie lokalnym na urządzeniu i dokonywać rejestracji przy użyciu centrum powiadomień za każdym razem, gdy użytkownik zmieni wybór kategorii.

## <a name="register-for-notifications"></a>Rejestrowanie do otrzymywania powiadomień

Te kroki umożliwiają zarejestrowanie przy użyciu centrum powiadomień przy uruchamianiu przy użyciu kategorii przechowywanych w magazynie lokalnym.

> [!NOTE]
> Ponieważ identyfikator URI kanału przypisany przez usługę powiadomień push firmy Microsoft (MPNS) może ulec zmianie w dowolnym momencie, należy przeprowadzać rejestrację do otrzymywania powiadomień często, aby unikać błędów związanych z powiadomieniami. Poniższy przykład wykonuje rejestrację do otrzymywania powiadomień za każdym razem, gdy aplikacja zostaje uruchomiona. W przypadku często uruchamianych aplikacji — więcej niż raz dziennie — prawdopodobnie możesz pominąć rejestrację, aby zachować przepustowość, jeśli od poprzedniej rejestracji upłynął czas krótszy niż jeden dzień.

1. Otwórz plik App.xaml.cs i dodaj modyfikator `async` do metody `Application_Launching` oraz zastąp kod rejestracji w usłudze Notification Hubs dodany w samouczku [Rozpoczynanie pracy z usługą Notification Hubs] następującym kodem:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Ten kod gwarantuje, że podczas każdego uruchomienia aplikacja pobiera kategorie z magazynu lokalnego oraz żąda rejestracji dla tych kategorii.
2. W pliku projektu MainPage.xaml.cs dodaj następujący kod, który implementuje metodę `OnNavigatedTo`:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Ten kod aktualizuje stronę główną w oparciu o stan poprzednio zapisanych kategorii.

Aplikacja jest teraz zakończona i może przechowywać w magazynie lokalnym na urządzeniu zestaw kategorii używanych do rejestracji w centrum powiadomień za każdym razem, gdy użytkownik zmieni wybór kategorii. Następnie zdefiniuj zaplecze, które może wysyłać powiadomienia dla kategorii do tej aplikacji.

## <a name="send-tagged-notifications"></a>Wysyłanie powiadomień z tagami

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testowanie aplikacji

1. W programie Visual Studio naciśnij klawisz F5, aby skompilować i uruchomić aplikację.

    ![Aplikacja mobilna z kategoriami][1]

    Interfejs użytkownika aplikacji udostępnia zestaw przełączników pozwalających wybrać kategorie do zasubskrybowania.
2. Włącz co najmniej jeden przełącznik kategorii, a następnie kliknij przycisk **Subscribe** (Subskrybuj).

    Aplikacja konwertuje wybrane kategorie na tagi i żąda nowej rejestracji urządzenia dla wybranych tagów z centrum powiadomień. Zarejestrowane kategorie są zwracane i wyświetlane w oknie dialogowym.

    ![Komunikat o subskrypcji][2]
3. Po odebraniu potwierdzenia, że subskrypcja kategorii została zakończona, uruchom aplikację konsoli, aby wysłać powiadomienia dla każdej kategorii. Upewnij się, że otrzymujesz powiadomienia tylko dla subskrybowanych kategorii.

    ![Komunikat z powiadomieniem][3]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wysyłania powiadomień push do konkretnych urządzeń, które mają tagi skojarzone z rejestracjami. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych użytkowników, którzy mogą używać wielu urządzeń, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Rozpoczynanie pracy z usługą Notification Hubs]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Phone]: ??
