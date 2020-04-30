---
title: Logowanie użytkowników w języku JavaScript aplikacje jednostronicowe z kodem uwierzytelniania | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, w jaki sposób aplikacja JavaScript może wywołać interfejs API, który wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: d362db3a51848603c78d663c5b628192ff028d02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209532"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Szybki Start: Logowanie użytkowników i uzyskiwanie tokenu dostępu w usłudze JavaScript SPA przy użyciu przepływu kodu uwierzytelniania 

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre aspekty tej funkcji mogą ulec zmianie przed ogólnym udostępnieniem.


Ten przewodnik Szybki Start używa MSAL. js 2,0 z przepływem kodu autoryzacji. Aby użyć MSAL. js 1,0 z niejawnym przepływem, Wyświetl [ten przewodnik Szybki Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

W tym przewodniku szybki start użyjesz przykładowego kodu, aby dowiedzieć się, jak aplikacja obsługująca skrypty JavaScript (single-page) może logować użytkowników z kont osobistych, kont służbowych i szkolnych. SPA może również uzyskać token dostępu, aby wywołać interfejs API Microsoft Graph lub dowolny internetowy interfejs API. Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz bezpłatnie subskrypcję platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (Aby edytować pliki projektu)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express): Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu
>
> 1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu w dzierżawie usługi Azure Active Directory (Azure AD), której chcesz użyć.
> 1. Wybierz pozycję [Rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. Przejdź do okienka szybkiego startu i postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2 (ręczna): Zarejestruj i ręcznie skonfiguruj aplikację oraz przykład kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
>
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu z dzierżawą usługi Azure AD, której chcesz użyć.
> 1. Wybierz pozycję [Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
> 1. W obszarze **Konfiguracja platformy**wybierz pozycję **Dodaj platformę**. Po lewej stronie zostanie otwarty panel. W tym miejscu wybierz region **aplikacje jednostronicowe** .
> 1. Nadal po lewej stronie Ustaw wartość **identyfikatora URI przekierowania** na `http://localhost:3000/`. 
> 1. Wybierz pozycję **Konfiguruj**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby próbkować kod w tym przewodniku Szybki Start, musisz dodać `redirectUri` jako. `http://localhost:3000/`
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

> [!div renderon="docs"]
> Aby uruchomić projekt z serwerem sieci Web przy użyciu środowiska Node. js, [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Uruchamianie projektu z serwerem sieci Web przy użyciu środowiska Node. js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3. Konfigurowanie aplikacji JavaScript
>
> W folderze *aplikacji* Edytuj *authConfig. js*, a następnie `clientID`Ustaw wartości `authority` i. `redirectUri` `msalConfig`
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Gdzie:
> - Enter_the_Application_Id_Here>to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji. * \<*
> - Enter_the_Cloud_Instance_Id_Here>jest wystąpieniem chmury platformy Azure. * \<* W przypadku głównej lub globalnej chmury platformy Azure po prostu *https://login.microsoftonline.com/* wprowadź. W przypadku chmur **narodowych** (na przykład Chin), zobacz [chmury narodowe](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - >Enter_the_Tenant_info_here jest ustawiona na jedną z następujących opcji: * \<*
>    - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym*, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład *contoso.Microsoft.com*).
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**.
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**.
> - * \<>Enter_the_Redirect_Uri_Here* jest`http://localhost:3000`
> > [!TIP]
> > Aby znaleźć wartości **identyfikatora aplikacji (klienta)**, **identyfikatora katalogu (dzierżawy)** i **obsługiwanych typów kont**, przejdź do strony **Przegląd** rejestracji aplikacji w Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji.

> [!div renderon="docs"]
>
> Następnie w tym samym folderze Edytuj plik *graphConfig. js* , aby ustawić `graphMeEndpoint` i `graphMailEndpoint` dla tego `apiConfig` obiektu.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Enter_the_Graph_Endpoint_Here>jest punktem końcowym, z którym będą wykonywane wywołania interfejsu API. * \<* W przypadku usługi API Main lub Global Microsoft Graph wpisz `https://graph.microsoft.com`polecenie. Aby uzyskać więcej informacji, zobacz [wdrażanie w chmurze krajowej](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Krok 4. uruchamianie projektu

Uruchom projekt z serwerem sieci Web przy użyciu środowiska [Node. js](https://nodejs.org/en/download/):

1. Aby uruchomić serwer, uruchom następujące polecenia w katalogu projektu:
    ```bash
    npm install
    npm start
    ```
1. Przejdź do `http://localhost:3000/`.

1. Wybierz pozycję **Zaloguj** , aby uruchomić proces logowania, a następnie wywołaj Microsoft Graph API.

    Przy pierwszym logowaniu zostanie wyświetlony monit o podanie zgody na umożliwienie aplikacji dostępu do Twojego profilu oraz zalogowanie się. Po pomyślnym zalogowaniu się na stronie zostaną wyświetlone informacje o profilu użytkownika.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład

![Sposób działania przykładowego SPA skryptu JavaScript: 1. SPA inicjuje logowanie. 2. SPA uzyskuje token identyfikatora z platformy tożsamości firmy Microsoft. 3. Uwierzytelnianie SPA wywołuje token pozyskiwania. 4. Platforma tożsamości firmy Microsoft zwraca token dostępu do SPA. 5. SPA wysyła żądania i HTTP GET z tokenem dostępu do interfejsu API Microsoft Graph. 6. Interfejs API programu Graph zwraca odpowiedź HTTP na SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal. js

Biblioteka MSAL. js rejestruje użytkowników i żąda tokenów, które są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Plik *index. html* z przykładem zawiera odwołanie do biblioteki:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Można zastąpić poprzednią wersję najnowszą wersją wydaną w ramach wersji [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Alternatywnie, jeśli masz zainstalowany program Node. js, możesz pobrać najnowszą wersję przy użyciu Menedżera pakietów Node. js (npm):

```batch
npm install msal
```

## <a name="next-steps"></a>Następne kroki

[Repozytorium GitHub MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js) zawiera dodatkową dokumentację biblioteki, często zadawane pytania i zapewnia pomoc techniczną.

Aby uzyskać bardziej szczegółowy przewodnik krok po kroku dotyczący tworzenia aplikacji dla tego samouczka szybkiego startu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek umożliwiający zalogowanie się i Wywołaj program MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
