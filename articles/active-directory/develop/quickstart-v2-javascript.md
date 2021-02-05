---
title: 'Szybki Start: Logowanie użytkowników w języku JavaScript aplikacje jednostronicowe | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja JavaScript może wywołać interfejs API, który wymaga tokenów dostępu wystawionych przez platformę tożsamości firmy Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 9a58e4087745ba0c09b85c2740781c493090a370
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583234"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Szybki Start: Logowanie użytkowników i uzyskiwanie tokenu dostępu w usłudze JavaScript SPA

W tym przewodniku szybki start pobierasz i uruchamiasz przykładowy kod, który pokazuje, jak aplikacja obsługująca skrypty JavaScript (single-page) może logować się do użytkowników i wywoływać Microsoft Graph. Przykład kodu demonstruje również, jak uzyskać token dostępu, aby wywołać interfejs API Microsoft Graph lub dowolny internetowy interfejs API.

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (Aby edytować pliki projektu)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express): Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu
>
> 1. Przejdź do środowiska szybkiego startu w <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal rejestracje aplikacji <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2 (ręczna): Zarejestruj i ręcznie skonfiguruj aplikację oraz przykład kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji. Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. W obszarze **konfiguracje platformy**  >  **Dodaj platformę**. Wybierz pozycję **Sieć Web**.
> 1. Ustaw wartość **identyfikatora URI przekierowania** na `http://localhost:3000/` . 
> 1. Wybierz **tokeny dostępu** i **tokeny identyfikatorów** w obszarze **niejawne i przepływy hybrydowe**  .
> 1. Wybierz pozycję **Konfiguruj**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby uzyskać przykładowy kod w tym przewodniku szybki start do pracy, Dodaj **Identyfikator URI przekierowania** `http://localhost:3000/` i Włącz **niejawny** przydział.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

> [!div renderon="docs"]
> Aby uruchomić projekt z serwerem sieci Web przy użyciu Node.js, [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt z serwerem sieci Web za pomocą Node.js

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3. Konfigurowanie aplikacji JavaScript
>
> W folderze *JavaScriptSPA* Edytuj *authConfig.js* i ustaw `clientID` `authority` wartości, i `redirectUri` `msalConfig` .
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Gdzie:
> - *\<Enter_the_Application_Id_Here>* to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.
> - *\<Enter_the_Cloud_Instance_Id_Here>* jest wystąpieniem chmury platformy Azure. W przypadku głównej lub globalnej chmury platformy Azure po prostu wprowadź *https://login.microsoftonline.com* . W przypadku chmur **narodowych** (na przykład Chin), zobacz [chmury narodowe](./authentication-national-cloud.md).
> - *\<Enter_the_Tenant_info_here>* jest ustawiona na jedną z następujących opcji:
>    - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym*, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład *contoso.Microsoft.com*).
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**.
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**.
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji.

