---
title: 'Szybki start: uzyskiwanie tokenów & wywołań Microsoft Graph w aplikacji konsolowej | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku Szybki start dowiesz się, jak przykładowa aplikacja .NET Core może używać przepływu poświadczeń klienta w celu uzyskania tokenu i wywołania Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: a31bf345f523eea940be5d56495890e8ab5c6dbd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861647"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Szybki start: uzyskiwanie tokenu i wywołanie interfejsu API Microsoft Graph przy użyciu tożsamości aplikacji konsolowej

W tym przewodniku Szybki start pobierzemy i uruchomemy przykładowy kod, który pokazuje, jak aplikacja konsolowa platformy .NET Core może uzyskać token dostępu w celu wywołania interfejsu API usługi Microsoft Graph i wyświetlenia listy użytkowników w katalogu. [](/graph/api/user-list) W przykładowym kodzie pokazano również, jak można uruchomić zadanie lub usługę systemu Windows przy użyciu tożsamości aplikacji, a nie tożsamości użytkownika. Przykładowa aplikacja konsolowa w tym przewodniku Szybki start jest również aplikacją demona, więc jest to poufne aplikacje klienckie.

> [!div renderon="docs"]
> Na poniższym diagramie przedstawiono sposób działania przykładowej aplikacji:
>
> ![Diagram przedstawiający sposób działania przykładowej aplikacji wygenerowanej w tym przewodniku Szybki start.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga [zestawu SDK platformy .NET Core 3.1,](https://dotnet.microsoft.com/download) ale będzie również działać z zestawem .NET 5.0 SDK.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Rejestrowanie i pobieranie aplikacji

> [!div renderon="docs" class="sxs-lookup"]
>
> Dostępne są dwie opcje rozpoczęcia tworzenia aplikacji: konfiguracja automatyczna lub ręczna.
>
> ### <a name="automatic-configuration"></a>Konfiguracja automatyczna
>
> Jeśli chcesz zarejestrować i automatycznie skonfigurować aplikację, a następnie pobrać przykładowy kod, wykonaj następujące kroki:
>
> 1. Przejdź do strony <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal rejestracji aplikacji.</a>
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="manual-configuration"></a>Konfiguracja ręczna
>
> Jeśli chcesz ręcznie skonfigurować aplikację i przykładowy kod, użyj poniższych procedur.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal </span> </a>.
> 1. Jeśli masz dostęp do wielu dzierżaw, użyj filtru **Katalog i** subskrypcja w górnym menu, aby wybrać dzierżawę, w której chcesz :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W **obszarze Zarządzanie** wybierz pozycję **Rejestracje aplikacji**  >  **Rejestracja.**
> 1. W **polach** Nazwa wprowadź nazwę aplikacji. Na przykład wprowadź **wartość Daemon-console**. Użytkownicy Twojej aplikacji zobaczą tę nazwę i będzie można ją zmienić później.
> 1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. W obszarze **Zarządzanie** wybierz pozycję **Certyfikaty i wpisy tajne**.
> 1. W **obszarze Klucze tajne** klienta wybierz pozycję Nowy klucz tajny **klienta,** wprowadź nazwę, a następnie wybierz pozycję **Dodaj**. Zanotuj wartość tajnego w bezpiecznej lokalizacji do użycia w późniejszym kroku.
> 1. W **obszarze Zarządzanie** wybierz pozycję Uprawnienia **interfejsu API** Dodaj  >  **uprawnienie.** Wybierz **Microsoft Graph**.
> 1. Wybierz pozycję **Uprawnienia aplikacji**.
> 1. W **węźle** Użytkownik wybierz **pozycję User.Read.All,** a następnie wybierz **pozycję Dodaj uprawnienia.**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Pobieranie i konfigurowanie aplikacji Szybki Start
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykładowy kod w tym przewodniku Szybki start działał, utwórz klucz tajny klienta i dodaj interfejs Graph API aplikacji **User.Read.All** klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-netcore-daemon/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

> [!div renderon="docs"]
> [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Podany projekt można uruchomić w programie Visual Studio lub Visual Studio dla komputerów Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobieranie przykładowego kodu](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio
>
> 1. Wyodrębnij plik zip do folderu lokalnego, który znajduje się w pobliżu katalogu głównego dysku. Na przykład wyodrębnij do *katalogu C:\Azure-Samples*.
>
>    Zalecamy wyodrębnienie archiwum do katalogu w pobliżu katalogu głównego dysku, aby uniknąć błędów spowodowanych przez ograniczenia długości ścieżki w systemie Windows.
>
> 1. Otwórz rozwiązanie w Visual Studio: *1-Call-MSGraph\daemon-console.sln* (opcjonalnie).
> 1. W *appsettings.jswartości* zastąp wartości , i `Tenant` `ClientId` `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    W tym kodzie:
>    - `Enter_the_Application_Id_Here` to identyfikator aplikacji (klienta) zarejestrowanej aplikacji.
        Aby znaleźć wartości identyfikatora aplikacji (klienta) i identyfikatora katalogu (dzierżawy),  przejdź do strony Przegląd aplikacji w Azure Portal.
>    - Zastąp `Enter_the_Tenant_Id_Here` identyfikatorem dzierżawy lub nazwą dzierżawy (na przykład `contoso.microsoft.com` ).
>    - Zastąp `Enter_the_Client_Secret_Here` klucz tajny klienta utworzony w kroku 1.
    Aby wygenerować nowy klucz, przejdź do strony **Certyfikaty & wpisów tajnych.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3. Zgoda administratora

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4. Zgoda administratora

Jeśli spróbujesz uruchomić aplikację w tym momencie, zostanie wyświetlony błąd *HTTP 403 — Zabronione:* "Niewystarczające uprawnienia do ukończenia operacji". Ten błąd występuje, ponieważ każde uprawnienie tylko do aplikacji wymaga od administratora globalnego katalogu wyrażenia zgody na aplikację. W zależności od roli wybierz jedną z następujących opcji.

##### <a name="global-tenant-administrator"></a>Administrator globalny dzierżawy

> [!div renderon="docs"]
> Jeśli jesteś administratorem globalnym dzierżawy, przejdź do strony **Aplikacje dla** przedsiębiorstw w Azure Portal. Wybierz rejestrację aplikacji, a następnie wybierz **pozycję Uprawnienia** w **sekcji Zabezpieczenia** okienka po lewej stronie. Następnie wybierz duży przycisk z etykietą **Grant admin consent for {Tenant Name}** (Przyznaj zgodę administratora dla nazwy dzierżawy} (gdzie **{Nazwa dzierżawy}** jest nazwą katalogu).

> [!div renderon="portal" class="sxs-lookup"]
> Jeśli jesteś administratorem globalnym, przejdź do strony Uprawnienia interfejsu **API,** a następnie wybierz pozycję U udzielić zgody administratora dla **usługi Enter_the_Tenant_Name_Here.**
> > [!div id="apipermissionspage"]
> > [Przejdź do strony Uprawnienia interfejsu API]()

##### <a name="standard-user"></a>Użytkownik standardowy

Jeśli jesteś użytkownikiem standardowym dzierżawy, poproś administratora globalnego o wyrażenie zgody administratora dla aplikacji. Aby to zrobić, udostępnij administratorowi następujący adres URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> W tym adresie URL:
> * Zastąp `Enter_the_Tenant_Id_Here` identyfikatorem dzierżawy lub nazwą dzierżawy (na przykład `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` to identyfikator aplikacji (klienta) zarejestrowanej aplikacji.

Po udzieleniu zgody aplikacji przy użyciu poprzedniego adresu URL może zostać wyświetlony błąd "AADSTS50011: Nie zarejestrowano adresu odpowiedzi dla aplikacji". Ten błąd występuje, ponieważ ta aplikacja i adres URL nie mają adresu URI przekierowania. Możesz ją zignorować.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5. Uruchomienie aplikacji

Jeśli używasz aplikacji Visual Studio lub Visual Studio dla komputerów Mac, naciśnij **klawisz F5,** aby uruchomić aplikację. W przeciwnym razie uruchom aplikację za pomocą wiersza polecenia, konsoli lub terminalu:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
W tym kodzie:
* `{ProjectFolder}` to folder, w którym wyodrębniony został plik zip. Może to być na przykład `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Powinna zostać wyświetlona lista użytkowników w Azure Active Directory w wyniku.

Ta aplikacja szybkiego startu używa tajnego hasła klienta, aby zidentyfikować się jako klient poufny. Klucz tajny klienta jest dodawany jako plik w postaci zwykłego tekstu do plików projektu. Ze względów bezpieczeństwa zalecamy użycie certyfikatu zamiast tajnego certyfikatu klienta przed rozważenie aplikacji jako aplikacji produkcyjnej. Aby uzyskać więcej informacji na temat sposobu używania certyfikatu, zobacz [te instrukcje](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) w repozytorium GitHub na potrzeby tego przykładu.

## <a name="more-information"></a>Więcej informacji
Ta sekcja zawiera omówienie kodu wymaganego do logowania użytkowników. To omówienie może być przydatne, aby zrozumieć, jak działa kod, jakie są główne argumenty i jak dodać logowanie do istniejącej aplikacji konsolowej .NET Core.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Jak działa przykład
>
> ![Diagram przedstawiający sposób działania przykładowej aplikacji wygenerowanej w tym przewodniku Szybki start.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Biblioteka uwierzytelniania firmy Microsoft (BIBLIOTEKA MSAL w pakiecie [Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client) to biblioteka używana do logowania użytkowników i żądania tokenów w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Ten przewodnik Szybki start żąda tokenów przy użyciu własnej tożsamości aplikacji zamiast uprawnień delegowanych. Przepływ uwierzytelniania w tym przypadku jest nazywany przepływem [OAuth poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md). Aby uzyskać więcej informacji na temat używania MSAL.NET z przepływem poświadczeń klienta, zobacz [ten artykuł.](https://aka.ms/msal-net-client-credentials)

 Możesz zainstalować MSAL.NET, uruchamiając następujące polecenie w konsoli Visual Studio Menedżer pakietów konsoli programu :

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie zaimicjuj program MSAL przy użyciu następującego kodu:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Element | Opis |
 |---------|---------|
 | `config.ClientSecret` | Klucz tajny klienta utworzony dla aplikacji w Azure Portal. |
 | `config.ClientId` | Identyfikator aplikacji (klienta) dla aplikacji zarejestrowanej w Azure Portal. Tę wartość można znaleźć na stronie Przegląd aplikacji **w** Azure Portal. |
 | `config.Authority`    | (Opcjonalnie) Punkt końcowy usługi tokenu zabezpieczającego (STS) na potrzeby uwierzytelniania użytkownika. Zwykle jest to nazwa dzierżawy lub identyfikator dzierżawy w chmurze `https://login.microsoftonline.com/{tenant}` `{tenant}` publicznej.|

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną `ConfidentialClientApplication` dla programu ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Aby zażądać tokenu przy użyciu tożsamości aplikacji, użyj `AcquireTokenForClient` metody :

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Element| Opis |
|---------|---------|
| `scopes` | Zawiera żądane zakresy. W przypadku klientów poufnych ta wartość powinna mieć format podobny do `{Application ID URI}/.default` . Ten format wskazuje, że żądane zakresy to te, które są zdefiniowane statycznie w obiekcie aplikacji ustawionym w Azure Portal. W Microsoft Graph wskazuje `{Application ID URI}` na `https://graph.microsoft.com` . W przypadku niestandardowych internetowych interfejsów API element jest definiowany w Azure Portal w obszarze Rejestracja `{Application ID URI}` **aplikacji (wersja zapoznawcza)**  >  **Uwidocznij interfejs API.** |

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną `AcquireTokenForClient` dla programu ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat aplikacji demona, zobacz omówienie scenariusza:

> [!div class="nextstepaction"]
> [Aplikacja demona, która wywołuje internetowe interfejsy API](scenario-daemon-overview.md)
