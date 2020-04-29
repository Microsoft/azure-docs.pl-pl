---
title: Microsoft Identity platform platformy UWP — wprowadzenie | Azure
description: Jak aplikacje platforma uniwersalna systemu Windows (platformy UWP) mogą wywołać interfejs API, który wymaga tokenów dostępu przez punkt końcowy platformy tożsamości firmy Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0dc70aa67a1414c08ec70e2e034f4ab12b194c0a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535948"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Wywoływanie interfejsu API Microsoft Graph z aplikacji platforma uniwersalna systemu Windows (XAML)

> [!div renderon="docs"]

W tym przewodniku wyjaśniono, jak aplikacja natywna platforma uniwersalna systemu Windows (platformy UWP) może zażądać tokenu dostępu. Aplikacja następnie wywołuje Microsoft Graph API. Przewodnik dotyczy również innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego platformy tożsamości firmy Microsoft.

Na końcu tego przewodnika aplikacja wywołuje chroniony interfejs API przy użyciu kont osobistych. Przykłady to outlook.com, live.com i inne. Aplikacja wywołuje również konta służbowe z dowolnej firmy lub organizacji, która ma Azure Active Directory (Azure AD).

>[!NOTE]
> Ten przewodnik wymaga programu Visual Studio z zainstalowaną platforma uniwersalna systemu Windows programowaniem. Zobacz sekcję [Pobierz konfigurację](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) , aby uzyskać instrukcje dotyczące pobierania i konfigurowania programu Visual Studio do opracowywania aplikacji platforma uniwersalna systemu Windows.

>[!NOTE]
> Jeśli dopiero zaczynasz pracę z platformą tożsamości firmy Microsoft, zalecamy rozpoczęcie od [wywołania interfejsu API Microsoft Graph z aplikacji platforma uniwersalna systemu Windows (platformy UWP)](quickstart-v2-uwp.md).

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

W tym przewodniku przedstawiono Tworzenie przykładowej aplikacji platformy UWP, która wysyła zapytania do Microsoft Graph API. W tym scenariuszu token jest dodawany do żądań HTTP przy użyciu nagłówka autoryzacji. Biblioteka Microsoft Authentication Library (MSAL) obsługuje pozyskiwanie i odnawianie tokenów.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku zastosowano następujący pakiet NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft|

## <a name="set-up-your-project"></a>konfigurowanie projektu

Ta sekcja zawiera instrukcje krok po kroku dotyczące integrowania aplikacji .NET Desktop (XAML) z logowaniem się z firmą Microsoft. Następnie aplikacja może wykonywać zapytania dotyczące internetowych interfejsów API, które wymagają tokenu, takiego jak Microsoft Graph API.

Ten przewodnik tworzy aplikację, która wyświetla przycisk, który wykonuje zapytania interfejs API programu Graph i przycisk do wylogowania. Wyświetla również pola tekstowe, które zawierają wyniki wywołań.

