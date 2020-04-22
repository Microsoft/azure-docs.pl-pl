---
title: Samouczek aplikacji w języku JavaScript - Platforma tożsamości firmy Microsoft | Azure
description: Jak aplikacje JavaScript SPA mogą wywoływać interfejs API, który wymaga tokenów dostępu przez punkt końcowy usługi Azure Active Directory w wersji 2.0
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 52b7c582848dd24f6d9963a9d37c8f12c5db6149
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678024"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Zaloguj użytkowników i wywołaj interfejs API programu Microsoft Graph z jednostronicowej aplikacji JavaScript (SPA)

W tym przewodniku pokazano, jak aplikacja jednostronicowa JavaScript (SPA) może:
- Zaloguj się na konta osobiste, a także konta służbowe i szkolne
- Uzyskiwanie tokenu dostępu
- Wywoływanie interfejsu API programu Microsoft Graph lub innych interfejsów API, które wymagają tokenów dostępu z *punktu końcowego platformy tożsamości firmy Microsoft*

>[!NOTE]
> Jeśli jesteś nowy na platformie tożsamości firmy Microsoft, zalecamy rozpoczęcie od [logowania użytkowników i uzyskanie tokenu dostępu w przewodniku Szybki start javascript SPA](quickstart-v2-javascript.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje, jak działa przykładowa aplikacja generowana przez ten samouczek](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Przykładowa aplikacja utworzona przez ten przewodnik umożliwia javascript SPA do kwerendy interfejsu API programu Microsoft Graph lub interfejsu API sieci web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu po zalogowaniu się użytkownika token dostępu jest wymagany i dodawany do żądań HTTP za pośrednictwem nagłówka autoryzacji. Ten token będzie używany do uzyskiwania profilu użytkownika i wiadomości e-mail za pośrednictwem **interfejsu API ms graph.** Pozyskiwanie i odnawianie tokenów są obsługiwane przez **bibliotekę uwierzytelniania Firmy Microsoft (MSAL) dla języka JavaScript**.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Biblioteki

W tym przewodniku użyto następującej biblioteki:

|Biblioteka|Opis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript|

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Konfigurowanie serwera sieci Web lub projektu

> Wolisz zamiast tego pobrać projekt tego przykładu? [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Aby skonfigurować przykładowy kod przed jego wykonaniem, przejdź do [kroku konfiguracji](#register-your-application).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby uruchomić ten samouczek, potrzebujesz lokalnego serwera sieci web, takiego jak [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core)lub IIS Express integracji z [programem Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Instrukcje zawarte w tym przewodniku są oparte na serwerze sieci web wbudowanym w plik Node.js. Zaleca się przy użyciu [programu Visual Studio Code](https://code.visualstudio.com/download) jako zintegrowanego środowiska programistycznego (IDE).

## <a name="create-your-project"></a>Tworzenie projektu

Upewnij się, że masz zainstalowany [plik Node.js,](https://nodejs.org/en/download/) a następnie utwórz folder do obsługi aplikacji. Tam zaimplementujemy [Express](https://expressjs.com/) prosty serwer internetowy `index.html` Express do obsługi twojego pliku.

1. Najpierw przy użyciu zintegrowanego terminala programu Visual Studio Code zlokalizuj folder projektu, a następnie zainstaluj program Express przy użyciu usługi NPM.

1. Następnie utwórz plik .js o nazwie `server.js`, a następnie dodaj następujący kod:

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

Masz teraz prosty serwer do obsługi SPA. Planowana struktura folderów na końcu tego samouczka jest następująca:

![obraz tekstu zamierzonej struktury folderów SPA](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Tworzenie interfejsu użytkownika SPA

1. Utwórz `index.html` plik dla środowiska JavaScript SPA. Ten plik implementuje interfejs użytkownika zbudowany za pomocą **programu Bootstrap 4 Framework** i importuje pliki skryptów do konfiguracji, uwierzytelniania i wywołania interfejsu API.

   W `index.html` pliku dodaj następujący kod:

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
   > Wersję msal.js można zastąpić w poprzednim skrypcie najnowszą wersją wydaną w obszarze [MsAL.js.](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

2. Teraz utwórz plik .js o nazwie `ui.js`, który będzie uzyskiwać dostęp do elementów DOM i aktualizować je, oraz dodać następujący kod:

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

Przed kontynuowaniem uwierzytelniania należy zarejestrować aplikację w **usłudze Azure Active Directory**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Jeśli twoje konto daje dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na dzierżawę usługi Azure AD, której chcesz użyć.
1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. W sekcji **Przekierowanie identyfikatora URI** wybierz platformę **sieci Web** z listy rozwijanej, a następnie ustaw wartość adresu URL aplikacji opartego na serwerze sieci Web.
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **Uwierzytelnianie**.
1. W **obszarze Ustawienia zaawansowane**w obszarze **Niejawne przyznawanie wybierz**pola wyboru **Tokeny identyfikatorów** i **Tokeny dostępu.** Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować się do użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Ustawianie adresu URL przekierowania dla pliku Node.js
>
> W polu Node.js można ustawić port serwera sieci web w pliku *server.js.* W tym samouczku użyto portu 3000, ale można użyć dowolnego innego dostępnego portu.
>
> Aby skonfigurować adres URL przekierowania w informacjach o rejestracji aplikacji, przełącz się z powrotem do **okienka Rejestracja aplikacji** i wykonaj jedną z następujących czynności:
>
> - Ustaw *`http://localhost:3000/`* jako **adres URL przekierowania**.
> - Jeśli używasz niestandardowego portu TCP, użyj *`http://localhost:<port>/`* (gdzie * \<>portu* jest niestandardowy numer portu TCP).
>   1. Skopiuj wartość **adresu URL.**
>   1. Przełącz się z powrotem do **okienka Rejestracja aplikacji** i wklej skopiowaną wartość jako **adres URL przekierowania**.
>

### <a name="configure-your-javascript-spa"></a>Konfigurowanie środowiska JavaScript SPA

Utwórz nowy plik .js o nazwie `authConfig.js`, który będzie zawierał parametry konfiguracji uwierzytelniania, i dodaj następujący kod:

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
 - Enter_the_Application_Id_Here>jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji. * \<*
 - Enter_the_Cloud_Instance_Id_Here>jest wystąpieniem chmury platformy Azure. * \<* W przypadku głównej lub globalnej *https://login.microsoftonline.com*chmury platformy Azure wystarczy wprowadzić plik . Zobacz też: Chmury **narodowe** [.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)
 - Enter_the_Tenant_info_here>jest ustawiona na jedną z następujących opcji: * \<*
   - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym,* zastąp tę wartość nazwą **dzierżawy** lub **dzierżawy** (na przykład *contoso.microsoft.com*).
   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym,* zastąp tę wartość **organizacjami**.
   - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft,* zastąp tę wartość **wspólną**. Aby ograniczyć obsługę tylko do *osobistych kont Microsoft,* zastąp tę wartość **konsumentami.**


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Logowanie użytkownika za pomocą biblioteki uwierzytelniania firmy Microsoft (MSAL)

Utwórz nowy plik .js o nazwie `authPopup.js`, który będzie zawierał logikę pozyskiwania uwierzytelniania i tokenu, i dodaj następujący kod:

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

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Po wybraniu przez użytkownika przycisku **Zaloguj** się `signIn` po `loginPopup` raz pierwszy metoda wywołuje logowanie użytkownika. Ta metoda otwiera wyskakujące okno z *punktem końcowym platformy tożsamości firmy Microsoft,* aby monitować i sprawdzać poprawność poświadczeń użytkownika. Po pomyślnym zalogowaniu użytkownik jest przekierowywał z powrotem do oryginalnej strony *index.html.* Token jest odbierany, `msal.js`przetwarzane przez program , a informacje zawarte w tokenie są buforowane. Ten token jest znany jako *token identyfikatora* i zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana użytkownika. Jeśli planujesz użyć żadnych danych dostarczonych przez ten token do dowolnych celów, należy upewnić się, że ten token jest sprawdzany przez serwer wewnętrznej bazy danych, aby zagwarantować, że token został wystawiony prawidłowemu użytkownikowi dla aplikacji.

Spa generowane przez ten `acquireTokenSilent` przewodnik `acquireTokenPopup` wywołuje i/lub uzyskać *token dostępu* używany do kwerendy interfejsu API programu Microsoft Graph dla informacji o profilu użytkownika. Jeśli potrzebujesz przykładu, który sprawdza poprawność tokenu identyfikatora, zapoznaj się z [tą](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 — przykład") przykładową aplikacją w usłudze GitHub. Przykład używa ASP.NET interfejsu API sieci web do sprawdzania poprawności tokenu.

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Po początkowym logowanie, nie chcesz prosić użytkowników o ponowne uwierzytelnienie za każdym razem, gdy trzeba zażądać tokenu, aby uzyskać dostęp do zasobu. Więc *acquireTokenSilent* powinny być używane przez większość czasu do nabycia tokenów. Istnieją jednak sytuacje, w których należy wymusić na użytkownikach interakcję z punktem końcowym platformy tożsamości firmy Microsoft. Przykłady:

- Użytkownicy muszą ponownie w ywuczeć swoje poświadczenia, ponieważ hasło wygasło.
- Aplikacja żąda dostępu do zasobu i potrzebujesz zgody użytkownika.
- Wymagane jest uwierzytelnianie dwuskładnikowe.

Wywołanie *acquireTokenPopup* otwiera wyskakujące okno (lub *acquireTokenRedirect* przekierowuje użytkowników do punktu końcowego platformy tożsamości firmy Microsoft). W tym oknie użytkownicy muszą wchodzić w interakcje, potwierdzając swoje poświadczenia, wyrażając zgodę na wymagany zasób lub wypełniając uwierzytelnianie dwuskładnikowe.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje pozyskiwanie tokenów i odnawianie bez interakcji z użytkownikiem. Po `loginPopup` (lub) `loginRedirect`jest wykonywany po `acquireTokenSilent` raz pierwszy, jest metoda powszechnie używana do uzyskiwania tokenów używanych do uzyskiwania dostępu do chronionych zasobów dla kolejnych wywołań. (Wywołania żądania lub odnowienia tokenów są składane w trybie cichym). `acquireTokenSilent` w niektórych przypadkach może się nie powieść. Na przykład hasło użytkownika mogło wygasnąć. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1. Natychmiast nawiązać `acquireTokenPopup` połączenie, co powoduje wyświetlenie monitu o logowanie użytkownika. Ten wzorzec jest powszechnie używany w aplikacjach online, gdzie nie ma nieuwierzytejnionej zawartości w aplikacji dostępnej dla użytkownika. Próbka wygenerowana przez tę konfigurację z przewodnikiem używa tego wzorca.

1. Aplikacje mogą również wskazać wizualne dla użytkownika, że wymagane jest interaktywne logowanie, dzięki czemu użytkownik może `acquireTokenSilent` wybrać odpowiedni czas logowania lub aplikacja może ponowić próbę w późniejszym czasie. Jest to często używane, gdy użytkownik może korzystać z innych funkcji aplikacji bez zakłócania. Na przykład może być nieuwierzytyfikowana zawartość dostępna w aplikacji. W tej sytuacji użytkownik może zdecydować, kiedy chcesz zalogować się, aby uzyskać dostęp do chronionego zasobu lub odświeżyć nieaktualne informacje.

> [!NOTE]
> Ten szybki start `loginPopup` domyślnie używa metod i. `acquireTokenPopup` Jeśli używasz programu Internet Explorer jako przeglądarki, `loginRedirect` `acquireTokenRedirect` zaleca się użycie i metody, ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany ze sposobem, w jaki program Internet Explorer obsługuje wyskakujące okna. Jeśli chcesz zobaczyć, jak osiągnąć ten `Redirect methods`sam wynik za pomocą , [zobacz](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który właśnie nabył

1. Najpierw utwórz plik .js o nazwie `graphConfig.js`, który będzie przechowywać punkty końcowe REST. Dodaj następujący kod:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Gdzie:
   - Enter_the_Graph_Endpoint_Here>jest wystąpieniem interfejsu API programu MS Graph. * \<* W przypadku globalnego punktu końcowego interfejsu API `https://graph.microsoft.com`programu MS Graph wystarczy zastąpić ten ciąg programem . Informacje na temat wdrożeń chmury krajowej można znaleźć w [dokumentacji interfejsu API wykresu](https://docs.microsoft.com/graph/deployments).

1. Następnie utwórz plik .js o nazwie `graph.js`, który spowoduje wywołanie REST do interfejsu API programu Microsoft Graph i dodaj następujący kod:

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

<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat wykonywania wywołań REST za okszewnie chronionego interfejsu API

W przykładowej aplikacji utworzonej `callMSGraph()` przez ten przewodnik metoda `GET` jest używana do żądania HTTP względem chronionego zasobu, który wymaga tokenu. Żądanie następnie zwraca zawartość do wywołującego. Ta metoda dodaje nabyty token w *nagłówku autoryzacji HTTP*. Dla przykładowej aplikacji utworzonej przez ten przewodnik zasób jest punktem końcowym interfejsu API programu Microsoft Graph *me,* który wyświetla informacje o profilu użytkownika.

<!--end-collapse-->

## <a name="test-your-code"></a>testowanie kodu

1. Skonfiguruj serwer do nasłuchiwać portu TCP opartego na lokalizacji pliku *index.html.* W przypadku pliku Node.js uruchom serwer sieci web, aby nasłuchiwać portu, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

   ```bash
   npm install
   npm start
   ```
1. W przeglądarce **http://localhost:3000** wprowadź **http://localhost:{port}** lub , gdzie *port* jest portem, którego nasłuchuje serwer www. Powinna zostać wyświetlona zawartość pliku *index.html* i przycisku **Zaloguj.**

## <a name="test-your-application"></a>Testowanie aplikacji

Po załadowaniu pliku *index.html* przez przeglądarkę wybierz pozycję **Zaloguj**się . Zostanie wyświetlony monit o zalogowanie się przy za pomocą punktu końcowego platformy tożsamości firmy Microsoft:

![Okno logowania do konta JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Udzielanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu się do aplikacji zostanie wyświetlony monit o udzielenie jej dostępu do profilu i zalogowanie się:

![Okno "Wymagane uprawnienia"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu informacje o profilu użytkownika są zwracane w wyświetlona odpowiedź interfejsu API programu Microsoft Graph:

![Wyniki wywołania interfejsu API programu Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i uprawnieniach delegowanych

Interfejs API programu Microsoft Graph wymaga, aby zakres *user.read* odczytywany był w profilu użytkownika. Domyślnie ten zakres jest automatycznie dodawany w każdej aplikacji, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API programu Microsoft Graph, a także niestandardowe interfejsy API serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API programu Microsoft Graph wymaga zakresu *Mail.Read* w celu wyświetlenia listy wiadomości e-mail użytkownika.

> [!NOTE]
> Użytkownik może zostać poproszony o dodatkowe zgody w miarę zwiększania liczby zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (nie zalecane), można użyć *clientId* jako zakres w wywołaniach do uzyskania tokenów.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
