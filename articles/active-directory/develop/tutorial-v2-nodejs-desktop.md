---
title: 'Samouczek: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph w aplikacji dla komputerów stacjonarnych elektronów | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację stacjonarną elektronów, która może logować użytkowników i używać przepływu kodu uwierzytelniania w celu uzyskania tokenu dostępu z platformy tożsamości firmy Microsoft i wywołania interfejsu API Microsoft Graph.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: v-doeris
ms.openlocfilehash: 4b07ef14b87d87aa78ad2174b0f4dc176b8a63dc
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562133"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Samouczek: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph w aplikacji klasycznej

W tym samouczku utworzysz aplikację stacjonarną elektronów, która loguje się do użytkowników i wywołuje Microsoft Graph przy użyciu przepływu kodu autoryzacji z PKCE. Aplikacja klasyczna, którą tworzysz, używa [biblioteki uwierzytelniania firmy Microsoft (MSAL) dla Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Wykonaj kroki opisane w tym samouczku, aby:

> [!div class="checklist"]
> - Zarejestruj aplikację w Azure Portal
> - Tworzenie projektu aplikacji klasycznej
> - Dodawanie logiki uwierzytelniania do aplikacji
> - Dodawanie metody do wywoływania interfejsu API sieci Web
> - Dodaj szczegóły rejestracji aplikacji
> - Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- [Node.js](https://nodejs.org/en/download/)
- [Elektron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

## <a name="register-the-application"></a>Rejestrowanie aplikacji

Najpierw wykonaj kroki opisane w temacie [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md) w celu zarejestrowania aplikacji.

Użyj następujących ustawień rejestracji aplikacji:

- Nazwa: `ElectronDesktopApp` (zalecane)
- Obsługiwane typy kont: **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**
- Typ platformy: **aplikacje mobilne i klasyczne**
- Identyfikator URI przekierowania: `msal://redirect`

## <a name="create-the-project"></a>Tworzenie projektu

Utwórz folder do hostowania aplikacji, na przykład *ElectronDesktopApp*.

1. Najpierw przejdź do katalogu projektu w terminalu, a następnie uruchom następujące `npm` polecenia:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Następnie utwórz folder o nazwie *App*. W tym folderze Utwórz plik o nazwie *index.html* , który będzie używany jako interfejs użytkownika. Dodaj następujący kod:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Następnie utwórz plik o nazwie *main.js* i Dodaj następujący kod:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

W powyższym fragmencie kodu inicjujemy obiekt głównego okna wyelektronu i tworzymy niektóre programy obsługi zdarzeń w celu interakcji z oknem elektronów. Importowane są również parametry konfiguracji, tworzenie wystąpienia klasy *authProvider* w celu obsługi logowania, wylogowywania i pozyskiwania tokenów oraz wywoływania interfejsu API Microsoft Graph.

4. W tym samym folderze (*aplikacji*) Utwórz inny plik o nazwie *renderer.js* i Dodaj następujący kod:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Na koniec Utwórz plik o nazwie *constants.js* , w którym będą przechowywane stałe ciągów opisujące **zdarzenia** aplikacji:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Masz teraz prosty graficzny interfejs użytkownika i interakcje dla aplikacji elektronowej. Po zakończeniu pozostałej części tego samouczka struktura plików i folderów projektu powinna wyglądać podobnie do następującej:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Dodawanie logiki uwierzytelniania do aplikacji

W folderze *aplikacji* Utwórz plik o nazwie *AuthProvider.js*. Będzie ona zawierać klasę dostawcy uwierzytelniania, która będzie obsługiwać logowanie, wylogowywanie, pozyskiwanie tokenów, Wybieranie konta i powiązane zadania uwierzytelniania przy użyciu węzła MSAL. Dodaj następujący kod:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of 
 * a custom file protocol instead of a regular web (https://) redirect URI in order to 
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation. 
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md 
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md 
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;
        
        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);
        
        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         * 
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         * 
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         * 
         * For details on PKCE code generation logic, consult the 
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = { 
            ...this.authCodeUrlParams, 
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method 
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);
        
        const authResponse = await this.clientApplication.acquireTokenByCode({ 
            ...this.authCodeRequest, 
            scopes: tokenRequest.scopes, 
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier 
        });
        
        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {
        
        authWindow.loadURL(navigateUrl);

        return new Promise((resolve, reject) => {
            authWindow.webContents.on('will-redirect', (event, responseUrl) => {
                try {
                    const parsedUrl = new URL(responseUrl);
                    const authCode = parsedUrl.searchParams.get('code');
                    resolve(authCode);
                } catch (err) {
                    reject(err);
                }
            });
        });
    }

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response 
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        // need to call getAccount here?
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

W powyższym fragmencie kodu najpierw zainicjował węzeł MSAL `PublicClientApplication` przez przekazanie obiektu konfiguracji ( `msalConfig` ). Następnie `login` zostanie uwidoczniony `logout` i `getToken` metody do wywołania przez moduł główny (*main.js*). W systemie `login` i `getToken` uzyskujemy odpowiednio identyfikatory i tokeny dostępu, najpierw żądając kodu autoryzacji, a następnie wymianę tego elementu z tokenem przy użyciu `acquireTokenByCode` publicznego interfejsu API węzła MSAL.

## <a name="add-a-method-to-call-a-web-api"></a>Dodawanie metody do wywoływania interfejsu API sieci Web

Utwórz inny plik o nazwie *fetch.js*. Ten plik będzie zawierać Axios klienta HTTP do tworzenia wywołań REST do interfejsu API Microsoft Graph.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint 
 * @param accessToken 
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Dodaj szczegóły rejestracji aplikacji

Na koniec Utwórz plik środowiska do przechowywania szczegółów rejestracji aplikacji, które będą używane podczas uzyskiwania tokenów. W tym celu Utwórz plik o nazwie *. env* w folderze głównym przykładu (*ElectronDesktopApp*), a następnie Dodaj następujący kod:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Podaj te szczegóły przy użyciu wartości uzyskanych z portalu rejestracji aplikacji platformy Azure:

- `Enter_the_Tenant_Id_here` powinien być jednym z następujących:
  - Jeśli aplikacja obsługuje *konta w tym katalogu organizacji*, Zamień tę wartość na **Identyfikator dzierżawy** lub **nazwę dzierżawy**. Na przykład `contoso.microsoft.com`.
  - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość wartością `organizations` .
  - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość wartością `common` .
  - Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość wartością `consumers` .
- `Enter_the_Application_Id_Here`: **Identyfikator aplikacji (klienta)** zarejestrowanej aplikacji.
- `Enter_the_Cloud_Instance_Id_Here`: Wystąpienie w chmurze platformy Azure, w którym zarejestrowano aplikację.
  - W przypadku głównej (lub *globalnej*) chmury platformy Azure wprowadź wartość `https://login.microsoftonline.com/` .
  - W przypadku chmur **narodowych** (na przykład Chin) można znaleźć odpowiednie wartości w [chmurach krajowych](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` jest wystąpieniem interfejsu API Microsoft Graph, z którym aplikacja ma się komunikować.
  - W przypadku **globalnego** punktu końcowego interfejsu API Microsoft Graph Zastąp oba wystąpienia tego ciągu ciągiem `https://graph.microsoft.com/` .
  - W przypadku punktów końcowych w ramach wdrożeń w chmurze **krajowej** zapoznaj się z dokumentacją dotyczącą [wdrożeń w chmurze krajowej](/graph/deployments) w dokumentacji Microsoft Graph.

## <a name="test-the-app"></a>Testowanie aplikacji

Ukończono tworzenie aplikacji i teraz można ją uruchomić w celu uruchomienia aplikacji klasycznej dla komputerów stacjonarnych i przetestowania jej funkcjonalności.

1. Uruchom aplikację, uruchamiając następujące polecenie z poziomu katalogu głównego folderu projektu:

```console
electron App/main.js
```

2. W oknie głównym aplikacji powinna zostać wyświetlona zawartość pliku *index.html* i przycisku **Zaloguj** .

## <a name="test-sign-in-and-sign-out"></a>Testowanie logowania i wylogowywanie

Po załadowaniu pliku *index.html* wybierz pozycję **Zaloguj**. Zostanie wyświetlony monit o zalogowanie się za pomocą platformy tożsamości firmy Microsoft:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="monit logowania":::

Jeśli użytkownik wyraża zgodę na żądane uprawnienia, w aplikacjach sieci Web zostanie wyświetlona nazwa użytkownika, co oznacza pomyślne zalogowanie:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="Pomyślne logowanie":::

## <a name="test-web-api-call"></a>Testowanie wywołania interfejsu API sieci Web

Po zalogowaniu wybierz pozycję **Zobacz Profil** , aby wyświetlić informacje o profilu użytkownika zwrócone w odpowiedzi z wywołania do interfejsu API Microsoft Graph:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="Informacje o profilu z Microsoft Graph":::

Wybierz pozycję **Odczytaj wiadomości e-mail** , aby wyświetlić komunikaty na koncie użytkownika. Zostanie wyświetlony ekran zgody:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="ekran wyrażania zgody dla uprawnienia do odczytu. mail":::

Po udzieleniu zgody zobaczysz komunikaty zwrócone w odpowiedzi z wywołania do interfejsu API Microsoft Graph:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="Informacje o wiadomościach od Microsoft Graph":::

## <a name="how-the-application-works"></a>Jak działa aplikacja

Gdy użytkownik wybierze przycisk **Zaloguj** po raz pierwszy, metoda get klasy `getTokenInteractive` *AuthProvider.js* jest wywoływana. Ta metoda przekierowuje użytkownika do logowania za pomocą *punktu końcowego platformy tożsamości firmy Microsoft* i sprawdza poprawność poświadczeń użytkownika, a następnie uzyskuje **kod autoryzacji**. Ten kod jest następnie wymieniany dla tokenu dostępu przy użyciu `acquireTokenByCode` publicznego interfejsu API węzła MSAL.

W tym momencie kod autoryzacji chroniony przez PKCE jest wysyłany do punktu końcowego tokenu chronionego przez funkcję CORS i jest wymieniany na potrzeby tokenów. Token identyfikatora, token dostępu i token odświeżania są odbierane przez aplikację i przetwarzane przez węzeł MSAL, a informacje zawarte w tokenach są buforowane.

Token identyfikatora zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana. Token dostępu ma ograniczony okres istnienia i wygasa po 24 godzinach. Jeśli planujesz używać tych tokenów do uzyskiwania dostępu do chronionego zasobu, serwer zaplecza *musi* sprawdzić jego poprawność, aby zagwarantować, że token został wystawiony dla prawidłowego użytkownika aplikacji.

Aplikacja klasyczna utworzona w tym samouczku tworzy wywołanie REST do interfejsu API Microsoft Graph przy użyciu tokenu dostępu jako tokenu okaziciela w nagłówku żądania ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Domyślnie ten zakres jest automatycznie dodawany w każdej aplikacji, która jest zarejestrowana w Azure Portal. Inne interfejsy API dla Microsoft Graph, a także niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API Microsoft Graph wymaga, aby w celu wyświetlenia listy wiadomości e-mail użytkownika był wymagany zakres *poczty. Read.*

Podczas dodawania zakresów użytkownicy mogą zostać poproszeni o podanie dodatkowej zgody na dodane zakresy.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia aplikacji klasycznych Node.js i elektronów na platformie tożsamości firmy Microsoft, zobacz nasze wieloczęściowe serie scenariuszy:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja klasyczna, która wywołuje interfejsy API sieci Web](scenario-desktop-overview.md)
