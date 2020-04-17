---
title: Rozpoczęcie pracy platformy tożsamości firmy Microsoft | Azure
description: Jak aplikacje platformy uniwersalnej systemu Windows (UWP) można wywołać interfejs API, który wymaga tokenów dostępu przez punkt końcowy platformy tożsamości firmy Microsoft.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535948"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Wywoływanie interfejsu API programu Microsoft Graph z aplikacji XAML (Universal Windows Platform)

> [!div renderon="docs"]

W tym przewodniku wyjaśniono, jak natywna aplikacja platformy uniwersalnej systemu Windows (UWP) może zażądać tokenu dostępu. Następnie aplikacja wywołuje interfejs API programu Microsoft Graph. Przewodnik dotyczy również innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego platformy tożsamości firmy Microsoft.

Na końcu tego przewodnika aplikacja wywołuje chroniony interfejs API przy użyciu kont osobistych. Przykładami są outlook.com, live.com i inne. Aplikacja wywołuje również konta służbowe i szkolne z dowolnej firmy lub organizacji, która ma usługę Azure Active Directory (Azure AD).

>[!NOTE]
> Ten przewodnik wymaga programu Visual Studio z zainstalowanym programem Rozwoju platformy systemu Windows. Zobacz [Pobierz instrukcje](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) dotyczące pobierania i konfigurowania programu Visual Studio w celu tworzenia aplikacji platformy uniwersalnej systemu Windows.

