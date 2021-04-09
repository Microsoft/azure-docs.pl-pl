---
title: Samouczek — Tworzenie aplikacji serwera Blazor, która używa platformy tożsamości firmy Microsoft do uwierzytelniania | Azure
titleSuffix: Microsoft identity platform
description: W tym samouczku skonfigurujesz uwierzytelnianie przy użyciu platformy tożsamości firmy Microsoft w aplikacji serwera Blazor.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 5a631d9ae7a7d1792e3c4e4a2cbf8281e1168283
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226020"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Samouczek: Tworzenie aplikacji serwera Blazor, która używa platformy tożsamości firmy Microsoft do uwierzytelniania

W tym samouczku utworzysz aplikację serwera Blazor, która loguje się do użytkowników i pobiera dane z Microsoft Graph przy użyciu platformy tożsamości firmy Microsoft i rejestrowania aplikacji w usłudze Azure Active Directory (Azure AD).

Mamy również samouczek dotyczący [BLAZOR WASM](tutorial-blazor-webassembly.md).

W tym samouczku:

> [!div class="checklist"]
> * Utwórz nową aplikację serwera Blazor skonfigurowaną do korzystania z Azure Active Directory (Azure AD) na potrzeby uwierzytelniania
> * Obsługa uwierzytelniania i autoryzacji przy użyciu aplikacji Microsoft. Identity. Web
> * Pobieranie danych z chronionego internetowego interfejsu API, Microsoft Graph

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Dzierżawa usługi Azure AD, w której można zarejestrować aplikację. Jeśli nie masz dostępu do dzierżawy usługi Azure AD, możesz ją uzyskać, rejestrując się w [programie dla deweloperów Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) lub tworząc [bezpłatne konto platformy Azure](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Zarejestruj aplikację w Azure Portal

Każda aplikacja, która używa Azure Active Directory (Azure AD) do uwierzytelniania, musi być zarejestrowana w usłudze Azure AD. Postępuj zgodnie z instrukcjami w temacie [Rejestrowanie aplikacji](quickstart-register-app.md) z następującymi dodatkami:

- W przypadku **obsługiwanych typów kont** wybierz opcję **konta tylko w tym katalogu organizacji**.
- Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i wprowadź wartość `https://localhost:5001/signin-oidc` . Domyślnym portem dla aplikacji działającej w Kestrel jest 5001. Jeśli aplikacja jest dostępna na innym porcie, określ ten numer portu zamiast `5001` .

W obszarze **Zarządzaj** wybierz opcję **uwierzytelnianie**  >  **niejawne i przepływy hybrydowe**. Wybierz pozycję **tokeny dostępu** i **tokeny identyfikatorów**, a następnie wybierz pozycję **Zapisz**.

Na koniec, ponieważ aplikacja wywołuje chroniony interfejs API (w tym przypadku Microsoft Graph), wymaga klucza tajnego klienta, aby zweryfikować jego tożsamość, gdy żąda tokenu dostępu do wywołania tego interfejsu API.

1. W ramach tej samej rejestracji aplikacji w obszarze **Zarządzaj** wybierz pozycję **Certyfikaty & wpisy tajne**.
2. Utwórz **nowy klucz tajny klienta** , który nigdy nie wygaśnie.
3. Zanotuj **wartość** wpisu tajnego, ponieważ będzie ona używana w następnym kroku. Nie możesz uzyskać dostępu do niego ponownie po przejściu z tego okienka. Można jednak utworzyć go ponownie zgodnie z wymaganiami.

## <a name="create-the-app-using-the-net-cli"></a>Tworzenie aplikacji przy użyciu interfejsu wiersza polecenia platformy .NET

Uruchom następujące polecenie, aby pobrać szablony dla elementu Microsoft. Identity. Web, które zostaną użyte w tym samouczku.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Następnie uruchom następujące polecenie, aby utworzyć aplikację. Zastąp symbole zastępcze w poleceniu odpowiednimi informacjami na stronie Przegląd aplikacji i wykonaj polecenie w powłoce poleceń. Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Symbol zastępczy   | Nazwa Azure Portal       | Przykład                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Identyfikator aplikacji (klienta) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Identyfikator katalogu (dzierżawcy)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Teraz przejdź do nowej aplikacji Blazor w edytorze i Dodaj klucz tajny klienta do *appsettings.jsw* pliku, zastępując tekst "klucz tajny w aplikacji-App-Registration".

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>Testowanie aplikacji

Teraz możesz skompilować i uruchomić aplikację. Po uruchomieniu tej aplikacji szablonu należy określić strukturę do uruchomienia za pomocą--Framework. W tym samouczku jest wykorzystywany zestaw .NET Core 3,1 SDK.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

W przeglądarce przejdź do `https://localhost:5001` i zaloguj się przy użyciu konta użytkownika usługi Azure AD, aby wyświetlić uruchomioną aplikację.

## <a name="retrieving-data-from-microsoft-graph"></a>Pobieranie danych z Microsoft Graph

[Microsoft Graph](/graph/overview) oferuje szereg interfejsów API, które zapewniają dostęp do danych Microsoft 365 użytkownika. Korzystając z platformy tożsamości firmy Microsoft jako dostawcy tożsamości dla aplikacji, możesz łatwiej uzyskać dostęp do tych informacji, ponieważ Microsoft Graph bezpośrednio obsługiwać tokeny wystawione przez platformę tożsamości firmy Microsoft. W tej sekcji dodasz kod umożliwiający wyświetlenie wiadomości e-mail zalogowanego użytkownika na stronie "pobieranie danych" aplikacji.

Przed rozpoczęciem Wyloguj się z aplikacji, ponieważ będziesz wprowadzać zmiany w wymaganych uprawnieniach, a bieżący token nie będzie działać. Jeśli jeszcze tego nie zrobiono, uruchom ponownie aplikację i wybierz opcję **Wyloguj** się przed aktualizacją poniższego kodu.

Teraz zaktualizujesz rejestrację i kod aplikacji w celu ściągnięcia wiadomości e-mail użytkownika i wyświetlenia komunikatów w aplikacji. Aby to osiągnąć, najpierw Zwiększ uprawnienia rejestracji aplikacji w usłudze Azure AD, aby umożliwić dostęp do danych poczty e-mail. Następnie Dodaj kod do aplikacji Blazor, aby pobrać i wyświetlić te dane na jednej z stron.

1. W Azure Portal wybierz aplikację w **rejestracje aplikacji**.
1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**.
1. Wybierz pozycję **Dodaj**  >  **Microsoft Graph** uprawnienia.
1. Wybierz pozycję **uprawnienia delegowane**, a następnie wyszukaj i wybierz uprawnienie **poczta. odczyt** .
1. Wybierz pozycję **Dodaj uprawnienia**.

W *appsettings.jsw* pliku Zaktualizuj swój kod, tak aby pobierał odpowiedni token z odpowiednimi uprawnieniami. Dodaj "mail. Read" po zakresie "User. Read" w obszarze "DownstreamAPI". Określa zakresy (lub uprawnienia), do których aplikacja będzie żądać dostępu.

```json
"Scopes": "user.read mail.read"
```

Następnie zaktualizuj kod w pliku *FetchData. Razor* , aby pobrać dane poczty e-mail zamiast domyślnych (losowych) informacji o pogodzie. Zastąp kod w tym pliku następującym:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Uruchomić aplikację. Zobaczysz, że zostanie wyświetlony monit o nowo dodane uprawnienia wskazujący, że wszystko działa zgodnie z oczekiwaniami. Teraz, po przekroczeniu podstawowych danych profilu użytkownika, aplikacja żąda dostępu do danych poczty e-mail.

Po udzieleniu zgody przejdź na stronę "pobieranie danych", aby odczytać wiadomości e-mail.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Zrzut ekranu przedstawiający ostateczną aplikację. Ma nagłówek o powitaniu Hello Nicholas i zawiera listę wiadomości e-mail należących do Nicholas.":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wywoływania tworzenia aplikacji sieci Web, które logują użytkowników w naszej wieloczęściowej serii scenariuszy:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja sieci Web, która loguje użytkowników](scenario-web-app-sign-user-overview.md)
