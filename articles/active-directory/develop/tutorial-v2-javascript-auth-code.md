---
title: Samouczek dotyczący jednostronicowej aplikacji JavaScript 2,0 — platforma tożsamości firmy Microsoft | Azure
description: Jak aplikacje SPA w języku JavaScript mogą używać przepływu kodu uwierzytelniania do wywoływania interfejsu API wymagającego tokenów dostępu przez punkt końcowy Azure Active Directory v 2.0
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ee156c8118a67061d0a000867ee64fe1f3ebd18c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182139"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji JavaScript jednostronicowej (SPA) — MSAL. js 2,0

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre aspekty tej funkcji mogą ulec zmianie przed ogólnym udostępnieniem.

Ten samouczek używa wersji MSAL. js, która używa przepływu kodu autoryzacji OAuth 2,0 z PKCE. Aby dowiedzieć się więcej na temat tego protokołu, a także różnic między przepływem niejawnym i przepływem kodu autoryzacji, zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow). Jeśli szukasz samouczka korzystającego z niejawnego przepływu, zobacz [samouczek MSAL. js V1](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

Ta wersja MSAL. js jest ulepszona w bieżącej bibliotece MSAL-Core i używa przepływu kodu autoryzacji w przeglądarce. Większość funkcji dostępnych w starej bibliotece jest dostępnych w tej wersji, ale w obu przypadkach istnieje wszystkie szczegóły do przepływu uwierzytelniania. Ta wersja nie **obsługuje przepływu** niejawnego.

W tym przewodniku przedstawiono sposób, w jaki aplikacja obsługująca skrypty JavaScript (single-page) może:
- Zaloguj się do kont osobistych, a także kont służbowych
- Uzyskiwanie tokenu dostępu
- Wywołaj interfejs API Microsoft Graph lub inne interfejsy API, które wymagają tokenów dostępu z *punktu końcowego platformy tożsamości firmy Microsoft*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa Przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Więcej informacji

Przykładowa aplikacja utworzona przez ten przewodnik umożliwia wykonywanie zapytań do interfejsu API Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu po zalogowaniu się użytkownika token dostępu zostanie zaproszony i dodany do żądań HTTP za pośrednictwem nagłówka autoryzacji. Pozyskiwanie i odnawianie tokenów jest obsługiwane przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).

### <a name="libraries"></a>Biblioteki

W tym przewodniku jest stosowana następująca Biblioteka:

|Biblioteka|Opis|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Biblioteka uwierzytelniania firmy Microsoft dla przeglądarki JavaScript|

## <a name="set-up-your-web-server-or-project"></a>Konfigurowanie serwera lub projektu sieci Web

