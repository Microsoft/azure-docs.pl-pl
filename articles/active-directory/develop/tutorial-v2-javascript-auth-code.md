---
title: 'Samouczek: Tworzenie jednostronicowej aplikacji JavaScript używającej przepływu kodu uwierzytelniania | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz SPA JavaScript, który może zalogować użytkowników i użyć przepływu kodu uwierzytelniania do uzyskania tokenu dostępu z platformy tożsamości firmy Microsoft i wywoływać interfejs API Microsoft Graph.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 89bc974e4d95da183f23ef6643a03b3f20cfa6fa
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611167"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Samouczek: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji jednostronicowej JavaScript (SPA) przy użyciu przepływu kodu uwierzytelniania

W tym samouczku pokazano, jak utworzyć aplikację jednostronicową języka JavaScript, która używa biblioteki Microsoft Authentication Library (MSAL) dla języka JavaScript 2.0 w celu:

> [!div class="checklist"]
> * Wykonywanie przepływu kodu autoryzacji OAuth 2,0 przy użyciu PKCE
> * Zaloguj się do osobistych kont Microsoft, a także kont służbowych
> * Uzyskiwanie tokenu dostępu
> * Wywołaj Microsoft Graph lub własny interfejs API wymagający tokenów dostępu uzyskanych z punktu końcowego platformy tożsamości firmy Microsoft

MSAL.js 2,0 usprawnia MSAL.js 1,0 przez obsługę przepływu kodu autoryzacji w przeglądarce zamiast niejawnego przepływu dotacji. MSAL.js 2,0 nie **obsługuje przepływu** niejawnego.

[!INCLUDE [MSAL.js 2.0 and Azure AD B2C temporary incompatibility notice](../../../includes/msal-b2c-cors-compatibility-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/) uruchamiania lokalnego serwera WebServer
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor kodu

## <a name="how-the-tutorial-app-works"></a>Jak działa aplikacja samouczka

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagram przedstawiający przepływ kodu autoryzacji w aplikacji jednostronicowej":::

Aplikacja, którą tworzysz w tym samouczku, umożliwia skryptom w języku JavaScript Wysyłanie zapytań do interfejsu API Microsoft Graph przez uzyskanie tokenów zabezpieczających z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu po zalogowaniu się użytkownika token dostępu jest wymagany i dodawany do żądań HTTP w nagłówku autoryzacji. Pozyskiwanie i odnawianie tokenów jest obsługiwane przez bibliotekę uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).

W tym samouczku jest stosowana następująca Biblioteka:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) Biblioteka Microsoft Authentication Library for JavaScript v 2.0 — pakiet przeglądarki

## <a name="get-the-completed-code-sample"></a>Pobierz ukończony przykład kodu

