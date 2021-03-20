---
title: 'Samouczek: Logowanie użytkowników w aplikacji sieci Web Node.js & Express | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku dowiesz się, jak dodać obsługę użytkowników logujących się w aplikacji sieci Web.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648994"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Samouczek: Logowanie użytkowników w aplikacji sieci Web Node.js & Express

W tym samouczku utworzysz aplikację sieci Web, która loguje się do użytkowników. Utworzona aplikacja sieci Web używa [biblioteki uwierzytelniania firmy Microsoft (MSAL) dla węzła](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Wykonaj kroki opisane w tym samouczku, aby:

> [!div class="checklist"]
> - Zarejestruj aplikację w Azure Portal
> - Tworzenie projektu aplikacji sieci Web Express
> - Instalowanie pakietów biblioteki uwierzytelniania
> - Dodaj szczegóły rejestracji aplikacji
> - Dodawanie kodu dla logowania użytkownika
> - Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

## <a name="register-the-application"></a>Rejestrowanie aplikacji

Najpierw wykonaj kroki opisane w temacie [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md) w celu zarejestrowania aplikacji.

Użyj następujących ustawień rejestracji aplikacji:

- Nazwa: `ExpressWebApp` (zalecane)
- Obsługiwane typy kont: **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**
- Typ platformy: **Sieć Web**
- Identyfikator URI przekierowania: `http://localhost:3000/redirect`
- Klucz tajny klienta: `*********` (Zapisz tę wartość do użycia w późniejszym kroku, która jest wyświetlana tylko raz)

## <a name="create-the-project"></a>Tworzenie projektu

Utwórz folder do hostowania aplikacji, na przykład *ExpressWebApp*.

1. Najpierw przejdź do katalogu projektu w terminalu, a następnie uruchom następujące `npm` polecenia:

```console
    npm init -y
    npm install --save express
```

2. Następnie utwórz plik o nazwie *index.js* i Dodaj następujący kod:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Masz teraz prosty serwer sieci Web uruchomiony na porcie 3000. Struktura plików i folderów projektu powinna wyglądać podobnie do następującej:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Instalowanie biblioteki uwierzytelniania

Znajdź katalog główny katalogu projektu w terminalu i zainstaluj pakiet węzła MSAL za pośrednictwem NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Dodaj szczegóły rejestracji aplikacji

W utworzonym wcześniej pliku *index.js* Dodaj następujący kod:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
```

Podaj te szczegóły przy użyciu wartości uzyskanych z portalu rejestracji aplikacji platformy Azure:

- `Enter_the_Tenant_Id_here` powinien być jednym z następujących:
  - Jeśli aplikacja obsługuje *konta w tym katalogu organizacji*, Zamień tę wartość na **Identyfikator dzierżawy** lub **nazwę dzierżawy**. Na przykład `contoso.microsoft.com`.
  - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość wartością `organizations` .
  - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość wartością `common` .
  - Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość wartością `consumers` .
- `Enter_the_Application_Id_Here`: **Identyfikator aplikacji (klienta)** zarejestrowanej aplikacji.
- `Enter_the_Cloud_Instance_Id_Here`: Wystąpienie w chmurze platformy Azure, w którym zarejestrowano aplikację.
  - W przypadku głównej (lub *globalnej*) chmury platformy Azure wprowadź wartość `https://login.microsoftonline.com` .
  - W przypadku chmur **narodowych** (na przykład Chin) można znaleźć odpowiednie wartości w [chmurach krajowych](authentication-national-cloud.md).
- `Enter_the_Client_secret`: Zastąp tę wartość wpisem tajnym klienta utworzonym wcześniej. Aby wygenerować nowy klucz, użyj **certyfikatów & wpisów tajnych** w ustawieniach rejestracji aplikacji w Azure Portal.

> [!WARNING]
> Każdy tajny tekst w kodzie źródłowym stanowi zwiększone ryzyko dla bezpieczeństwa. W tym artykule użyto tylko elementu tajnego w postaci zwykłego tekstu dla uproszczenia. Użyj [poświadczeń certyfikatu](active-directory-certificate-credentials.md) zamiast wpisów tajnych klienta w poufnych aplikacjach klienckich, zwłaszcza tych, które zamierzasz wdrożyć w środowisku produkcyjnym.

## <a name="add-code-for-user-login"></a>Dodawanie kodu dla logowania użytkownika

W utworzonym wcześniej pliku *index.js* Dodaj następujący kod:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Testuj logowanie

Ukończono tworzenie aplikacji i teraz wszystko jest gotowe do przetestowania funkcjonalności aplikacji.

1. Uruchom aplikację konsolową Node.js, uruchamiając następujące polecenie z poziomu katalogu głównego folderu projektu:

```console
   node index.js
```

2. Otwórz okno przeglądarki i przejdź pod adres `http://localhost:3000`. Powinien zostać wyświetlony ekran logowania:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Wyświetlanie ekranu logowania usługi Azure AD":::

3. Po wprowadzeniu poświadczeń powinien zostać wyświetlony ekran zgody z prośbą o zatwierdzenie uprawnień aplikacji.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Wyświetlanie ekranu zgody usługi Azure AD":::

## <a name="how-the-application-works"></a>Jak działa aplikacja

W tym samouczku zainicjowano obiekt MSAL węzła [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) przez przekazanie go do obiektu konfiguracji (*msalConfig*) zawierającego parametry uzyskane z rejestracji aplikacji usługi Azure AD na Azure Portal. Utworzona aplikacja sieci Web używa [przepływu przydzielania kodu autoryzacji OAuth 2,0](./v2-oauth2-auth-code-flow.md) do logowania użytkowników i uzyskiwania identyfikatorów i tokenów dostępu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia aplikacji sieci Web w Node.js & Express na platformie tożsamości firmy Microsoft, zobacz nasze wieloczęściowe serie scenariuszy:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja sieci Web, która loguje użytkowników](scenario-web-app-sign-user-overview.md)