Wolisz pobrać projekt tego przykładu? Aby uruchomić projekt przy użyciu lokalnego serwera sieci Web, takiego jak Node. js, Sklonuj pliki projektu:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Aby skonfigurować przykładowy kod przed jego wykonaniem, przejdź do [kroku konfiguracji](#register-your-application).

## <a name="prerequisites"></a>Wymagania wstępne

* Do uruchomienia tego samouczka potrzebny jest lokalny serwer sieci Web, na przykład [Node. js](https://nodejs.org/en/download/) lub [.NET Core](https://www.microsoft.com/net/core).

* Jeśli używasz środowiska Node. js do uruchamiania projektu, zainstaluj zintegrowane środowisko programistyczne (IDE), takie jak [Visual Studio Code](https://code.visualstudio.com/download) do edytowania plików projektu.

* Instrukcje zawarte w tym samouczku są oparte na platformie Node. js.

## <a name="create-your-project"></a>Tworzenie projektu

Upewnij się, że masz zainstalowany program [Node. js](https://nodejs.org/en/download/) , a następnie utwórz folder do hostowania aplikacji. Następnie Zaimplementuj [mały serwer](https://expressjs.com/) sieci Web, aby obtworzyć `index.html` plik.

1. Najpierw przejdź do folderu projektu w terminalu, a następnie uruchom następujące polecenia NPM.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Następnie utwórz plik. js o nazwie *Server. js*, a następnie Dodaj następujący kod:

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

Masz teraz prosty serwer, który będzie obsługiwał Twoje SPA. Zaplanowana struktura folderów na końcu tego samouczka jest następująca:

![tekst przedstawiający zamierzoną strukturę folderów SPA](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Tworzenie interfejsu użytkownika SPA

1. Utwórz plik *index. html* dla hasła spa w folderze *aplikacji* . Ten plik implementuje interfejs użytkownika zbudowany przy użyciu programu **Bootstrap 4 Framework** i importuje pliki skryptów dla wywołań konfiguracji, uwierzytelniania i interfejsu API.

   W pliku *index. html* Dodaj następujący kod:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
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
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
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

   > [!TIP]
   > Wersję MSAL. js w powyższym skrypcie można zastąpić najnowszymi wersjami w sekcji [releases MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


2. Teraz Utwórz plik. js o nazwie *UI. js* , który będzie uzyskiwać dostęp do elementów Dom i ich aktualizować, i Dodaj następujący kod:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

Postępuj zgodnie z instrukcjami, aby [zarejestrować nową aplikację jednostronicową](https://docs.microsoft.com/zure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Ustawianie adresu URL przekierowania dla środowiska Node. js

W przypadku środowiska Node. js można ustawić port serwera sieci Web w pliku *Server. js* . W tym samouczku jest używany port 3000, ale można użyć dowolnego innego dostępnego portu.

Aby skonfigurować adres URL przekierowania w informacjach o rejestracji aplikacji, przełącz się z powrotem do okienka **rejestracji aplikacji** i Zarejestruj nowe **Spa** z jedną z następujących czynności:

- Ustaw *`http://localhost:3000/`* jako **adres URL przekierowania**.
- Jeśli używasz niestandardowego portu TCP, użyj *`http://localhost:<port>/`* (gdzie * \<port>* jest niestandardowym numerem portu TCP).

### <a name="configure-your-javascript-spa"></a>Konfigurowanie protokołu JavaScript SPA

Utwórz nowy plik js o nazwie *authConfig. js* , aby zawierał parametry konfiguracji na potrzeby uwierzytelniania, a następnie Dodaj następujący kod:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Gdzie:
 - Enter_the_Application_Id_Here>to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji. * \<*
 - Enter_the_Cloud_Instance_Id_Here>jest wystąpieniem chmury platformy Azure. * \<* W przypadku głównej lub globalnej chmury platformy Azure po prostu *https://login.microsoftonline.com*wprowadź. W przypadku chmur **narodowych** (na przykład Chin), zobacz [chmury narodowe](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - >Enter_the_Tenant_info_here jest ustawiona na jedną z następujących opcji: * \<*
   - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym*, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład *contoso.Microsoft.com*).
   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**.
   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**.
- *\ Enter_the_Redirect_Uri_Here>* to port zarejestrowany w portalu (*`http://localhost:3000/`*)


Utwórz nowy plik js o nazwie `graphConfig.js`, który będzie zawierać parametry konfiguracji WYWOŁUJĄCE interfejs API Microsoft Graph i Dodaj następujący kod:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- Enter_the_Graph_Endpoint_Here>jest wystąpieniem programu MS interfejs API programu Graph. * \<* W przypadku globalnego punktu końcowego MS interfejs API programu Graph należy po prostu zastąpić ten `https://graph.microsoft.com`ciąg ciągiem. W przypadku wdrożeń w chmurze krajowej zapoznaj się z [dokumentacją interfejs API programu Graph](https://docs.microsoft.com/graph/deployments).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Logowanie użytkownika przy użyciu biblioteki Microsoft Authentication Library (MSAL)

### <a name="popup"></a>Elementy
Utwórz nowy plik. js o nazwie `authPopup.js`, który będzie zawierać logikę uwierzytelniania i pozyskiwania tokenów dla okna podręcznego logowania, a następnie Dodaj następujący kod:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Przekierowanie
Utwórz nowy plik js o nazwie `authRedirect.js`, który będzie zawierać logikę uwierzytelniania i tokenu pozyskiwania dla przekierowania logowania, a następnie Dodaj następujący kod:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>Więcej informacji

Gdy użytkownik wybierze przycisk **Zaloguj** po raz pierwszy, wywołuje `signIn` `loginPopup` metodę logowania użytkownika. Ta metoda umożliwia otwarcie okna podręcznego z *punktem końcowym platformy tożsamości firmy Microsoft* w celu wyświetlenia monitu i zweryfikowania poświadczeń użytkownika. Po pomyślnym zalogowaniu *msal. js* inicjuje [przepływ kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

W tym momencie kod autoryzacji chroniony przez PKCE jest wysyłany do punktu końcowego tokenu chronionego przez funkcję CORS i jest wymieniany na potrzeby tokenów. Tokeny identyfikatora, token dostępu i token odświeżania są odbierane, przetwarzane przez *msal. js*, a informacje zawarte w tokenie są buforowane.

Token identyfikatora zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana. Jeśli planujesz używać dowolnych danych z tego tokenu, muszą one zostać zweryfikowane przez serwer zaplecza, aby zagwarantować, że token został wystawiony dla prawidłowego użytkownika aplikacji. Token odświeżania ma ograniczony okres istnienia i wygasa po 24 godzinach. Token odświeżania może służyć do dyskretnego pozyskiwania nowych tokenów dostępu.

SPA wygenerowane przez ten przewodnik wywołuje `acquireTokenSilent` i/lub `acquireTokenPopup` uzyskuje *token dostępu* używany do wykonywania zapytań dotyczących interfejsu API Microsoft Graph informacji o profilu użytkownika. Jeśli potrzebujesz przykładu, który sprawdza poprawność tokenu identyfikatora, zobacz przykładową aplikację [Active-Directory-JavaScript-singlepageapp-dotnet-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) w witrynie GitHub. Przykład używa interfejsu API sieci Web ASP.NET do sprawdzania poprawności tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Po wstępnym logowaniu użytkownicy nie powinni otrzymywać monitów o ponowne uwierzytelnienie przy każdym zażądaniu tokenu w celu uzyskania dostępu do zasobu. Aby uniemożliwić takie żądania ponownego uwierzytelniania, użyj `acquireTokenSilent`programu. Istnieją jednak sytuacje, w których konieczne może być wymuszenie współpracy użytkowników z punktem końcowym platformy tożsamości firmy Microsoft. Przykład:

- Użytkownicy muszą ponownie wprowadzić swoje poświadczenia, ponieważ hasło wygasło.
- Aplikacja żąda dostępu do zasobu i potrzebujesz zgody użytkownika.
- Wymagane jest uwierzytelnianie dwuskładnikowe.

Wywołanie `acquireTokenPopup` powoduje otwarcie okna podręcznego (lub `acquireTokenRedirect` przekieruje użytkowników do punktu końcowego platformy tożsamości firmy Microsoft). W tym oknie Użytkownicy muszą mieć możliwość działania przez potwierdzenie poświadczeń, udzielenie zgody na wymagane zasoby lub zakończenie uwierzytelniania dwuskładnikowego.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`acquireTokenSilent` Metoda obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Gdy `loginPopup` (lub `loginRedirect`) jest wykonywane po raz pierwszy, `acquireTokenSilent` jest to metoda często używana do uzyskiwania tokenów używanych w celu uzyskiwania dostępu do chronionych zasobów dla kolejnych wywołań. (Wywołania żądania lub odnawiania tokenów są wykonywane w trybie dyskretnym). `acquireTokenSilent` może zakończyć się niepowodzeniem w niektórych przypadkach. Na przykład hasło użytkownika mogło wygasnąć. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1. Utwórz wywołanie `acquireTokenPopup` natychmiast, aby wyzwolić monit logowania użytkownika. Ten wzorzec jest często używany w aplikacjach online, w których aplikacja nie ma dostępnej nieuwierzytelnionej zawartości. Przykład generowany przez tę konfigurację z przewodnikiem używa tego wzorca.

1. Wizualnie wskazuje użytkownikowi, że logowanie interaktywne jest wymagane, aby użytkownik mógł wybrać odpowiedni czas na zalogowanie się, a aplikacja może ponowić próbę `acquireTokenSilent` w późniejszym czasie. Ta technika jest często używana, gdy użytkownik może korzystać z innych funkcji aplikacji bez zakłócania pracy. Na przykład w aplikacji może być dostępna nieuwierzytelniona zawartość. W takiej sytuacji użytkownik może zdecydować się na zalogowanie się w celu uzyskania dostępu do chronionego zasobu lub odświeżenie nieaktualnych informacji.

> [!NOTE]
> Ten przewodnik Szybki Start `loginPopup` domyślnie `acquireTokenPopup` używa metod i. Jeśli używasz programu Internet Explorer jako przeglądarki, zaleca się korzystanie `loginRedirect` z nich i `acquireTokenRedirect` metod ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z sposobem obsługi wyskakujących okienek przez program Internet Explorer. Jeśli chcesz dowiedzieć się, jak osiągnąć ten sam wynik przy użyciu metod przekierowywania, zobacz [*authRedirect. js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Wywoływanie interfejsu API Microsoft Graph przy użyciu właśnie uzyskanego tokenu


 Utwórz plik. js o nazwie *Graph. js* , który spowoduje wywołanie REST do Microsoft Graph API i Dodaj następujący kod:

   ```javascript

// Helper function to call MS Graph API endpoint
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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W przykładowej aplikacji utworzonej przez ten przewodnik `callMSGraph()` Metoda jest używana do wykonania żądania HTTP `GET` względem chronionego zasobu, który wymaga tokenu. Żądanie zwraca zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w *nagłówku autoryzacji http*. W przypadku przykładowej aplikacji utworzonej w tym przewodniku zasób jest punktem końcowym usługi Microsoft Graph API *Me* , który wyświetla informacje o profilu użytkownika.

## <a name="test-your-code"></a>testowanie kodu

1. W przypadku środowiska Node. js Uruchom serwer sieci Web, uruchamiając następujące polecenia z poziomu folderu aplikacji:

   ```bash
   npm install
   npm start
   ```
1. W przeglądarce wpisz **http://localhost:3000** lub **http://localhost:{port}**, gdzie *port* to port, na którym nasłuchuje serwer sieci Web. Powinna zostać wyświetlona zawartość pliku *index. html* i przycisku **Zaloguj** .

## <a name="test-your-application"></a>Testowanie aplikacji

Po załadowaniu przez przeglądarkę pliku *index. html* wybierz pozycję **Zaloguj**. Zostanie wyświetlony monit o zalogowanie się za pomocą punktu końcowego platformy tożsamości firmy Microsoft:

![Okno logowania do konta SPA w języku JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Wyrażanie zgody na dostęp do aplikacji

Po pierwszym zalogowaniu się do aplikacji zostanie wyświetlony monit o udzielenie dostępu do Twojego profilu i zalogowanie się w:

![Okno "wymagane uprawnienia"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu informacje o profilu użytkownika zostaną zwrócone w odpowiedzi interfejsu API Microsoft Graph, która jest wyświetlana:

![Wyniki wywołania interfejsu API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Domyślnie ten zakres jest automatycznie dodawany w każdej aplikacji, która jest zarejestrowana w Azure Portal. Inne interfejsy API dla Microsoft Graph, a także niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API Microsoft Graph wymaga, aby w celu wyświetlenia listy wiadomości e-mail był określony zakres *wiadomości e-mail* .

> [!NOTE]
> Użytkownik może zostać poproszony o dodatkową zgodę podczas dodawania zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (niezalecane), można użyć *clientId* jako zakresu w wywołaniach w celu uzyskania tokenów.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

[Repozytorium GitHub MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js) zawiera dodatkową dokumentację biblioteki, często zadawane pytania i zapewnia pomoc techniczną.
