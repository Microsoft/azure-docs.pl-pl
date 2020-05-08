---
title: Interfejsy API realizacji SaaS w wersji 2 w portalu komercyjnym firmy Microsoft
description: Dowiedz się, jak utworzyć i zarządzać ofertą SaaS na Microsoft AppSource i witrynie Azure Marketplace przy użyciu interfejsów API realizacji w wersji 2.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: 8f452349c0d636bc74a3722e2526623c955093f7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854191"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>Interfejsy API realizacji SaaS w wersji 2 w portalu komercyjnym firmy Microsoft

Ten artykuł zawiera szczegółowe informacje o interfejsach API realizacji, które umożliwiają partnerom sprzedawanie aplikacji SaaS w Microsoft AppSource i witrynie Azure Marketplace. Te interfejsy API są wymagane do transakcyjnych ofert SaaS.

## <a name="managing-the-saas-subscription-life-cycle"></a>Zarządzanie cyklem życia subskrypcji SaaS

Usługa Azure SaaS zarządza całym cyklem życia zakupu subskrypcji SaaS. Używa interfejsów API realizacji jako mechanizmu do kierowania rzeczywistej realizacji, zmian planów i usuwania subskrypcji partnera. Rachunek klienta jest oparty na stanie subskrypcji SaaS obsługiwanej przez firmę Microsoft. Na poniższym diagramie przedstawiono Stany i operacje, które są związane z wprowadzaniem zmian między Stanami.

