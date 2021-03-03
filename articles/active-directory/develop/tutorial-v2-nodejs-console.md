---
title: 'Samouczek: wywoływanie Microsoft Graph w aplikacji konsolowej Node.js | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację konsolową do wywoływania Microsoft Graph do aplikacji konsolowej Node.js.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645793"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Samouczek: wywoływanie interfejsu API Microsoft Graph w aplikacji konsolowej Node.js

W tym samouczku utworzysz aplikację konsolową, która wywołuje Microsoft Graph API przy użyciu własnej tożsamości. Aplikacja konsolowa, którą tworzysz, korzysta z [biblioteki Microsoft Authentication Library (MSAL) dla Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Wykonaj kroki opisane w tym samouczku, aby:

> [!div class="checklist"]
> - Zarejestruj aplikację w Azure Portal
> - Tworzenie projektu aplikacji konsolowej Node.js
> - Dodawanie logiki uwierzytelniania do aplikacji
> - Dodaj szczegóły rejestracji aplikacji
> - Dodawanie metody do wywoływania interfejsu API sieci Web
> - Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

## <a name="register-the-application"></a>Rejestrowanie aplikacji

Najpierw wykonaj kroki opisane w temacie [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md) w celu zarejestrowania aplikacji.

Użyj następujących ustawień rejestracji aplikacji:

- Nazwa: `NodeConsoleApp` (zalecane)
- Obsługiwane typy kont: **konta tylko w tym katalogu organizacji**
- Uprawnienia interfejsu API: **Microsoft API**  >  **Microsoft Graph**  >  **uprawnienia aplikacji** > `User.Read.All`
- Klucz tajny klienta: `*********` (Zapisz tę wartość do użycia w późniejszym kroku, która jest wyświetlana tylko raz)

## <a name="create-the-project"></a>Tworzenie projektu

Utwórz folder do hostowania aplikacji, na przykład *NodeConsoleApp*.

1. Najpierw przejdź do katalogu projektu w terminalu, a następnie uruchom następujące polecenia NPM:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Następnie utwórz folder o nazwie *bin*. Następnie w tym folderze Utwórz plik o nazwie *index.js* i Dodaj następujący kod:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Ten plik odwołuje się do dwóch innych modułów węzła: *auth.js* zawierający implementację węzła MSAL na potrzeby uzyskiwania tokenów dostępu, a *fetch.js* , który zawiera metodę wykonywania żądania HTTP w celu Microsoft Graph interfejsu API z tokenem dostępu. Po zakończeniu pozostałej części tego samouczka struktura plików i folderów projektu powinna wyglądać podobnie do następującej:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Dodaj logikę uwierzytelniania

W folderze *bin* Utwórz inny plik o nazwie *auth.js* i Dodaj następujący kod w celu uzyskania tokenu dostępu, który ma być obecny podczas wywoływania interfejsu API Microsoft Graph.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

W powyższym fragmencie kodu najpierw tworzymy obiekt konfiguracji (*msalConfig*) i przekazujemy go, aby zainicjować MSAL [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md). Następnie tworzymy metodę uzyskiwania tokenów za pośrednictwem **poświadczeń klienta** i udostępnienia tego modułu dostępnym przez *main.js*. Parametry konfiguracji w tym module są rysowane z pliku środowiska, który zostanie utworzony w następnym kroku.

## <a name="add-app-registration-details"></a>Dodaj szczegóły rejestracji aplikacji

Utwórz plik środowiska do przechowywania szczegółów rejestracji aplikacji, które będą używane podczas uzyskiwania tokenów. W tym celu Utwórz plik o nazwie *. env* w folderze głównym przykładu (*NodeConsoleApp*), a następnie Dodaj następujący kod:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
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
- `Enter_the_Graph_Endpoint_Here` jest wystąpieniem interfejsu API Microsoft Graph, z którym aplikacja ma się komunikować.
  - W przypadku **globalnego** punktu końcowego interfejsu API Microsoft Graph Zastąp oba wystąpienia tego ciągu ciągiem `https://graph.microsoft.com` .
  - W przypadku punktów końcowych w ramach wdrożeń w chmurze **krajowej** zapoznaj się z dokumentacją dotyczącą [wdrożeń w chmurze krajowej](/graph/deployments) w dokumentacji Microsoft Graph.

## <a name="add-a-method-to-call-a-web-api"></a>Dodawanie metody do wywoływania interfejsu API sieci Web

W folderze *bin* Utwórz inny plik o nazwie *fetch.js* i Dodaj następujący kod służący do tworzenia wywołań REST do interfejsu API Microsoft Graph:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

W tym miejscu `callApi` Metoda jest używana do wysyłania żądania HTTP `GET` względem chronionego zasobu, który wymaga tokenu dostępu. Żądanie zwraca zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w *nagłówku autoryzacji http*. Chroniony zasób to [punkt końcowy użytkowników](/graph/api/user-list) interfejsu API Microsoft Graph, który wyświetla użytkowników w dzierżawie, w której ta aplikacja jest zarejestrowana.

## <a name="test-the-app"></a>Testowanie aplikacji

Ukończono tworzenie aplikacji i teraz wszystko jest gotowe do przetestowania funkcjonalności aplikacji.

Uruchom aplikację konsolową Node.js, uruchamiając następujące polecenie z poziomu katalogu głównego folderu projektu:

```console
node . --op getUsers
```

Powinno to skutkować niektórymi odpowiedziami JSON z Microsoft Graph interfejsu API, a w konsoli powinna zostać wyświetlona tablica obiektów użytkownika:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Interfejs wiersza polecenia wyświetlający odpowiedź wykresu":::

## <a name="how-the-application-works"></a>Jak działa aplikacja

Ta aplikacja używa [przyznania poświadczeń klienta OAuth 2,0](./v2-oauth2-client-creds-grant-flow.md). Ten typ uprawnień jest często używany do interakcji między serwerami, które muszą działać w tle bez natychmiastowej interakcji z użytkownikiem. Przepływ przyznania poświadczeń zezwala usłudze sieci Web (poufnemu klienta) na używanie własnych poświadczeń, a nie personifikowanie użytkownika w celu uwierzytelniania podczas wywoływania innej usługi sieci Web. Typ aplikacji obsługiwanych przez ten model uwierzytelniania jest zwykle **demonami** lub **kontami usług**.

Zakresem żądania dla przepływu poświadczeń klienta jest nazwa zasobu, po którym następuje `/.default` . Ta notacja informuje Azure Active Directory (Azure AD) o korzystaniu z uprawnień na poziomie aplikacji, które są zadeklarowane statycznie podczas rejestrowania aplikacji. Ponadto te uprawnienia interfejsu API muszą zostać przyznane przez **administratora dzierżawy**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia aplikacji konsolowych Node.js na platformie tożsamości firmy Microsoft, zapoznaj się z naszą wieloczęściową serią scenariuszy:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja demona](scenario-daemon-overview.md)