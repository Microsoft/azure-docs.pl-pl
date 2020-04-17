---
title: Wprowadzenie do komputera stacjonarnego platformy tożsamości firmy Microsoft dla systemu Windows
description: Jak aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez platformę tożsamości firmy Microsoft.
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
ms.openlocfilehash: ba4afa31a1ed7b6e2ddf43787ca32a06e97455ce
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533772"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Wywoływanie interfejsu API programu Microsoft Graph z aplikacji pulpitu systemu Windows

W tym przewodniku pokazano, jak natywna aplikacja Windows Desktop .NET (XAML) używa tokenu dostępu do wywołania interfejsu API programu Microsoft Graph. Aplikacja może również uzyskać dostęp do innych interfejsów API, które wymagają tokenów dostępu z platformy tożsamości firmy Microsoft dla deweloperów w wersji 2.0 punktu końcowego. Ta platforma była wcześniej nazywana usługą Azure AD.

Po zakończeniu przewodnika aplikacja będzie mogła wywołać chroniony interfejs API, który używa kont osobistych (w tym outlook.com, live.com i innych). Aplikacja będzie również używać kont służbowych i szkolnych z dowolnej firmy lub organizacji korzystającej z usługi Azure Active Directory.

> [!NOTE]
> Przewodnik wymaga aktualizacji programu Visual Studio 2015 3, visual studio 2017 lub programu Visual Studio 2019. Nie masz żadnej z tych wersji? [Pobierz program Visual Studio 2019 za darmo](https://www.visualstudio.com/downloads/).

>[!NOTE]
> Jeśli jesteś nowy na platformie tożsamości firmy Microsoft, zalecamy rozpoczęcie od [pozyskania tokenu i wywołanie interfejsu API programu Microsoft Graph z aplikacji klasycznej systemu Windows.](quickstart-v2-windows-desktop.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje, jak działa przykładowa aplikacja generowana przez ten samouczek](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Przykładowa aplikacja utworzona za pomocą tego przewodnika umożliwia aplikacji pulpitu systemu Windows, która wysyła kwerendy do interfejsu API programu Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu można dodać token do żądań HTTP za pośrednictwem nagłówka autoryzacji. Biblioteka uwierzytelniania firmy Microsoft (MSAL) obsługuje pozyskiwanie i odnawianie tokenów.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Obsługa pozyskiwania tokenów w celu uzyskania dostępu do chronionych interfejsów API sieci Web

Po uwierzytelnianiu użytkownika przykładowa aplikacja otrzymuje token, którego można użyć do wykonywania zapytań o interfejs API programu Microsoft Graph lub internetowy interfejs API zabezpieczony przez platformę tożsamości firmy Microsoft dla deweloperów.

Interfejsy API, takie jak Microsoft Graph, wymagają tokenu, aby umożliwić dostęp do określonych zasobów. Na przykład token jest wymagany do odczytu profilu użytkownika, uzyskania dostępu do kalendarza użytkownika lub wysłania wiadomości e-mail. Aplikacja może zażądać tokenu dostępu przy użyciu msal, aby uzyskać dostęp do tych zasobów, określając zakresy interfejsu API. Ten token dostępu jest następnie dodawany do nagłówka autoryzacji HTTP dla każdego wywołania, które jest wykonane względem chronionego zasobu.

MSAL zarządza buforowania i odświeżania tokenów dostępu dla Ciebie, dzięki czemu aplikacja nie musi.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku użyto następujących pakietów NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft (MSAL.NET)|

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji należy utworzyć nowy projekt, aby zademonstrować sposób integracji aplikacji platformy .NET pulpitu systemu Windows (XAML) z *logowaniem się za pomocą firmy Microsoft,* aby aplikacja mogła wysyłać zapytania do interfejsów API sieci Web, które wymagają tokenu.

Aplikacja utworzona za pomocą tego przewodnika wyświetla przycisk, który jest używany do wywoływania wykresu, obszar, aby wyświetlić wyniki na ekranie i przycisk wylogowania.

> [!NOTE]
> Wolisz zamiast tego przykładowego projektu programu Visual Studio? [Pobierz projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)i przejdź do [kroku Konfiguracja,](#register-your-application) aby skonfigurować przykładowy kod przed jego wykonaniem.
>

Aby utworzyć aplikację, wykonaj następujące czynności:

1. W programie Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu**.
2. W obszarze **Szablony**wybierz pozycję **Visual C#**.
3. Wybierz **aplikację WPF (.NET Framework)**, w zależności od wersji używanej wersji programu Visual Studio.

## <a name="add-msal-to-your-project"></a>Dodawanie usługi MSAL do projektu

1. W programie Visual Studio wybierz pozycję **Narzędzia** > **NuGet Package Manager**> **Package Manager Console**.
2. W oknie konsoli Menedżera pakietów wklej następujące polecenie programu Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > To polecenie instaluje bibliotekę uwierzytelniania firmy Microsoft. Usługa MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkowników, które są używane do uzyskiwania dostępu do interfejsów API chronionych przez usługę Azure Active Directory w wersji 2.0
    >

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aplikację można zarejestrować na dwa sposoby.

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Możesz szybko zarejestrować aplikację, wykonując następujące czynności:
1. Przejdź do [witryny Azure portal — rejestracja aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Wybierz **pozycję Nowa rejestracja**.
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `Win-App-calling-MsGraph`.
   - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)**.
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
   1. W sekcji **Przekierowanie identyfikatorów URI** na liście Przekierowanie identyfikatorów URI:
   1. W kolumnie **TYP** wybierz **pozycję Klient publiczny/natywny (mobilny & pulpit)**.
   1. W kolumnie **REDIRECT URI** wprowadź`https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Wybierz pozycję **Zarejestruj**.
1. Przejdź do programu Visual Studio, otwórz plik `Enter_the_Application_Id_here` *App.xaml.cs,* a następnie zastąp poniższy fragment kodu identyfikatorem aplikacji, który właśnie został zarejestrowany i skopiowany.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Dodaj kod do inicjowania msal

W tym kroku utworzysz klasę do obsługi interakcji z MSAL, takich jak obsługa tokenów.

1. Otwórz plik *App.xaml.cs,* a następnie dodaj odwołanie do msal do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Zaktualizuj klasę aplikacji do następujących czynności:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
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

Plik *MainWindow.xaml* powinien zostać automatycznie utworzony jako część szablonu projektu. Otwórz ten plik, a następnie zastąp węzeł * \<>siatki* aplikacji następującym kodem:

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

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Użyj usługi MSAL, aby uzyskać token dla interfejsu API programu Microsoft Graph

W tej sekcji można użyć msal, aby uzyskać token dla interfejsu API programu Microsoft Graph.

1. W pliku *MainWindow.xaml.cs* dodaj odwołanie do msal do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Zastąp `MainWindow` kod klasy następującym:

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
    ```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Wywołanie `AcquireTokenInteractive` metody powoduje okno, które monituje użytkowników o zalogowanie się. Aplikacje zwykle wymagają od użytkowników interakcyjnego logowania się po raz pierwszy, gdy będą musieli uzyskać dostęp do chronionego zasobu. Może być również konieczne zalogowanie się, gdy operacja dyskretna w celu uzyskania tokenu nie powiedzie się (na przykład, gdy hasło użytkownika wygasło).

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `AcquireTokenSilent` obsługuje nabycia tokenów i odnowień bez interakcji z użytkownikiem. Po `AcquireTokenInteractive` jest wykonywany po `AcquireTokenSilent` raz pierwszy, jest zwykle metoda do użycia w celu uzyskania tokenów, które uzyskują dostęp do chronionych zasobów dla kolejnych wywołań, ponieważ wywołania żądania lub odnowienia tokenów są wykonywane w trybie cichym.

Ostatecznie metoda `AcquireTokenSilent` zakończy się niepowodzeniem. Przyczyną błędu może być to, że użytkownik wylogował lub zmienił hasło na innym urządzeniu. Gdy msal wykryje, że problem można rozwiązać, wymagając `MsalUiRequiredException` akcji interaktywnej, uruchamia wyjątek. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Może natychmiast wywołać `AcquireTokenInteractive` przeciwko. To wywołanie powoduje monitowanie użytkownika o zalogowanie się. Ten wzorzec jest zwykle używany w aplikacjach online, w których nie ma dostępnej zawartości w trybie offline dla użytkownika. Przykład wygenerowany przez tę konfigurację z przewodnikiem jest zgodny z tym wzorcem, który można zobaczyć w akcji przy pierwszym wykonaniu próbki.

* Ponieważ żaden użytkownik nie `PublicClientApp.Users.FirstOrDefault()` użył aplikacji, zawiera `MsalUiRequiredException` wartość null i zgłaszany jest wyjątek.

* Kod w próbce następnie obsługuje wyjątek przez wywołanie `AcquireTokenInteractive`, co powoduje monitowanie użytkownika, aby zalogować się.

* Zamiast tego można przedstawić wizualne wskazanie użytkownikom, że wymagane jest interaktywne logowanie, dzięki czemu mogą wybrać odpowiedni czas logowania. Lub aplikacja może `AcquireTokenSilent` ponowić próbę później. Ten wzorzec jest często używany, gdy użytkownicy mogą korzystać z innych funkcji aplikacji bez zakłóceń — na przykład, gdy zawartość w trybie offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą zdecydować, kiedy chcą zalogować się, aby uzyskać dostęp do chronionego zasobu lub odświeżyć nieaktualne informacje. Alternatywnie aplikacja może podjąć decyzję o ponowieniu próby `AcquireTokenSilent` po przywróceniu sieci po czasowej niedostępności.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który właśnie uzyskałeś

Dodaj następującą nową `MainWindow.xaml.cs`metodę do pliku . Metoda jest używana do `GET` żądania względem interfejsu API wykresu przy użyciu nagłówka Authorize:

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

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat wykonywania wywołań REST za okszewnie chronionego interfejsu API

W tej przykładowej aplikacji `GetHttpContentWithToken` należy użyć `GET` metody, aby zrobić żądanie HTTP względem chronionego zasobu, który wymaga tokenu, a następnie zwrócić zawartość do wywołującego. Ta metoda dodaje nabyty token w nagłówku autoryzacji HTTP. W tym przykładzie zasób jest punktem końcowym interfejsu API programu Microsoft Graph *me,* który wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Dodawanie metody wylogowywania użytkownika

Aby wylogować użytkownika, dodaj `MainWindow.xaml.cs` do pliku następującą metodę:

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

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Więcej informacji o wylogowywania się użytkownika

Metoda `SignOutButton_Click` usuwa użytkowników z pamięci podręcznej użytkownika MSAL, który skutecznie informuje MSAL zapomnieć bieżącego użytkownika, tak aby przyszłe żądanie nabycia tokenu zakończy się pomyślnie tylko wtedy, gdy jest on akcyjne.

Mimo że aplikacja w tym przykładzie obsługuje pojedynczych użytkowników, MSAL obsługuje scenariusze, w których można zalogować wiele kont w tym samym czasie. Przykładem jest aplikacja poczty e-mail, w której użytkownik ma wiele kont.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Wyświetlanie podstawowych informacji o tokenie

Aby wyświetlić podstawowe informacje o tokenie, dodaj następującą metodę do *pliku MainWindow.xaml.cs:*

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

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Oprócz tokenu dostępu, który jest używany do wywoływania interfejsu API programu Microsoft Graph, po zalogowaniu się użytkownika msal uzyskuje również token identyfikatora. Ten token zawiera niewielki podzbiór informacji, który jest odpowiedni dla użytkowników. Metoda `DisplayBasicTokenInfo` wyświetla podstawowe informacje, które są zawarte w tokenie. Na przykład wyświetla nazwę wyświetlaną użytkownika i identyfikator, a także datę wygaśnięcia tokenu i ciąg reprezentujący sam token dostępu. Można wybrać przycisk *Wywołaj interfejs API programu Microsoft Graph* wiele razy i zobaczyć, że ten sam token został ponownie użyczony dla kolejnych żądań. Można również zobaczyć datę wygaśnięcia jest przedłużony, gdy MSAL zdecyduje, że nadszedł czas, aby odnowić token.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
