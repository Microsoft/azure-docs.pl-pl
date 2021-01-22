---
title: 'Szybki Start: Logowanie użytkowników w języku JavaScript aplikacje jednostronicowe (SPA) z kodem uwierzytelniania i wywołaniem Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja jednostronicowa (SPA) języka JavaScript może logować użytkowników kont osobistych, kont służbowych i szkolnych przy użyciu przepływu kodu autoryzacji i wywoływać Microsoft Graph.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: acaa87f44fbd496aea843de673c0b2d7652fe542
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681047"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>Szybki Start: Logowanie i uzyskiwanie tokenu dostępu w przypadku SPA, przy użyciu przepływu kodu uwierzytelniania

W tym przewodniku szybki start pobrano i uruchomimy przykład kodu, który demonstruje, jak aplikacja jednostronicowa (SPA) w języku JavaScript może zalogować użytkowników i wywoływać Microsoft Graph przy użyciu przepływu kodu autoryzacji. Przykład kodu demonstruje, jak uzyskać token dostępu, aby wywołać interfejs API Microsoft Graph lub dowolny internetowy interfejs API. 

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

Ten przewodnik Szybki Start używa MSAL kątowy v2 z przepływem kodu autoryzacji. W przypadku podobnego przewodnika Szybki Start, który używa MSAL skośnie 1. x z niejawnym przepływem, zobacz [Szybki Start: Logowanie użytkowników w aplikacjach jednostronicowych JavaScript](./quickstart-v2-angular.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz bezpłatnie subskrypcję platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express): Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu
>
> 1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu z dzierżawą usługi Azure AD, której chcesz użyć.
> 1. Wybierz pozycję [Rejestracje aplikacji](https://aka.ms/AAatehv).
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
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na dzierżawę usługi Azure Active Directory (Azure AD), której chcesz użyć.
> 1. Wybierz pozycję [Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
> 1. W obszarze **konfiguracje platformy** wybierz opcję `Add a platform` .
> 1. W oknie wyników wybierz pozycję **aplikacja jednostronicowa**.
> 1. Ustaw wartość **identyfikatorów URI przekierowania** na `http://localhost:4200/` . Jest to domyślny port NodeJS nasłuchuje na komputerze lokalnym. Po pomyślnym uwierzytelnieniu użytkownika zwrócimy odpowiedź uwierzytelniania do tego identyfikatora URI. 
> 1. Kliknij przycisk **Konfiguruj** , aby zastosować zmiany.
> 1. W obszarze **konfiguracje platformy** rozwiń **aplikację jednostronicową**.
> 1. Upewnij się, że w obszarze **przyznane typy** ![ już skonfigurowano ](media/quickstart-v2-javascript/green-check.png) Identyfikator URI przekierowania jest uprawniony do przepływu kodu autoryzacji z PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby próbkować kod w tym przewodniku Szybki Start, musisz dodać `redirectUri` jako `http://localhost:4200/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

 #### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

> [!div renderon="docs"]
> Aby uruchomić projekt z serwerem sieci Web przy użyciu Node.js, [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt z serwerem sieci Web za pomocą Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3. Konfigurowanie aplikacji JavaScript
>
> W folderze *src* Otwórz folder *App* , a następnie otwórz plik *App. module. TS* i zaktualizuj `clientID` `authority` wartości,, i `redirectUri` w `auth` obiekcie.
>
> ```javascript
> // MSAL instance to be passed to msal-angular
> export function MSALInstanceFactory(): IPublicClientApplication {
>   return new PublicClientApplication({
>     auth: {
>       clientId: 'Enter_the_Application_Id_Here',
>       authority: 'Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here',
>       redirectUri: 'Enter_the_Redirect_Uri_Here'
>     },
>     cache: {
>       cacheLocation: BrowserCacheLocation.LocalStorage,
>       storeAuthStateInCookie: isIE, // set to true for IE 11
>     },
>   });
> }
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Zmodyfikuj wartości w `auth` sekcji zgodnie z opisem w tym miejscu:
>
> - `Enter_the_Application_Id_Here` to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.
> - `Enter_the_Cloud_Instance_Id_Here` jest wystąpieniem chmury platformy Azure. W głównej lub globalnej chmurze platformy Azure wprowadź wartość `https://login.microsoftonline.com/` . W przypadku chmur **narodowych** (na przykład Chin), zobacz [chmury narodowe](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` jest ustawiony na jedną z następujących wartości:
>   - Jeśli aplikacja obsługuje *konta w tym katalogu organizacji*, Zamień tę wartość na **Identyfikator dzierżawy** lub **nazwę dzierżawy**. Na przykład `contoso.microsoft.com`.
>   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość wartością `organizations` .
>   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość wartością `common` . **W tym przewodniku szybki start Użyj programu** `common` .
>   - Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość wartością `consumers` .
> - `Enter_the_Redirect_Uri_Here` to `http://localhost:4200/`.
>
> `authority`Wartość w *aplikacji App. module. TS* powinna być podobna do następującej, jeśli używasz głównej (globalnej) chmury platformy Azure:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Aby znaleźć wartości **identyfikatora aplikacji (klienta)**, **identyfikatora katalogu (dzierżawy)** i **obsługiwanych typów kont**, przejdź do strony **Przegląd** rejestracji aplikacji w Azure Portal.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji.

> [!div renderon="docs"]
>
> Przewiń w dół do tego samego pliku i zaktualizuj `graphMeEndpoint` . 
> - Zastąp ciąg `Enter_the_Graph_Endpoint_Herev1.0/me` ciągiem `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` jest punktem końcowym, z którym będą wykonywane wywołania interfejsu API. W przypadku usługi interfejsu API Main (Global) Microsoft Graph wprowadź `https://graph.microsoft.com/` wartość (Dołącz końcowy ukośnik do przodu). Więcej informacji zawiera strona [dokumentacji](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
> export function MSALInterceptorConfigFactory(): MsalInterceptorConfiguration {
>   const protectedResourceMap = new Map<string, Array<string>>();
>   protectedResourceMap.set('Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']);
>
>   return {
>     interactionType: InteractionType.Redirect,
>     protectedResourceMap
>   };
> }
> ```
>
>
 #### <a name="step-4-run-the-project"></a>Krok 4. uruchamianie projektu

Uruchom projekt z serwerem sieci Web przy użyciu Node.js:

1. Aby uruchomić serwer, uruchom następujące polecenia w katalogu projektu:
    ```console
    npm install
    npm start
    ```
1. Przejdź na stronę `http://localhost:4200/`.

1. Wybierz pozycję **Zaloguj** , aby uruchomić proces logowania, a następnie Wywołaj interfejs API Microsoft Graph.

    Przy pierwszym logowaniu zostanie wyświetlony monit o podanie zgody na umożliwienie aplikacji dostępu do Twojego profilu oraz zalogowanie się. Po pomyślnym zalogowaniu kliknij przycisk **profilu** , aby wyświetlić informacje o użytkowniku na stronie.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład

![Diagram przedstawiający przepływ kodu autoryzacji dla aplikacji jednostronicowej.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

Biblioteka MSAL.js umożliwia użytkownikom i żąda tokenów, które są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. 

Jeśli zainstalowano Node.js, możesz pobrać najnowszą wersję przy użyciu Menedżera pakietów Node.js (npm):

```console
npm install @azure/msal-browser @azure/msal-angular@2
```

## <a name="next-steps"></a>Następne kroki

Szczegółowe instrukcje krok po kroku dotyczące tworzenia aplikacji przepływu kodu uwierzytelniania przy użyciu polecenia JavaScript można znaleźć w następującym samouczku:

> [!div class="nextstepaction"]
> [Samouczek umożliwiający zalogowanie się i Wywołaj program MS Graph](./tutorial-v2-javascript-auth-code.md)