> [!div renderon="docs"]
>
> Następnie w tym samym folderze Edytuj plik *graphConfig.js* , aby ustawić `graphMeEndpoint` i `graphMeEndpoint` dla tego `apiConfig` obiektu.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
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
> Gdzie:
> - *\<Enter_the_Graph_Endpoint_Here>* jest punktem końcowym, z którym będą wykonywane wywołania interfejsu API. W przypadku usługi interfejsu API głównej lub globalnej Microsoft Graph, wystarczy wprowadzić polecenie `https://graph.microsoft.com` . Aby uzyskać więcej informacji, zobacz [wdrażanie w chmurze krajowej](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Krok 4. uruchamianie projektu

Uruchom projekt z serwerem sieci Web przy użyciu [Node.js](https://nodejs.org/en/download/):

1. Aby uruchomić serwer, uruchom następujące polecenie w katalogu projektu:
    ```cmd
    npm install
    npm start
    ```
1. Otwórz przeglądarkę internetową i przejdź do `http://localhost:3000/` .

1. Wybierz pozycję **Zaloguj** się, aby rozpocząć logowanie, a następnie wywołaj Microsoft Graph API.

Po załadowaniu przez przeglądarkę aplikacji wybierz pozycję **Zaloguj**. Gdy logujesz się po raz pierwszy, zostanie wyświetlony monit o zezwolenie aplikacji na dostęp do Twojego profilu i zalogowanie się. Po pomyślnym zalogowaniu się na stronie zostaną wyświetlone informacje o profilu użytkownika.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład

![Sposób działania przykładowego SPA skryptu JavaScript: 1. SPA inicjuje logowanie. 2. SPA uzyskuje token identyfikatora z platformy tożsamości firmy Microsoft. 3. Uwierzytelnianie SPA wywołuje token pozyskiwania. 4. Platforma tożsamości firmy Microsoft zwraca token dostępu do SPA. 5. SPA wysyła żądanie i HTTP GET z tokenem ACE do interfejsu API Microsoft Graph. 6. Interfejs API programu Graph zwraca odpowiedź HTTP na SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Biblioteka MSAL rejestruje użytkowników i żąda tokenów, które są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Plik szybkiego startu *index.html* zawiera odwołanie do biblioteki:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Można zastąpić poprzednią wersję najnowszą wersją wydaną w obszarze [MSAL.js wersje](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Alternatywnie, jeśli zainstalowano Node.js, możesz pobrać najnowszą wersję za pomocą Menedżera pakietów Node.js (npm):

```cmd
npm install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Kod szybkiego startu pokazuje również, jak zainicjować bibliotekę MSAL:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Lokalizacja  | Opis |
> |---------|---------|
> |`clientId`     | Identyfikator aplikacji, która jest zarejestrowana w Azure Portal.|
> |`authority`    | Obowiązkowe Adres URL urzędu obsługujący typy kont, zgodnie z opisem wcześniej w sekcji konfiguracji. Domyślny urząd to `https://login.microsoftonline.com/common` . |
> |`redirectUri`     | Skonfigurowana odpowiedź/redirectUri rejestracji aplikacji. W takim przypadku `http://localhost:3000/` . |
> |`cacheLocation`  | Obowiązkowe Ustawia magazyn przeglądarki dla stanu uwierzytelniania. Wartość domyślna to sessionStorage.   |
> |`storeAuthStateInCookie`  | Obowiązkowe Biblioteka, w której jest przechowywany stan żądania uwierzytelniania, który jest wymagany do weryfikacji przepływów uwierzytelniania w plikach cookie w przeglądarce. Ten plik cookie jest ustawiany dla przeglądarki IE i programu Edge, aby wyeliminować pewne [znane problemy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji klienckich](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Logowanie użytkowników

Poniższy fragment kodu pokazuje, jak logować użytkowników:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Lokalizacja  | Opis |
> |---------|---------|
> | `scopes`   | Obowiązkowe Zawiera zakresy, które są wymagane do wyrażania zgody użytkownika w czasie logowania. Na przykład `[ "user.read" ]` dla Microsoft Graph lub `[ "<Application ID URL>/scope" ]` niestandardowych interfejsów API sieci Web (czyli `api://<Application ID>/access_as_user` ). |

> [!TIP]
> Alternatywnie możesz chcieć użyć `loginRedirect` metody, aby przekierować bieżącą stronę do strony logowania zamiast okna podręcznego.

### <a name="request-tokens"></a>Żądanie tokenów

MSAL używa trzech metod do uzyskiwania tokenów: `acquireTokenRedirect` , `acquireTokenPopup` i `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje uzyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Po wykonaniu metody `loginRedirect` lub `loginPopup` po raz pierwszy często stosuje się metodę `acquireTokenSilent` do uzyskiwania tokenów, które są używane w celu uzyskiwania dostępu do chronionych zasobów w kolejnych wywołaniach. Wywołania żądania lub odnowienia tokenów są wykonywane dyskretnie.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Lokalizacja  | Opis |
> |---------|---------|
> | `scopes`   | Zawiera zakresy żądane na potrzeby zwracania w tokenie dostępu dla interfejsu API. Na przykład `[ "mail.read" ]` dla Microsoft Graph lub `[ "<Application ID URL>/scope" ]` niestandardowych interfejsów API sieci Web (czyli `api://<Application ID>/access_as_user` ).|

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Istnieją sytuacje, w których użytkownicy mogą korzystać z platformy tożsamości firmy Microsoft. Na przykład:
* Może być konieczne ponowne wprowadzenie poświadczeń przez użytkowników, ponieważ ich hasło wygasło.
* Aplikacja żąda dostępu do dodatkowych zakresów zasobów, do których użytkownik musi wyrazić zgodę.
* Wymagane jest uwierzytelnianie dwuskładnikowe.

Typowym zalecanym wzorcem dla większości aplikacji jest Wywołaj `acquireTokenSilent` pierwsze, a następnie Przechwyć wyjątek, a następnie Wywołaj `acquireTokenPopup` (lub `acquireTokenRedirect` ), aby uruchomić żądanie interaktywne.

Wywoływanie `acquireTokenPopup` wyników w oknie podręcznym do logowania. (Lub `acquireTokenRedirect` wyniki przekierowywania użytkowników do platformy tożsamości firmy Microsoft). W tym oknie Użytkownicy muszą mieć możliwość działania przez potwierdzenie poświadczeń, udzielenie zgody na wymagane zasoby lub zakończenie uwierzytelniania dwuskładnikowego.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Ten przewodnik Szybki Start `loginRedirect` używa `acquireTokenRedirect` metod i programu Microsoft Internet Explorer ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z obsługą okien podręcznych przez Internet Explorer.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bardziej szczegółowy przewodnik krok po kroku dotyczący tworzenia aplikacji dla tego samouczka szybkiego startu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z jednostronicowej aplikacji (SPA) języka JavaScript](tutorial-v2-javascript-spa.md)
