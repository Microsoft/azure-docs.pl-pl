---
title: 'Szybki start: logowanie użytkowników w aplikacjach jednostronicowych angular — Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku Szybki start dowiesz się, jak aplikacja Angular może wywołać interfejs API, który wymaga tokenów dostępu wystawionych przez platformę tożsamości firmy Microsoft.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: d53ce97c4af302801098d9abaa633ced98c93f3a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814032"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Szybki start: logowanie użytkowników i uzyskiwanie tokenu dostępu w aplikacji jednostronicowej usługi Angular

W tym przewodniku Szybki start pobierzesz i uruchomesz przykładowy kod, który pokazuje, jak jednostronicowa aplikacja Angular może logować użytkowników i Microsoft Graph. Przykładowy kod pokazuje, jak uzyskać token dostępu w celu wywołania interfejsu API Microsoft Graph api lub dowolnego internetowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js. ](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) edytować pliki projektu lub Visual Studio [2019 r.](https://visualstudio.microsoft.com/downloads/) w celu uruchomienia projektu.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
>
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opcja 1 (ekspresowa): zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do Azure Portal <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">— Rejestracje aplikacji</a> szybki start.
> 1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **Zarejestruj**.
> 1. W okienku Szybki start znajdź przewodnik Szybki start angular. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opcja 2 (ręczna): rejestrowanie i ręczne konfigurowanie aplikacji i przykładowego kodu
>
> #### <a name="step-1-register-the-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Postępuj zgodnie z [instrukcjami, aby zarejestrować aplikację jednostronicową](./scenario-spa-app-registration.md) w Azure Portal.
> 1. Dodaj nową platformę w **okienku Uwierzytelnianie** rejestracji aplikacji i zarejestruj adres URI przekierowania: `http://localhost:4200/` .
> 1. W tym przewodniku Szybki start jest [używany niejawny przepływ udzielania](v2-oauth2-implicit-grant-flow.md). W sekcji **Niejawne udzielanie i przepływy hybrydowe** wybierz pozycję **Tokeny identyfikatorów i Tokeny** **dostępu.** Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja podpisuje użytkowników i wywołuje interfejs API.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w Azure Portal
>
> Aby przykładowy kod w tym przewodniku Szybki start działał, należy dodać do funkcji przekierowywanie URI i włączyć `http://localhost:4200/` **niejawne udzielenie**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-code-sample"></a>Krok 2. Pobieranie przykładowego kodu
>[!div renderon="docs"]
>Aby uruchomić projekt z serwerem internetowym przy użyciu programu Node.js, sklonuj przykładowe [repozytorium](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) lub [pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Otwórz pliki w edytorze, takim jak Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobieranie przykładowego kodu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Krok 3. Konfigurowanie aplikacji JavaScript
>
> W *folderze src/app* edytuj *plik app.module.ts* i ustaw wartości `clientId` i w obszarze `authority` `MsalModule.forRoot` .
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Zastąp następujące wartości:
>
>|Nazwa wartości|Opis|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na stronie **Przegląd** rejestracji aplikacji jest to wartość Identyfikatora aplikacji **(klienta).** |
>|Enter_the_Cloud_Instance_Id_Here|Jest to wystąpienie chmury platformy Azure. W przypadku głównej lub globalnej chmury platformy Azure wprowadź `https://login.microsoftonline.com` . W przypadku chmur krajowych (na przykład w Chinach) zobacz [Chmury krajowe](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Ustaw na jedną z następujących opcji: Jeśli aplikacja obsługuje konta w tym katalogu organizacyjnym, zastąp tę wartość identyfikatorem katalogu (dzierżawy) lub nazwą dzierżawy (na przykład `contoso.microsoft.com` ). Jeśli aplikacja obsługuje konta *w dowolnym katalogu organizacyjnym,* zastąp tę wartość wartością `organizations` . Jeśli aplikacja obsługuje konta *w dowolnym katalogu organizacyjnym* i osobiste konta Microsoft, zastąp tę wartość wartością `common` . Aby ograniczyć obsługę tylko *do osobistych kont Microsoft,* zastąp tę wartość wartością `consumers` . |
>|Enter_the_Redirect_Uri_Here|Zamień na `http://localhost:4200` .|
>|cacheLocation  | (Opcjonalnie) Ustaw magazyn przeglądarki dla stanu uwierzytelniania. Wartość domyślna to `sessionStorage`.   |
>|storeAuthStateInCookie  | (Opcjonalnie) Zidentyfikuj bibliotekę, która przechowuje stan żądania uwierzytelniania. Ten stan jest wymagany do zweryfikowania przepływów uwierzytelniania w plikach cookie przeglądarki. Ten plik cookie jest ustawiany dla Internet Explorer i Microsoft Edge w celu uwzględnienia tych dwóch przeglądarek. Aby uzyskać więcej informacji, zobacz [znane problemy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
>
> Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

> Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji klienckich.](msal-js-initializing-client-applications.md)

> Kod źródłowy biblioteki MSAL.js można znaleźć w repozytorium [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) w witrynie GitHub.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3. Aplikacja jest skonfigurowana i gotowa do uruchomienia
> Skonfigurowaliśmy projekt przy użyciu wartości właściwości aplikacji.

> [!div renderon="docs"]
>
> Przewiń w dół w tym samym pliku i zaktualizuj plik `graphMeEndpoint` . 
> - Zastąp ciąg `Enter_the_Graph_Endpoint_Herev1.0/me` ciągiem `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` to punkt końcowy, względem których będą dokonywane wywołania interfejsu API. W przypadku głównej (globalnej) Microsoft Graph API wprowadź `https://graph.microsoft.com/` (dołącz ukośnik). Więcej informacji zawiera strona [dokumentacji](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Krok 4. Uruchamianie projektu

Jeśli używasz środowiska Node.js:

1. Uruchom serwer, uruchamiając następujące polecenia w katalogu projektu:

   ```console
   npm install
   npm start
   ```

1. Przejdź do witryny `http://localhost:4200/`.
1. Wybierz **pozycję Zaloguj się.** Podczas pierwszego logowania zostanie wyświetlony monit o umożliwienie aplikacji dostępu do Twojego profilu i automatyczne zalogowanie Cię.
1. Wybierz **pozycję Profil,** aby wywołać Microsoft Graph. Informacje o profilu użytkownika są wyświetlane na stronie.

## <a name="how-the-sample-works"></a>Jak działa przykład

![Diagram przedstawiający sposób działania przykładowej aplikacji w tym przewodniku Szybki start.](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zalogować użytkownika i uzyskać tokeny w samouczku usługi Angular:

> [!div class="nextstepaction"]
> [Samouczek dotyczący angular](./tutorial-v2-angular.md)
