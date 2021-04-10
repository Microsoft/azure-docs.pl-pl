---
title: Samouczek — Logowanie użytkowników i wywoływanie chronionego interfejsu API z aplikacji Blazor webassembly
titleSuffix: Microsoft identity platform
description: W tym samouczku zalogowanie użytkowników i wywołanie chronionego interfejsu API przy użyciu platformy tożsamości firmy Microsoft w aplikacji Blazor webassembly (WASM).
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 47b4f36aec9a906317a9704a7d73bf66385d9e88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552120"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Samouczek: Logowanie użytkowników i wywoływanie chronionego interfejsu API z aplikacji Blazor webassembly

W tym samouczku utworzysz aplikację webassembly Blazor, która loguje użytkowników i pobiera dane z Microsoft Graph przy użyciu platformy tożsamości firmy Microsoft i rejestrowania aplikacji w usłudze Azure Active Directory (Azure AD). 

W tym samouczku:

> [!div class="checklist"]
>
> * Utwórz nową aplikację Blazor webassembly skonfigurowaną do korzystania z Azure Active Directory (Azure AD) na potrzeby [uwierzytelniania i autoryzacji](authentication-vs-authorization.md) przy użyciu platformy tożsamości firmy Microsoft
> * Pobieranie danych z chronionego internetowego interfejsu API, w tym przypadku [Microsoft Graph](/graph/overview)

Ten samouczek używa programu .NET Core 3,1. Dokumentacja platformy .NET zawiera instrukcje dotyczące [zabezpieczania aplikacji Blazor webassembly](/aspnet/core/blazor/security/webassembly/graph-api) przy użyciu ASP.NET Core 5,0. 

