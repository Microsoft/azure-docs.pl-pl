---
title: 'Szybki Start: uzyskiwanie Microsoft Graph & wywołań tokenu w aplikacji konsoli | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, w jaki sposób Przykładowa aplikacja .NET Core może używać przepływu poświadczeń klienta, aby uzyskać token i wywoływać Microsoft Graph.
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
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022929"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Szybki Start: uzyskiwanie tokenu i wywoływanie interfejsu API Microsoft Graph przy użyciu tożsamości aplikacji konsoli

W tym przewodniku szybki start pobrano i uruchomiono przykład kodu, który pokazuje, jak Aplikacja konsolowa .NET Core może uzyskać token dostępu do wywołania interfejsu API Microsoft Graph i wyświetlić [listę użytkowników](/graph/api/user-list) w katalogu. Przykład kodu demonstruje również, jak zadanie lub usługa systemu Windows mogą być uruchamiane przy użyciu tożsamości aplikacji, a nie tożsamości użytkownika. Przykładowa aplikacja konsolowa w tym przewodniku szybki start to również aplikacja demona, więc jest to poufna aplikacja kliencka.

> [!div renderon="docs"]
> Na poniższym diagramie przedstawiono sposób działania przykładowej aplikacji:
>
> ![Diagram przedstawiający sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga [programu .net core 3,1](https://www.microsoft.com/net/download/dotnet-core) , ale również współpracuje z platformą .net Core 5,0.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Rejestrowanie i pobieranie aplikacji

> [!div renderon="docs" class="sxs-lookup"]
>
> Dostępne są dwie opcje rozpoczęcia kompilowania aplikacji: Konfiguracja automatyczna lub ręczna.
>
> ### <a name="automatic-configuration"></a>Automatyczna konfiguracja
>
> Jeśli chcesz zarejestrować i automatycznie skonfigurować aplikację, a następnie pobrać przykład kodu, wykonaj następujące kroki:
>
> 1. Przejdź do <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">strony Azure Portal na potrzeby rejestracji aplikacji</a>.
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację w jednym kliknięciem.
>
> ### <a name="manual-configuration"></a>Konfiguracja ręczna
>
> Jeśli chcesz ręcznie skonfigurować aplikację i przykład kodu, Użyj poniższych procedur.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal </span> </a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. W obszarze **Nazwa** wprowadź nazwę aplikacji. Na przykład wprowadź **demo-Console**. Użytkownicy Twojej aplikacji będą widzieć tę nazwę i można ją później zmienić.
> 1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. W obszarze **Zarządzanie** wybierz pozycję **Certyfikaty i wpisy tajne**.
> 1. W obszarze wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**, wprowadź nazwę, a następnie wybierz pozycję **Dodaj**. Zapisz wartość klucza tajnego w bezpiecznej lokalizacji do użycia w późniejszym kroku.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**. Wybierz **Microsoft Graph**.
> 1. Wybierz pozycję **Uprawnienia aplikacji**.
> 1. W węźle **użytkownik** wybierz pozycję **użytkownik. odczyt. wszystkie**, a następnie wybierz pozycję **Dodaj uprawnienia**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Pobieranie i konfigurowanie aplikacji Szybki Start
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby uzyskać przykładowy kod w tym przewodniku Szybki Start, Utwórz klucz tajny klienta i Dodaj **użytkownika interfejs API programu Graph. uprawnienie Odczyt. wszystkie** aplikacje.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-netcore-daemon/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

> [!div renderon="docs"]
> [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Udostępniony projekt można uruchomić w Visual Studio lub Visual Studio dla komputerów Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio
>
> 1. Wyodrębnij plik zip do folderu lokalnego znajdującego się blisko katalogu głównego dysku. Na przykład Wyodrębnij do *C:\Azure-Samples*.
>
>    Zalecamy wyodrębnienie archiwum do katalogu w sąsiedztwie katalogu głównego dysku, aby uniknąć błędów spowodowanych ograniczeniami długości ścieżki w systemie Windows.
>
> 1. Otwórz rozwiązanie w programie Visual Studio: *1-Call-MSGraph\daemon-Console.sln* (opcjonalnie).
> 1. W *appsettings.jsna*, Zastąp wartości `Tenant` , `ClientId` i `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    W tym kodzie:
>    - `Enter_the_Application_Id_Here` to identyfikator aplikacji (klienta) dla zarejestrowanej aplikacji.
        Aby znaleźć wartości dla identyfikatora aplikacji (klienta) i katalogu (dzierżawcy), przejdź do strony **Przegląd** aplikacji w Azure Portal.
>    - Zamień na `Enter_the_Tenant_Id_Here` Identyfikator dzierżawy lub nazwę dzierżawy (na przykład `contoso.microsoft.com` ).
>    - Zamień na `Enter_the_Client_Secret_Here` wpis tajny klienta utworzony w kroku 1.
    Aby wygenerować nowy klucz, przejdź do strony **certyfikaty & wpisy tajne** .

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3. zgoda administratora

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4. Zgoda administratora

Jeśli spróbujesz uruchomić aplikację w tym momencie, otrzymasz komunikat o błędzie *HTTP 403 — Dostęp zabroniony* : "niewystarczające uprawnienia do ukończenia operacji". Ten błąd występuje, ponieważ każde uprawnienie tylko do aplikacji wymaga od administratora globalnego katalogu, aby wyrazić zgodę na Twoją aplikację. Wybierz jedną z następujących opcji, w zależności od roli.

##### <a name="global-tenant-administrator"></a>Administrator globalny dzierżawy

> [!div renderon="docs"]
> Jeśli jesteś administratorem globalnym dzierżawy, przejdź do pozycji **aplikacje dla przedsiębiorstw** w Azure Portal. Wybierz rejestrację aplikacji, a następnie wybierz pozycję **uprawnienia** z sekcji **zabezpieczenia** w okienku po lewej stronie. Następnie wybierz duży przycisk z etykietą **Udziel zgody administratora na {nazwa dzierżawy}** (gdzie **{Nazwa dzierżawy}** jest nazwą katalogu).

> [!div renderon="portal" class="sxs-lookup"]
> Jeśli jesteś administratorem globalnym, przejdź do strony **uprawnienia interfejsu API** i wybierz pozycję **Udziel zgody administratorowi na Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Przejdź do strony Uprawnienia interfejsu API]()

##### <a name="standard-user"></a>Użytkownik standardowy

Jeśli jesteś użytkownikiem standardowym dzierżawy, poproszenie administratora globalnego o przyznanie zgody administratora dla aplikacji. Aby to zrobić, udostępnij administratorowi następujący adres URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> W tym adresie URL:
> * Zamień na `Enter_the_Tenant_Id_Here` Identyfikator dzierżawy lub nazwę dzierżawy (na przykład `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` to identyfikator aplikacji (klienta) dla zarejestrowanej aplikacji.

Po udzieleniu zgody na aplikację przy użyciu powyższego adresu URL może zostać wyświetlony komunikat o błędzie "AADSTS50011: nie zarejestrowano adresu odpowiedzi dla aplikacji". Ten błąd występuje, ponieważ ta aplikacja i adres URL nie mają identyfikatora URI przekierowania. Można go zignorować.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5. Uruchomienie aplikacji

Jeśli używasz programu Visual Studio lub Visual Studio dla komputerów Mac, naciśnij klawisz **F5** , aby uruchomić aplikację. W przeciwnym razie uruchom aplikację za pomocą wiersza polecenia, konsoli lub terminalu:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
W tym kodzie:
* `{ProjectFolder}` jest folderem, w którym został wyodrębniony plik. zip. Może to być na przykład `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

W związku z tym powinna zostać wyświetlona lista użytkowników w Azure Active Directory.

Ta aplikacja szybkiego startu używa klucza tajnego klienta, aby zidentyfikować siebie jako klienta poufnego. Wpis tajny klienta jest dodawany jako plik tekstowy do plików projektu. Ze względów bezpieczeństwa zaleca się użycie certyfikatu zamiast wpisu tajnego klienta przed uwzględnieniem aplikacji jako aplikacji produkcyjnej. Aby uzyskać więcej informacji na temat sposobu używania certyfikatu, zobacz [te instrukcje](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) w repozytorium GitHub na potrzeby tego przykładu.

## <a name="more-information"></a>Więcej informacji
Ta sekcja zawiera omówienie kodu wymaganego do zalogowania użytkowników. Ten przegląd może być przydatny do zrozumienia, w jaki sposób działa kod, opis głównych argumentów i sposób dodawania logowania do istniejącej aplikacji konsolowej .NET Core.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Jak działa przykład
>
> ![Diagram przedstawiający sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Biblioteka uwierzytelniania firmy Microsoft (MSAL, w pakiecie [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) ) to biblioteka służąca do logowania użytkowników i żądania tokenów w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Ten przewodnik Szybki Start wysyła tokeny przy użyciu tożsamości aplikacji zamiast uprawnień delegowanych. W tym przypadku przepływ uwierzytelniania jest znany jako [przepływ OAuth poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md). Aby uzyskać więcej informacji na temat używania MSAL.NET z przepływem poświadczeń klienta, zobacz [ten artykuł](https://aka.ms/msal-net-client-credentials).

 Możesz zainstalować MSAL.NET, uruchamiając następujące polecenie w konsoli Menedżera pakietów programu Visual Studio:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie zainicjuj MSAL przy użyciu następującego kodu:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Element | Opis |
 |---------|---------|
 | `config.ClientSecret` | Wpis tajny klienta utworzony dla aplikacji w Azure Portal. |
 | `config.ClientId` | Identyfikator aplikacji (klienta) dla aplikacji zarejestrowanej w Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w Azure Portal. |
 | `config.Authority`    | Obowiązkowe Punkt końcowy usługi tokenu zabezpieczającego (STS), dla którego użytkownik może się uwierzytelnić. Zwykle jest to `https://login.microsoftonline.com/{tenant}` chmura publiczna, gdzie `{tenant}` jest nazwą dzierżawy lub identyfikatorem dzierżawy.|

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną `ConfidentialClientApplication` dla programu ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Aby zażądać tokenu przy użyciu tożsamości aplikacji, użyj `AcquireTokenForClient` metody:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Element| Opis |
|---------|---------|
| `scopes` | Zawiera żądane zakresy. W przypadku klientów poufnych ta wartość powinna mieć format podobny do `{Application ID URI}/.default` . Ten format wskazuje, że żądane zakresy są tymi, które są zdefiniowane statycznie w obiekcie aplikacji ustawionym w Azure Portal. W przypadku Microsoft Graph `{Application ID URI}` wskazuje na `https://graph.microsoft.com` . W przypadku niestandardowych interfejsów API sieci Web `{Application ID URI}` jest zdefiniowany w Azure Portal w obszarze **Rejestracja aplikacji (wersja zapoznawcza)**  >  **uwidaczniasz interfejs API**. |

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną `AcquireTokenForClient` dla programu ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat aplikacji demonów, zobacz Omówienie scenariusza:

> [!div class="nextstepaction"]
> [Aplikacja demona, która wywołuje interfejsy API sieci Web](scenario-daemon-overview.md)
