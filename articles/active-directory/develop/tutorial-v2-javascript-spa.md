---
title: 'Samouczek: Tworzenie jednostronicowej aplikacji JavaScript korzystającej z platformy tożsamości firmy Microsoft do uwierzytelniania | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację jednostronicową języka JavaScript, która używa platformy tożsamości firmy Microsoft do logowania użytkowników i uzyskiwania tokenu dostępu w celu wywołania interfejsu API Microsoft Graph w ich imieniu.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: fca1ab61c4c07d8c619719d79872470626137249
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611184"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji JavaScript jednostronicowej (SPA)

W tym samouczku utworzysz aplikację jednostronicową (SPA) w języku JavaScript, która może zalogować użytkowników przy użyciu osobistych kont Microsoft lub kont służbowych, a następnie uzyskać token dostępu służący do wywoływania interfejsu API Microsoft Graph.

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie projektu JavaScript z `npm`
> * Zarejestruj aplikację w Azure Portal
> * Dodawanie kodu do obsługi logowania i wylogowywania użytkowników
> * Dodawanie kodu do wywołania interfejsu API Microsoft Graph
> * Testowanie aplikacji

>[!TIP]
> W tym samouczku użyto MSAL.js v1. x, które jest ograniczone do użycia niejawnego przepływu dotacji dla aplikacji jednostronicowych. Zalecamy używanie wszystkich nowych aplikacji zamiast [MSAL.js 2. x i przepływu kodu autoryzacji z obsługą PKCE i CORS](tutorial-v2-javascript-auth-code.md) .

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/) na potrzeby uruchamiania lokalnego serwera sieci Web.
* [Visual Studio Code](https://code.visualstudio.com/download) lub inny edytor do modyfikowania plików projektu.
* Nowoczesnej przeglądarki sieci Web. Program **Internet Explorer** **nie jest obsługiwany** przez aplikację utworzoną w tym samouczku ze względu na użycie konwencji [ES6](http://www.ecma-international.org/ecma-262/6.0/) przez aplikację.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa Przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

Przykładowa aplikacja utworzona przez ten przewodnik umożliwia wykonywanie zapytań do interfejsu API Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu po zalogowaniu się użytkownika token dostępu zostanie zaproszony i dodany do żądań HTTP za pośrednictwem nagłówka autoryzacji. Ten token będzie używany do uzyskiwania profilu użytkownika i wiadomości e-mail za pośrednictwem **MS interfejs API programu Graph**.

Pozyskiwanie i odnawianie tokenów jest obsługiwane przez [bibliotekę uwierzytelniania firmy Microsoft (MSAL) dla języka JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js).

## <a name="set-up-your-web-server-or-project"></a>Konfigurowanie serwera lub projektu sieci Web

> Wolisz pobrać projekt tego przykładu? [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Aby skonfigurować przykładowy kod przed jego wykonaniem, przejdź do [kroku konfiguracji](#register-your-application).

## <a name="create-your-project"></a>Tworzenie projektu

Upewnij się, że zainstalowano [Node.js](https://nodejs.org/en/download/) , a następnie utwórz folder do hostowania aplikacji. W tym miejscu zostanie wdrożony prosty serwer sieci Web [Express](https://expressjs.com/) , który będzie obsługiwał `index.html` plik.

1. Korzystając z terminalu (takiego jak Visual Studio Code zintegrowany terminal), zlokalizuj folder projektu, a następnie wpisz:

   ```console
   npm init
   ```

2. Następnie zainstaluj wymagane zależności:

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. Teraz Utwórz plik. js o nazwie `index.js` , a następnie Dodaj następujący kod:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Masz teraz prosty serwer, który będzie obsługiwał Twoje SPA. Zaplanowana struktura folderów na końcu tego samouczka jest następująca:

![tekst przedstawiający zamierzoną strukturę folderów SPA](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Tworzenie interfejsu użytkownika SPA

1. Utwórz `index.html` plik dla Spa JavaScript. Ten plik implementuje interfejs użytkownika zbudowany przy użyciu programu **Bootstrap 4 Framework** i importuje pliki skryptów na potrzeby konfiguracji, uwierzytelniania i wywołania interfejsu API.

   W `index.html` pliku Dodaj następujący kod:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > Wersję MSAL.js w powyższym skrypcie można zastąpić najnowszą wersją opublikowaną w obszarze [ wersjeMSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Teraz Utwórz plik. js o nazwie `ui.js` , który będzie uzyskiwać dostęp do elementów Dom i aktualizować je, a następnie Dodaj następujący kod:

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

Przed kontynuowaniem uwierzytelniania Zarejestruj swoją aplikację na **Azure Active Directory**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu z dzierżawą usługi Azure AD, której chcesz użyć.
1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. W sekcji **Identyfikator URI przekierowania** Wybierz platformę **sieci Web** z listy rozwijanej, a następnie ustaw wartość na adres URL aplikacji oparty na serwerze sieci Web.
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Ustawienia zaawansowane**w obszarze **niejawne przyznanie**zaznacz pola wyboru **tokeny identyfikatorów** i **tokeny dostępu** . Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Ustaw adres URL przekierowania dla Node.js
>
> W przypadku Node.js można ustawić port serwera sieci Web w pliku *index.js* . W tym samouczku jest używany port 3000, ale można użyć dowolnego innego dostępnego portu.
>
> Aby skonfigurować adres URL przekierowania w informacjach rejestracyjnych aplikacji, Wróć do okienka **rejestracji aplikacji** i wykonaj jedną z następujących czynności:
>
> - Ustaw *`http://localhost:3000/`* jako **adres URL przekierowania**.
> - Jeśli używasz niestandardowego portu TCP, użyj *`http://localhost:<port>/`* (gdzie *\<port>* jest niestandardowym numerem portu TCP).
>   1. Skopiuj wartość **adresu URL** .
>   1. Wróć do okienka **rejestracji aplikacji** i wklej skopiowaną wartość jako **adres URL przekierowania**.
>

### <a name="configure-your-javascript-spa"></a>Konfigurowanie protokołu JavaScript SPA

Utwórz nowy plik js o nazwie `authConfig.js` , który będzie zawierać parametry konfiguracji do uwierzytelniania, i Dodaj następujący kod:

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
    scopes: ["Mail.Read"]
  };
```

 Gdzie:
 - *\<Enter_the_Application_Id_Here>* to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.
 - *\<Enter_the_Cloud_Instance_Id_Here>* jest wystąpieniem chmury platformy Azure. W przypadku głównej lub globalnej chmury platformy Azure po prostu wprowadź *https://login.microsoftonline.com* . W przypadku chmur **narodowych** (na przykład Chin), zobacz [chmury narodowe](./authentication-national-cloud.md).
 - *\<Enter_the_Tenant_info_here>* jest ustawiona na jedną z następujących opcji:
   - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym*, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład *contoso.Microsoft.com*).
   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**.
   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Logowanie użytkownika przy użyciu biblioteki Microsoft Authentication Library (MSAL)

Utwórz nowy plik js o nazwie `authPopup.js` , który będzie zawierać logikę uwierzytelniania i tokenów pozyskiwania, a następnie Dodaj następujący kod:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
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
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>Więcej informacji

Gdy użytkownik wybierze przycisk **Zaloguj** po raz pierwszy, `signIn` wywołuje metodę `loginPopup` logowania użytkownika. Ta metoda umożliwia otwarcie okna podręcznego z *punktem końcowym platformy tożsamości firmy Microsoft* w celu wyświetlenia monitu i zweryfikowania poświadczeń użytkownika. Po pomyślnym zalogowaniu użytkownik zostanie przekierowany z powrotem do oryginalnej strony *index.html* . Token jest odbierany, przetwarzany przez `msal.js` i informacje zawarte w tokenie są buforowane. Token ten jest znany jako *token ID* i zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana użytkownika. Jeśli planujesz używać dowolnych danych z tego tokenu do dowolnych celów, musisz upewnić się, że ten token jest zweryfikowany przez serwer wewnętrznej bazy danych, aby zagwarantować, że token został wystawiony dla prawidłowego użytkownika aplikacji.

SPA wygenerowane przez ten przewodnik wywołuje `acquireTokenSilent` i/lub `acquireTokenPopup` uzyskuje *token dostępu* używany do wykonywania zapytań dotyczących interfejsu API Microsoft Graph informacji o profilu użytkownika. Jeśli potrzebujesz przykładu, który sprawdza poprawność tokenu identyfikatora, zapoznaj się z [tą](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Przykład usługi GitHub Active-Directory-JavaScript-singlepageapp-dotnet-WebAPI-v2") przykładową aplikacją w serwisie GitHub. Przykład używa interfejsu API sieci Web ASP.NET do sprawdzania poprawności tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Po wstępnym logowaniu nie chcesz poprosić użytkowników o ponowne uwierzytelnienie przy każdym zażądaniu tokenu w celu uzyskania dostępu do zasobu. Dlatego *acquireTokenSilent* powinien być używany w większości czasu do uzyskania tokenów. Istnieją jednak sytuacje, w których należy wymusić, aby użytkownicy mogli korzystać z punktu końcowego platformy tożsamości firmy Microsoft. Przykłady:

- Użytkownicy muszą ponownie wprowadzić swoje poświadczenia, ponieważ hasło wygasło.
- Aplikacja żąda dostępu do zasobu i potrzebujesz zgody użytkownika.
- Wymagane jest uwierzytelnianie dwuskładnikowe.

Wywołanie *acquireTokenPopup* otwiera okno podręczne (lub *acquireTokenRedirect* przekierowuje użytkowników do punktu końcowego platformy tożsamości firmy Microsoft). W tym oknie Użytkownicy muszą mieć możliwość działania przez potwierdzenie poświadczeń, udzielenie zgody na wymagane zasoby lub zakończenie uwierzytelniania dwuskładnikowego.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`acquireTokenSilent`Metoda obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Gdy `loginPopup` (lub `loginRedirect` ) jest wykonywane po raz pierwszy, `acquireTokenSilent` jest to metoda często używana do uzyskiwania tokenów używanych w celu uzyskiwania dostępu do chronionych zasobów dla kolejnych wywołań. (Wywołania żądania lub odnawiania tokenów są wykonywane w trybie dyskretnym). `acquireTokenSilent` może zakończyć się niepowodzeniem w niektórych przypadkach. Na przykład hasło użytkownika mogło wygasnąć. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1. Wykonaj wywołanie `acquireTokenPopup` natychmiast, aby wyzwolić monit logowania użytkownika. Ten wzorzec jest często używany w aplikacjach online, w których aplikacja nie ma dostępnej nieuwierzytelnionej zawartości. Przykład generowany przez tę konfigurację z przewodnikiem używa tego wzorca.

1. Aplikacje mogą także wprowadzać wizualizację do użytkownika, że wymagane jest logowanie interaktywne, aby użytkownik mógł wybrać odpowiedni czas na zalogowanie się, a aplikacja może ponowić próbę `acquireTokenSilent` w późniejszym czasie. Jest to często używane, gdy użytkownik może korzystać z innych funkcji aplikacji bez zakłócania pracy. Na przykład w aplikacji może być dostępna nieuwierzytelniona zawartość. W takiej sytuacji użytkownik może zdecydować się na zalogowanie się w celu uzyskania dostępu do chronionego zasobu lub odświeżenie nieaktualnych informacji.

> [!NOTE]
> Ten przewodnik Szybki Start `loginPopup` `acquireTokenPopup` domyślnie używa metod i. Jeśli używasz programu Internet Explorer jako przeglądarki, zaleca się korzystanie z nich `loginRedirect` i `acquireTokenRedirect` metod ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z sposobem obsługi wyskakujących okienek przez program Internet Explorer. Jeśli chcesz dowiedzieć się, jak osiągnąć ten sam wynik przy użyciu `Redirect methods` , [Zobacz](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Wywoływanie interfejsu API Microsoft Graph przy użyciu właśnie uzyskanego tokenu

1. Najpierw utwórz plik. js o nazwie `graphConfig.js` , który będzie przechowywać punkty końcowe Rest. Dodaj następujący kod:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Gdzie:
   - *\<Enter_the_Graph_Endpoint_Here>* jest wystąpieniem programu MS interfejs API programu Graph. W przypadku globalnego punktu końcowego MS interfejs API programu Graph należy po prostu zastąpić ten ciąg ciągiem `https://graph.microsoft.com` . W przypadku wdrożeń w chmurze krajowej zapoznaj się z [dokumentacją interfejs API programu Graph](/graph/deployments).

1. Następnie utwórz plik. js o nazwie `graph.js` , który spowoduje wywołanie REST do Microsoft Graph API i Dodaj następujący kod:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W przykładowej aplikacji utworzonej przez ten przewodnik `callMSGraph()` Metoda jest używana do wykonania `GET` żądania HTTP względem chronionego zasobu, który wymaga tokenu. Żądanie zwraca zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w *nagłówku autoryzacji http*. W przypadku przykładowej aplikacji utworzonej w tym przewodniku zasób jest punktem końcowym usługi Microsoft Graph API *Me* , który wyświetla informacje o profilu użytkownika.

## <a name="test-your-code"></a>Testowanie kodu

1. Skonfiguruj serwer do nasłuchiwania na porcie TCP, który jest oparty na lokalizacji pliku *index.html* . Aby uzyskać Node.js, uruchom serwer sieci Web w celu nasłuchiwania na porcie, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

   ```bash
   npm install
   npm start
   ```
1. W przeglądarce wpisz **http://localhost:3000** lub **http://localhost:{port}** , gdzie *port* jest portem, z którym nasłuchuje serwer sieci Web. Powinna zostać wyświetlona zawartość pliku *index.html* i przycisku **Zaloguj** .

Po załadowaniu przez przeglądarkę pliku *index.html* wybierz pozycję **Zaloguj**. Zostanie wyświetlony monit o zalogowanie się za pomocą punktu końcowego platformy tożsamości firmy Microsoft:

![Okno logowania do konta SPA w języku JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Wyrażanie zgody na dostęp do aplikacji

Po pierwszym zalogowaniu się do aplikacji zostanie wyświetlony monit o udzielenie dostępu do Twojego profilu i zalogowanie się w:

![Okno "wymagane uprawnienia"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu informacje o profilu użytkownika zostaną zwrócone w odpowiedzi interfejsu API Microsoft Graph, która jest wyświetlana:

![Wyniki wywołania interfejsu API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Domyślnie ten zakres jest automatycznie dodawany w każdej aplikacji, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API dla Microsoft Graph, a także niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API Microsoft Graph wymaga, aby w celu wyświetlenia listy wiadomości e-mail był określony zakres *wiadomości e-mail* .

> [!NOTE]
> Użytkownik może zostać poproszony o dodatkowe przesłanie w miarę zwiększania liczby zakresów.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Zajrzyj do aplikacji jednostronicowej (SPA) na platformie tożsamości firmy Microsoft w naszej serii scenariuszy wieloczęściowych.

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja jednostronicowa](scenario-spa-overview.md)