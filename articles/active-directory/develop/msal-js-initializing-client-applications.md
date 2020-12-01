---
title: Inicjuj MSAL.js aplikacje klienckie | Azure
titleSuffix: Microsoft identity platform
description: Informacje na temat inicjowania aplikacji klienckich przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 6b5093c5a1a45aed3493fabd7a362b0579998171
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96343590"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicjuj aplikacje klienckie przy użyciu MSAL.js

W tym artykule opisano inicjowanie biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) z wystąpieniem aplikacji agent użytkownika.

Aplikacja agent użytkownika jest formą publicznej aplikacji klienckiej, w której kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka sieci Web. Tacy klienci nie przechowują wpisów tajnych, ponieważ kontekst przeglądarki jest otwarty.

Aby dowiedzieć się więcej na temat typów aplikacji klienta i opcji konfiguracji aplikacji, zobacz [publiczne i poufne aplikacje klienckie w programie MSAL](msal-client-applications.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainicjowaniem aplikacji należy najpierw [zarejestrować ją w Azure Portal](scenario-spa-app-registration.md), ustanawiając relację zaufania między aplikacją i platformą tożsamości firmy Microsoft.

Po zarejestrowaniu aplikacji będą potrzebne niektóre lub wszystkie poniższe wartości, które można znaleźć w Azure Portal.

| Wartość | Wymagane | Opis |
|:----- | :------: | :---------- |
| Identyfikator aplikacji (klienta) | Wymagane | Identyfikator GUID, który jednoznacznie identyfikuje aplikację w ramach platformy tożsamości firmy Microsoft. |
| Urząd | Opcjonalne | Adres URL dostawcy tożsamości ( *wystąpienie*) i *odbiorca logowania* dla aplikacji. W przypadku łączenia się z wystąpieniem i logowaniem, należy utworzyć *Urząd*. |
| Identyfikator katalogu (dzierżawcy) | Opcjonalne | Określ tę opcję, jeśli tworzysz aplikację biznesową tylko dla organizacji, która jest często określana jako *aplikacja z jedną dzierżawą*. |
| Identyfikator URI przekierowania | Opcjonalne | Jeśli tworzysz aplikację sieci Web, `redirectUri` określa miejsce, w którym dostawca tożsamości (platforma tożsamości firmy Microsoft) powinien zwrócić tokeny zabezpieczające, które wystawiły. |

## <a name="initialize-msaljs-2x-apps"></a>Inicjowanie aplikacji MSAL.js 2. x

Zainicjuj kontekst uwierzytelniania MSAL, tworząc wystąpienie elementu [PublicClientApplication][msal-js-publicclientapplication] z obiektem [konfiguracji][msal-js-configuration] . Minimalna wymagana właściwość konfiguracji jest `clientID` aplikacją wyświetlaną jako **Identyfikator aplikacji (klienta)** na stronie **przegląd** rejestracji aplikacji w Azure Portal.

Oto przykład obiektu konfiguracji i wystąpienia elementu `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Wywołaj [handleRedirectPromise][msal-js-handleredirectpromise] , gdy aplikacja używa przepływów przekierowywania. W przypadku korzystania z przepływów przekierowywania `handleRedirectPromise` należy uruchamiać każde ładowanie strony.

Istnieją trzy możliwe wyniki z obietnic:

- `.then` jest wywoływany i `tokenResponse` ma prawdziwą wartość: aplikacja zwraca z operacji przekierowania, która zakończyła się pomyślnie.
- `.then` jest wywoływany i `tokenResponse` jest błędna ( `null` ): aplikacja nie zwraca z operacji przekierowania.
- `.catch` jest wywoływany: aplikacja zwraca z operacji przekierowania i wystąpił błąd.

## <a name="initialize-msaljs-1x-apps"></a>Inicjowanie aplikacji MSAL.js 1. x

Zainicjuj kontekst uwierzytelniania MSAL 1. x, tworząc wystąpienie [UserAgentApplication][msal-js-useragentapplication] z obiektem konfiguracji. Minimalna wymagana właściwość konfiguracji jest `clientID` aplikacją wyświetlaną jako **Identyfikator aplikacji (klienta)** na stronie **przegląd** rejestracji aplikacji w Azure Portal.

W przypadku metod uwierzytelniania z przepływami przekierowania ([loginRedirect][msal-js-loginredirect] i [acquireTokenRedirect][msal-js-acquiretokenredirect]) w MSAL.js 1.2. x lub starszych należy jawnie zarejestrować wywołanie zwrotne dla sukcesu lub błędu za pomocą `handleRedirectCallback()` metody. Jawne rejestrowanie wywołania zwrotnego jest wymagane w MSAL.js 1.2. x i starszych, ponieważ przepływy przekierowania nie zwracają niesie obietnice zwiększenia, podobnie jak metody z wyskakującym doświadczeniem. Rejestrowanie wywołania zwrotnego jest *opcjonalne* w MSAL.js wersji 1.3. x i nowszych.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Pojedyncze wystąpienie i konfiguracja

Zarówno MSAL.js 1. x, jak i 2. x są przeznaczone do reprezentowania pojedynczego wystąpienia i konfiguracji `UserAgentApplication` lub `PublicClientApplication` , aby reprezentować pojedynczy kontekst uwierzytelniania.

Wiele wystąpień `UserAgentApplication` lub `PublicClientApplication` nie jest zalecane, ponieważ powodują konfliktowe wpisy w pamięci podręcznej i zachowanie w przeglądarce.

## <a name="next-steps"></a>Następne kroki

Ten MSAL.js przykładowy kod 2. x w witrynie GitHub ilustruje tworzenie wystąpień [PublicClientApplication][msal-js-publicclientapplication] z obiektem [konfiguracji][msal-js-configuration] :

[Azure-Samples/MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_configuration_.html
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
