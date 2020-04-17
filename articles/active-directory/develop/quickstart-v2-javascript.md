---
title: Logowanie użytkowników w aplikacjach jednostronicowych w języku JavaScript | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak aplikacja JavaScript może wywoływać interfejs API, który wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: 1d22f66ad5f7adf5bb8196c3e72a2a343f4558b0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536101"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Szybki start: logowanie użytkowników i uzyskiwanie tokenu dostępu w javascript SPA

W tym przewodniku Szybki start można użyć przykładu kodu, aby dowiedzieć się, jak aplikacja jednostronicowa JavaScript (SPA) może logować się do użytkowników kont osobistych, kont służbowych i kont szkolnych. JavaScript SPA można również uzyskać token dostępu do wywołania interfejsu API programu Microsoft Graph lub dowolnego interfejsu API sieci web. (Zobacz [Jak działa przykład](#how-the-sample-works) dla ilustracji).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (do edycji plików projektu)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację Szybki start, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express): Zarejestruj się i automatycznie skonfiguruj aplikację, a następnie pobierz przykład kodu
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
> 1. Jeśli twoje konto daje dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na dzierżawę usługi Azure Active Directory (Azure AD), której chcesz użyć.
> 1. Przejdź do nowego okienka [Azure portal — rejestracje aplikacji.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2 (ręczna): Zarejestruj się i ręcznie skonfiguruj przykład aplikacji i kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
>
> 1. Jeśli twoje konto daje dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na dzierżawę usługi Azure AD, której chcesz użyć.
> 1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Wybierz **pozycję Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **Uwierzytelnianie**.
> 1. W obszarze **Konfiguracje platformy**wybierz pozycję **Dodaj platformę**. Po lewej stronie zostanie otwarty panel. Tam wybierz region **Aplikacji sieci Web.**
> 1. Nadal po lewej stronie ustaw wartość `http://localhost:3000/` **Przekierowanie identyfikatora URI** na . Następnie wybierz token **dostępu** i **token identyfikatora**.
> 1. Wybierz pozycję **Konfiguruj**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykładowy kod w tym przewodniku Szybki `redirectUri` start `http://localhost:3000/` działał, należy dodać as i włączyć **niejawne przyznanie**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

> [!div renderon="docs"]
> Aby uruchomić projekt z serwerem sieci web przy użyciu pliku Node.js, [pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal"]
> Uruchamianie projektu z serwerem sieci web przy użyciu pliku Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykładowy kod](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurowanie aplikacji JavaScript
>
> W folderze *JavaScriptSPA* edytuj *plik authConfig.js* `redirectUri` i `msalConfig`ustaw `clientID`pozycję , `authority` i wartości w obszarze .
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

> [!div renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Gdzie:
> - Enter_the_Application_Id_Here>jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji. * \<*
> - Enter_the_Cloud_Instance_Id_Here>jest wystąpieniem chmury platformy Azure. * \<* W przypadku głównej lub globalnej *https://login.microsoftonline.com*chmury platformy Azure wystarczy wprowadzić plik . Zobacz też: Chmury **narodowe** [.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)
> - Enter_the_Tenant_info_here>jest ustawiona na jedną z następujących opcji: * \<*
>    - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym,* zastąp tę wartość nazwą **dzierżawy** lub **dzierżawy** (na przykład *contoso.microsoft.com*).
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym,* zastąp tę wartość **organizacjami**.
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft,* zastąp tę wartość **wspólną**. Aby ograniczyć obsługę tylko do *osobistych kont Microsoft,* zastąp tę wartość **konsumentami.**
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: Aplikacja jest skonfigurowana i gotowa do uruchomienia
> Skonfigurowaliśmy projekt z wartościami właściwości aplikacji.

> [!div renderon="docs"]
>
> Następnie, nadal w tym samym folderze, edytuj plik *graphConfig.js,* aby ustawić `graphMeEndpoint` i `graphMeEndpoint` dla `apiConfig` obiektu.
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
> Gdzie:
> - Enter_the_Graph_Endpoint_Here>jest punktem końcowym, dla których będą nawiązywają wywołania interfejsu API. * \<* W przypadku głównej lub globalnej usługi `https://graph.microsoft.com`interfejsu API programu Microsoft Graph wystarczy wprowadzić program . Aby uzyskać więcej informacji, zobacz [Wdrażanie chmury krajowej](https://docs.microsoft.com/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Krok 4: Uruchom projekt

Uruchamianie projektu za pomocą serwera sieci Web przy użyciu [pliku Node.js](https://nodejs.org/en/download/):

1. Aby uruchomić serwer, uruchom następujące polecenie z katalogu projektu:
    ```batch
    npm install
    npm start
    ```
1. Otwórz przeglądarkę internetową `http://localhost:3000/`i przejdź do pliku .

1. Wybierz **pozycję Zaloguj się,** aby rozpocząć logowanie, a następnie zadzwoń do interfejsu API programu Microsoft Graph.

Po załadowaniu aplikacji przez przeglądarkę wybierz pozycję **Zaloguj**się . Przy pierwszym logowaniu zostanie wyświetlony monit o wyrażenie zgody na umożliwienie aplikacji dostępu do twojego profilu i zalogowania się. Po pomyślnym zalogowaniu informacje o profilu użytkownika powinny być wyświetlane na stronie.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa próbka

![Jak działa przykładowa aplikacja w tym przewodniku Szybki start](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Biblioteka MSAL loguje się do użytkowników i żąda tokenów, które są używane do uzyskiwania dostępu do interfejsu API, który jest chroniony przez platformę tożsamości firmy Microsoft. Plik szybki start *index.html* zawiera odwołanie do biblioteki:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Poprzednią wersję można zastąpić najnowszą wersją wydaną w ramach [programu MSAL.js.](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

Alternatywnie, jeśli masz zainstalowany node.js, możesz pobrać najnowszą wersję za pośrednictwem Node.js Package Manager (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Kod przewodnika Szybki start pokazuje również, jak zainicjować bibliotekę MSAL:

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

> |Lokalizacja  |  |
> |---------|---------|
> |`clientId`     | Identyfikator aplikacji zarejestrowanej w witrynie Azure portal.|
> |`authority`    | (Opcjonalnie) Adres URL urzędu obsługujący typy kont, zgodnie z opisem opisanym wcześniej w sekcji konfiguracji. Domyślnym urzędem jest `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | Rejestracja aplikacji jest skonfigurowana reply/redirectUri. W tym `http://localhost:3000/`przypadku . |
> |`cacheLocation`  | (Opcjonalnie) Ustawia magazyn przeglądarki dla stanu eru. Wartość domyślna to sessionStorage.   |
> |`storeAuthStateInCookie`  | (Opcjonalnie) Biblioteka, która przechowuje stan żądania uwierzytelniania, który jest wymagany do sprawdzania poprawności przepływów uwierzytelniania w plikach cookie przeglądarki. Ten plik cookie jest ustawiony dla przeglądarek IE i Edge w celu złagodzenia niektórych [znanych problemów.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) |

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

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | (Opcjonalnie) Zawiera zakresy, które są wymagane do zgody użytkownika w czasie logowania. Na przykład `[ "user.read" ]` dla programu `[ "<Application ID URL>/scope" ]` Microsoft Graph lub niestandardowych `api://<Application ID>/access_as_user`interfejsów API sieci Web (tj. ). |

> [!TIP]
> Alternatywnie można użyć `loginRedirect` metody, aby przekierować bieżącą stronę do strony logowania zamiast okna podręcznego.

### <a name="request-tokens"></a>Żądanie tokenów

MSAL wykorzystuje trzy metody pozyskiwania `acquireTokenRedirect`tokenów: , `acquireTokenPopup`i`acquireTokenSilent`

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

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | Zawiera zakresy żądane na potrzeby zwracania w tokenie dostępu dla interfejsu API. Na przykład `[ "mail.read" ]` dla programu `[ "<Application ID URL>/scope" ]` Microsoft Graph lub niestandardowych `api://<Application ID>/access_as_user`interfejsów API sieci Web (tj. ).|

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Istnieją sytuacje, w których należy wymusić na użytkownikach interakcję z punktem końcowym platformy tożsamości firmy Microsoft. Przykład:
* Użytkownicy mogą być zmuszeni do ponownego wprowadzenia swoich poświadczeń, ponieważ ich hasło wygasło.
* Aplikacja żąda dostępu do dodatkowych zakresów zasobów, na które użytkownik musi wyrazić zgodę.
* Wymagane jest uwierzytelnianie dwuskładnikowe.

Zwykle zalecany wzorzec dla `acquireTokenSilent` większości aplikacji jest najpierw wywołać, a następnie złapać wyjątek, a następnie wywołać `acquireTokenPopup` (lub `acquireTokenRedirect`), aby uruchomić żądanie interaktywne.

Wywołanie `acquireTokenPopup` wyników w oknie podręcznym do logowania. (Lub `acquireTokenRedirect` powoduje przekierowanie użytkowników do punktu końcowego platformy tożsamości firmy Microsoft.) W tym oknie użytkownicy muszą wchodzić w interakcje, potwierdzając swoje poświadczenia, wyrażając zgodę na wymagany zasób lub wypełniając uwierzytelnianie dwuskładnikowe.

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
> Ten przewodnik Szybki `loginRedirect` start `acquireTokenRedirect` używa i metod z programem Microsoft Internet Explorer, ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z obsługą wyskakujących okienek przez program Internet Explorer.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bardziej szczegółowy przewodnik krok po kroku dotyczący tworzenia aplikacji dla tego przewodnika Szybki start, zobacz:

> [!div class="nextstepaction"]
> [Samouczek, aby zalogować się i zadzwonić do MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Aby przeglądać repozytorium MSAL w poszukiwaniu dokumentacji, często zadawanych pytań, problemów i innych informacji, zobacz:

> [!div class="nextstepaction"]
> [Repozytorium usługi MSAL.js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js)
