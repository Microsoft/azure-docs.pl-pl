---
title: Jak zabezpieczyć aplikację sieci Web za pomocą interakcyjnego logowania jednokrotnego
titleSuffix: Azure Maps
description: Jak skonfigurować aplikację sieci Web, która obsługuje logowanie jednokrotne w usłudze Azure AD za pomocą zestawu Azure Maps Web SDK przy użyciu protokołu Connect OpenID Connect.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895584"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Zabezpieczanie aplikacji sieci Web przy użyciu logowania użytkownika

Poniższy przewodnik dotyczy aplikacji hostowanej na serwerach sieci Web, obsługi wielu scenariuszy roboczych i wdrażania na serwerach sieci Web. Aplikacja wymaga zapewnienia, że chronione zasoby są zabezpieczone tylko dla użytkowników usługi Azure AD. Celem tego scenariusza jest umożliwienie aplikacji sieci Web uwierzytelnienia w usłudze Azure AD i Wywołaj Azure Maps interfejsów API REST w imieniu użytkownika.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Tworzenie rejestracji aplikacji w usłudze Azure AD

Aby użytkownicy mogli się zalogować, musisz utworzyć aplikację sieci Web w usłudze Azure AD. Ta aplikacja sieci Web będzie następnie delegować dostęp użytkowników do Azure Maps interfejsów API REST.

1. W Azure Portal na liście usług platformy Azure wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.  

    > [!div class="mx-imgBorder"]
    > ![Rejestrowanie aplikacji](./media/how-to-manage-authentication/app-registration.png)

2. Wprowadź **nazwę**, wybierz **Typ konta pomocy technicznej**, podaj identyfikator URI przekierowania, który będzie reprezentować adres URL, który będzie WYSTAWIAŁ token usługi Azure AD, a także adres URL, pod którym jest hostowany formant mapy. Aby uzyskać więcej informacji, zobacz scenariusz usługi Azure AD [: aplikacja sieci Web, która umożliwia użytkownikom logowanie](../active-directory/develop/scenario-web-app-sign-user-overview.md)się. Wykonaj podane kroki w scenariuszu usługi Azure AD.  

3. Po zakończeniu rejestracji aplikacji upewnij się, że zalogowanie aplikacji jest obsługiwane dla użytkowników. Po zalogowaniu aplikacja może otrzymać delegowany dostęp do Azure Maps interfejsów API REST.
    
4.  Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji. Następnie wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację**, Wyszukaj i wybierz **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![Dodawanie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

5. Zaznacz pole wyboru obok pozycji **dostęp Azure Maps**, a następnie wybierz pozycję **Dodaj uprawnienia**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

6. Umożliwienie aplikacji sieci Web wywoływania Azure Maps interfejsów API REST przez skonfigurowanie rejestracji aplikacji przy użyciu klucza tajnego aplikacji. Aby uzyskać szczegółowe instrukcje, zobacz [aplikację sieci Web, która wywołuje interfejsy API sieci Web: Rejestracja aplikacji](../active-directory/develop/scenario-web-app-call-api-app-registration.md). Wpis tajny jest wymagany do uwierzytelnienia w usłudze Azure AD w imieniu użytkownika. Certyfikat rejestracji aplikacji lub wpis tajny powinien być przechowywany w bezpiecznym magazynie dla aplikacji sieci Web do pobrania w celu uwierzytelnienia w usłudze Azure AD. 
   
   * Jeśli aplikacja ma już skonfigurowaną rejestrację aplikacji usługi Azure AD i wpis tajny, ten krok może zostać pominięty.

> [!Tip]
> Jeśli aplikacja jest hostowana w środowisku platformy Azure, zalecamy użycie [tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) i wystąpienia Azure Key Vault w celu uzyskania dostępu do kluczy tajnych przez [uzyskanie tokenu dostępu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) w celu uzyskania dostępu do Azure Key Vault Secret lub certyfikatów. Aby nawiązać połączenie z usługą Azure Key Vault w celu pobrania wpisów tajnych, zobacz [Samouczek dotyczący nawiązywania połączenia przez zarządzaną tożsamość](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Zaimplementuj bezpieczny punkt końcowy tokenu dla Azure Maps Web SDK, aby uzyskać dostęp do tokenu. 
   
   * Aby zapoznać się z przykładowym kontrolerem tokenów, zobacz [Azure Maps Azure AD Samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * Aby uzyskać implementację inną niż AspNetCore lub inną, zobacz [pozyskiwanie tokenu dla aplikacji](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) z dokumentacji usługi Azure AD.
   * Bezpieczny punkt końcowy tokenu jest odpowiedzialny za zwracanie tokenu dostępu uwierzytelnionego i autoryzowanego użytkownika do wywoływania Azure Maps interfejsów API REST.

8. Skonfiguruj kontrolę dostępu opartą na rolach (Azure RBAC) dla użytkowników lub grup. Zobacz [udzielanie użytkownikom dostępu opartego na rolach](#grant-role-based-access-for-users-to-azure-maps).

9. Skonfiguruj stronę aplikacji sieci Web za pomocą zestawu SDK sieci Web Azure Maps, aby uzyskać dostęp do punktu końcowego bezpiecznego tokenu. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Następne kroki

Dalsze informacje na temat scenariusza aplikacji sieci Web:
> [!div class="nextstepaction"]
> [Scenariusz: aplikacja sieci Web, która loguje użytkowników](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Znajdź metryki użycia interfejsu API dla konta usługi Azure Maps:
> [!div class="nextstepaction"]
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)

Zapoznaj się z przykładami, które pokazują, jak zintegrować usługę Azure AD z Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps przykładów aplikacji sieci Web usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)