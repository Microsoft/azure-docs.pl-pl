---
title: Jak zabezpieczyć aplikację jednostronicową z logowaniem nieinterakcyjnym
titleSuffix: Azure Maps
description: Jak skonfigurować aplikację jednostronicową przy użyciu nieinteraktywnej kontroli dostępu opartej na rolach usługi Azure AD i Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: e49954065f8a4ec03eb54d7333a3fff34bafb143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319660"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Jak zabezpieczyć aplikację jednostronicową z logowaniem nieinterakcyjnym

Poniższy przewodnik dotyczy aplikacji korzystającej z Azure Active Directory (Azure AD) w celu zapewnienia tokenu dostępu Azure Maps aplikacji, gdy użytkownik nie może zalogować się do usługi Azure AD. Ten przepływ wymaga hostowania usługi sieci Web, która musi być zabezpieczona tylko przez jednostronicową aplikację sieci Web. Istnieje wiele implementacji, które mogą wykonywać uwierzytelnianie w usłudze Azure AD. Ten przewodnik wykorzystuje produkt, funkcję platformy Azure, aby uzyskać tokeny dostępu.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Usługi Azure Maps mogą obsługiwać tokeny dostępu od użytkowników logujących się lub interaktywnych. Interaktywne przepływy umożliwiają bardziej ograniczony zakres odwoływania dostępu i zarządzania kluczami tajnymi.

## <a name="create-azure-function"></a>Tworzenie funkcji platformy Azure

Utwórz bezpieczną aplikację usługi sieci Web, która jest odpowiedzialna za uwierzytelnianie w usłudze Azure AD. 

1. Utwórz funkcję w Azure Portal. Aby uzyskać więcej informacji, zobacz [Tworzenie funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function).

2. Skonfiguruj zasady CORS w funkcji platformy Azure, aby były dostępne dla jednostronicowej aplikacji sieci Web. Dzięki temu klienci przeglądarki będą zabezpieczeni z dozwolonymi źródłami aplikacji sieci Web. Zobacz [Dodawanie funkcji CORS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#add-cors-functionality).

3. [Dodaj tożsamość przypisaną do systemu](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) w funkcji platformy Azure, aby umożliwić tworzenie jednostki usługi w celu uwierzytelniania w usłudze Azure AD.  

4. Udziel dostępu opartego na rolach dla tożsamości przypisanej do systemu do konta Azure Maps. Aby uzyskać szczegółowe informacje, zobacz [udzielanie dostępu opartego na rolach](#grant-role-based-access) .

5. Napisz kod dla funkcji platformy Azure, aby uzyskać Azure Maps tokeny dostępu przy użyciu tożsamości przypisanej do systemu z jednym z obsługiwanych mechanizmów lub protokołu REST. Zobacz [Uzyskiwanie tokenów dla zasobów platformy Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

    Przykładowy przykład protokołu REST:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Przykładowa odpowiedź:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Konfigurowanie zabezpieczeń funkcji platformy Azure HttpTrigger

   * [Utwórz klucz dostępu do funkcji](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)
   * [Bezpieczny punkt końcowy HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production) dla funkcji platformy Azure w środowisku produkcyjnym.
   
7. Skonfiguruj aplikację sieci Web Azure Maps Web SDK. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Udzielanie dostępu opartego na rolach

Dostęp do *kontroli dostępu opartej na rolach (Azure RBAC)* jest udzielany przez przypisanie tożsamości przypisanej do systemu do co najmniej jednej definicji roli platformy Azure. Aby wyświetlić definicje ról platformy Azure, które są dostępne dla Azure Maps, przejdź do obszaru **Kontrola dostępu (IAM)**. Wybierz pozycję **role**, a następnie wyszukaj role zaczynające się od *Azure Maps*.

1. Przejdź do swojego **konta Azure Maps**. Wybierz pozycję **Kontrola dostępu (IAM)**  >  **przypisanie roli**.

    > [!div class="mx-imgBorder"]
    > ![Udzielanie dostępu przy użyciu funkcji RBAC platformy Azure](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na karcie **przypisania roli** w obszarze **rola**wybierz wbudowaną definicję roli Azure Maps, na przykład **Azure Maps czytnika danych** lub **Azure Maps współautor danych**. W obszarze **Przypisz dostęp do**wybierz pozycję **aplikacja funkcji**. Wybierz nazwę główną według nazwy. Następnie wybierz przycisk **Zapisz**.

   * Zobacz szczegóły dotyczące [dodawania lub usuwania przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Azure Maps wbudowane definicje ról zapewniają bardzo duże uprawnienia dostępu do wielu Azure Maps interfejsów API REST. Aby ograniczyć dostęp interfejsów API do minimum, zobacz [Tworzenie niestandardowej definicji roli i przypisywanie tożsamości przypisanej do systemu](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) do definicji roli niestandardowej. Spowoduje to włączenie najniższych uprawnień niezbędnych do uzyskania dostępu do Azure Maps przez aplikację.

## <a name="next-steps"></a>Następne kroki

Dalsze informacje na temat scenariusza aplikacji jednostronicowej:
> [!div class="nextstepaction"]
> [Aplikacja jednostronicowa](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Znajdź metryki użycia interfejsu API dla konta usługi Azure Maps:
> [!div class="nextstepaction"]
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)

Zapoznaj się z innymi przykładami, które pokazują, jak zintegrować usługę Azure AD z Azure Maps:
> [!div class="nextstepaction"]
> [Przykłady Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
