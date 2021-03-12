---
title: 'Szybki Start: Logowanie użytkowników w języku JavaScript reaguje na aplikacje jednostronicowe (SPA) z kodem uwierzytelniania i Microsoft Graph wywołań | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak skrypt JavaScript reaguje na aplikacje jednostronicowe (SPA) na kontach osobistych, kontach służbowych i szkolnych za pomocą przepływu kodu autoryzacji i Microsoft Graph wywoływania.
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
ms.openlocfilehash: 0bf08c45e82dc6f36d4e179e95e1b58e655b14db
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224371"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-a-react-spa-using-the-auth-code-flow"></a>Szybki Start: Logowanie i uzyskiwanie tokenu dostępu w reakcji SPA przy użyciu przepływu kodu uwierzytelniania

W tym przewodniku szybki start pobierasz i uruchamiasz przykładowy kod, który demonstruje, jak skrypt JavaScript reaguje na aplikacje jednostronicowe (SPA) i Wywołaj Microsoft Graph przy użyciu przepływu kodu autoryzacji. Przykład kodu demonstruje, jak uzyskać token dostępu, aby wywołać interfejs API Microsoft Graph lub dowolny internetowy interfejs API. 

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

Ten przewodnik Szybki Start używa reakcji MSAL z przepływem kodu autoryzacji. W przypadku podobnego przewodnika Szybki Start, który używa MSAL.js z niejawnym przepływem, zobacz [Szybki Start: Logowanie użytkowników w aplikacjach jednostronicowych języka JavaScript](./quickstart-v2-javascript.md).

> [!IMPORTANT]
> Reagowanie na MSAL [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz bezpłatnie subskrypcję platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express): Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu
>
> 1. Przejdź do środowiska szybkiego startu w <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal rejestracje aplikacji</a> .
> 1. Wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. Przejdź do okienka szybkiego startu i postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2 (ręczna): Zarejestruj i ręcznie skonfiguruj aplikację oraz przykład kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. W obszarze **Konfiguracja platformy** wybierz pozycję **Dodaj platformę**. W otwartym okienku wybierz pozycję **aplikacja jednostronicowa**.
> 1. Ustaw wartość **identyfikatorów URI przekierowania** na `http://localhost:3000/` . Jest to domyślny port NodeJS nasłuchuje na komputerze lokalnym. Po pomyślnym uwierzytelnieniu użytkownika zwrócimy odpowiedź uwierzytelniania do tego identyfikatora URI. 
> 1. Wybierz pozycję **Konfiguruj** , aby zastosować zmiany.
> 1. W obszarze **konfiguracje platformy** rozwiń **aplikację jednostronicową**.
> 1. Upewnij się, że w obszarze **przyznane typy** ![ już skonfigurowano ](media/quickstart-v2-javascript/green-check.png) Identyfikator URI przekierowania jest uprawniony do przepływu kodu autoryzacji z PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby uzyskać przykładowy kod w tym przewodniku szybki start do pracy, Dodaj **Identyfikator URI przekierowania** `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

> [!div renderon="docs"]
> Aby uruchomić projekt z serwerem sieci Web przy użyciu Node.js, [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Uruchom projekt z serwerem sieci Web za pomocą Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Krok 3. Konfigurowanie aplikacji JavaScript
>
> W folderze *src* otwórz plik *authConfig.js* i zaktualizuj `clientID` `authority` wartości,, i `redirectUri` w `msalConfig` obiekcie.
>
> ```javascript
> /**
> * Configuration object to be passed to MSAL instance on creation. 
> * For a full list of MSAL.js configuration parameters, visit:
> * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md 
> */
> export const msalConfig = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>        redirectUri: "Enter_the_Redirect_Uri_Here"
>    },
>    cache: {
>        cacheLocation: "sessionStorage", // This configures where your cache will be stored
>        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    },
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Zmodyfikuj wartości w `msalConfig` sekcji zgodnie z opisem w tym miejscu:
>
> - `Enter_the_Application_Id_Here` to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.
>
>    Aby znaleźć wartość **identyfikatora aplikacji (klienta)**, przejdź do strony **Przegląd** rejestracji aplikacji w Azure Portal.
> - `Enter_the_Cloud_Instance_Id_Here` jest wystąpieniem chmury platformy Azure. W głównej lub globalnej chmurze platformy Azure wprowadź wartość `https://login.microsoftonline.com/` . W przypadku chmur **narodowych** (na przykład Chin), zobacz [chmury narodowe](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` jest ustawiony na jedną z następujących wartości:
>   - Jeśli aplikacja obsługuje *konta w tym katalogu organizacji*, Zamień tę wartość na **Identyfikator dzierżawy** lub **nazwę dzierżawy**. Na przykład `contoso.microsoft.com`.
>
>    Aby znaleźć wartość **identyfikatora katalogu (dzierżawy)**, przejdź do strony **Przegląd** rejestracji aplikacji w Azure Portal.
>   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość wartością `organizations` .
>   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość wartością `common` . **W tym przewodniku szybki start Użyj programu** `common` .
>   - Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość wartością `consumers` .
>
>    Aby znaleźć wartość **obsługiwanych typów kont**, przejdź do strony **Przegląd** rejestracji aplikacji w Azure Portal.
> - `Enter_the_Redirect_Uri_Here` to `http://localhost:3000/`.
>
> `authority`Wartość w *authConfig.js* powinna wyglądać podobnie do poniższego, jeśli używasz głównej (globalnej) chmury platformy Azure:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Twoja aplikacja jest skonfigurowana i gotowa do uruchomienia
> Twój projekt został skonfigurowany z wartościami właściwości aplikacji.

> [!div renderon="docs"]
>
> Przewiń w dół do tego samego pliku i zaktualizuj `graphMeEndpoint` . 
> - Zastąp ciąg `Enter_the_Graph_Endpoint_Herev1.0/me` ciągiem `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` jest punktem końcowym, z którym będą wykonywane wywołania interfejsu API. W przypadku usługi interfejsu API Main (Global) Microsoft Graph wprowadź `https://graph.microsoft.com/` wartość (Dołącz końcowy ukośnik do przodu). Więcej informacji zawiera strona [dokumentacji](/graph/deployments).
>
>
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>    export const graphConfig = {
>        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me"
>    };
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
1. Przejdź na stronę `http://localhost:3000/`.

1. Wybierz pozycję **Zaloguj** , aby uruchomić proces logowania, a następnie Wywołaj interfejs API Microsoft Graph.

    Przy pierwszym logowaniu zostanie wyświetlony monit o podanie zgody na umożliwienie aplikacji dostępu do Twojego profilu oraz zalogowanie się. Po pomyślnym zalogowaniu kliknij **Informacje o profilu żądania** , aby wyświetlić informacje o profilu na stronie.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład

![Diagram przedstawiający przepływ kodu autoryzacji dla aplikacji jednostronicowej.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

Biblioteka MSAL.js umożliwia użytkownikom i żąda tokenów, które są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft.

Jeśli zainstalowano Node.js, możesz pobrać najnowszą wersję przy użyciu Menedżera pakietów Node.js (npm):

```console
npm install @azure/msal-browser @azure/msal-react
```

## <a name="next-steps"></a>Następne kroki

Szczegółowe instrukcje krok po kroku dotyczące tworzenia aplikacji przepływu kodu uwierzytelniania przy użyciu polecenia JavaScript można znaleźć w następującym samouczku:

> [!div class="nextstepaction"]
> [Samouczek umożliwiający zalogowanie się i Wywołaj program MS Graph](./tutorial-v2-javascript-auth-code.md)