Wolisz pobrać zakończono przykładowego projektu w tym samouczku? Aby uruchomić projekt przy użyciu lokalnego serwera sieci Web, takiego jak Node.js, Sklonuj repozytorium [MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) :

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Następnie aby skonfigurować przykładowy kod przed jego wykonaniem, przejdź do [kroku konfiguracji](#register-your-application).

Aby kontynuować pracę z samouczkiem i samodzielnie skompilować aplikację, przejdź do następnej sekcji, [wymagania wstępne](#prerequisites).

## <a name="create-your-project"></a>Tworzenie projektu

Po zainstalowaniu [Node.js](https://nodejs.org/en/download/) Utwórz folder do hostowania aplikacji, na przykład *msal-Spa-samouczek*.

Następnie Zaimplementuj [mały serwer](https://expressjs.com/) sieci Web, który będzie obsługiwał plik *index.html* .

1. Najpierw przejdź do katalogu projektu w terminalu, a następnie uruchom następujące `npm` polecenia:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Następnie utwórz plik o nazwie *server.js* i Dodaj następujący kod:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Masz teraz niewielki serwer sieci Web, który będzie obsługiwał Twoje SPA. Po zakończeniu pozostałej części tego samouczka struktura plików i folderów projektu powinna wyglądać podobnie do następującej:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Tworzenie interfejsu użytkownika SPA

1. Utwórz folder *aplikacji* w katalogu projektu i utwórz plik *index.html* dla Spa JavaScript. Ten plik implementuje interfejs użytkownika utworzony przy użyciu **środowiska Bootstrap 4** i importuje pliki skryptów do konfiguracji, uwierzytelniania i wywołań interfejsu API.

    W pliku *index.html* Dodaj następujący kod:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Następnie w folderze *aplikacja* Utwórz plik o nazwie *ui.js* i Dodaj następujący kod. Ten plik będzie miał dostęp do elementów DOM i je zaktualizować.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Wykonaj kroki w [aplikacji jednostronicowej: Rejestracja aplikacji](scenario-spa-app-registration.md) , aby utworzyć rejestrację aplikacji dla spa.

W obszarze [URI przekierowania: MSAL.js 2,0 z etapem przepływu kodu uwierzytelniania](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) wprowadź `http://localhost:3000` lokalizację domyślną, w której jest uruchamiana aplikacja tego samouczka.

Jeśli chcesz użyć innego portu, wprowadź `http://localhost:<port>` , gdzie `<port>` jest preferowanym numerem portu TCP. Jeśli określisz numer portu inny niż `3000` , zaktualizuj również *server.js* przy użyciu preferowanego numeru portu.

### <a name="configure-your-javascript-spa"></a>Konfigurowanie protokołu JavaScript SPA

Utwórz plik o nazwie *authConfig.js* w folderze *App* , aby zawierał parametry konfiguracji na potrzeby uwierzytelniania, a następnie Dodaj następujący kod:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Zmodyfikuj wartości w `msalConfig` sekcji zgodnie z opisem w tym miejscu:

- `Enter_the_Application_Id_Here`: **Identyfikator aplikacji (klienta)** zarejestrowanej aplikacji.
- `Enter_the_Cloud_Instance_Id_Here`: Wystąpienie w chmurze platformy Azure, w którym zarejestrowano aplikację.
  - W przypadku głównej (lub *globalnej*) chmury platformy Azure wprowadź wartość `https://login.microsoftonline.com` .
  - W przypadku chmur **narodowych** (na przykład Chin) można znaleźć odpowiednie wartości w [chmurach krajowych](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` powinien być jednym z następujących:
  - Jeśli aplikacja obsługuje *konta w tym katalogu organizacji*, Zamień tę wartość na **Identyfikator dzierżawy** lub **nazwę dzierżawy**. Na przykład `contoso.microsoft.com`.
  - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość wartością `organizations` .
  - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość wartością `common` .
  - Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość wartością `consumers` .
- `Enter_the_Redirect_Uri_Here` to `http://localhost:3000`.

`authority`Wartość w *authConfig.js* powinna wyglądać podobnie do poniższego, jeśli używana jest globalna Chmura platformy Azure:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Nadal w folderze *aplikacji* Utwórz plik o nazwie *graphConfig.js*. Dodaj następujący kod w celu udostępnienia aplikacji parametrów konfiguracji służących do wywoływania interfejsu API Microsoft Graph:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Zmodyfikuj wartości w `graphConfig` sekcji zgodnie z opisem w tym miejscu:

- `Enter_the_Graph_Endpoint_Here` jest wystąpieniem interfejsu API Microsoft Graph, z którym aplikacja ma się komunikować.
  - W przypadku **globalnego** punktu końcowego interfejsu API Microsoft Graph Zastąp oba wystąpienia tego ciągu ciągiem `https://graph.microsoft.com` .
  - W przypadku punktów końcowych w ramach wdrożeń w chmurze **krajowej** zapoznaj się z dokumentacją dotyczącą [wdrożeń w chmurze krajowej](https://docs.microsoft.com/graph/deployments) w dokumentacji Microsoft Graph.

`graphMeEndpoint`Wartości i `graphMailEndpoint` w *graphConfig.js* powinny wyglądać podobnie do następujących, jeśli używasz globalnego punktu końcowego:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>Użyj biblioteki Microsoft Authentication Library (MSAL) do logowania użytkownika

### <a name="pop-up"></a>Wyskakujące okienko

W folderze *aplikacja* Utwórz plik o nazwie *authPopup.js* i Dodaj następujący kod uwierzytelniania i token pozyskiwania dla logowania w wyskakującym logowaniu:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Przekierowanie

Utwórz plik o nazwie *authRedirect.js* w folderze *App* i Dodaj następujący kod uwierzytelniania i token pozyskiwania dla przekierowania logowania:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Działanie kodu

Gdy użytkownik wybierze przycisk **Zaloguj** po raz pierwszy, `signIn` wywołuje metodę `loginPopup` logowania użytkownika. `loginPopup`Metoda powoduje otwarcie okna podręcznego z *punktem końcowym platformy tożsamości firmy Microsoft* w celu wyświetlenia monitu i zweryfikowania poświadczeń użytkownika. Po pomyślnym zalogowaniu *msal.js* inicjuje [przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md).

W tym momencie kod autoryzacji chroniony przez PKCE jest wysyłany do punktu końcowego tokenu chronionego przez funkcję CORS i jest wymieniany na potrzeby tokenów. Token identyfikatora, token dostępu i token odświeżania są odbierane przez aplikację i przetwarzane przez *msal.js*, a informacje zawarte w tokenach są buforowane.

Token identyfikatora zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana. Jeśli planujesz używać dowolnych danych dostarczonych przez token identyfikatora, serwer zaplecza *musi* sprawdzić jego poprawność, aby zagwarantować, że token został wystawiony dla prawidłowego użytkownika aplikacji. Token odświeżania ma ograniczony okres istnienia i wygasa po 24 godzinach. Token odświeżania może służyć do dyskretnego pozyskiwania nowych tokenów dostępu.

SPA, który został utworzony w tym samouczku, wywołuje `acquireTokenSilent` i/lub `acquireTokenPopup` uzyskuje *token dostępu* używany do wykonywania zapytań dotyczących interfejsu API Microsoft Graph informacji o profilu użytkownika. Jeśli potrzebujesz przykładu, który sprawdza poprawność tokenu identyfikatora, zobacz przykładową aplikację [Active-Directory-JavaScript-singlepageapp-dotnet-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) w witrynie GitHub. Przykład używa interfejsu API sieci Web ASP.NET do sprawdzania poprawności tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Po wstępnym logowaniu aplikacja nie powinna prosić użytkowników o ponowne uwierzytelnienie za każdym razem, gdy potrzebują dostępu do chronionego zasobu (czyli do żądania tokenu). Aby uniemożliwić takie żądania ponownego uwierzytelniania, wywołaj polecenie `acquireTokenSilent` . Istnieją jednak sytuacje, w których konieczne może być wymuszenie współpracy użytkowników z punktem końcowym platformy tożsamości firmy Microsoft. Na przykład:

- Użytkownicy muszą ponownie wprowadzić swoje poświadczenia, ponieważ hasło wygasło.
- Aplikacja żąda dostępu do zasobu i potrzebujesz zgody użytkownika.
- Wymagane jest uwierzytelnianie dwuskładnikowe.

Wywołanie `acquireTokenPopup` powoduje otwarcie okna podręcznego (lub `acquireTokenRedirect` przekieruje użytkowników do punktu końcowego platformy tożsamości firmy Microsoft). W tym oknie Użytkownicy muszą mieć możliwość działania przez potwierdzenie poświadczeń, udzielenie zgody na wymagane zasoby lub zakończenie uwierzytelniania dwuskładnikowego.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`acquireTokenSilent`Metoda obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Gdy `loginPopup` (lub `loginRedirect` ) jest wykonywane po raz pierwszy, `acquireTokenSilent` jest to metoda często używana do uzyskiwania tokenów używanych w celu uzyskiwania dostępu do chronionych zasobów dla kolejnych wywołań. (Wywołania żądania lub odnawiania tokenów są wykonywane w trybie dyskretnym). `acquireTokenSilent` może zakończyć się niepowodzeniem w niektórych przypadkach. Na przykład hasło użytkownika mogło wygasnąć. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1. Utwórz wywołanie natychmiast, `acquireTokenPopup` Aby wyzwolić monit logowania użytkownika. Ten wzorzec jest często używany w aplikacjach online, w których aplikacja nie ma dostępnej nieuwierzytelnionej zawartości. Przykład generowany przez tę konfigurację z przewodnikiem używa tego wzorca.
1. Wizualnie wskazuje użytkownikowi, że logowanie interaktywne jest wymagane, aby użytkownik mógł wybrać odpowiedni czas na zalogowanie się, a aplikacja może ponowić próbę `acquireTokenSilent` w późniejszym czasie. Ta technika jest często używana, gdy użytkownik może korzystać z innych funkcji aplikacji bez zakłócania pracy. Na przykład w aplikacji może być dostępna nieuwierzytelniona zawartość. W takiej sytuacji użytkownik może zdecydować się na zalogowanie się w celu uzyskania dostępu do chronionego zasobu lub odświeżenie nieaktualnych informacji.

> [!NOTE]
> W tym samouczku `loginPopup` Domyślnie są stosowane `acquireTokenPopup` metody i. Jeśli używasz programu Internet Explorer, zalecamy użycie `loginRedirect` `acquireTokenRedirect` metod i ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) z programem Internet Explorer i wyskakującymi oknami. Przykład osiągnięcia tego samego wyniku przy użyciu metod przekierowania można znaleźć w temacie [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) w witrynie GitHub.

## <a name="call-the-microsoft-graph-api"></a>Wywoływanie interfejsu API Microsoft Graph

Utwórz plik o nazwie *graph.js* w folderze *aplikacji* i Dodaj następujący kod w celu utworzenia wywołań REST do interfejsu API Microsoft Graph:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

W przykładowej aplikacji utworzonej w tym samouczku `callMSGraph()` Metoda służy do tworzenia `GET` żądania HTTP względem chronionego zasobu, który wymaga tokenu. Żądanie zwraca zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w *nagłówku autoryzacji http*. W przykładowej aplikacji utworzonej w tym samouczku chroniony zasób jest punktem końcowym Microsoft Graph API *Me* , który wyświetla informacje o profilu zalogowanego użytkownika.

## <a name="test-your-application"></a>Testowanie aplikacji

Ukończono tworzenie aplikacji i teraz można jej użyć do uruchomienia serwera sieci Web Node.js i przetestowania funkcji aplikacji.

1. Uruchom serwer sieci Web Node.js, uruchamiając następujące polecenie z poziomu katalogu głównego folderu projektu:

   ```console
   npm start
   ```
1. W przeglądarce przejdź do `http://localhost:3000` `http://localhost:<port>` lokalizacji lub, gdzie `<port>` jest port, na którym nasłuchuje serwer sieci Web. Powinna zostać wyświetlona zawartość pliku *index.html* i przycisku **Zaloguj** .

### <a name="sign-in-to-the-application"></a>Logowanie się do aplikacji

Po załadowaniu przez przeglądarkę pliku *index.html* wybierz pozycję **Zaloguj**. Zostanie wyświetlony monit o zalogowanie się za pomocą punktu końcowego platformy tożsamości firmy Microsoft:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Diagram przedstawiający przepływ kodu autoryzacji w aplikacji jednostronicowej":::

### <a name="provide-consent-for-application-access"></a>Wyrażanie zgody na dostęp do aplikacji

Po pierwszym zalogowaniu się do aplikacji zostanie wyświetlony monit o udzielenie dostępu do Twojego profilu i zalogowanie się w:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Diagram przedstawiający przepływ kodu autoryzacji w aplikacji jednostronicowej":::

Jeśli użytkownik wyraża zgodę na żądane uprawnienia, w aplikacjach sieci Web zostanie wyświetlona nazwa użytkownika, co oznacza pomyślne zalogowanie:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Diagram przedstawiający przepływ kodu autoryzacji w aplikacji jednostronicowej":::

### <a name="call-the-graph-api"></a>Wywołaj interfejs API programu Graph

Po zalogowaniu wybierz pozycję **Zobacz Profil** , aby wyświetlić informacje o profilu użytkownika zwrócone w odpowiedzi z wywołania do interfejsu API Microsoft Graph:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Diagram przedstawiający przepływ kodu autoryzacji w aplikacji jednostronicowej":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Domyślnie ten zakres jest automatycznie dodawany w każdej aplikacji, która jest zarejestrowana w Azure Portal. Inne interfejsy API dla Microsoft Graph, a także niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API Microsoft Graph wymaga, aby w celu wyświetlenia listy wiadomości e-mail użytkownika był wymagany zakres *poczty. Read.*

Podczas dodawania zakresów użytkownicy mogą zostać poproszeni o podanie dodatkowej zgody na dodane zakresy.

Jeśli interfejs API zaplecza nie wymaga zakresu, co nie jest zalecane, można użyć `clientId` jako zakresu wywołań w celu uzyskania tokenów.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat programowania aplikacji jednostronicowych w języku JavaScript na platformie tożsamości firmy Microsoft, zapoznaj się z naszą wieloczęściową serią scenariuszy:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja jednostronicowa](scenario-spa-overview.md)