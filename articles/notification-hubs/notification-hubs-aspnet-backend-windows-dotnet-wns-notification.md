---
title: Wysyłanie powiadomień do konkretnych użytkowników przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak wysyłać powiadomienia do określonych użytkowników przy użyciu aplikacji platformy uniwersalnej systemu Windows (UWP).
documentationcenter: windows
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 97a6a45ab01fc113b79a48ba7fcb246d528684be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019484"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień do konkretnych użytkowników przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Omówienie

W tym samouczku opisano, jak za pomocą usługi Azure Notification Hubs wysyłać powiadomienia wypychane do określonego użytkownika aplikacji na określonym urządzeniu. Zaplecze ASP.NET interfejsu WebAPI jest używane do uwierzytelniania klientów. Kiedy zaplecze uwierzytelnia użytkownika aplikacji klienckiej, automatycznie dodaje tag do rejestracji powiadomienia. Zaplecze używa tego tagu do wysyłania powiadomień do określonego użytkownika.

> [!NOTE]
> Ukończony kod dla tego samouczka można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu interfejsu WebAPI
> * Uwierzytelnianie klientów w zapleczu interfejsu WebAPI
> * Rejestrowanie na potrzeby powiadomień za pomocą zaplecza interfejsu WebAPI
> * Wysyłanie powiadomień z zaplecza interfejsu WebAPI
> * Publikowanie nowego zaplecza interfejsu WebAPI
> * Aktualizowanie kodu na potrzeby projektu klienta
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek jest oparty na centrum powiadomień i projekcie programu Visual Studio, które zostały utworzone w samouczku [Samouczek: wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). W związku z tym należy zakończyć powyższy samouczek przed rozpoczęciem tego samouczka.

> [!NOTE]
> Jeśli używasz usługi Mobile Apps w usłudze Azure App Service jako usługi zaplecza, zobacz sekcję [Wersja usługi Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push) niniejszego samouczka.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>Aktualizowanie kodu dla klienta platformy UWP

