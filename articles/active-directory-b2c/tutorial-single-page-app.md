---
title: 'Samouczek: Włączanie uwierzytelniania w aplikacji jednostronicowej'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak za pomocą usługi Azure Active Directory B2C zapewnić Logowanie użytkownika dla aplikacji jednostronicowej opartej na języku JavaScript.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3a3eb77315953c3791e09c4326af7cc3e3231a69
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670036"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Samouczek: Włączanie uwierzytelniania w aplikacji jednostronicowej przy użyciu Azure AD B2C

W tym samouczku pokazano, jak używać programu Azure Active Directory B2C (Azure AD B2C) do rejestrowania i logowania użytkowników w aplikacji jednostronicowej (SPA) przy użyciu niejawnego przepływu OAuth 2,0.

W tym samouczku pierwszy z serii dwóch części:

> [!div class="checklist"]
> * Dodawanie adresu URL odpowiedzi do aplikacji zarejestrowanej w dzierżawie Azure AD B2C
> * Pobierz przykład kodu z usługi GitHub
> * Modyfikowanie kodu przykładowej aplikacji w celu współdziałania z dzierżawcą
> * Utwórz konto za pomocą przepływu użytkownika dotyczącego rejestracji/logowania

[Następny samouczek](tutorial-single-page-app-webapi.md) z serii obejmuje część interfejsu API sieci Web przykład kodu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku potrzebne są następujące Azure AD B2C zasoby:

* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja zarejestrowana](tutorial-register-spa.md) w dzierżawie (Użyj niejawnych opcji przepływu)
* [Przepływy użytkowników utworzone](tutorial-create-user-flows.md) w dzierżawie

Ponadto w lokalnym środowisku programistycznym są potrzebne następujące elementy:

* [Visual Studio Code](https://code.visualstudio.com/) lub inny edytor kodu
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W drugim samouczku, który został ukończony w ramach wymagań wstępnych, zarejestrowano aplikację sieci Web w Azure AD B2C. Aby włączyć komunikację z przykładem kodu w tym samouczku, Dodaj adres URL odpowiedzi (nazywany także identyfikatorem URI przekierowania) do rejestracji aplikacji.

Aby zaktualizować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C** . Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C** .
1. Wybierz pozycję **rejestracje aplikacji** , wybierz kartę **posiadane aplikacje** , a następnie wybierz aplikację *webapp1* .
1. W obszarze **Sieć Web** wybierz łącze **Dodaj identyfikator URI** , a następnie wprowadź `http://localhost:6420` .
1. W obszarze **niejawne przyznanie** zaznacz pola wyboru **tokenów dostępu** i **tokenów identyfikatorów** , jeśli nie została jeszcze wybrana, a następnie wybierz pozycję **Zapisz** .
1. Wybierz pozycję **Omówienie** .
1. Rejestrowanie **identyfikatora aplikacji (klienta)** do użycia w późniejszym kroku podczas aktualizowania kodu w jednostronicowej aplikacji sieci Web.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **Azure AD B2C** .
1. Wybierz pozycję **aplikacje (starsza wersja)** , a następnie wybierz aplikację *webapp1* .
1. W obszarze **Adres URL odpowiedzi** dodaj `http://localhost:6420`.
1. Wybierz pozycję **Zapisz** .
1. Na stronie właściwości Zapisz **Identyfikator aplikacji** . IDENTYFIKATORA aplikacji można użyć w późniejszym kroku podczas aktualizowania kodu w jednostronicowej aplikacji sieci Web.

* * *

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

W tym samouczku skonfigurujesz przykładowy kod pobrany z usługi GitHub do pracy z dzierżawcą B2C. Przykład demonstruje, jak aplikacja jednostronicowa może używać Azure AD B2C do tworzenia kont i logowania użytkowników, a także do wywoływania chronionego internetowego interfejsu API (w następnym samouczku w serii) zostanie włączony internetowy interfejs API.

[Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizowanie przykładu

Teraz, gdy uzyskano przykład, zaktualizuj kod przy użyciu nazwy dzierżawy Azure AD B2C i identyfikatora aplikacji zapisanego w poprzednim kroku.

1. Otwórz plik *authConfig.js* w folderze *JavaScriptSPA* .
1. W `msalConfig` obiekcie zaktualizuj:
    * `clientId` z wartością z **identyfikatorem aplikacji (klienta)** zapisanym we wcześniejszym kroku
    * `authority` Identyfikator URI z nazwą dzierżawy Azure AD B2C i nazwą przepływu użytkownika tworzenia konta/logowania utworzonego w ramach wymagań wstępnych (na przykład *B2C_1_signupsignin1* )

    ```javascript
    const msalConfig = {
        auth: {
          clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
          authority: b2cPolicies.authorities.signUpSignIn.authority,
          validateAuthority: false
        },
        cache: {
          cacheLocation: "localStorage",
          storeAuthStateInCookie: true
        }
    };

    const loginRequest = {
       scopes: ["openid", "profile"],
    };

    const tokenRequest = {
      scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
    };
    ```

1. Otwórz plik *authConfig.js* w folderze *JavaScriptSPA* .
1. W `msalConfig` obiekcie zaktualizuj:
    * `clientId` przy użyciu **identyfikatora aplikacji (klienta)** zapisanego we wcześniejszym kroku
    * `authority` Identyfikator URI z nazwą dzierżawy Azure AD B2C i nazwą przepływu użytkownika tworzenia konta/logowania utworzonego w ramach wymagań wstępnych (na przykład *B2C_1_signupsignin1* )
1. Otwórz plik *policies.js* .
1. Znajdź wpisy dla `names` i `authorities` zastąp je odpowiednio odpowiednimi nazwami zasad, które zostały utworzone w kroku 2. Zamień na `fabrikamb2c.onmicrosoft.com` nazwę dzierżawy Azure AD B2C, na przykład `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Otwórz plik *apiConfig.js* .
1. Znajdź przypisanie zakresów `b2cScopes` i Zastąp adres URL zakresem, który został utworzony dla internetowego interfejsu API, na przykład `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. Znajdź przypisanie dla adresu URL interfejsu API `webApi` i zastąp bieżący adres URL adresem URL, na którym został wdrożony internetowy interfejs API w kroku 4, na przykład `webApi: http://localhost:5000/hello` .

Otrzymany kod powinien wyglądać następująco:

### <a name="authconfigjs"></a>authConfig.js

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```
### <a name="policiesjs"></a>policies.js

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```
### <a name="apiconfigjs"></a>apiConfig.js

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Otwórz okno konsoli i przejdź do katalogu zawierającego przykład. Na przykład:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Uruchom następujące polecenia:

    ```console
    npm install && npm update
    npm start
    ```

    W oknie konsoli jest wyświetlany numer portu lokalnego uruchomionego serwera Node.js:

    ```console
    Listening on port 6420...
    ```
1. Przejdź do strony, aby `http://localhost:6420` wyświetlić aplikację sieci Web działającą na komputerze lokalnym.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Przeglądarka sieci Web przedstawiająca aplikację jednostronicową działającą lokalnie":::

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

Ta przykładowa aplikacja obsługuje rejestrowanie, logowanie i resetowanie hasła. W tym samouczku utworzysz konto przy użyciu adresu e-mail.

1. Wybierz pozycję **Zaloguj** , aby zainicjować przepływ użytkownika *B2C_1_signupsignin1* określonego we wcześniejszym kroku.
1. Azure AD B2C przedstawia stronę logowania, która zawiera link do rejestracji. Ponieważ jeszcze nie masz konta, wybierz link **Utwórz konto teraz** .
1. Przepływ pracy tworzenia konta przedstawia stronę do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji również zbiera hasło użytkownika i wymagane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Przeglądarka sieci Web przedstawiająca aplikację jednostronicową działającą lokalnie":::

1. Wybierz pozycję **Utwórz** , aby utworzyć konto lokalne w katalogu Azure AD B2C.

Po wybraniu opcji **Utwórz** aplikacja wyświetli nazwę zalogowanego użytkownika.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Przeglądarka sieci Web przedstawiająca aplikację jednostronicową działającą lokalnie":::

Jeśli chcesz przetestować logowanie, wybierz przycisk **Wyloguj** się, a następnie wybierz pozycję **Zaloguj** się i zaloguj się przy użyciu adresu e-mail i hasła wprowadzonego podczas rejestracji.

### <a name="what-about-calling-the-api"></a>Co z wywołaniem interfejsu API?

Jeśli wybierzesz przycisk **wywoływania interfejsu API** po zalogowaniu, zobaczysz stronę ze strumieniem rejestracji/logowania użytkownika zamiast wyników wywołania interfejsu API. Jest to oczekiwane, ponieważ część interfejsu API aplikacji nie została jeszcze skonfigurowana w celu komunikowania się z aplikacją internetowego interfejsu API zarejestrowanej w dzierżawie *Azure AD B2C.*

W tym momencie aplikacja nadal próbuje komunikować się z interfejsem API zarejestrowanym w dzierżawie demonstracyjnej (fabrikamb2c.onmicrosoft.com) i ponieważ nie masz uwierzytelnienia z tą dzierżawą, zostanie wyświetlona strona Rejestrowanie/logowanie.

Przejdź do następnego samouczka w serii w, aby włączyć chroniony interfejs API (zobacz sekcję [następne kroki](#next-steps) ).

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano aplikację jednostronicową do pracy z przepływem użytkownika w dzierżawie Azure AD B2C, aby zapewnić możliwość tworzenia konta i logowania. Wykonano następujące kroki:

> [!div class="checklist"]
> * Dodano adres URL odpowiedzi do aplikacji zarejestrowanej w dzierżawie Azure AD B2C
> * Pobieranie przykładu kodu z usługi GitHub
> * Zmodyfikowano kod przykładowej aplikacji do pracy z dzierżawcą
> * Rejestracja przy użyciu przepływu użytkownika dotyczącego rejestracji/logowania

Teraz przejdź do następnego samouczka w serii, aby udzielić dostępu do chronionego internetowego interfejsu API przy użyciu SPA:

> [!div class="nextstepaction"]
> [Samouczek: Ochrona i udzielanie dostępu do internetowego interfejsu API z jednostronicowej aplikacji >](tutorial-single-page-app-webapi.md)
