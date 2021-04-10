---
title: 'Samouczek: Tworzenie aplikacji Windows Presentation Foundation (WPF), która używa platformy tożsamości firmy Microsoft do uwierzytelniania | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację WPF, która używa platformy tożsamości firmy Microsoft do logowania użytkowników i uzyskiwania tokenu dostępu w celu wywołania interfejsu API Microsoft Graph w ich imieniu.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 60a29efc4d2daa9d1bc90f00e71094da382a83b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686890"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Samouczek: wywoływanie interfejsu API Microsoft Graph z aplikacji klasycznej systemu Windows

W tym samouczku utworzysz natywną aplikację platformy .NET dla systemu Windows (XAML), która loguje się do użytkowników i pobiera token dostępu w celu wywołania interfejsu API Microsoft Graph. 

Po zakończeniu przewodnika aplikacja będzie mogła wywołać chroniony interfejs API, który korzysta z kont osobistych (w tym outlook.com, live.com i innych). Aplikacja będzie również używać kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie projektu *Windows Presentation Foundation (WPF)* w programie Visual Studio
> * Zainstaluj bibliotekę uwierzytelniania firmy Microsoft (MSAL) dla platformy .NET
> * Zarejestruj aplikację w Azure Portal
> * Dodawanie kodu do obsługi logowania i wylogowywania użytkowników
> * Dodawanie kodu do wywołania interfejsu API Microsoft Graph
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa Przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Przykładowa aplikacja utworzona za pomocą tego przewodnika umożliwia aplikacji klasycznych systemu Windows, która będzie wysyłać zapytania do interfejsu API Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu należy dodać token do żądań HTTP za pośrednictwem nagłówka autoryzacji. Biblioteka Microsoft Authentication Library (MSAL) obsługuje pozyskiwanie i odnawianie tokenów.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Obsługa pozyskiwania tokenu do uzyskiwania dostępu do chronionych interfejsów API sieci Web

Po uwierzytelnieniu użytkownika Przykładowa aplikacja odbiera token, za pomocą którego można wysyłać zapytania do interfejsu API Microsoft Graph lub internetowego interfejsu API, który jest zabezpieczony przez platformę tożsamości firmy Microsoft.

Interfejsy API, takie jak Microsoft Graph wymagają tokenu, aby zezwolić na dostęp do określonych zasobów. Na przykład token jest wymagany do odczytywania profilu użytkownika, uzyskiwania dostępu do kalendarza użytkownika lub wysyłania wiadomości e-mail. Aplikacja może zażądać tokenu dostępu za pomocą MSAL, aby uzyskać dostęp do tych zasobów, określając zakresy interfejsów API. Ten token dostępu jest następnie dodawany do nagłówka autoryzacji HTTP dla każdego wywołania, które zostało wykonane względem chronionego zasobu.

MSAL zarządza buforowaniem i odświeża tokeny dostępu, dzięki czemu aplikacja nie musi.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku są stosowane następujące pakiety NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft (MSAL.NET)|

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji utworzysz nowy projekt, aby zademonstrować, jak zintegrować aplikację .NET Desktop (XAML) z *logowaniem się z firmą Microsoft* , aby aplikacja mogła wysyłać zapytania do internetowych interfejsów API, które wymagają tokenu.

Aplikacja utworzona za pomocą tego przewodnika wyświetla przycisk, który służy do wywoływania grafu, obszar do wyświetlania wyników na ekranie i przycisk Wyloguj.