W tej sekcji aktualizujesz kod w projekcie utworzonym na potrzeby samouczka [Samouczek: wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Projekt powinien już być skojarzony ze Sklepem Windows. Ponadto powinien być skonfigurowany do używania centrum powiadomień. W tej sekcji dodasz kod, aby wywołać nowe zaplecze interfejsu WebAPI oraz rejestrować i wysyłać powiadomienia za jego pomocą.

1. W programie Visual Studio otwórz rozwiązanie utworzone w ramach sekcji [Samouczek: wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt platforma uniwersalna systemu Windows (platformy UWP), a następnie kliknij pozycję **Zarządzaj pakietami NuGet**.
3. Po lewej stronie wybierz pozycję **Przeglądaj**.
4. W polu **Wyszukaj** wpisz ciąg **Klient HTTP**.
5. Na liście wyników kliknij pozycję **System.Net.Http**, a następnie kliknij opcję **Zainstaluj**. Ukończ instalację.
6. W polu **Wyszukaj** w menedżerze pakietów NuGet wpisz ciąg **Json.net**. Zainstaluj pakiet **Newtonsoft.json**, a następnie zamknij okno menedżera pakietów NuGet.
7. W Eksploratorze rozwiązań kliknij projekt **WindowsApp** i kliknij dwukrotnie plik **MainPage.xaml**, aby otworzyć go w edytorze programu Visual Studio.
8. W `MainPage.xaml` pliku Zastąp `<Grid>` sekcję następującym kodem: ten kod dodaje pole tekstowe username i Password, z którym użytkownik jest uwierzytelniany. Ponadto kod dodaje pola tekstowe dla komunikatu powiadomienia oraz tagu nazwy użytkownika, który ma otrzymać powiadomienie:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```

9. W Eksploratorze rozwiązań otwórz plik `MainPage.xaml.cs` dla projektów **(Windows 8.1)** i **(Windows Phone 8.1)**. Dodaj następujące instrukcje `using` na początku obu plików:

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```

10. W pliku `MainPage.xaml.cs` dla projektu **WindowsApp** dodaj następujący element członkowski do klasy `MainPage`. Pamiętaj, aby zastąpić ciąg `<Enter Your Backend Endpoint>` wcześniej pozyskanym, faktycznym punktem końcowym zaplecza. Na przykład `http://mybackend.azurewebsites.net`.

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

11. Dodaj poniższy kod do klasy MainPage w pliku `MainPage.xaml.cs` dla projektów **(Windows 8.1)** i **(Windows Phone 8.1)**.

    Metoda `PushClick` to procedura obsługi kliknięcia dla przycisku **Send Push** (Wyślij powiadomienie push). Metoda wywołuje zaplecze, aby wyzwolić powiadomienie do wszystkich urządzeń z tagiem nazwy użytkownika, który pasuje do parametru `to_tag`. Komunikat powiadomienia jest wysyłany jako zawartość JSON w treści żądania.

    Metoda `LoginAndRegisterClick` to procedura obsługi kliknięcia dla przycisku **Login and register** (Zaloguj i zarejestruj się). Zapisuje ona podstawowy token uwierzytelniania (reprezentuje dowolny token używany w danym schemacie uwierzytelniania) w magazynie lokalnym, a następnie używa metody `RegisterClient`, aby dokonać rejestracji na potrzeby powiadomień przy użyciu zaplecza.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS.
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```

12. Otwórz plik `App.xaml.cs` i znajdź wywołanie metody `InitNotificationsAsync()` w programie obsługi zdarzeń `OnLaunched()`. Oznacz wywołanie metody `InitNotificationsAsync()` jako komentarz lub usuń je. Procedura obsługi przycisków inicjuje rejestracje powiadomień:

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```

13. Kliknij prawym przyciskiem myszy projekt **WindowsApp**, kliknij polecenie **Dodaj**, a następnie kliknij polecenie **Klasa**. Nadaj klasie nazwę `RegisterClient.cs`, a następnie kliknij przycisk **OK**, aby wygenerować klasę.

    Ta klasa opakowuje wywołania REST wymagane do kontaktowania się z zapleczem aplikacji, aby przeprowadzać rejestrację na potrzeby powiadomień push. Ponadto zapisuje lokalnie identyfikatory *registrationId* utworzone przez centrum powiadomień zgodnie z opisem w sekcji [Rejestrowanie z poziomu zaplecza aplikacji](/previous-versions/azure/azure-services/dn743807(v=azure.100)). Klasa używa tokenu autoryzacji przechowywanego w magazynie lokalnym po kliknięciu przycisku **Login and register** (Zaloguj i zarejestruj się).
14. Dodaj następujące instrukcje `using` na początku pliku RegisterClient.cs:

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```

15. Dodaj następujący kod w `RegisterClient` definicji klasy:

    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```

16. Zapisz wszystkie zmiany.

## <a name="test-the-application"></a>Testowanie aplikacji

1. Uruchom aplikację w obu systemach Windows.
2. Wprowadź **nazwę użytkownika** i **hasło**, jak pokazano na poniższym zrzucie ekranu. Powinny różnić się od nazwy użytkownika i hasła, które zostały wprowadzone w systemie Windows Phone.
3. Kliknij przycisk **Log in and register** (Zaloguj i zarejestruj się) i upewnij się, że okno dialogowe wyświetla potwierdzenie zalogowania. Ponadto ten kod włącza przycisk **Send Push** (Wyślij powiadomienie push).

    ![Zrzut ekranu przedstawiający aplikację Notification Hubs, w której wypełniono pole Nazwa użytkownika i hasło.][14]
4. Następnie w polu **Recipient Username Tag** (Tag nazwy użytkownika odbiorcy) wprowadź zarejestrowaną nazwę użytkownika. Wprowadź komunikat powiadomienia i kliknij opcję **Send Push** (Wyślij powiadomienie push).
5. Komunikat powiadomienia otrzymają tylko te urządzenia, które zarejestrowały się przy użyciu pasującego tagu nazwy użytkownika.

    ![Zrzut ekranu aplikacji Notification Hubs wyświetlającej komunikat, który został wypchnięte.][15]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wysyłania powiadomień push do konkretnych użytkowników, którzy mają tagi skojarzone ze swoimi rejestracjami. Aby dowiedzieć się, jak wypychać powiadomienia oparte na lokalizacji, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień na podstawie lokalizacji](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
