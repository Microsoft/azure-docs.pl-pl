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
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: bf9a2232a04b929d716d3b2412f1b2c666b29f62
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767285"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API przy użyciu tożsamości aplikacji konsoli

W tym przewodniku Szybki start dowiesz się, jak napisać aplikację .NET Core, która może uzyskać token dostępu przy użyciu własnej tożsamości aplikacji, a następnie wywołać interfejs API programu Microsoft Graph w celu wyświetlenia [listy użytkowników](/graph/api/user-list) w katalogu. Ten scenariusz przydaje się w sytuacjach, które wymagają uruchamiania bezobsługowego, nienadzorowanego zadania lub usługi systemu Windows przy użyciu tożsamości aplikacji, a nie tożsamości użytkownika. (Zobacz [, jak działa przykład](#how-the-sample-works) dla ilustracji).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga [programu .NET Core 3,1](https://www.microsoft.com/net/download/dotnet-core).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start

> [!div renderon="docs" class="sxs-lookup"]
>
> Dostępne są dwie opcje uruchomienia aplikacji szybkiego startu: Express (opcja 1 poniżej) i ręczna (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) , wyszukując **rejestracje aplikacji** na pasku wyszukiwania w witrynie Azure Portal.
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Gdy zostanie wyświetlona strona **zarejestruj aplikację** , wprowadź informacje rejestracyjne swojej aplikacji.
> 1. W sekcji **Nazwa** podaj zrozumiałą nazwę aplikacji, która będzie widoczna dla użytkowników, na przykład `Daemon-console`, a następnie wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. Po jej zarejestrowaniu wybierz menu **Certyfikaty i klucze tajne**.
> 1. W obszarze **Klucze tajne klienta** wybierz pozycję **+ Nowy klucz tajny klienta**. Nadaj kluczowi nazwę i wybierz pozycję **Dodaj**. Skopiuj klucz tajny do bezpiecznej lokalizacji. Będzie ona potrzebna do użycia w kodzie i nie będzie ponownie wyświetlana w portalu.
> 1. Wybierz menu **Uprawnienia interfejsu API**, wybierz przycisk **+ Dodaj uprawnienie**, a następnie wybierz pozycję **Microsoft Graph**.
> 1. Wybierz pozycję **Uprawnienia aplikacji**.
> 1. W węźle **Użytkownik** wybierz pozycję **User.Read.All**, a następnie wybierz pozycję **Dodaj uprawnienia**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Pobieranie i konfigurowanie aplikacji Szybki Start
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby działał przykładowy kod z tego przewodnika Szybki start, musisz utworzyć klucz tajny klienta i dodać uprawnienie aplikacji **User.Read.All** interfejsu API programu Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-netcore-daemon/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

> [!div renderon="docs"]
> [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div renderon="docs"]
> > [!NOTE]
> > Udostępniony projekt można uruchomić w Visual Studio lub Visual Studio dla komputerów Mac


> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt przy użyciu programu Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio
>
> 1. Wyodrębnij plik zip do folderu lokalnego blisko folderu głównego dysku, na przykład **C:\Azure-Samples**.
> 1. Otwórz rozwiązanie w programie Visual Studio — **1 — Call-MSGraph\daemon-Console.sln** (opcjonalnie).
> 1. Edytuj **appsettings.jsna** i Zastąp wartości pól `ClientId` `Tenant` oraz `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Gdzie:
>   - `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
>   - `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>   - `Enter_the_Client_Secret_Here` — zastąp tę wartość kluczem tajnym klienta utworzonym w kroku 1.

> [!div renderon="docs"]
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)**, przejdź do strony **Przegląd** aplikacji w witrynie Azure Portal. Aby wygenerować nowy klucz, przejdź do strony **Certyfikaty i klucze tajne**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3. zgoda administratora

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4. Zgoda administratora

Jeśli spróbujesz uruchomić aplikację w tym momencie, otrzymasz komunikat o błędzie *HTTP 403 — Dostęp zabroniony* : `Insufficient privileges to complete the operation` . Dzieje się tak, ponieważ każde *uprawnienie tylko do aplikacji* wymaga zgody administratora, co oznacza, że administrator globalny katalogu musi wyrazić zgodę na swoją aplikację. Wybierz jedną z poniższych opcji, w zależności od roli:

##### <a name="global-tenant-administrator"></a>Administrator globalny dzierżawy

> [!div renderon="docs"]
> Jeśli jesteś administratorem globalnym dzierżawy, w witrynie Azure Portal przejdź do obszaru **aplikacje dla przedsiębiorstw** > kliknij pozycję rejestracja aplikacji > wybierz pozycję **"uprawnienia"** z sekcji Zabezpieczenia w okienku nawigacji po lewej stronie. Kliknij pozycję duży przycisk zatytułowany **Udziel zgody administratora na {nazwa dzierżawy}** (gdzie {nazwa dzierżawy} to nazwa katalogu).

> [!div renderon="portal" class="sxs-lookup"]
> Jeśli jesteś administratorem globalnym, przejdź do strony **Uprawnienia interfejsu API** i wybierz pozycję **Wyraź zgodę administratora dla katalogu wpisz_tutaj_nazwę_dzierżawy**
> > [!div id="apipermissionspage"]
> > [Przejdź do strony Uprawnienia interfejsu API]()

##### <a name="standard-user"></a>Użytkownik standardowy

Jeśli jesteś użytkownikiem standardowym Twojej dzierżawy, musisz poprosił administratora globalnego o przyznanie zgody administratora dla aplikacji. Aby to zrobić, udostępnij administratorowi następujący adres URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Gdzie:
>> * `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.

> [!NOTE]
> Po udzieleniu zgody dla aplikacji za pomocą powyższego adresu URL może zostać wyświetlony komunikat o błędzie *„AADSTS50011: brak adresów odpowiedzi zarejestrowanych dla aplikacji”*. Należy go zignorować — dzieje się tak, ponieważ aplikacja i adres URL nie mają identyfikatora URI przekierowania.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5. Uruchomienie aplikacji

Jeśli używasz programu Visual Studio lub Visual Studio dla komputerów Mac, naciśnij klawisz **F5** , aby uruchomić aplikację, w przeciwnym razie uruchom aplikację za pomocą wiersza polecenia, konsoli lub terminalu:

```console
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> Gdzie:
> * *{ProjectFolder} * — folder, w którym wyodrębniono plik zip, na przykład **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Powinna pojawić się lista użytkowników w katalogu usługi Azure AD.

> [!IMPORTANT]
> Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst w plikach projektu, ze względów bezpieczeństwa zaleca się używanie certyfikatu zamiast klucza tajnego klienta, zanim będzie można uznać aplikację za produkcyjną. Aby uzyskać więcej informacji na temat sposobu używania certyfikatu, zobacz [te instrukcje](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) w repozytorium GitHub na potrzeby tego przykładu.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład
![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Zgodnie z opisem, ten przewodnik Szybki Start żąda tokenów przy użyciu tożsamości własnej aplikacji zamiast uprawnień delegowanych. W tym przypadku przepływ uwierzytelniania jest określany jako *[przepływ OAuth poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md)*. Aby uzyskać więcej informacji na temat używania MSAL.NET z przepływem poświadczeń klienta, zobacz [ten artykuł](https://aka.ms/msal-net-client-credentials).

 Platformę MSAL.NET można zainstalować, uruchamiając następujące polecenie w **konsoli menedżera pakietów** programu Visual Studio:

```powershell twhitney
```console
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Gdzie: | Opis |
> |---------|---------|
> | `config.ClientSecret` | Klucz tajny klienta utworzony dla aplikacji w witrynie Azure Portal. |
> | `config.ClientId` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |
> | `config.Authority`    | (Opcjonalnie) Punkt końcowy usługi STS umożliwiający uwierzytelnienia użytkownika. W chmurze publicznej jest to zwykle `https://login.microsoftonline.com/{tenant}`, gdzie {tenant} jest nazwą dzierżawy lub identyfikatorem dzierżawy.|

Więcej informacji można znaleźć w [dokumentacji dotyczącej metody `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Aby zażądać tokenu przy użyciu tożsamości aplikacji, należy użyć metody `AcquireTokenForClient`:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Gdzie:| Opis |
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy. W przypadku klientów poufnych format powinien być podobny do `{Application ID URI}/.default`, aby wskazać, że żądane zakresy są zdefiniowane statycznie w obiekcie aplikacji ustawionym w witrynie Azure Portal (w przypadku programu Microsoft Graph element `{Application ID URI}` wskazuje na adres `https://graph.microsoft.com`). W przypadku niestandardowych interfejsów API sieci Web `{Application ID URI}` jest zdefiniowany w sekcji **UWIDACZNIANIE interfejsu API** w rejestracji aplikacji w witrynie Azure Portal (wersja zapoznawcza). |

Więcej informacji można znaleźć w [dokumentacji dotyczącej metody `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat aplikacji demonów, zobacz Omówienie scenariusza:

> [!div class="nextstepaction"]
> [Aplikacja demona, która wywołuje interfejsy API sieci Web](scenario-daemon-overview.md)