![Cykl życia subskrypcji SaaS — Stany cyklu](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stany subskrypcji SaaS

W poniższej tabeli wymieniono Stany aprowizacji dla subskrypcji SaaS, w tym opis i diagram sekwencji dla każdego (jeśli dotyczy). 

#### <a name="provisioning"></a>Inicjowanie obsługi

Gdy klient inicjuje zakup, partner otrzymuje te informacje w kodzie autoryzacji na stronie internetowej klienta, która używa parametru adresu URL. Przykładem jest `https://contoso.com/signup?token=..`, natomiast adres URL strony docelowej w centrum partnerskim `https://contoso.com/signup`to. Kod autoryzacji może być zweryfikowany i wymieniany w celu uzyskania szczegółowych informacji o usłudze aprowizacji, wywołując interfejs API rozpoznawania.  Po zakończeniu aprowizacji usługi SaaS usługa wysyła wywołanie aktywacji, aby sygnalizować zakończenie realizacji i rozliczenie klienta. 

Na poniższym diagramie przedstawiono sekwencję wywołań interfejsu API dla scenariusza aprowizacji.  

![Wywołania interfejsu API do aprowizacji usługi SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Aprowizowane

Ten stan jest stałym stanem zainicjowanej usługi.

##### <a name="provisioning-for-update"></a>Inicjowanie obsługi administracyjnej aktualizacji 

Ten stan oznacza, że oczekująca aktualizacja istniejącej usługi. Taka aktualizacja może zostać zainicjowana przez klienta, z witryny Marketplace lub usługi SaaS (tylko w przypadku transakcji bezpośrednio do klienta).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Inicjowanie obsługi administracyjnej aktualizacji (gdy jest inicjowana z portalu Marketplace)

Na poniższym diagramie przedstawiono sekwencję akcji po zainicjowaniu aktualizacji z portalu Marketplace.

![Wywołania interfejsu API po zainicjowaniu aktualizacji z portalu Marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Inicjowanie obsługi administracyjnej aktualizacji (gdy jest inicjowana z usługi SaaS)

Na poniższym diagramie przedstawiono akcje po zainicjowaniu aktualizacji z usługi SaaS. (Wywołanie elementu webhook jest zastępowane przez aktualizację subskrypcji zainicjowanej przez usługę SaaS). 

![Wywołania interfejsu API po zainicjowaniu aktualizacji z usługi SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

Ten stan wskazuje, że płatność klienta nie została odebrana. Zgodnie z zasadami firma Microsoft dostarczy klientowi okres prolongaty przed anulowaniem subskrypcji. Gdy subskrypcja jest w tym stanie: 

- Jako partner możesz wybrać opcję obniżenia lub zablokowania dostępu użytkownika do usługi.
- Subskrypcja musi być w stanie możliwym do odzyskania, która umożliwia przywrócenie pełnej funkcjonalności bez utraty danych lub ustawień. 
- Oczekuje się, że żądanie przywrócenia dla tej subskrypcji jest realizowane za pośrednictwem interfejsów API realizacji lub żądania anulowania aprowizacji po upływie okresu prolongaty. 

#### <a name="unsubscribed"></a>Anulowano subskrypcję 

Subskrypcje docierają do tego stanu w odpowiedzi na jawne żądanie klienta lub niepłaty opłat. Oczekiwanie od partnera polega na tym, że dane klienta są przechowywane na żądanie przez określoną liczbę dni, a następnie usuwane. 


## <a name="api-reference"></a>Dokumentacja interfejsu API

Ta sekcja dotyczy *interfejsu API subskrypcji* SaaS i *interfejsu API operacji*.  Wartość `api-version` parametru dla interfejsów API w wersji 2 to `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definicje parametrów i jednostek

Poniższa tabela zawiera definicje typowych parametrów i jednostek używanych przez interfejsy API realizacji.

|     Jednostka/parametr     |     Definicja                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identyfikator GUID zasobu SaaS.  |
| `name`                   | Przyjazna nazwa podana dla tego zasobu przez klienta. |
| `publisherId`            | Unikatowy identyfikator ciągu dla każdego wydawcy (na przykład: "contoso"). |
| `offerId`                | Unikatowy identyfikator ciągu dla każdej oferty (na przykład: "offer1").  |
| `planId`                 | Unikatowy identyfikator ciągu dla każdego planu/jednostki SKU (na przykład "Silver"). |
| `operationId`            | Identyfikator GUID dla określonej operacji.  |
|  `action`                | Akcja wykonywana `Unsubscribe`na zasobie, `Suspend` `Reinstate`,, lub `ChangePlan`,. `ChangeQuantity` `Transfer` |
|   |   |

Unikatowe identyfikatory globalne ([GUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier)) to 128-bitowe (32-szesnastkowo), które są zazwyczaj generowane automatycznie. 

#### <a name="resolve-a-subscription"></a>Rozwiązywanie subskrypcji 

Rozwiązywanie punktu końcowego umożliwia wydawcy rozpoznanie tokenu witryny Marketplace jako trwałego identyfikatora zasobu. Identyfikator zasobu jest unikatowym identyfikatorem dla subskrypcji SaaS. Gdy użytkownik zostanie przekierowany do witryny sieci Web partnera, adres URL zawiera token w parametrach zapytania. Partner powinien używać tego tokenu i wysłać żądanie rozwiązania problemu. Odpowiedź zawiera unikatowy identyfikator subskrypcji SaaS, nazwę, identyfikator oferty oraz plan dla zasobu. Ten token jest ważny tylko przez jedną godzinę. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Opublikuj<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-MS-identyfikator żądania    |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-MS-identyfikator korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Na przykład: "`Bearer <access_token>`". |
|  x-MS-Marketplace-token  |  Parametr zapytania tokenu w adresie URL, gdy użytkownik jest przekierowywany do witryny sieci Web partnera SaaS z platformy Azure (na `https://contoso.com/signup?token=..`przykład:). *Uwaga:* Adres URL dekoduje wartość tokenu z przeglądarki przed jej użyciem.  |

*Kody odpowiedzi:*

Kod: 200<br>
Rozwiązuje nieprzezroczysty token do subskrypcji SaaS. Treść odpowiedzi:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kod: 400<br>
Nieprawidłowe żądanie. Brak "x-MS-Marketplace", źle sformułowano lub wygasło.

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Interfejs API subskrypcji

Interfejs API subskrypcji obsługuje następujące operacje HTTPS: **Get**, **post**, **patch**i **delete**.


#### <a name="list-subscriptions"></a>Wyświetlanie listy subskrypcji

Wyświetla wszystkie subskrypcje SaaS wydawcy.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry zapytania:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-MS-identyfikator żądania     |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| x-MS-identyfikator korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
| autoryzacja      |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Na przykład: "`Bearer <access_token>`".  |

*Kody odpowiedzi:*

Kod: 200 <br/>
Pobiera wydawcę i odpowiednie subskrypcje dla wszystkich ofert wydawcy na podstawie tokenu uwierzytelniania.

>[!Note]
>[Makiety interfejsów API](#mock-apis) są używane podczas pierwszego opracowywania oferty, podczas gdy rzeczywiste interfejsy API muszą być używane podczas rzeczywistej publikacji oferty.  Rzeczywiste interfejsy API i interfejsy API służące do makiety różnią się w zależności od pierwszego wiersza kodu.  W rzeczywistym interfejsie API znajduje się `subscription` sekcja, podczas gdy ta sekcja nie istnieje dla interfejsu API.

Ładunek odpowiedzi dla interfejsu API makiety:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
I dla rzeczywistego interfejsu API: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Token kontynuacji będzie dostępny tylko wtedy, gdy istnieją dodatkowe "strony" planów do pobrania. 

Kod: 403 <br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy. 

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Pobierz subskrypcję

Pobiera określoną subskrypcję SaaS. To wywołanie służy do uzyskiwania informacji o licencji i planowania informacji.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Unikatowy identyfikator subskrypcji SaaS, która jest uzyskiwana po rozwiązaniu tokenu za pośrednictwem interfejsu API rozpoznawania.   |
|  ApiVersion        |   Wersja operacji do użycia dla tego żądania.   |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-MS-identyfikator żądania    |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-MS-identyfikator korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Na przykład: "`Bearer <access_token>`".  |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera subskrypcję SaaS z identyfikatora. Ładunek odpowiedzi:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.<br> 

Kod: 500<br>
Wewnętrzny błąd serwera.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Wyświetl dostępne plany

Użyj tego wywołania, aby dowiedzieć się, czy istnieją oferty prywatne lub publiczne dla bieżącego wydawcy.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-MS-identyfikator żądania   |   Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-MS-identyfikator korelacji  | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Na przykład: "`Bearer <access_token>`". |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera listę dostępnych planów dla klienta. Treść odpowiedzi:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Kod: 404<br>
Nie znaleziono.<br> 

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy. <br> 

Kod: 500<br>
Wewnętrzny błąd serwera.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktywuj subskrypcję

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Opublikuj<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która jest uzyskiwana po rozwiązaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-MS-identyfikator żądania    | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-MS-identyfikator korelacji  | Unikatowa wartość ciągu dla operacji na kliencie. Ten ciąg skorelowany ze wszystkimi zdarzeniami z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Na przykład: "`Bearer <access_token>`". |

*Ładunek żądania:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Kody odpowiedzi:*

Kod: 200<br>
Aktywuje subskrypcję.<br>

Kod: 400<br>
Złe żądanie: Błędy walidacji.

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Zmiana planu subskrypcji

Zaktualizuj plan w ramach subskrypcji.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która jest uzyskiwana po rozwiązaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-MS-identyfikator żądania    |   Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-MS-identyfikator korelacji  |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.    |
| autoryzacja      |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Na przykład: "`Bearer <access_token>`".  |

*Ładunek żądania:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Lokalizacja operacji | Link do zasobu w celu uzyskania stanu operacji.   |

*Kody odpowiedzi:*

Kod: 202<br>
Żądanie zmiany planu zostało zaakceptowane. Partner oczekuje na sondowanie lokalizacji operacji w celu określenia sukcesu lub niepowodzenia. <br>

Kod: 400<br>
Złe żądanie: Błędy walidacji.

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Jednocześnie można poprawić tylko plan lub ilość, ale nie oba naraz. Edytowanie subskrypcji z **aktualizacją** nie jest w `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Zmiana ilości w subskrypcji

Zaktualizuj ilość w ramach subskrypcji.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Wysłana<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która jest uzyskiwana po rozwiązaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-MS-identyfikator żądania    |   Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-MS-identyfikator korelacji  |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.    |
| autoryzacja      |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Na przykład: "`Bearer <access_token>`".  |

*Ładunek żądania:*

```json
Request Body:
{
    "quantity": 5
}
```

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Lokalizacja operacji | Połącz z zasobem, aby uzyskać stan operacji.   |

*Kody odpowiedzi:*

Kod: 202<br>
Żądanie zmiany ilości zostało zaakceptowane. Partner oczekuje na sondowanie lokalizacji operacji w celu określenia sukcesu lub niepowodzenia. <br>

Kod: 400<br>
Złe żądanie: Błędy walidacji.


Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Jednocześnie można poprawić tylko plan lub ilość, ale nie oba naraz. Edytowanie subskrypcji z **aktualizacją** nie jest w `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Usuwanie subskrypcji

Anuluj subskrypcję i Usuń określoną subskrypcję.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Usuń<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która jest uzyskiwana po rozwiązaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-MS-identyfikator żądania    |   Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.   |
|  x-MS-identyfikator korelacji  |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.   |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Na przykład: "`Bearer <access_token>`".  |

*Kody odpowiedzi:*

Kod: 202<br>
Partner zainicjował wywołanie anulowania subskrypcji SaaS.<br>

Kod: 400<br>
Usuwanie subskrypcji z opcją **Usuń** nie w `allowedCustomerOperations`.

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Interfejs API operacji

Interfejs API operacji obsługuje następujące poprawki i pobiera operacje.

#### <a name="list-outstanding-operations"></a>Utwórz listę zaległych operacji 

Wyświetla listę zaległych operacji dla bieżącego wydawcy. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry zapytania:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Wersja operacji do użycia dla tego żądania.                |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która jest uzyskiwana po rozwiązaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-MS-identyfikator żądania    |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-MS-identyfikator korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Na przykład: "`Bearer <access_token>`".  |

*Kody odpowiedzi:*

Kod: 200<br> Pobiera listę oczekujących operacji w ramach subskrypcji. Ładunek odpowiedzi:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Kod: 400<br>
Złe żądanie: Błędy walidacji.

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Pobierz stan operacji

Umożliwia wydawcy śledzenie stanu określonej wyzwalanej operacji asynchronicznej `Subscribe`(takiej jak `Unsubscribe` `ChangePlan`,, lub `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-MS-identyfikator żądania    |   Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-MS-identyfikator korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Na przykład: "`Bearer <access_token>`".  |

*Kody odpowiedzi:*<br>

Kod: 200<br> Pobiera określoną oczekującą operację SaaS. Ładunek odpowiedzi:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Kod: 400<br>
Złe żądanie: Błędy walidacji.

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.
 
Kod: 404<br>
Nie znaleziono.

Kod: 500<br> Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aktualizowanie stanu operacji

Zaktualizuj stan operacji, aby wskazać powodzenie lub niepowodzenie przy użyciu podanych wartości.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która jest uzyskiwana po rozwiązaniu tokenu przy użyciu interfejsu API rozpoznawania.  |
|  operationId       | Operacja, która jest ukończona. |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-MS-identyfikator żądania   |   Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-MS-identyfikator korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  autoryzacja     |  [Pobierz token okaziciela sieci Web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Na przykład: "`Bearer <access_token>`".  |

*Ładunek żądania:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Kody odpowiedzi:*

Kod: 200<br> Wywołanie do informowania o zakończeniu operacji po stronie partnera. Na przykład ta odpowiedź może sygnalizować zmianę miejsc lub planów.

Kod: 400<br>
Złe żądanie: Błędy walidacji.

Kod: 403<br>
Próby. Nie podano tokenu uwierzytelniania lub jest on nieprawidłowy lub żądanie próbuje uzyskać dostęp do przejęcia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 409<br>
Kolizj. Na przykład istnieje już nowsza transakcja.

Kod: 500<br> Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementowanie elementu webhook w usłudze SaaS

Wydawca musi zaimplementować element webhook w tej usłudze SaaS, aby aktywnie powiadamiać użytkowników o zmianach w swojej usłudze. Usługa SaaS powinna wywołać interfejs API operacji w celu weryfikacji i autoryzacji przed wykonaniem akcji na powiadomieniu elementu webhook.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Gdzie akcja może być jedną z następujących czynności: 
- `Unsubscribe`(po usunięciu zasobu)
- `ChangePlan`(po zakończeniu operacji zmiany planu)
- `ChangeQuantity`(po zakończeniu operacji zmiany ilości)
- `Suspend`(gdy zasób został zawieszony)
- `Reinstate`(gdy zasób został przywrócony po zawieszeniu)

Stan może mieć jedną z następujących wartości: 
- **NotStarted** <br>
 - **Toku** <br>
- **Powiodło się** <br>
- **Niepowodzenie** <br>
- **Kolizj** <br>

W powiadomieniu elementu webhook Stany akcji zostały **wykonane pomyślnie** i **zakończyły się niepowodzeniem**. Cykl życia operacji pochodzi z **NotStarted** do stanu terminalu, takiego jak **sukces**, **Niepowodzenie**lub **konflikt**. Jeśli otrzymasz **NotStarted** lub w **toku**, Kontynuuj Zażądaj stanu za pomocą interfejsu API Get, dopóki operacja nie osiągnie stanu terminalu przed podjęciem działania. 

## <a name="mock-apis"></a>Makiety interfejsów API

Korzystając z naszych interfejsów API, można rozpocząć pracę z programowaniem, szczególnie prototypami, a także testować projekty. 

Punkt końcowy hosta `https://marketplaceapi.microsoft.com/api` : (bez uwierzytelniania wymagane)<br/>
Wersja interfejsu API:`2018-09-15`<br/>
Przykładowy identyfikator URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Ścieżki punktów końcowych interfejsu API są takie same dla makiet i prawdziwych interfejsów API, ale wersje interfejsu API są różne. Wersja jest `2018-09-15` dla wersji makiety i `2018-08-31` wersji produkcyjnej. 

Dowolne wywołania interfejsu API w tym artykule można wykonać w punkcie końcowym hosta. Ogólnie rzecz biorąc, należy oczekiwać, że dane makiety są z powrotem odbierane jako odpowiedź. Wywołania metod aktualizacji subskrypcji w interfejsie API makiety zawsze zwracają 500. 

## <a name="next-steps"></a>Następne kroki

Deweloperzy mogą również programowo pobierać obciążenia, oferty i profile wydawcy oraz manipulować nimi przy użyciu [Portal Cloud partner interfejsów API REST](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
