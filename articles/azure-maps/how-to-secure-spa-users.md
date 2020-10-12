---
title: Jak zabezpieczyć aplikację jednostronicową przy użyciu logowania użytkownika
titleSuffix: Azure Maps
description: Jak skonfigurować aplikację jednostronicową, która obsługuje logowanie jednokrotne w usłudze Azure AD za pomocą zestawu Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 0ba2e23e8121a76ec281b5e411819ee7d450cbe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319694"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Zabezpieczanie jednostronicowej aplikacji z logowaniem użytkownika

Poniższy przewodnik dotyczy aplikacji hostowanej na serwerze zawartości lub z minimalnymi zależnościami serwera sieci Web. Aplikacja zapewnia chronione zasoby zabezpieczone tylko dla użytkowników usługi Azure AD. Celem tego scenariusza jest umożliwienie aplikacji sieci Web uwierzytelnienia w usłudze Azure AD i Wywołaj Azure Maps interfejsów API REST w imieniu użytkownika.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Tworzenie rejestracji aplikacji w usłudze Azure AD

Utwórz aplikację sieci Web w usłudze Azure AD, aby użytkownicy mogli się zalogować. Aplikacja sieci Web deleguje dostęp użytkowników do Azure Maps interfejsów API REST.

1. W Azure Portal na liście usług platformy Azure wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.  

    > [!div class="mx-imgBorder"]
    > ![Rejestrowanie aplikacji](./media/how-to-manage-authentication/app-registration.png)

2. Wprowadź **nazwę**, wybierz **Typ konta pomocy technicznej**, podaj identyfikator URI przekierowania, który będzie reprezentować adres URL, który będzie WYSTAWIAŁ token usługi Azure AD, a także adres URL, pod którym jest hostowany formant mapy. Aby uzyskać szczegółowe przykłady, zobacz [Azure Maps usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Następnie wybierz pozycję **Zarejestruj**.  

3. Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji. Następnie w obszarze **rejestracje aplikacji**wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację**, Wyszukaj i wybierz **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![Dodawanie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

4. Zaznacz pole wyboru obok pozycji **dostęp Azure Maps**, a następnie wybierz pozycję **Dodaj uprawnienia**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

5. Włącz `oauth2AllowImplicitFlow` . Aby ją włączyć, w sekcji **manifestu** rejestracji aplikacji ustaw wartość `oauth2AllowImplicitFlow` na `true` .

6. Skopiuj identyfikator aplikacji usługi Azure AD i identyfikator dzierżawy usługi Azure AD z rejestracji aplikacji do użycia w zestawie SDK sieci Web. Dodaj szczegóły rejestracji aplikacji usługi Azure AD i `x-ms-client-id` z konta usługi Azure map do zestawu SDK sieci Web.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Konfigurowanie kontroli dostępu opartej na rolach na platformie Azure dla użytkowników lub grup. Zapoznaj [się z następującymi sekcjami, aby włączyć funkcję RBAC](#grant-role-based-access-for-users-to-azure-maps).
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Następne kroki

Dalsze informacje na temat scenariusza aplikacji jednostronicowej:
> [!div class="nextstepaction"]
> [Aplikacja jednostronicowa](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Znajdź metryki użycia interfejsu API dla konta usługi Azure Maps:
> [!div class="nextstepaction"]
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)

Zapoznaj się z przykładami, które pokazują, jak zintegrować usługę Azure AD z Azure Maps:
> [!div class="nextstepaction"]
> [Przykłady Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
