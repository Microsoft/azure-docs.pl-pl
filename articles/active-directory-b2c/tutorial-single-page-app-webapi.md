---
title: 'Samouczek: Ochrona Node.js internetowego interfejsu API przy użyciu Azure AD B2C i udzielanie dostępu do aplikacji jednostronicowej (SPA)'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak za pomocą Active Directory B2C chronić Node.js internetowy interfejs API i wywoływać go z aplikacji jednostronicowej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9fe1363ffc714754c1de333a77d36595ce4223e6
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442341"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Samouczek: Ochrona i udzielanie dostępu do Node.js internetowego interfejsu API z aplikacji jednostronicowej z Azure AD B2C

W tym samouczku pokazano, jak wywoływać interfejs API sieci Web Node.js chronionych Azure Active Directory B2C (Azure AD B2C) z aplikacji jednostronicowej.

W tym samouczku drugi w serii dwóch części:

> [!div class="checklist"]
> * Tworzenie rejestracji aplikacji internetowego interfejsu API w dzierżawie Azure AD B2C
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Modyfikowanie przykładu kodu internetowego interfejsu API do pracy z dzierżawcą

W [pierwszym samouczku](tutorial-single-page-app.md) w tej serii pobrano przykładowy kod i zmodyfikowano go w celu logowania użytkowników przy użyciu przepływu użytkownika w dzierżawie Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki i wymagania wstępne w [samouczku: Włączanie uwierzytelniania w aplikacji jednostronicowej przy użyciu Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) lub inny edytor kodu
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zapisz wartość w obszarze **zakresy** dla `demo.read` zakresu, który ma być używany w późniejszym kroku podczas konfigurowania aplikacji jednostronicowej. Pełny zakres wartości jest podobny do `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z innej aplikacji, należy przyznać tej aplikacji uprawnienia do internetowego interfejsu API.

W samouczku wymagań wstępnych została utworzona aplikacja sieci Web o nazwie *webapp1*. W tym samouczku skonfigurujesz aplikację do wywoływania internetowego interfejsu API utworzonego w poprzedniej sekcji *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Aplikacja sieci Web na jednej stronie ma teraz przyznane uprawnienia do chronionego internetowego interfejsu API dla określonych zakresów. Użytkownik jest uwierzytelniany przy użyciu Azure AD B2C do korzystania z aplikacji jednostronicowej. Aplikacja jednostronicowa używa przepływu grantu autoryzacji do uzyskiwania dostępu do chronionego internetowego interfejsu API za pomocą tokenu dostępu zwróconego przez Azure AD B2C.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Teraz, gdy internetowy interfejs API jest zarejestrowany i zdefiniowane zakresy, skonfiguruj kod internetowego interfejsu API do pracy z dzierżawą Azure AD B2C. W tym samouczku zostanie skonfigurowany przykładowy Node.js internetowy interfejs API pobierany z usługi GitHub.

[Pobierz \* archiwum zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) lub Sklonuj przykładowy projekt internetowego interfejsu API z usługi GitHub. Możesz także przejść bezpośrednio do projektu [Azure-Samples/Active-Directory-B2C-JavaScript-NodeJS-WebAPI](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) w witrynie GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz *config.jsw* pliku w edytorze kodu.
1. Zmodyfikuj wartości zmiennych w celu odzwierciedlenia wcześniej utworzonej rejestracji aplikacji. Należy również zaktualizować `policyName` program przy użyciu przepływu użytkownika utworzonego w ramach wymagań wstępnych. Na przykład *B2C_1_signupsignin1*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>Włączanie mechanizmu CORS

Aby umożliwić aplikacji jednostronicowej wywoływanie Node.js internetowego interfejsu API, należy włączyć funkcję [CORS](https://expressjs.com/en/resources/middleware/cors.html) w interfejsie API sieci Web. W aplikacji produkcyjnej należy zachować ostrożność, która domena zgłasza żądanie, ale na potrzeby tego samouczka zezwala na żądania z dowolnej domeny.

Aby włączyć mechanizm CORS, użyj następującego oprogramowania pośredniczącego. Przykład kodu internetowego interfejsu API Node.js w tym samouczku został już dodany do pliku *index.js* .

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Konfigurowanie aplikacji jednostronicowej

Aplikacja jednostronicowa (SPA) z [poprzedniego samouczka](tutorial-single-page-app.md) w serii używa Azure AD B2C do rejestracji i logowania użytkowników, a domyślnie wywołuje Node.js internetowy interfejs API chroniony przez dzierżawę demonstracyjną *fabrikamb2c* .

Ta sekcja umożliwia zaktualizowanie jednostronicowej aplikacji sieci Web w celu wywołania Node.js internetowego interfejsu API chronionego *przez* dzierżawę Azure AD B2C (i uruchamiana na maszynie lokalnej).

Aby zmienić ustawienia w SPA:

1. W poprzednim samouczku do projektu [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] , który został pobrany lub sklonowany, otwórz plik *apiConfig.js* wewnątrz folderu *JavaScriptSPA* .
1. Skonfiguruj przykład za pomocą identyfikatora URI dla *demonstracji.* wcześniej utworzony zakres odczytu i adres URL internetowego interfejsu API.
    1. W `apiConfig` definicji Zastąp `b2cScopes` wartość pełnym identyfikatorem URI dla *demonstracji. Odczytaj* zakres (wartość **zakresu** zarejestrowana wcześniej).
    1. Zmień domenę w `webApi` wartość na identyfikator URI przekierowania, który został dodany podczas rejestrowania aplikacji internetowego interfejsu API we wcześniejszym kroku.

    Ponieważ interfejs API jest dostępny w `/hello` punkcie końcowym, należy pozostawić */Hello* w identyfikatorze URI.

    `apiConfig`Definicja powinna wyglądać podobnie do następującego bloku kodu, ale z nazwą dzierżawy B2C w miejscu `<your-tenant-name>` :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Uruchamianie SPA i internetowego interfejsu API

Teraz można przystąpić do testowania dostępu do zakresu aplikacji jednostronicowej do interfejsu API. Uruchom zarówno Node.js Web API, jak i przykładową aplikację jednostronicową JavaScript na komputerze lokalnym. Następnie zaloguj się do aplikacji jednostronicowej i wybierz przycisk **Wywołaj interfejs API** , aby zainicjować żądanie do CHRONIONEGO interfejsu API.

Mimo że obie aplikacje działają lokalnie w ramach tego samouczka, zostały one skonfigurowane do używania Azure AD B2C do bezpiecznego rejestrowania/logowania i udzielania dostępu do chronionego internetowego interfejsu API.

### <a name="run-the-nodejs-web-api"></a>Uruchamianie interfejsu API sieci Web Node.js

1. Otwórz okno konsoli i przejdź do katalogu zawierającego przykład internetowego interfejsu API Node.js. Na przykład:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Uruchom następujące polecenia:

    ```console
    npm install && npm update
    node index.js
    ```

    W oknie konsoli jest wyświetlany numer portu, w którym aplikacja jest hostowana.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Uruchamianie aplikacji jednostronicowej

1. Otwórz inne okno konsoli i przejdź do katalogu zawierającego próbę SPA JavaScript. Na przykład:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Uruchom następujące polecenia:

    ```console
    npm install && npm update
    npm start
    ```

    W oknie konsoli jest wyświetlany numer portu, w którym jest hostowana aplikacja.

    ```console
    Listening on port 6420...
    ```

1. Przejdź do `http://localhost:6420` przeglądarki, aby wyświetlić aplikację.

    ![Przykładowa aplikacja jednostronicowa wyświetlana w przeglądarce](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [poprzednim samouczku](tutorial-single-page-app.md). Po pomyślnym zalogowaniu powinien zostać wyświetlony `User 'Your Username' logged-in` komunikat.
1. Wybierz przycisk **wywoływania interfejsu API** . SPA uzyskuje autoryzację od Azure AD B2C, następnie uzyskuje dostęp do chronionego internetowego interfejsu API w celu wyświetlenia nazwy zalogowanego użytkownika:

    ![Aplikacja jednostronicowa w przeglądarce przedstawiająca wynik JSON elementu username zwrócony przez interfejs API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie rejestracji aplikacji internetowego interfejsu API w dzierżawie Azure AD B2C
> * Skonfigurowane zakresy dla internetowego interfejsu API
> * Przyznano uprawnienia do internetowego interfejsu API
> * Modyfikowanie przykładu kodu internetowego interfejsu API do pracy z dzierżawcą

Teraz, gdy zostało wystawione żądanie SPA zasobu z chronionego internetowego interfejsu API, uzyskaj dokładniejsze informacje o tym, jak te typy aplikacji współdziałają ze sobą i z Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikacji, które mogą być używane w Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
