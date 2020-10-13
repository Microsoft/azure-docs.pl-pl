---
title: Wysyłanie powiadomień do konkretnych urządzeń (platforma uniwersalna systemu Windows) | Microsoft Docs
description: Za pomocą usługi Azure Notification Hubs z tagami w rejestracji wysyłaj ważne wiadomości do aplikacji platformy uniwersalnej systemu Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 027bbebaaab0694cfa0157464d47ef2c8e08fbff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998224"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Samouczek: wysyłanie powiadomień do określonych urządzeń z uruchomionymi platforma uniwersalna systemu Windows aplikacjami

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Omówienie

W tym samouczku pokazano, jak za pomocą usługi Azure Notification Hubs emitować powiadomienia o najświeższych wiadomościach. Ten samouczek dotyczy aplikacji ze sklepu Windows lub Windows Phone 8,1 (innych niż Silverlight). Jeśli jesteś celem Windows Phone 8,1 Silverlight, zobacz [powiadomienia wypychane do określonych Windows Phone urządzeń za pomocą usługi Azure Notification Hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

W tym samouczku dowiesz się, jak za pomocą usługi Azure Notification Hubs wysyłać powiadomienia wypychane do określonych urządzeń z systemem Windows, na których działa aplikacja platforma uniwersalna systemu Windows (platformy UWP). Po ukończeniu tego samouczka możesz zarejestrować się, aby uzyskać informacje o interesujących kategoriach wiadomości. Powiadomienia wypychane są odbierane tylko dla tych kategorii.

Aby włączyć scenariusze emisji, Dołącz jeden lub więcej *tagów* podczas tworzenia rejestracji w centrum powiadomień. Gdy powiadomienia są wysyłane do tagu, wszystkie urządzenia zarejestrowane dla tagu otrzymają powiadomienie. Aby uzyskać więcej informacji na temat tagów, zobacz [Routing i wyrażenia tagów](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> W programie Visual Studio 2019 nie są obsługiwane projekty w Sklepie Windows i Windows Phone w wersji 8,1 lub starszej. Aby uzyskać więcej informacji, zobacz temat [określanie i zgodność platformy Visual Studio 2019](/visualstudio/releases/2019/compatibility).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Dodawanie wyboru kategorii do aplikacji mobilnej
> * Rejestrowanie do otrzymywania powiadomień
> * Wysyłanie powiadomień z tagami
> * Uruchamianie aplikacji i generowanie powiadomień

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj kroki podane w artykule [Samouczek: wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs][get-started].  

## <a name="add-category-selection-to-the-app"></a>Dodawanie wyboru kategorii do aplikacji

Pierwszym krokiem jest dodanie elementów interfejsu użytkownika do istniejącej strony głównej, co umożliwi użytkownikom wybieranie kategorii do zarejestrowania. Wybrane kategorie są przechowywane na urządzeniu. Po uruchomieniu aplikacja tworzy w centrum powiadomień rejestrację urządzenia z wybranymi kategoriami jako tagami.

1. Otwórz plik projektu *MainPage. XAML* , a następnie skopiuj następujący kod do `Grid` elementu:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj**  >  **klasę**. W obszarze **Dodaj nowy element**Nazwij pozycję *powiadomienia*, a następnie wybierz pozycję **Dodaj**. W razie potrzeby Dodaj `public` modyfikator do definicji klasy.

1. Dodaj następujące `using` instrukcje do nowego pliku:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Skopiuj następujący kod do nowej klasy `Notifications`:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Ta klasa używa magazynu lokalnego do przechowywania kategorii wiadomości, które mają być odbierane przez to urządzenie. Zamiast wywoływać metodę `RegisterNativeAsync`, wywołaj metodę `RegisterTemplateAsync`, aby przeprowadzić rejestrację na potrzeby kategorii przy użyciu rejestracji szablonu.

    Jeśli chcesz zarejestrować więcej niż jeden szablon, podaj nazwę szablonu, na przykład *simpleWNSTemplateExample*. Nadaj nazwy szablonom, aby można było je aktualizować lub usuwać. Możesz zarejestrować więcej niż jeden szablon, aby mógł on wyskakujące powiadomienia i jeden dla kafelków.

    >[!NOTE]
    > W przypadku Notification Hubs urządzenie może zarejestrować wiele szablonów przy użyciu tego samego tagu. W takim przypadku komunikat przychodzący, który jest przeznaczony dla tagu, powoduje przepełnienie wielu powiadomień do urządzenia, po jednym dla każdego szablonu. Ten proces umożliwia wyświetlenie tego samego komunikatu w wielu powiadomieniach wizualnych, takich jak identyfikator i wyskakujące powiadomienie w aplikacji ze sklepu Windows.

    Aby uzyskać więcej informacji, zobacz [Szablony](notification-hubs-templates-cross-platform-push-messages.md).

1. W pliku projektu *App.XAML.cs* Dodaj następującą właściwość do `App` klasy:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Ta właściwość służy do tworzenia wystąpienia klasy `Notifications` i uzyskiwania do niego dostępu.

    W kodzie zamień symbole zastępcze `<hub name>` i `<connection string with listen access>` na uzyskane wcześniej wartości: nazwę centrum powiadomień i parametry połączenia **DefaultListenSharedAccessSignature**.

   > [!NOTE]
   > Ponieważ poświadczenia dystrybuowane przy użyciu aplikacji klienckiej nie są zazwyczaj zabezpieczone, przy użyciu aplikacji klienckiej dystrybuuj wyłącznie klucz dostępu do *nasłuchiwania*. Dostęp do nasłuchiwania umożliwia aplikacji rejestrowanie powiadomień, ale nie może ona modyfikować istniejących rejestracji ani wysyłać powiadomień. Klucz pełnego dostępu jest używany w zabezpieczonej usłudze zaplecza do wysyłania powiadomień oraz zmiany istniejących rejestracji.

1. W pliku *MainPage.XAML.cs* Dodaj następujący wiersz:

    ```csharp
    using Windows.UI.Popups;
    ```

1. W pliku *MainPage.XAML.cs* Dodaj następującą metodę:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Ta metoda tworzy listę kategorii i używa klasy `Notifications` do przechowywania listy w magazynie lokalnym. Ponadto rejestruje ona odpowiednie tagi w centrum powiadomień. Gdy kategorie zmienią się, rejestracja zostanie utworzona w nowej kategorii.

Aplikacja może teraz przechowywać zestaw kategorii w magazynie lokalnym na urządzeniu. Aplikacja rejestruje się w centrum powiadomień za każdym razem, gdy użytkownik zmieni wybór kategorii.

## <a name="register-for-notifications"></a>Rejestrowanie do otrzymywania powiadomień

W tej sekcji rejestrujesz się w centrum powiadomień przy uruchamianiu przy użyciu kategorii przechowywanych w magazynie lokalnym.

> [!NOTE]
> Ponieważ identyfikator URI kanału przypisany przez usługę powiadomień systemu Windows (WNS) może ulec zmianie w dowolnym momencie, należy często rejestrować się w celu otrzymywania powiadomień, aby uniknąć niepowodzeń ich dostarczania. Poniższy przykład przeprowadza rejestrację w celu otrzymywania powiadomień za każdym razem, gdy aplikacja jest uruchamiana. W przypadku aplikacji, które są uruchamiane często, powiedzmy, więcej niż raz dziennie, możesz prawdopodobnie pominąć rejestrację, aby zachować przepustowość, jeśli od czasu poprzedniej rejestracji upłynęło mniej niż jeden dzień.

1. Aby użyć `notifications` klasy do subskrybowania na podstawie kategorii, Otwórz plik *App.XAML.cs* , a następnie zaktualizuj `InitNotificationsAsync` metodę.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Ten proces zapewnia, że po uruchomieniu aplikacji pobiera kategorie z magazynu lokalnego. Następnie żąda rejestracji tych kategorii. W `InitNotificationsAsync` samouczku [wysyłanie powiadomień do platforma uniwersalna systemu Windows aplikacji za pomocą usługi Azure Notification Hubs][get-started] można było utworzyć metodę.
2. W pliku projektu *MainPage.XAML.cs* Dodaj następujący kod do `OnNavigatedTo` metody:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Ten kod aktualizuje stronę główną w oparciu o stan poprzednio zapisanych kategorii.

Aplikacja jest teraz ukończona. Można przechowywać zestaw kategorii w lokalnym magazynie urządzenia. Gdy użytkownicy zmienią wybór kategorii, zapisane kategorie są używane do rejestracji w centrum powiadomień. W następnej sekcji zdefiniujesz zaplecze, które może wysyłać do tej aplikacji powiadomienia w odpowiednich kategoriach.

## <a name="run-the-uwp-app"></a>Uruchamianie aplikacji platformy UWP

1. W programie Visual Studio naciśnij klawisz F5, aby skompilować i uruchomić aplikację. Interfejs użytkownika aplikacji udostępnia zestaw przełączników pozwalających wybrać kategorie do zasubskrybowania.

   ![Aplikacja Breaking News](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Włącz co najmniej jeden przełącznik kategorii, a następnie wybierz pozycję **Subskrybuj**.

   Aplikacja konwertuje wybrane kategorie na tagi i żąda nowej rejestracji urządzenia dla wybranych tagów z centrum powiadomień. Aplikacja wyświetla zarejestrowane kategorie w oknie dialogowym.

    ![Przełączniki kategorii i przycisk Subscribe (Subskrybuj)](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Tworzenie aplikacji konsolowej do wysyłania otagowanych powiadomień

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Uruchamianie aplikacji konsolowej w celu wysyłania otagowanych powiadomień

Uruchom aplikację utworzoną w poprzedniej sekcji. Powiadomienia dla wybranych kategorii będą wyświetlane jako powiadomienia wyskakujące.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rozgłaszania ważnych wiadomości według kategorii. Aplikacja zaplecza wysyła otagowane powiadomienia do urządzeń, które zostały zarejestrowane w celu otrzymywania powiadomień dla tego tagu. Aby dowiedzieć się, jak wypychanie powiadomień do określonych użytkowników niezależnie od używanego urządzenia, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [wysyłanie zlokalizowanych powiadomień push do aplikacji systemu Windows przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