> [!NOTE]
> Czy chcesz pobrać ten przykładowy projekt programu Visual Studio, zamiast tworzyć go? [Pobierz projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) i przejdź do kroku [rejestracji aplikacji](#register-your-application "krok rejestracji aplikacji") , aby skonfigurować przykładowy kod przed jego uruchomieniem.

### <a name="create-your-application"></a>Tworzenie aplikacji

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt**wybierz pozycję **pusta aplikacja (uniwersalna platforma Windows)** dla języka C# i wybierz pozycję **dalej**.
1. W obszarze **Konfigurowanie nowego projektu**Nadaj nazwę aplikacji, a następnie wybierz pozycję **Utwórz**.
1. Jeśli zostanie wyświetlony monit, w oknie **Nowy projekt platforma uniwersalna systemu Windows**wybierz dowolną wersję dla wersji **docelowej** i **minimalnej** , a następnie wybierz **przycisk OK**.

   ![Wersje minimalne i docelowe](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Dodawanie biblioteki uwierzytelniania firmy Microsoft do projektu

1. W programie Visual Studio wybierz kolejno pozycje **Narzędzia** > **Menedżer pakietów** > NuGet**konsola Menedżera pakietów**.
1. Skopiuj i wklej następujące polecenie w oknie **konsola Menedżera pakietów** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > To polecenie powoduje zainstalowanie [biblioteki uwierzytelniania firmy Microsoft](https://aka.ms/msal-net). MSAL uzyskuje, buforuje i odświeża tokeny użytkowników, które uzyskują dostęp do interfejsów API chronionych przez platformę tożsamości firmy Microsoft.

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Użyj MSAL, aby uzyskać token dla Microsoft Graph interfejsu API

W tej sekcji pokazano, jak uzyskać token Microsoft Graph API przy użyciu programu MSAL. Wprowadź zmiany w pliku *MainPage.XAML.cs* .

1. W *MainPage.XAML.cs*Dodaj następujące odwołania:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Zastąp `MainPage` klasę następującym kodem:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        public IPublicClientApplication PublicClientApp { get; }

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

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
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika<a name="more-information"></a>

`AcquireTokenInteractive` Metoda powoduje pojawienie się okna z monitowaniem użytkowników o zalogowanie się. Aplikacje zwykle wymagają od użytkowników logowania się interakcyjnie po raz pierwszy w celu uzyskania dostępu do chronionego zasobu. Może być również konieczne zalogowanie się, gdy operacja dyskretna uzyskiwania tokenu nie powiedzie się. Przykładem jest to, że hasło użytkownika wygasło.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`AcquireTokenSilent` Metoda obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Po `AcquireTokenInteractive` uruchomieniu po raz pierwszy i Monituj użytkownika o poświadczenia, użyj `AcquireTokenSilent` metody, aby zażądać tokenów w celu późniejszego wywołania. Ta metoda uzyskuje tokeny w trybie dyskretnym. MSAL obsługuje pamięć podręczną i odnawianie tokenów.

Ostatecznie `AcquireTokenSilent` Metoda kończy się niepowodzeniem. Przyczyny niepowodzenia obejmują użytkownika, który wylogowano lub zmienił hasło na innym urządzeniu. Gdy MSAL wykryje, że problem wymaga interaktywnej akcji, zgłasza `MsalUiRequiredException` wyjątek. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Aplikacja zostanie natychmiast `AcquireTokenInteractive` wywołana. To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika. Zwykle należy używać tej metody w przypadku aplikacji online, w przypadku których użytkownik nie ma dostępnej zawartości w trybie offline. Przykład wygenerowany przez tę konfigurację z przewodnikiem jest zgodny ze wzorcem. Zobaczysz ją w działaniu przy pierwszym uruchomieniu przykładu.

   Ponieważ żaden użytkownik nie użył aplikacji, `accounts.FirstOrDefault()` zawiera wartość null i zgłasza `MsalUiRequiredException` wyjątek.

   Kod w przykładzie przechwytuje wyjątek przez wywołanie `AcquireTokenInteractive`. To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika.

* Twoja aplikacja prezentuje wizualne wskazanie użytkownikom, którzy muszą się zalogować. Następnie mogą wybrać odpowiedni czas na zalogowanie się. Aplikacja może ponowić próbę `AcquireTokenSilent` później. Użyj tej metody, jeśli użytkownicy mogą korzystać z innych funkcji aplikacji bez zakłóceń. Przykładem jest to, że zawartość w trybie offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą zdecydować się na zalogowanie się. Aplikacja może ponowić próbę `AcquireTokenSilent` , gdy sieć była tymczasowo niedostępna.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywoływanie interfejsu API Microsoft Graph przy użyciu właśnie uzyskanego tokenu

Dodaj następującą nową metodę do *MainPage.XAML.cs*:

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
           // Add the token in Authorization header
           request.Headers.Authorization =
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

 Ta metoda wysyła `GET` żądanie z interfejs API programu Graph przy użyciu `Authorization` nagłówka.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W tej przykładowej aplikacji `GetHttpContentWithToken` Metoda przetworzy żądanie HTTP `GET` względem chronionego zasobu, który wymaga tokenu. Następnie metoda zwraca zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w nagłówku **autoryzacji http** . Dla tego przykładu zasób **jest punktem** końcowym usługi Microsoft Graph API, który wyświetla informacje o profilu użytkownika.

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

> [!NOTE]
> MSAL.NET używa metod asynchronicznych do pozyskiwania tokenów lub manipulowania kontami. Należy obsługiwać akcje interfejsu użytkownika w wątku interfejsu użytkownika. Jest to powód `Dispatcher.RunAsync` wywołania i środków ostrożności do wywołania `ConfigureAwait(false)`.

#### <a name="more-information-about-signing-out"></a>Więcej informacji na temat wylogowywania<a name="more-information-on-sign-out"></a>

`SignOutButton_Click` Metoda usuwa użytkownika z pamięci podręcznej użytkownika MSAL. Ta metoda skutecznie informuje MSAL o zapomnieniu bieżącego użytkownika. Przyszłe żądanie uzyskania tokenu powiedzie się tylko wtedy, gdy jest ono interaktywne.

Aplikacja w tym przykładzie obsługuje jednego użytkownika. MSAL obsługuje scenariusze, w których użytkownik może zalogować się na więcej niż jednym koncie. Przykładem jest aplikacja poczty e-mail, w której użytkownik ma kilka kont.

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

Tokeny identyfikatorów nabyte za pomocą programu **OpenID Connect Connect** również zawierają niewielki podzbiór informacji dotyczących użytkownika. `DisplayBasicTokenInfo`Wyświetla podstawowe informacje zawarte w tokenie. Te informacje obejmują nazwę wyświetlaną użytkownika i identyfikator. Zawiera również datę wygaśnięcia tokenu i ciąg, który reprezentuje token dostępu. W przypadku wybrania przycisku **Wywołaj Microsoft Graph interfejsu API** kilka razy zobaczysz, że ten sam token został ponownie użyty na potrzeby kolejnych żądań. Można także zobaczyć datę wygaśnięcia rozszerzoną, gdy MSAL zdecyduje, że czas odnowienia tokenu.

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

Teraz musisz zarejestrować aplikację:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **Azure Active Directory** > **rejestracje aplikacji**Azure Active Directory.
1. Wybierz pozycję **Nowa rejestracja**. Wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład *platformy UWP-App-Call-MSGraph*.
1. W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox)**, a następnie wybierz pozycję **zarejestruj** , aby kontynuować.
1. Na stronie Przegląd Znajdź wartość **Identyfikator aplikacji (klienta)** i skopiuj ją. Wróć do programu Visual Studio, Otwórz *MainPage.XAML.cs*i Zastąp wartość `ClientId` tą wartością.

Skonfiguruj uwierzytelnianie dla aplikacji:

1. Wróć do [Azure Portal](https://portal.azure.com), w obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**.
1. W sekcji **adresy** | URI przekierowania**sugerowane identyfikatory URI dla klientów publicznych (Mobile, Desktop)** zaznacz pole wyboru **https://login.microsoftonline.com/common/oauth2/nativeclient**.
1. Wybierz pozycję **Zapisz**.

Skonfiguruj uprawnienia interfejsu API dla aplikacji:

1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. Wybierz pozycję **Dodaj uprawnienia** , a następnie upewnij się, że zostały wybrane **interfejsy API firmy Microsoft**.
1. Wybierz **Microsoft Graph**.
1. Wybierz pozycję **uprawnienia delegowane**, Wyszukaj pozycję *użytkownik. Przeczytaj* i sprawdź, czy wybrano opcję **użytkownik. odczyt** .
1. Jeśli wprowadzono jakiekolwiek zmiany, wybierz pozycję **Dodaj uprawnienia** , aby je zapisać.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Włącz uwierzytelnianie zintegrowane w domenach federacyjnych (opcjonalnie)

Aby włączyć uwierzytelnianie zintegrowane systemu Windows, gdy jest ono używane z federacyjną domeną usługi Azure AD, manifest aplikacji musi włączyć dodatkowe możliwości. Wróć do aplikacji w programie Visual Studio.

1. Otwórz *pakiet Package. appxmanifest*.
1. Wybierz pozycję **możliwości** i Włącz następujące ustawienia:

   * **Uwierzytelnianie w przedsiębiorstwie**
   * **Sieci prywatne (serwer & klienta)**
   * **Udostępnione certyfikaty użytkowników**

> [!IMPORTANT]
> [Zintegrowane uwierzytelnianie systemu Windows](https://aka.ms/msal-net-iwa) nie jest domyślnie skonfigurowane dla tego przykładu. Aplikacje, które `Enterprise Authentication` żądają lub `Shared User Certificates` możliwości wymagają wyższego poziomu weryfikacji w Sklepie Windows. Ponadto nie wszyscy deweloperzy chcą przeprowadzić wyższy poziom weryfikacji. Włącz to ustawienie tylko wtedy, gdy wymagane jest zintegrowane uwierzytelnianie systemu Windows z federacyjną domeną usługi Azure AD.

## <a name="test-your-code"></a>testowanie kodu

Aby przetestować aplikację, wybierz F5, aby uruchomić projekt w programie Visual Studio. Zostanie wyświetlone okno główne:

![Interfejs użytkownika aplikacji](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Gdy wszystko będzie gotowe do przetestowania, wybierz pozycję **Wywołaj Microsoft Graph interfejs API**. Aby się zalogować, użyj konta organizacyjnego usługi Azure AD lub konto Microsoft, takiego jak live.com lub outlook.com. Podczas pierwszego uruchomienia tego użytkownika aplikacja wyświetli okno z prośbą o zalogowanie się użytkownika.

### <a name="consent"></a>Posiadacz

Po pierwszym zalogowaniu się do aplikacji jest wyświetlany ekran zgody podobny do poniższego. Wybierz pozycję **tak** , aby jawnie wyrazić zgodę na dostęp:

![Ekran zgody na dostęp](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Oczekiwane wyniki

Informacje o profilu użytkownika zwracane przez wywołanie interfejsu API Microsoft Graph na ekranie **wyniki wywołania interfejsu API** :

![Ekran wyników wywołań interfejsu API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Widoczne są również podstawowe informacje o tokenie uzyskanym `AcquireTokenInteractive` za `AcquireTokenSilent` pośrednictwem lub w polu **Informacje o tokenie** :

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|`Username` |`user@domain.com` |Nazwa użytkownika.|
|`Token Expires` |`DateTime` |Czas wygaśnięcia tokenu. MSAL rozszerza datę wygaśnięcia przez odnowienie tokenu w razie potrzeby.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga `user.read` zakresu odczytywania profilu użytkownika. Ten zakres jest domyślnie dodawany w każdej aplikacji, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla Microsoft Graph i niestandardowych interfejsów API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład, Microsoft Graph API wymaga `Calendars.Read` zakresu, aby wyświetlić kalendarze użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, Dodaj `Calendars.Read` delegowane uprawnienie do informacji o rejestracji aplikacji. Następnie Dodaj `Calendars.Read` zakres do `acquireTokenSilent` wywołania.

> [!NOTE]
> Po zwiększeniu liczby zakresów użytkownicy mogą otrzymywać monity o dodatkowe przesłanie.

## <a name="known-issues"></a>Znane problemy

### <a name="issue-1"></a>Problem 1

Po zalogowaniu się do aplikacji w federacyjnej domenie usługi Azure AD zostanie wyświetlony jeden z następujących komunikatów o błędach:

* Nie znaleziono prawidłowego certyfikatu klienta w żądaniu.
* Nie znaleziono prawidłowych certyfikatów w magazynie certyfikatów użytkownika.
* Spróbuj ponownie wybrać inną metodę uwierzytelniania.

Przyczyna: możliwości przedsiębiorstwa i certyfikatów nie są włączone.

Rozwiązanie: wykonaj kroki opisane w temacie [Włączanie uwierzytelniania zintegrowanego w domenach federacyjnych (opcjonalnie)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problem 2

[Uwierzytelnianie zintegrowane można włączyć w domenach federacyjnych](#enable-integrated-authentication-on-federated-domains-optional) i próbować użyć usługi Windows Hello na komputerze z systemem Windows 10 w celu zalogowania się do środowiska z skonfigurowanym uwierzytelnianiem wieloskładnikowym. Zostanie wyświetlona lista certyfikatów. Jeśli jednak zdecydujesz się użyć numeru PIN, okno numeru PIN nigdy nie zostanie wyświetlone.

Przyczyna: ten problem jest znanym ograniczeniem brokera uwierzytelniania w sieci Web w aplikacjach platformy UWP działających na komputerze stacjonarnym z systemem Windows 10. Działa prawidłowo w systemie Windows 10 Mobile.

Obejście: wybierz pozycję **Zaloguj się z innymi opcjami**. Następnie wybierz pozycję **Zaloguj się przy użyciu nazwy użytkownika i hasła**. Wybierz pozycję **Podaj hasło**. Następnie przejdź przez proces uwierzytelniania telefonu.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
