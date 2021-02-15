---
title: 'Samouczek: Tworzenie aplikacji platforma uniwersalna systemu Windows (platformy UWP), która używa platformy tożsamości firmy Microsoft do uwierzytelniania | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację platformy UWP, która korzysta z platformy tożsamości firmy Microsoft do logowania użytkowników i uzyskiwania tokenu dostępu w celu wywołania interfejsu API Microsoft Graph w ich imieniu.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 6383f63d2118d8618f07bf3cb6cd08a0b16140f3
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102652"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Samouczek: wywoływanie interfejsu API Microsoft Graph z aplikacji platforma uniwersalna systemu Windows (platformy UWP)

W tym samouczku utworzysz natywną aplikację platforma uniwersalna systemu Windows (platformy UWP), która loguje się do użytkowników i pobiera token dostępu do wywoływania interfejsu API Microsoft Graph. 

Na końcu tego przewodnika aplikacja wywołuje chroniony interfejs API przy użyciu kont osobistych. Przykłady to outlook.com, live.com i inne. Aplikacja wywołuje również konta służbowe z dowolnej firmy lub organizacji, która ma Azure Active Directory (Azure AD).

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie projektu *platforma uniwersalna systemu Windows (platformy UWP)* w programie Visual Studio
> * Zarejestruj aplikację w Azure Portal
> * Dodawanie kodu do obsługi logowania i wylogowywania użytkowników
> * Dodawanie kodu do wywołania interfejsu API Microsoft Graph
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) z zainstalowanym obciążeniem [programowania platforma uniwersalna systemu Windows](/windows/uwp/get-started/get-set-up)

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