>[!NOTE]
> Jeśli jesteś nowy na platformie tożsamości firmy Microsoft, zalecamy rozpoczęcie od [szybkiego startu interfejsu API Microsoft Graph z aplikacji uniwersalnej platformy systemu Windows (UWP).](quickstart-v2-uwp.md)

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Pokazuje, jak działa przykładowa aplikacja generowana przez ten samouczek](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

W tym przewodniku utworzono przykładową aplikację platformy uniwersalnej systemu i platformy uniwersalnej systemu z dostępem do systemu Microsoft Graph API. W tym scenariuszu token jest dodawany do żądań HTTP przy użyciu nagłówka autoryzacji. Biblioteka uwierzytelniania firmy Microsoft (MSAL) obsługuje akwizycje i odnawianie tokenów.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku użyto następującego pakietu NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft|

## <a name="set-up-your-project"></a>konfigurowanie projektu

Ta sekcja zawiera instrukcje krok po kroku dotyczące integracji aplikacji XAML dla pulpitu systemu Windows z logowaniem się za pomocą firmy Microsoft. Następnie aplikacja może wysyłać zapytania do interfejsów API sieci Web, które wymagają tokenu, takich jak microsoft graph interfejsu API.

Ten przewodnik tworzy aplikację, która wyświetla przycisk, który wysyła kwerendy Graph API i przycisk, aby się wylogować. Wyświetla również pola tekstowe, które zawierają wyniki wywołań.

> [!NOTE]
> Czy chcesz pobrać projekt programu Visual Studio w tym przykładzie zamiast go utworzyć? [Pobierz projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) i przejdź do kroku [rejestracji aplikacji,](#register-your-application "etap rejestracji aplikacji") aby skonfigurować przykładowy kod przed jego uruchomieniu.

### <a name="create-your-application"></a>Tworzenie aplikacji

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W **obszarze Tworzenie nowego projektu**wybierz pozycję **Pusta aplikacja (uniwersalny system Windows)** dla języka C# i wybierz pozycję **Dalej**.
1. W **obszarze Konfiguruj nowy projekt**nazwij aplikację i wybierz pozycję **Utwórz**.
1. Jeśli zostanie wyświetlony monit, w **programie New Universal Windows Platform Project**wybierz dowolną wersję dla wersji **docelowych** i **minimalnych** i wybierz **przycisk OK**.

   ![Wersje minimalne i docelowe](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Dodawanie biblioteki uwierzytelniania firmy Microsoft do projektu

1. W programie Visual Studio wybierz pozycję **Narzędzia** > **NuGet Package Manager** > **Package Manager Console**.
1. Skopiuj i wklej następujące polecenie w oknie **Konsoli Menedżera pakietów:**

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > To polecenie instaluje [bibliotekę uwierzytelniania firmy Microsoft](https://aka.ms/msal-net). Msal uzyskuje, buforuje i odświeża tokeny użytkowników, które uzyskują dostęp do interfejsów API chronionych przez platformę tożsamości firmy Microsoft.

### <a name="create-your-applications-ui"></a>Tworzenie interfejsu użytkownika aplikacji

Program Visual Studio tworzy *plik MainPage.xaml* jako część szablonu projektu. Otwórz ten plik, a następnie zastąp węzeł **siatki** aplikacji następującym kodem:

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Użyj usługi MSAL, aby uzyskać token dla interfejsu API programu Microsoft Graph

W tej sekcji pokazano, jak używać usługi MSAL do uzyskania tokenu dla interfejsu API programu Microsoft Graph. Wprowadzanie zmian w pliku *MainPage.xaml.cs.*

1. W *MainPage.xaml.cs*dodaj następujące odwołania:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Zastąp klasę `MainPage` następującym kodem:

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

Metoda `AcquireTokenInteractive` powoduje okno, które monituje użytkowników o zalogowanie się. Aplikacje zwykle wymagają od użytkowników interakcyjnego logowania się za pierwszym razem, aby uzyskać dostęp do chronionego zasobu. Może również trzeba zalogować się, gdy operacja dyskretna, aby uzyskać token nie powiedzie się. Przykładem jest, gdy hasło użytkownika wygasło.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `AcquireTokenSilent` obsługuje nabycia tokenów i odnowień bez interakcji z użytkownikiem. Po `AcquireTokenInteractive` uruchomieniu po raz pierwszy i monituje użytkownika `AcquireTokenSilent` o poświadczenia, użyj metody, aby zażądać tokenów dla późniejszych wywołań. Ta metoda nabywa tokeny po cichu. MSAL obsługuje pamięć podręczną tokenów i odnawianie.

Ostatecznie metoda `AcquireTokenSilent` kończy się niepowodzeniem. Przyczyny niepowodzenia obejmują użytkownika, który wyloguje się lub zmienił hasło na innym urządzeniu. Gdy MSAL wykryje, że problem wymaga `MsalUiRequiredException` akcji interaktywnej, zgłasza wyjątek. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Aplikacja wywołuje `AcquireTokenInteractive` natychmiast. To wywołanie powoduje monitowanie użytkownika o zalogowanie się. Zwykle należy użyć tej metody dla aplikacji online, w których nie ma dostępnej zawartości w trybie offline dla użytkownika. Próbka wygenerowana przez tę konfigurację z przewodnikiem jest zgodna ze wzorcem. Zobaczysz go w akcji przy pierwszym uruchomieniu próbki.

   Ponieważ żaden użytkownik nie `accounts.FirstOrDefault()` użył aplikacji, zawiera wartość `MsalUiRequiredException` null i zgłasza wyjątek.

   Kod w próbce następnie obsługuje wyjątek przez wywołanie `AcquireTokenInteractive`. To wywołanie powoduje monitowanie użytkownika o zalogowanie się.

* Aplikacja przedstawia wizualne wskazanie dla użytkowników, że muszą się zalogować. Następnie mogą wybrać odpowiedni czas na zalogowanie się. Aplikacja może ponowić próbę `AcquireTokenSilent` później. Użyj tej metody, gdy użytkownicy mogą korzystać z innych funkcji aplikacji bez zakłóceń. Przykładem jest, gdy zawartość w trybie offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą zdecydować, kiedy chcą się zalogować. Aplikacja może ponowić próbę `AcquireTokenSilent` po tymczasowo niedostępnej sieci.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywołaj interfejs API programu Microsoft Graph przy użyciu właśnie uzyskanego tokenu

Dodaj następującą nową metodę do *MainPage.xaml.cs:*

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

 Ta metoda `GET` powoduje żądanie z interfejsu `Authorization` API wykresu przy użyciu nagłówka.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat wykonywania wywołania REST względem chronionego interfejsu API

W tej przykładowej `GetHttpContentWithToken` aplikacji metoda `GET` zrobić żądanie HTTP względem chronionego zasobu, który wymaga tokenu. Następnie metoda zwraca zawartość do wywołującego. Ta metoda dodaje nabyty token w nagłówku **autoryzacji HTTP.** W tym przykładzie zasób jest punktem końcowym interfejsu API programu Microsoft Graph **me,** który wyświetla informacje o profilu użytkownika.

### <a name="add-a-method-to-sign-out-the-user"></a>Dodawanie metody wylogowywania użytkownika

Aby wylogować użytkownika, dodaj następującą metodę do *MainPage.xaml.cs:*

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
> MSAL.NET używa metod asynchronicznych do pozyskiwania tokenów lub manipulowania kontami. Musisz obsługiwać akcje interfejsu użytkownika w wątku interfejsu użytkownika. To jest powód `Dispatcher.RunAsync` połączenia i środków ostrożności, aby zadzwonić `ConfigureAwait(false)`.

#### <a name="more-information-about-signing-out"></a>Więcej informacji o wylogowaniu się<a name="more-information-on-sign-out"></a>

Metoda `SignOutButton_Click` usuwa użytkownika z pamięci podręcznej użytkownika MSAL. Ta metoda skutecznie informuje MSAL, aby zapomnieć bieżącego użytkownika. Przyszłe żądanie uzyskania tokenu powiedzie się tylko wtedy, gdy jest interaktywne.

Aplikacja w tym przykładzie obsługuje jednego użytkownika. MsAL obsługuje scenariusze, w których użytkownik może zalogować się na więcej niż jednym koncie. Przykładem jest aplikacja poczty e-mail, w której użytkownik ma kilka kont.

### <a name="display-basic-token-information"></a>Wyświetlanie podstawowych informacji o tokenie

Dodaj następującą metodę, aby *MainPage.xaml.cs,* aby wyświetlić podstawowe informacje o tokenie:

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

Tokeny identyfikatora nabyte przy użyciu **OpenID Connect** zawierają również niewielki podzbiór informacji istotnych dla użytkownika. `DisplayBasicTokenInfo`wyświetla podstawowe informacje zawarte w tokenie. Informacje te obejmują nazwę wyświetlaną i identyfikator użytkownika. Zawiera również datę wygaśnięcia tokenu i ciąg, który reprezentuje sam token dostępu. Jeśli wybierzesz przycisk **Wywołaj interfejs API programu Microsoft Graph** kilka razy, zobaczysz, że ten sam token został ponownie użyczony dla późniejszych żądań. Można również zobaczyć datę wygaśnięcia przedłużony, gdy MSAL decyduje, że nadszedł czas, aby odnowić token.

### <a name="display-message"></a>Komunikat wyświetlany

Dodaj następującą nową metodę do *MainPage.xaml.cs:*

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

Teraz musisz zarejestrować swoją aplikację:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz**rejestracje aplikacji** **usługi Azure Active Directory** > .
1. Wybierz **pozycję Nowa rejestracja**. Wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład *UWP-App-calling-MSGraph*.
1. W obszarze **Obsługiwane typy kont**wybierz **pozycję Konta w dowolnym katalogu organizacji i osobistych kontach Microsoft (np.** **Register**
1. Na stronie przegląd znajdź wartość **identyfikatora aplikacji (klienta)** i skopiuj ją. Wróć do programu Visual Studio, otwórz *MainPage.xaml.cs*i `ClientId` zastąp wartość tą wartością.

Konfigurowanie uwierzytelniania dla aplikacji:

1. Powrót do [portalu Azure](https://portal.azure.com)w obszarze **Zarządzanie**wybierz pozycję **Uwierzytelnianie**.
1. W sekcji **Redirect URIs** | **Suggested Redirect Redirect URI dla klientów publicznych (mobilnych, stacjonarnych)** zaznacz pole wyboru **https://login.microsoftonline.com/common/oauth2/nativeclient**.
1. Wybierz pozycję **Zapisz**.

Konfigurowanie uprawnień interfejsu API dla aplikacji:

1. W obszarze **Zarządzanie**wybierz pozycję **Uprawnienia interfejsu API**.
1. Wybierz **pozycję Dodaj uprawnienie,** a następnie upewnij się, że wybrano **interfejsy API firmy Microsoft**.
1. Wybierz microsoft **graph**.
1. Wybierz **pozycję Delegowane uprawnienia**, wyszukaj *user.Read* i sprawdź, czy wybrano **opcję User.Read.**
1. Jeśli wprowadzono jakiekolwiek zmiany, wybierz **pozycję Dodaj uprawnienia,** aby je zapisać.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Włącz zintegrowane uwierzytelnianie w domenach federacyjnej (opcjonalnie)

Aby włączyć uwierzytelnianie zintegrowane z systemem Windows, gdy jest używane z federowaną domeną usługi Azure AD, manifest aplikacji musi włączyć dodatkowe funkcje. Wróć do aplikacji w programie Visual Studio.

1. Otwórz *Package.appxmanifest*.
1. Wybierz **pozycję Możliwości** i włącz następujące ustawienia:

   * **Uwierzytelnianie przedsiębiorstwa**
   * **Sieci prywatne (serwer & klienta)**
   * **Udostępnione certyfikaty użytkowników**

> [!IMPORTANT]
> [Zintegrowane uwierzytelnianie systemu Windows](https://aka.ms/msal-net-iwa) nie jest domyślnie skonfigurowane dla tego przykładu. Aplikacje, `Enterprise Authentication` `Shared User Certificates` które żądają lub możliwości wymagają wyższego poziomu weryfikacji przez Sklep Windows. Ponadto nie wszyscy deweloperzy chcą przeprowadzić wyższy poziom weryfikacji. Włącz to ustawienie tylko wtedy, gdy potrzebujesz zintegrowanego uwierzytelniania systemu Windows z federowaną domeną usługi Azure AD.

## <a name="test-your-code"></a>testowanie kodu

Aby przetestować aplikację, wybierz F5, aby uruchomić projekt w programie Visual Studio. Pojawi się okno główne:

![Interfejs użytkownika aplikacji](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Gdy będziesz gotowy do testowania, wybierz pozycję **Wywołaj interfejs API programu Microsoft Graph**. Następnie użyj konta organizacyjnego usługi Azure AD lub konta Microsoft, takiego jak live.com lub outlook.com, aby się zalogować. Przy pierwszym uruchomieniu tego użytkownika aplikacja wyświetla okno z prośbą o zalogowanie się użytkownika.

### <a name="consent"></a>Zgody

Przy pierwszym logowaniu się do aplikacji zostanie wyświetlony ekran zgody podobny do następującego. Wybierz **opcję Tak,** aby jawnie wyrazić zgodę na dostęp:

![Ekran zgody na dostęp](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Oczekiwane wyniki

Informacje o profilu użytkownika zwrócone przez wywołanie interfejsu API programu Microsoft Graph na ekranie **Wyniki wywołania interfejsu API:**

![Ekran Wyniki wywołania interfejsu API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Zobaczysz również podstawowe informacje o `AcquireTokenInteractive` tokenie nabytym za pośrednictwem lub `AcquireTokenSilent` w polu **Informacje o tokenie:**

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|`Username` |`user@domain.com` |Nazwa użytkownika identyfikującego użytkownika.|
|`Token Expires` |`DateTime` |Czas wygaśnięcia tokenu. MSAL rozszerza datę wygaśnięcia, odnawiając token w razie potrzeby.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i uprawnieniach delegowanych

Interfejs API programu `user.read` Microsoft Graph wymaga zakresu odczytu profilu użytkownika. Ten zakres jest domyślnie dodawany w każdej aplikacji, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API programu Microsoft Graph i niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API programu `Calendars.Read` Microsoft Graph wymaga zakresu do listy kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście `Calendars.Read` aplikacji, dodaj delegowane uprawnienia do informacji o rejestracji aplikacji. Następnie dodaj `Calendars.Read` zakres `acquireTokenSilent` do wywołania.

> [!NOTE]
> Użytkownicy mogą być monitowani o dodatkowe zgody w miarę zwiększania liczby zakresów.

## <a name="known-issues"></a>Znane problemy

### <a name="issue-1"></a>Problem 1

Podczas logowania się w aplikacji w federacyjnej domenie usługi Azure AD pojawia się jeden z następujących komunikatów o błędach:

* W żądaniu nie znaleziono prawidłowego certyfikatu klienta.
* W magazynie certyfikatów użytkownika nie znaleziono prawidłowych certyfikatów.
* Spróbuj ponownie wybrać inną metodę uwierzytelniania.

Przyczyna: Możliwości przedsiębiorstwa i certyfikatów nie są włączone.

Rozwiązanie: Wykonaj kroki opisane w [obszarze Włącz zintegrowane uwierzytelnianie w domenach federacyjnej (opcjonalnie).](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Problem 2

Włącz [zintegrowane uwierzytelnianie w domenach federacyjnej](#enable-integrated-authentication-on-federated-domains-optional) i spróbuj użyć funkcji Windows Hello na komputerze z systemem Windows 10 do logowania się do środowiska z skonfigurowanym uwierzytelnianiem wieloskładnikowym. Zostanie wyświetlona lista certyfikatów. Jeśli jednak zdecydujesz się użyć numeru PIN, okno KOD PIN nigdy nie zostanie wyświetlone.

Przyczyna: Ten problem jest znanym ograniczeniem brokera uwierzytelniania sieci web w aplikacjach platformy uniwersalnej systemu Windows, które działają na pulpicie systemu Windows 10. To działa dobrze na Windows 10 Mobile.

Obejście: wybierz pozycję **Zaloguj się przy innych opcjach**. Następnie wybierz pozycję **Zaloguj się przy użyciu nazwy użytkownika i hasła**. Wybierz **pozycję Podaj hasło**. Następnie przejdź przez proces uwierzytelniania telefonu.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
