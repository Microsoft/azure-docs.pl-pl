---
title: 'Szybki Start: Logowanie użytkowników w aplikacjach jednostronicowych na platformie Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja kątowa może wywołać interfejs API, który wymaga tokenów dostępu wystawionych przez platformę tożsamości firmy Microsoft.
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
ms.openlocfilehash: 0de3c46eba0b9364e38c4a952a320ac1c3de512f
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583573"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Szybki Start: Logowanie użytkowników i uzyskiwanie tokenu dostępu w jednostronicowej aplikacji pojedynczej

W tym przewodniku szybki start pobrano i uruchomimy przykład kodu, który demonstruje, jak aplikacja jednostronicowa (SPA) może logować się do użytkowników i wywoływać Microsoft Graph. Przykład kodu demonstruje, jak uzyskać token dostępu, aby wywołać interfejs API Microsoft Graph lub dowolny internetowy interfejs API.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) edytowania plików projektu lub [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) , aby uruchomić projekt.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji szybkiego startu
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opcja 1 (Express): Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykładowy kod
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Jeśli Twoje konto ma dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu w dzierżawie usługi Azure Active Directory (Azure AD), która ma być używana.
> 1. Otwórz okienko nowe [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) w Azure Portal.
> 1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **zarejestruj**.
> 1. Przejdź do okienka szybkiego startu i Wyświetl kątowy przewodnik szybkiego startu. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opcja 2 (ręczna): Zarejestruj i ręcznie skonfiguruj przykład aplikacji i kodu
>
> #### <a name="step-1-register-the-application"></a>Krok 1. rejestrowanie aplikacji
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Jeśli Twoje konto ma dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu i ustaw sesję portalu dla dzierżawy usługi Azure AD, której chcesz użyć.
> 1. Postępuj zgodnie z instrukcjami, aby [zarejestrować aplikację jednostronicową](./scenario-spa-app-registration.md) w Azure Portal.
> 1. Dodaj nową platformę w okienku **uwierzytelnianie** rejestracji aplikacji i zarejestruj identyfikator URI przekierowania: `http://localhost:4200/` .
> 1. Ten przewodnik Szybki Start używa [niejawnego przepływu dotacji](v2-oauth2-implicit-grant-flow.md). W sekcji **niejawne udzielenie i przepływy hybrydowe** wybierz pozycję **identyfikatory tokeny** i **tokeny dostępu**. Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja loguje się do użytkowników i wywołuje interfejs API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w Azure Portal
> Aby przykład kodu w tym przewodniku szybki start działał, należy dodać identyfikator URI przekierowania **http://localhost:4200/** i włączyć **niejawny** przydział.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-code-sample"></a>Krok 2. Pobieranie przykładu kodu
>[!div renderon="docs"]
>Aby uruchomić projekt z serwerem sieci Web przy użyciu Node.js, [Sklonuj przykładowe repozytorium](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) lub [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Otwórz pliki przy użyciu edytora, takiego jak Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Krok 3. Konfigurowanie aplikacji JavaScript
>
> W folderze *src/App* Edytuj *aplikację App. module. TS* i ustaw `clientId` `authority` wartości i w obszarze `MsalModule.forRoot` .
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
> Zamień te wartości:
>
>|Nazwa wartości|Opis|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na stronie **Przegląd** rejestracji aplikacji jest to wartość **identyfikatora aplikacji (klienta)** . |
>|Enter_the_Cloud_Instance_Id_Here|Jest to wystąpienie chmury platformy Azure. W głównej lub globalnej chmurze platformy Azure wprowadź wartość **https://login.microsoftonline.com** . W przypadku chmur narodowych (na przykład Chin), zobacz [chmury narodowe](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Ustaw jedną z następujących opcji: Jeśli aplikacja obsługuje *konta w tym katalogu organizacji*, Zastąp tę wartość identyfikatorem katalogu (dzierżawy) lub nazwą dzierżawy (na przykład **contoso.Microsoft.com**). Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**. Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**. |
>|Enter_the_Redirect_Uri_Here|Zamień na **http://localhost:4200** .|
>|cacheLocation  | Obowiązkowe Ustaw magazyn przeglądarki dla stanu uwierzytelniania. Wartość domyślna to **sessionStorage**.   |
>|storeAuthStateInCookie  | Obowiązkowe Zidentyfikuj bibliotekę, w której jest przechowywany stan żądania uwierzytelnienia. Ten stan jest wymagany do weryfikowania przepływów uwierzytelniania w plikach cookie w przeglądarce. Ten plik cookie jest ustawiany dla programu Internet Explorer i programu Edge, aby umożliwić te dwie przeglądarki. Aby uzyskać więcej informacji, zobacz [znane problemy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji klienckich](msal-js-initializing-client-applications.md).

Kod źródłowy biblioteki MSAL.js można znaleźć w repozytorium [AzureAD/Microsoft-Authentication-Library-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) w witrynie GitHub.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Krok 3. uruchamianie projektu

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Krok 4. uruchamianie projektu

Jeśli używasz środowiska Node.js:

1. Uruchom serwer, uruchamiając następujące polecenia w katalogu projektu:

   ```console
   npm install
   npm start
   ```

1. Przejdź do **http://localhost:4200/**.
1. Wybierz pozycję **Zaloguj**.
1. Wybierz **profil** , aby wywołać Microsoft Graph.

Po załadowaniu aplikacji przez przeglądarkę wybierz pozycję **Zaloguj**. Po pierwszym zalogowaniu zostanie wyświetlony monit o zezwolenie aplikacji na dostęp do Twojego profilu i zalogowanie się. Po pomyślnym zalogowaniu wybierz pozycję **profil**, a na stronie zostaną wyświetlone informacje o profilu użytkownika.

## <a name="how-the-sample-works"></a>Jak działa przykład

![Diagram pokazujący, jak działa Przykładowa aplikacja w tym przewodniku Szybki Start](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>Następne kroki

Następnie Dowiedz się, jak zalogować użytkownika i uzyskać tokeny w samouczku kątowym:

> [!div class="nextstepaction"]
> [Samouczek kątowy](./tutorial-v2-angular.md)