W tym przewodniku przedstawiono Tworzenie przykładowej aplikacji platformy UWP, która wysyła zapytanie do interfejsu API Microsoft Graph. W tym scenariuszu token jest dodawany do żądań HTTP przy użyciu nagłówka autoryzacji. Biblioteka uwierzytelniania firmy Microsoft obsługuje pozyskiwanie i odnawianie tokenów.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku zastosowano następujący pakiet NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)| Biblioteka uwierzytelniania firmy Microsoft|
|[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph Biblioteka kliencka|

## <a name="set-up-your-project"></a>konfigurowanie projektu

Ta sekcja zawiera instrukcje krok po kroku dotyczące integrowania aplikacji .NET Desktop (XAML) z logowaniem się z firmą Microsoft. Następnie aplikacja może wykonywać zapytania dotyczące internetowych interfejsów API, które wymagają tokenu, takiego jak Microsoft Graph API.

Ten przewodnik tworzy aplikację, która wyświetla przycisk, który wysyła zapytanie do interfejsu API Microsoft Graph i przycisk, aby się wylogować. Wyświetla również pola tekstowe, które zawierają wyniki wywołań.

> [!Tip]
> Aby wyświetlić ukończoną wersję projektu skompilowanego w tym samouczku, można [pobrać ją z witryny GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip).

### <a name="create-your-application"></a>Tworzenie aplikacji

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** wybierz pozycję **pusta aplikacja (uniwersalna platforma Windows)** dla języka C# i wybierz pozycję **dalej**.
1. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę aplikacji, a następnie wybierz pozycję **Utwórz**.
1. Jeśli zostanie wyświetlony monit, w oknie **Nowy projekt platforma uniwersalna systemu Windows** wybierz dowolną wersję dla wersji **docelowej** i **minimalnej** , a następnie wybierz **przycisk OK**.

   ![Wersje minimalne i docelowe](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-the-microsoft-authentication-library-to-your-project"></a>Dodawanie biblioteki uwierzytelniania firmy Microsoft do projektu

1. W programie Visual Studio wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.
1. Skopiuj i wklej następujące polecenia w oknie **konsola Menedżera pakietów** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > Pierwsze polecenie instaluje [bibliotekę uwierzytelniania firmy Microsoft (MSAL.NET)](https://aka.ms/msal-net). MSAL.NET pobiera, buforuje i odświeża tokeny użytkowników, które uzyskują dostęp do interfejsów API, które są chronione przez platformę tożsamości firmy Microsoft. Drugie polecenie instaluje [Microsoft Graph biblioteki klienta .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) do uwierzytelniania żądań Microsoft Graph i wykonywania wywołań do usługi.

### <a name="create-your-applications-ui"></a>Tworzenie interfejsu użytkownika aplikacji

Program Visual Studio tworzy *MainPage. XAML* jako część szablonu projektu. Otwórz ten plik, a następnie zastąp węzeł **siatki** aplikacji następującym kodem:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-the-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Korzystanie z biblioteki uwierzytelniania firmy Microsoft w celu uzyskania tokenu dla interfejsu API Microsoft Graph

W tej sekcji przedstawiono sposób korzystania z biblioteki uwierzytelniania firmy Microsoft w celu uzyskania tokenu dla interfejsu API Microsoft Graph. Wprowadź zmiany w pliku *MainPage.XAML.cs* .

1. W *MainPage.XAML.cs* Dodaj następujące odwołania:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Zastąp `MainPage` klasę następującym kodem:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika<a name="more-information"></a>

`AcquireTokenInteractive`Metoda powoduje pojawienie się okna z monitowaniem użytkowników o zalogowanie się. Aplikacje zwykle wymagają od użytkowników logowania się interakcyjnie po raz pierwszy w celu uzyskania dostępu do chronionego zasobu. Może być również konieczne zalogowanie się, gdy operacja dyskretna uzyskiwania tokenu nie powiedzie się. Przykładem jest to, że hasło użytkownika wygasło.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`AcquireTokenSilent`Metoda obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Po `AcquireTokenInteractive` uruchomieniu po raz pierwszy i Monituj użytkownika o poświadczenia, użyj `AcquireTokenSilent` metody, aby zażądać tokenów w celu późniejszego wywołania. Ta metoda uzyskuje tokeny w trybie dyskretnym. Biblioteka uwierzytelniania firmy Microsoft obsługuje pamięć podręczną i odnawianie tokenów.

Ostatecznie `AcquireTokenSilent` Metoda kończy się niepowodzeniem. Przyczyny niepowodzenia obejmują użytkownika, który wylogowano lub zmienił hasło na innym urządzeniu. Gdy biblioteka uwierzytelniania firmy Microsoft wykryje, że problem wymaga interaktywnej akcji, zgłasza `MsalUiRequiredException` wyjątek. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Aplikacja zostanie `AcquireTokenInteractive` natychmiast wywołana. To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika. Zwykle należy używać tej metody w przypadku aplikacji online, w przypadku których użytkownik nie ma dostępnej zawartości w trybie offline. Przykład wygenerowany przez tę konfigurację z przewodnikiem jest zgodny ze wzorcem. Zobaczysz ją w działaniu przy pierwszym uruchomieniu przykładu.

   Ponieważ żaden użytkownik nie użył aplikacji, `accounts.FirstOrDefault()` zawiera wartość null i zgłasza `MsalUiRequiredException` wyjątek.

   Kod w przykładzie przechwytuje wyjątek przez wywołanie `AcquireTokenInteractive` . To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika.

* Twoja aplikacja prezentuje wizualne wskazanie użytkownikom, którzy muszą się zalogować. Następnie mogą wybrać odpowiedni czas na zalogowanie się. Aplikacja może ponowić próbę `AcquireTokenSilent` później. Użyj tej metody, jeśli użytkownicy mogą korzystać z innych funkcji aplikacji bez zakłóceń. Przykładem jest to, że zawartość w trybie offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą zdecydować się na zalogowanie się. Aplikacja może ponowić próbę, `AcquireTokenSilent` gdy sieć była tymczasowo niedostępna.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>Tworzenie wystąpienia Microsoft Graph klienta usługi przez uzyskanie tokenu z metody SignInUserAndGetTokenUsingMSAL

Dodaj następującą nową metodę do *MainPage.XAML.cs*:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W tej przykładowej aplikacji `GetGraphServiceClient` Metoda tworzy wystąpienie przy `GraphServiceClient` użyciu tokenu dostępu. Następnie `GraphServiceClient` służy do uzyskiwania informacji o profilu użytkownika z punktu końcowego **Me** .

### <a name="add-a-method-to-sign-out-the-user"></a>Dodawanie metody w celu wylogowania użytkownika

Aby wylogować użytkownika, Dodaj następującą metodę do *MainPage.XAML.cs*:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

MSAL.NET używa metod asynchronicznych do pozyskiwania tokenów lub manipulowania kontami. W związku z tym obsługują akcje interfejsu użytkownika w wątku interfejsu użytkownika. Jest to powód `Dispatcher.RunAsync` wywołania i środków ostrożności do wywołania `ConfigureAwait(false)` .

#### <a name="more-information-about-signing-out"></a>Więcej informacji na temat wylogowywania<a name="more-information-on-sign-out"></a>

`SignOutButton_Click`Metoda usuwa użytkownika z pamięci podręcznej użytkownika biblioteki uwierzytelniania firmy Microsoft. Ta metoda efektywnie instruuje bibliotekę uwierzytelniania firmy Microsoft w celu zapomnienia bieżącego użytkownika. Przyszłe żądanie uzyskania tokenu powiedzie się tylko wtedy, gdy jest ono interaktywne.

Aplikacja w tym przykładzie obsługuje jednego użytkownika. Biblioteka uwierzytelniania firmy Microsoft obsługuje scenariusze, w których użytkownik może zalogować się na więcej niż jednym koncie. Przykładem jest aplikacja poczty e-mail, w której użytkownik ma kilka kont.

### <a name="display-basic-token-information"></a>Wyświetl podstawowe informacje o tokenie

Dodaj następującą metodę do *MainPage.XAML.cs* , aby wyświetlić podstawowe informacje o tokenie:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>Więcej informacji<a name="more-information-1"></a>

Tokeny identyfikatorów nabyte za pomocą programu **OpenID Connect Connect** również zawierają niewielki podzbiór informacji dotyczących użytkownika. `DisplayBasicTokenInfo` Wyświetla podstawowe informacje zawarte w tokenie. Te informacje obejmują nazwę wyświetlaną użytkownika i identyfikator. Zawiera również datę wygaśnięcia tokenu i ciąg, który reprezentuje token dostępu. W przypadku wybrania przycisku **Wywołaj Microsoft Graph interfejsu API** kilka razy zobaczysz, że ten sam token został ponownie użyty na potrzeby kolejnych żądań. Możesz również zobaczyć datę wygaśnięcia rozszerzoną, gdy biblioteka uwierzytelniania firmy Microsoft zdecyduje się na odnowienie tokenu.

### <a name="display-message"></a>Wyświetl komunikat

Dodaj następującą nową metodę do *MainPage.XAML.cs*:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Teraz Zarejestruj aplikację:

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji, na przykład `UWP-App-calling-MSGraph` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**. 
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie Przegląd Znajdź wartość **Identyfikator aplikacji (klienta)** i skopiuj ją. Wróć do programu Visual Studio, Otwórz *MainPage.XAML.cs* i Zastąp wartość `ClientId` tą wartością.

Skonfiguruj uwierzytelnianie dla aplikacji:

1. Z powrotem w <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>w obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**  >  **Dodaj platformę**, a następnie wybierz pozycję **aplikacje mobilne i klasyczne**.
1. W sekcji **identyfikatory URI przekierowania** wprowadź wartość `https://login.microsoftonline.com/common/oauth2/nativeclient` .
1. Wybierz pozycję **Konfiguruj**.

Skonfiguruj uprawnienia interfejsu API dla aplikacji:

1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**.
1. Wybierz **Microsoft Graph**.
1. Wybierz pozycję **uprawnienia delegowane**, Wyszukaj *użytkownika. Odczytaj* i sprawdź, czy wybrano pozycję **użytkownik. odczyt** .
1. Jeśli wprowadzono jakiekolwiek zmiany, wybierz pozycję **Dodaj uprawnienia** , aby je zapisać.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Włącz uwierzytelnianie zintegrowane w domenach federacyjnych (opcjonalnie)

Aby włączyć zintegrowane uwierzytelnianie systemu Windows, gdy jest on używany z federacyjną domeną usługi Azure AD, manifest aplikacji musi włączyć dodatkowe możliwości. Wróć do aplikacji w programie Visual Studio.

1. Otwórz *pakiet Package. appxmanifest*.
1. Wybierz pozycję **możliwości** i Włącz następujące ustawienia:

   * **Uwierzytelnianie w przedsiębiorstwie**
   * **Sieci prywatne (serwer & klienta)**
   * **Udostępnione certyfikaty użytkowników**

> [!IMPORTANT]
> [Zintegrowane uwierzytelnianie systemu Windows](https://aka.ms/msal-net-iwa) nie jest domyślnie skonfigurowane dla tego przykładu. Aplikacje, które żądają `Enterprise Authentication` lub `Shared User Certificates` możliwości wymagają wyższego poziomu weryfikacji w Sklepie Windows. Ponadto nie wszyscy deweloperzy chcą przeprowadzić wyższy poziom weryfikacji. Włącz to ustawienie tylko wtedy, gdy potrzebujesz zintegrowanego uwierzytelniania systemu Windows z federacyjną domeną usługi Azure AD.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>Alternatywne podejście do korzystania z WithDefaultRedirectURI ()

W bieżącym przykładzie `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` Metoda jest używana. Aby użyć programu `WithDefaultRedirectURI()` , wykonaj następujące kroki:

1. W *MainPage.XAML.cs*, Zamień `WithRedirectUri` na `WithDefaultRedirectUri` :

   **Bieżący kod**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Zaktualizowany kod**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Znajdź identyfikator URI wywołania zwrotnego dla aplikacji, dodając `redirectURI` pole w *MainPage.XAML.cs* i ustawiając na nim punkt przerwania:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Uruchom aplikację, a następnie skopiuj wartość `redirectUri` po trafieniu punktu przerwania. Wartość powinna wyglądać podobnie do następującej: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    Następnie można usunąć wiersz kodu, ponieważ jest on wymagany tylko raz, aby pobrać wartość.

3. W portalu rejestracji aplikacji Dodaj wartość zwróconą w **RedirectUri** w okienku **uwierzytelnianie** .

## <a name="test-your-code"></a>Testowanie kodu

Aby przetestować aplikację, wybierz klawisz **F5** , aby uruchomić projekt w programie Visual Studio. Zostanie wyświetlone okno główne:

![Interfejs użytkownika aplikacji](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Gdy wszystko będzie gotowe do przetestowania, wybierz pozycję **Wywołaj Microsoft Graph interfejs API**. Aby się zalogować, użyj konta organizacyjnego usługi Azure AD lub konto Microsoft, takiego jak live.com lub outlook.com. Podczas pierwszego uruchomienia tego testu przez użytkownika aplikacja wyświetli okno z prośbą o zalogowanie się użytkownika.

### <a name="consent"></a>Wyrażanie zgody

Po pierwszym zalogowaniu się do aplikacji ekran zgody wygląda podobnie do poniższej ilustracji. Wybierz pozycję **tak** , aby jawnie wyrazić zgodę na dostęp:

![Ekran zgody na dostęp](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Oczekiwane wyniki

Informacje o profilu użytkownika zwracane przez wywołanie interfejsu API Microsoft Graph na ekranie **wyniki wywołania interfejsu API** :

![Ekran wyników wywołań interfejsu API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Widoczne są również podstawowe informacje o tokenie uzyskanym za pośrednictwem `AcquireTokenInteractive` lub `AcquireTokenSilent` w polu **Informacje o tokenie** :

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|`Username` |`user@domain.com` |Nazwa użytkownika.|
|`Token Expires` |`DateTime` |Czas wygaśnięcia tokenu. Biblioteka uwierzytelniania firmy Microsoft rozszerza datę wygaśnięcia przez odnowienie tokenu w razie potrzeby.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga `user.read` zakresu odczytywania profilu użytkownika. Ten zakres jest domyślnie dodawany w każdej aplikacji, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla Microsoft Graph i niestandardowych interfejsów API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API Microsoft Graph wymaga zakresu, `Calendars.Read` Aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, Dodaj `Calendars.Read` delegowane uprawnienie do informacji o rejestracji aplikacji. Następnie Dodaj `Calendars.Read` zakres do `acquireTokenSilent` wywołania.

Po zwiększeniu liczby zakresów użytkownicy mogą otrzymywać monity o dodatkowe przesłanie.

## <a name="known-issues"></a>Znane problemy

### <a name="issue-1"></a>Problem 1

Po zalogowaniu się do aplikacji w federacyjnej domenie usługi Azure AD zostanie wyświetlony jeden z następujących komunikatów o błędach:

* "W żądaniu nie znaleziono prawidłowego certyfikatu klienta".
* "W magazynie certyfikatów użytkownika nie znaleziono prawidłowych certyfikatów".
* "Spróbuj ponownie wybrać inną metodę uwierzytelniania".

**Przyczyna:** Możliwości przedsiębiorstwa i certyfikatów nie są włączone.

**Rozwiązanie:** Wykonaj kroki opisane w temacie [Włączanie uwierzytelniania zintegrowanego w domenach federacyjnych (opcjonalnie)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problem 2

[Uwierzytelnianie zintegrowane można włączyć w domenach federacyjnych](#enable-integrated-authentication-on-federated-domains-optional) i próbować użyć Windows Hello na komputerze z systemem Windows 10, aby zalogować się do środowiska z skonfigurowanym uwierzytelnianiem wieloskładnikowym. Zostanie wyświetlona lista certyfikatów. Jeśli zdecydujesz się użyć numeru PIN, okno numeru PIN nigdy nie zostanie wyświetlone.

**Przyczyna:** Ten problem to znane ograniczenie brokera uwierzytelniania w sieci Web w aplikacjach platformy UWP, które działają na komputerach stacjonarnych z systemem Windows 10. Działa prawidłowo w systemie Windows 10 Mobile.

**Obejście problemu:** Wybierz pozycję **Zaloguj się z innymi opcjami**. Następnie wybierz pozycję **Zaloguj się przy użyciu nazwy użytkownika i hasła**. Wybierz pozycję **Podaj hasło**. Następnie przejdź przez proces uwierzytelniania telefonu.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat używania biblioteki uwierzytelniania firmy Microsoft (MSAL) do autoryzacji i uwierzytelniania w aplikacjach .NET:

> [!div class="nextstepaction"]
> [Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)](msal-overview.md)