> [!NOTE]
> Wolisz pobrać projekt przykładowego programu Visual Studio? [Pobierz projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)i przejdź do [kroku konfiguracji](#register-your-application) , aby skonfigurować przykładowy kod przed jego uruchomieniem.
>

Aby utworzyć aplikację, wykonaj następujące czynności:

1. W programie Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.
2. W obszarze **Szablony** wybierz pozycję **Visual C#**.
3. Wybierz pozycję **Aplikacja WPF (.NET Framework)**, w zależności od używanej wersji programu Visual Studio.

## <a name="add-msal-to-your-project"></a>Dodawanie MSAL do projektu

1. W programie Visual Studio wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet** >  **konsola Menedżera pakietów**.
2. W oknie Konsola Menedżera pakietów Wklej następujące polecenie Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > To polecenie powoduje zainstalowanie biblioteki uwierzytelniania firmy Microsoft. MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkowników, które są używane w celu uzyskania dostępu do interfejsów API chronionych przez Azure Active Directory v 2.0
    >

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aplikację możesz zarejestrować na dwa sposoby.

### <a name="option-1-express-mode"></a>Opcja 1: tryb ekspresowy

Możesz szybko zarejestrować aplikację, wykonując następujące czynności:
1. Przejdź do środowiska szybkiego startu w <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure Portal rejestracje aplikacji</a> .
1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.

### <a name="option-2-advanced-mode"></a>Opcja 2: tryb zaawansowany

Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji, na przykład `Win-App-calling-MsGraph` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
1. W sekcji **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacji (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**.
1. Wybierz pozycję **Zarejestruj**.
1. W obszarze **Zarządzanie** wybierz pozycję **uwierzytelnianie**  >  **Dodaj platformę**.
1. Wybierz **aplikacje mobilne i klasyczne**.
1. W sekcji **identyfikatory URI przekierowania** wybierz pozycję **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Wybierz pozycję **Konfiguruj**.
1. Przejdź do programu Visual Studio, Otwórz plik *App. XAML. cs* , a następnie zastąp ciąg `Enter_the_Application_Id_here` w poniższym fragmencie kodu identyfikatorem aplikacji, która została właśnie zarejestrowana i skopiowana.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Dodaj kod w celu zainicjowania MSAL

W tym kroku utworzysz klasę do obsługi interakcji z MSAL, taką jak obsługa tokenów.

1. Otwórz plik *App. XAML. cs* , a następnie Dodaj odwołanie do MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Zaktualizuj klasę aplikacji do następujących:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .WithDefaultRedirectUri()
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Tworzenie interfejsu użytkownika aplikacji

W tej sekcji pokazano, jak aplikacja może wysyłać zapytania do chronionego serwera zaplecza, takiego jak Microsoft Graph.

Plik *MainWindow. XAML* powinien być automatycznie tworzony jako część szablonu projektu. Otwórz ten plik, a następnie zastąp węzeł aplikacji *\<Grid>* następującym kodem:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Użyj MSAL, aby uzyskać token dla interfejsu API Microsoft Graph

W tej sekcji użyjesz MSAL, aby uzyskać token dla interfejsu API Microsoft Graph.

1. W pliku *MainWindow. XAML. cs* Dodaj odwołanie do MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Zastąp `MainWindow` kod klasy następującymi:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>Więcej informacji

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Wywołanie `AcquireTokenInteractive` metody skutkuje oknem, które poprosi użytkowników o zalogowanie się. Aplikacje zwykle wymagają od użytkowników logowania się interaktywnie przy pierwszym dostępie do chronionego zasobu. Może być również konieczne zalogowanie się w przypadku niepowodzenia operacji dyskretnej uzyskania tokenu (na przykład po wygaśnięciu hasła przez użytkownika).

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`AcquireTokenSilent`Metoda obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Gdy `AcquireTokenInteractive` jest wykonywane po raz pierwszy, `AcquireTokenSilent` jest zazwyczaj stosowana do uzyskiwania tokenów, które uzyskują dostęp do chronionych zasobów dla kolejnych wywołań, ponieważ wywołania żądania lub odnowienia tokenów są wykonywane w trybie dyskretnym.

Ostatecznie Metoda zakończy `AcquireTokenSilent` się niepowodzeniem. Przyczyną niepowodzenia może być wylogowanie lub zmiana hasła przez użytkownika na innym urządzeniu. Gdy MSAL wykryje, że problem może zostać rozwiązany przez wymaganie interaktywnej akcji, wyzwala `MsalUiRequiredException` wyjątek. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Może to spowodować `AcquireTokenInteractive` natychmiastowe wywołanie. To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika. Ten wzorzec jest zwykle używany w aplikacjach online, w przypadku których użytkownik nie ma dostępnej zawartości w trybie offline. Przykład generowany przez tę konfigurację z przewodnikiem jest zgodny z tym wzorcem, który można zobaczyć w akcji przy pierwszym wykonaniu przykładu.

* Ponieważ żaden użytkownik nie użył aplikacji, `PublicClientApp.Users.FirstOrDefault()` zawiera wartość null i `MsalUiRequiredException` zgłaszany jest wyjątek.

* Kod w próbce następnie obsługuje wyjątek przez wywołanie `AcquireTokenInteractive` , które powoduje wyświetlenie monitu o zalogowanie użytkownika.

* Może zamiast tego przedstawić wizualne wskazanie użytkownikom, że jest wymagane logowanie interaktywne, aby umożliwić im wybranie odpowiedniego czasu do zalogowania. Lub aplikacja może ponowić próbę `AcquireTokenSilent` później. Ten wzorzec jest często używany, gdy użytkownicy mogą korzystać z innych funkcji aplikacji bez zakłóceń — na przykład gdy zawartość offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą zdecydować się na zalogowanie się w celu uzyskania dostępu do chronionego zasobu lub odświeżenia nieaktualnych informacji. Alternatywnie aplikacja może zdecydować się na ponowienie próby `AcquireTokenSilent` po przywróceniu sieci po jej tymczasowym udostępnieniu.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywoływanie interfejsu API Microsoft Graph przy użyciu właśnie uzyskanego tokenu

Dodaj następującą nową metodę do programu `MainWindow.xaml.cs` . Metoda jest używana do `GET` żądania odinterfejs API programu Graph przy użyciu autoryzowanego nagłówka:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W tej przykładowej aplikacji użyto metody, `GetHttpContentWithToken` Aby wykonać `GET` żądanie HTTP względem chronionego zasobu, który wymaga tokenu, a następnie zwrócić zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w nagłówku autoryzacji HTTP. Dla tego przykładu zasób *jest punktem* końcowym usługi Microsoft Graph API, który wyświetla informacje o profilu użytkownika.

## <a name="add-a-method-to-sign-out-a-user"></a>Dodawanie metody do wylogowania użytkownika

Aby wylogować użytkownika, Dodaj następującą metodę do `MainWindow.xaml.cs` pliku:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>Więcej informacji na temat wylogowywania użytkowników

`SignOutButton_Click`Metoda usuwa użytkowników z pamięci podręcznej użytkownika usługi MSAL, co efektywnie informuje, że MSAL o zapomnieniu bieżącego użytkownika, aby przyszłe żądanie uzyskania tokenu zakończyło się powodzeniem tylko wtedy, gdy zostanie wykonane interaktywnie.

Mimo że aplikacja w tym przykładzie obsługuje pojedynczych użytkowników, MSAL obsługuje scenariusze, w których może być zalogowanych wiele kont w tym samym czasie. Przykładem jest aplikacja poczty e-mail, w której użytkownik ma wiele kont.

## <a name="display-basic-token-information"></a>Wyświetl podstawowe informacje o tokenie

Aby wyświetlić podstawowe informacje o tokenie, Dodaj następującą metodę do pliku *MainWindow. XAML. cs* :

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>Więcej informacji

Oprócz tokenu dostępu, który jest używany do wywoływania interfejsu API Microsoft Graph, po zalogowaniu się użytkownika MSAL również uzyskuje token identyfikatora. Ten token zawiera niewielki podzbiór informacji przydatnych dla użytkowników. `DisplayBasicTokenInfo`Metoda wyświetla podstawowe informacje zawarte w tokenie. Na przykład wyświetla nazwę wyświetlaną i identyfikator użytkownika, a także datę wygaśnięcia tokenu i ciąg reprezentujący sam token dostępu. Przycisk *wywołania Microsoft Graph API* można wybrać wiele razy i zobaczyć, że ten sam token został ponownie użyty dla kolejnych żądań. Możesz również sprawdzić datę wygaśnięcia, która zostanie rozszerzona, gdy MSAL zdecyduje, że jest czas odnowienia tokenu.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tworzeniu aplikacji klasycznych, które wywołują chronione interfejsy API sieci Web w naszej wieloczęściowej serii scenariuszy:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja klasyczna, która wywołuje interfejsy API sieci Web](scenario-desktop-overview.md)
