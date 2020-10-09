---
title: Samouczek aplikacji sieci Web — zapisywanie aplikacji sieci Web
description: Ten samouczek przeprowadzi Cię przez przykład wdrożenia prostej aplikacji sieci Web. W tej części samouczka pokazano, jak napisać aplikację sieci Web.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87848030"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Napisz aplikację sieci Web platformy Azure w celu odczytu danych FHIR
Teraz, gdy masz możliwość nawiązywania połączenia z serwerem FHIR i publikowania danych, możesz napisać aplikację sieci Web, która będzie odczytywać dane FHIR. W tym ostatnim kroku samouczka przeprowadzimy przez proces pisania i uzyskiwania dostępu do aplikacji sieci Web.

## <a name="create-web-application"></a>Tworzenie aplikacji sieci Web
Na platformie Azure wybierz pozycję **Utwórz zasób** i wybierz pozycję **aplikacja internetowa**. Upewnij się, że nazwa aplikacji sieci Web została określona w identyfikatorze URI przekierowania dla aplikacji klienckiej, lub wróć i zaktualizuj identyfikator URI przekierowania o nową nazwę. 

![Tworzenie aplikacji sieci Web](media/tutorial-web-app/create-web-app.png)

Po udostępnieniu aplikacji sieci Web **Przejdź do pozycji zasób**. Wybierz pozycję **Edytor usługi App Service (wersja zapoznawcza)** w obszarze Narzędzia programistyczne po prawej stronie, a następnie wybierz pozycję **Przejdź**. Wybranie opcji przejdź spowoduje otwarcie Edytor usługi App Service. Kliknij prawym przyciskiem myszy szare miejsce w obszarze *Eksploruj* i Utwórz nowy plik o nazwie **index.html**.

Poniżej znajduje się kod, do którego można wprowadzać dane w **index.html**. Należy zaktualizować następujące elementy:
* **clientId** — aktualizowanie przy użyciu identyfikatora aplikacji klienckiej. Ten identyfikator będzie miał ten sam identyfikator, który został pobrany podczas pobierania tokenu
* **Urząd** — aktualizowanie przy użyciu identyfikatora dzierżawy usługi Azure AD
* **FHIRendpoint** — zaktualizuj FHIRendpoint, aby zawierała nazwę usługi FHIR
* **zakresy** — Aktualizuj, aby odzwierciedlały pełny adres URL dla odbiorców

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

W tym miejscu możesz wrócić do zasobu aplikacji sieci Web i otworzyć adres URL znajdujący się na stronie Przegląd. Zaloguj się, aby zobaczyć, że Tiberious Kirka, który został wcześniej utworzony.

## <a name="next-steps"></a>Następne kroki
Pomyślnie wdrożono interfejs API platformy Azure dla usługi FHIR, zarejestrowano publiczną aplikację kliencką, przetestowano dostęp i utworzono małą aplikację sieci Web. W następnym kroku zapoznaj się z interfejsem API platformy Azure pod kątem FHIR obsługiwanych funkcji.

>[!div class="nextstepaction"]
>[Obsługiwane funkcje](fhir-features-supported.md)