Mamy również [Samouczek dotyczący serwera Blazor](tutorial-blazor-server.md). 

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Dzierżawa usługi Azure AD, w której można zarejestrować aplikację. Jeśli nie masz dostępu do dzierżawy usługi Azure AD, możesz ją uzyskać, rejestrując się w [programie dla deweloperów Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) lub tworząc [bezpłatne konto platformy Azure](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Zarejestruj aplikację w Azure Portal

Każda aplikacja, która używa Azure Active Directory (Azure AD) do uwierzytelniania, musi być zarejestrowana w usłudze Azure AD. Postępuj zgodnie z instrukcjami w temacie [Rejestrowanie aplikacji](quickstart-register-app.md) z następującymi specyfikacjami:

- W przypadku **obsługiwanych typów kont** wybierz opcję **konta tylko w tym katalogu organizacji**.
- Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i wprowadź wartość `https://localhost:5001/authentication/login-callback` . Domyślnym portem dla aplikacji działającej w Kestrel jest 5001. Jeśli aplikacja jest dostępna na innym porcie, określ ten numer portu zamiast `5001` .

Po zarejestrowaniu w obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**  >  **niejawne i przepływy hybrydowe**. Wybierz pozycję **tokeny dostępu** i **tokeny identyfikatorów**, a następnie wybierz pozycję **Zapisz**.

## <a name="create-the-app-using-the-net-core-cli"></a>Tworzenie aplikacji przy użyciu interfejs wiersza polecenia platformy .NET Core

Aby utworzyć aplikację, potrzebujesz najnowszych szablonów Blazor. Można je zainstalować dla interfejs wiersza polecenia platformy .NET Core za pomocą następującego polecenia:

```dotnetcli
dotnet new -i Microsoft.Identity.Web.ProjectTemplates::1.6.0
```

Następnie uruchom następujące polecenie, aby utworzyć aplikację. Zastąp symbole zastępcze w poleceniu odpowiednimi informacjami na stronie Przegląd aplikacji i wykonaj polecenie w powłoce poleceń. Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Symbol zastępczy   | Nazwa Azure Portal       | Przykład                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | Identyfikator aplikacji (klienta) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Identyfikator katalogu (dzierżawcy)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Testowanie aplikacji

Teraz możesz skompilować i uruchomić aplikację. Po uruchomieniu tej aplikacji szablonu należy określić strukturę do uruchomienia za pomocą--Framework. Ten samouczek używa .NET Standard 2,1, ale szablon obsługuje również inne struktury.

```dotnetcli
dotnet run --framework netstandard2.1
```

W przeglądarce przejdź do `https://localhost:5001` , a następnie zaloguj się przy użyciu konta użytkownika usługi Azure AD, aby zobaczyć aplikację działającą i logujący użytkowników przy użyciu platformy tożsamości firmy Microsoft.

Składniki tego szablonu, które umożliwiają logowanie za pomocą usługi Azure AD przy użyciu platformy tożsamości firmy Microsoft, objaśniono w [dokumencie ASP.NET w tym temacie](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>Pobieranie danych z chronionego interfejsu API (Microsoft Graph)

[Microsoft Graph](/graph/overview) zawiera interfejsy API zapewniające dostęp do Microsoft 365 danych dla użytkowników i obsługuje tokeny wystawione przez platformę tożsamości firmy Microsoft, co sprawia, że jest to dobry chroniony interfejs API do użycia jako przykład. W tej sekcji dodasz kod do wywoływania Microsoft Graph i wyświetlania wiadomości e-mail użytkownika na stronie "pobieranie danych" aplikacji.

Ta sekcja jest zapisywana przy użyciu typowego podejścia do wywoływania chronionego interfejsu API przy użyciu nazwanego klienta. Tej samej metody można użyć dla innych chronionych interfejsów API, które mają być wywoływane. Jeśli jednak planujesz wywołać Microsoft Graph z aplikacji, możesz użyć zestawu SDK programu Graph, aby zmniejszyć liczbę standardowych. Dokumentacja platformy .NET zawiera instrukcje dotyczące [korzystania z zestawu Graph SDK](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true).

Przed rozpoczęciem Wyloguj się z aplikacji, ponieważ będziesz wprowadzać zmiany w wymaganych uprawnieniach, a bieżący token nie będzie działać. Jeśli jeszcze tego nie zrobiono, uruchom ponownie aplikację i wybierz opcję **Wyloguj** się przed aktualizacją poniższego kodu.

Teraz zaktualizujesz rejestrację i kod aplikacji w celu ściągnięcia wiadomości e-mail użytkownika i wyświetlenia komunikatów w aplikacji.

Najpierw Dodaj `Mail.Read` uprawnienie API do rejestracji aplikacji, aby usługa Azure AD wie, że aplikacja będzie żądać dostępu do wiadomości e-mail użytkowników.

1. W Azure Portal wybierz aplikację w **rejestracje aplikacji**.
1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**.
1. Wybierz pozycję **Dodaj**  >  **Microsoft Graph** uprawnienia.
1. Wybierz pozycję **uprawnienia delegowane**, a następnie wyszukaj i wybierz uprawnienie **poczta. odczyt** .
1. Wybierz pozycję **Dodaj uprawnienia**.

Następnie Dodaj następujący **element** do pliku *. csproj* projektu w programie. Pozwoli to utworzyć niestandardowe HttpClient w następnym kroku.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Następnie zmodyfikuj kod określony w następnych kilku krokach. Te zmiany spowodują dodanie [tokenów dostępu](access-tokens.md) do żądań wychodzących wysyłanych do interfejsu API Microsoft Graph. Ten wzorzec został szczegółowo omówiony w temacie [ASP.NET Core Blazor webassembly dodatkowe scenariusze zabezpieczeń](/aspnet/core/blazor/security/webassembly/additional-scenarios).

Najpierw utwórz nowy plik o nazwie *GraphAPIAuthorizationMessageHandler. cs* z poniższym kodem. Ta procedura obsługi będzie dodawać token dostępu dla `User.Read` `Mail.Read` zakresów i do żądań wychodzących do interfejsu API Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Następnie zastąp zawartość `Main` metody w *programie program. cs* następującym kodem. Ten kod wykorzystuje nowe `GraphAPIAuthorizationMessageHandler` i dodatkowe zakresy, które `User.Read` `Mail.Read` aplikacja będzie żądać, gdy użytkownik po raz pierwszy zaloguje się.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Na koniec Zastąp zawartość strony *FetchData. Razor* następującym kodem. Ten kod pobiera dane e-mail użytkownika z interfejsu API Microsoft Graph i wyświetla je jako listę. W programie `OnInitializedAsync` Nowy, `HttpClient` który używa odpowiedniego tokenu dostępu, jest tworzony i używany do wysyłania żądania do interfejsu API Microsoft Graph.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Teraz ponownie uruchom aplikację. Zobaczysz, że zostanie wyświetlony monit o przyznanie aplikacji dostępu do odczytu wiadomości e-mail. Jest to oczekiwane, gdy aplikacja żąda `Mail.Read` zakresu.

Po udzieleniu zgody przejdź na stronę "pobieranie danych", aby odczytać wiadomości e-mail.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Zrzut ekranu przedstawiający ostateczną aplikację. Ma nagłówek o powitaniu Hello Nicholas i zawiera listę wiadomości e-mail należących do Nicholas.":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania i zalecenia dotyczące platformy tożsamości firmy Microsoft](./identity-platform-integration-checklist.md)
