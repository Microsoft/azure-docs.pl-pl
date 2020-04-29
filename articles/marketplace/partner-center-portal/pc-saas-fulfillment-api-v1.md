---
title: Interfejsy API realizacji SaaS v1 | Portal Azure Marketplace
description: Wyjaśnia, jak utworzyć i zarządzać ofertą SaaS w portalu Azure Marketplace przy użyciu skojarzonych interfejsów API realizacji w wersji 1.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288346"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>Interfejsy API realizacji SaaS w wersji 1 (przestarzałe)

W tym artykule opisano sposób tworzenia oferty SaaS z interfejsami API. Interfejsy API, składające się z metod REST i punktów końcowych, są niezbędne, aby umożliwić korzystanie z subskrypcji oferty SaaS w przypadku wybrania sprzedaży za pomocą platformy Azure.  

> [!WARNING]
> Ta początkowa wersja interfejsu API realizacji SaaS jest przestarzała; Zamiast tego należy użyć [interfejsu API realizacji SaaS w wersji 2](./pc-saas-fulfillment-api-v2.md).  Ta początkowa wersja interfejsu API jest obecnie utrzymywana tylko do obsługi istniejących wydawców. 

Dostępne są następujące interfejsy API, które ułatwiają integrację usługi SaaS z platformą Azure:

-   Rozwiąż
-   Subskrybuj
-   Convert
-   Anuluj subskrypcję


## <a name="api-methods-and-endpoints"></a>Metody i punkty końcowe interfejsu API

W poniższych sekcjach opisano metody interfejsu API i punkty końcowe dostępne do włączenia subskrypcji oferty SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Punkt końcowy interfejsu API portalu Marketplace i wersja interfejsu API

Punkt końcowy interfejsu API portalu Azure Marketplace `https://marketplaceapi.microsoft.com`to.

Bieżąca wersja interfejsu API to `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Rozwiąż subskrypcję

Akcja po operacji po rozwiązaniu punktu końcowego umożliwia użytkownikom rozpoznawanie tokenów portalu Marketplace w ramach trwałego identyfikatora zasobu.  Identyfikator zasobu jest unikatowym identyfikatorem dla subskrypcji SAAS. 

Gdy użytkownik zostanie przekierowany do witryny sieci Web niezależnego dostawcy oprogramowania, adres URL zawiera token w parametrach zapytania. Dostawca niezależnego dostawcy oprogramowania powinien używać tego tokenu i żądać go do rozwiązania. Odpowiedź zawiera unikatowy identyfikator subskrypcji SAAS, nazwę, identyfikator oferty oraz plan dla zasobu. Ten token jest ważny tylko przez godzinę.

*Request*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nazwa parametru** |     **Opis**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  Wersja operacji do użycia dla tego żądania.   |
|  |  |


*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-identyfikator żądania     | Nie           | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
| x-MS-identyfikator korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. To pole służy do skorelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| Typ zawartości       | Tak          | `application/json`                                        |
| autoryzacja      | Tak          | Token okaziciela sieci Web JSON (JWT).                    |
| x-MS-Marketplace-token| Tak| Parametr zapytania tokenu w adresie URL, po przekierowaniu użytkownika do witryny sieci Web niezależnego dostawcy oprogramowania SaaS z platformy Azure. **Uwaga:** Ten token jest prawidłowy tylko przez 1 godzinę. Ponadto należy zdekodować wartość tokenu z przeglądarki przed jej użyciem.|
|  |  |  |
  

*Treść odpowiedzi*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Nazwa parametru** | **Typ danych** | **Opis**                       |
|--------------------|---------------|---------------------------------------|
| id                 | String        | Identyfikator subskrypcji SaaS.          |
| subscriptionName| String| Nazwa subskrypcji SaaS ustawiona przez użytkownika na platformie Azure podczas subskrybowania usługi SaaS.|
| OfferId            | String        | Identyfikator oferty, do której użytkownik subskrybuje. |
| Identyfikator planu             | String        | Identyfikator planu, do którego użytkownik subskrybuje.  |
|  |  |  |


*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token został pomyślnie rozwiązany.                                                            |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. Nie można rozpoznać tokenu, ponieważ token jest źle sformułowany lub wygasł (token jest prawidłowy tylko po 1 godzinie). |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma autoryzacji do wykonania tej operacji.                                 |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, spróbuj ponownie później.                                |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączona, spróbuj ponownie później.                                        |
|  |  |  |


*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-MS-identyfikator korelacji | Tak          | Identyfikator korelacji, jeśli został przesłany przez klienta. w przeciwnym razie ta wartość jest IDENTYFIKATORem korelacji serwera.                   |
| x-MS-ActivityId    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Ten identyfikator jest używany do dowolnych uzgodnień. |
| Ponów próbę po        | Nie           | Ta wartość jest ustawiana tylko dla odpowiedzi 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Subskrybuj

Punkt końcowy subskrybowania umożliwia użytkownikom uruchamianie subskrypcji usługi SaaS dla danego planu i włączanie rozliczeń w systemie handlowym.

**Ubrani**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{Identyfikator subskrypcji}*? API-Version = 2017 — 04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozwiązaniu tokenu za pośrednictwem interfejsu API rozpoznawania.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

|  **Klucz nagłówka**        | **Wymagane** |  **Opis**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-MS-identyfikator żądania         |   Nie         | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie określona, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| x-MS-identyfikator korelacji     |   Nie         | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do skorelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie określona, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| If-Match/If-None-Match |   Nie         |   Silna wartość ETag modułu sprawdzania poprawności.                                                          |
| Typ zawartości           |   Tak        |    `application/json`                                                                   |
|  autoryzacja         |   Tak        |    Token okaziciela sieci Web JSON (JWT).                                               |
| x-MS-Marketplace-Tryb sesji| Nie | Flaga włączenia trybu przebiegu suchego podczas subskrybowania oferty SaaS. Jeśli ta wartość jest ustawiona, subskrypcja nie zostanie obciążona. Jest to przydatne w scenariuszach testowania niezależnych dostawców oprogramowania. Ustaw ją na **"dryrun"**|
|  |  |  |

*Treść*

``` json
{
    "lanId": "",
}
```

| **Nazwa elementu** | **Typ danych** | **Opis**                      |
|------------------|---------------|--------------------------------------|
| Identyfikator planu           | Potrzeb Parametry        | Identyfikator planu użytkownika usługi SaaS jest subskrybowany.  |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Odebrano aktywację subskrypcji SaaS dla danego planu.                   |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub treść kodu JSON jest nieprawidłowo sformułowana. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma autoryzacji do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji o podanym IDENTYFIKATORze                                  |
| 409                  | `Conflict`           | Inna operacja jest w toku dla subskrypcji.                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, spróbuj ponownie później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączona, spróbuj ponownie później.                          |
|  |  |  |

W przypadku odpowiedzi 202 postępuj zgodnie z informacjami o stanie operacji żądania w nagłówku "Operation-Location". Uwierzytelnianie jest takie samo jak w przypadku innych interfejsów API portalu Marketplace.

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-MS-identyfikator korelacji | Tak          | Identyfikator korelacji, jeśli został przesłany przez klienta. w przeciwnym razie ta wartość jest IDENTYFIKATORem korelacji serwera.                   |
| x-MS-ActivityId    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Ta wartość jest używana w przypadku wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, za pomocą którego klient może sprawdzić stan.                                                       |
| Lokalizacja operacji | Tak          | Połącz z zasobem, aby uzyskać stan operacji.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Zmień punkt końcowy planu

Punkt końcowy zmiany umożliwia użytkownikowi konwertowanie aktualnie subskrybowanego planu na nowy plan.

**WYSŁANA**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{Identyfikator subskrypcji}*? API-Version = 2017 — 04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Identyfikator subskrypcji SaaS.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**          | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania          | Nie           | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta. Zaleca się użycie identyfikatora GUID. Jeśli ta wartość nie zostanie określona, zostanie wygenerowana i podana w nagłówkach odpowiedzi.   |
| x-MS-identyfikator korelacji      | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do skorelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie określona, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| If-Match/If-None-Match | Nie           | Silna wartość ETag modułu sprawdzania poprawności.                              |
| Typ zawartości            | Tak          | `application/json`                                        |
| autoryzacja           | Tak          | Token okaziciela sieci Web JSON (JWT).                    |
|  |  |  |

*Treść*

```json
{
    "planId": ""
}
```

|  **Nazwa elementu** |  **Typ danych**  | **Opis**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  Identyfikator planu           |  Potrzeb Parametry         | Identyfikator planu użytkownika usługi SaaS jest subskrybowany.          |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Odebrano aktywację subskrypcji SaaS dla danego planu.                   |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub treść kodu JSON jest nieprawidłowo sformułowana. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma autoryzacji do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji o podanym IDENTYFIKATORze                                  |
| 409                  | `Conflict`           | Inna operacja jest w toku dla subskrypcji.                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, spróbuj ponownie później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączona, spróbuj ponownie później.                          |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-MS-identyfikator korelacji | Tak          | Identyfikator korelacji, jeśli został przesłany przez klienta. w przeciwnym razie ta wartość jest IDENTYFIKATORem korelacji serwera.                   |
| x-MS-ActivityId    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Ta wartość jest używana w przypadku wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, za pomocą którego klient może sprawdzić stan.                                                       |
| Lokalizacja operacji | Tak          | Połącz z zasobem, aby uzyskać stan operacji.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Usuwanie subskrypcji

Akcja Usuń w punkcie końcowym subskrybowania umożliwia użytkownikowi usunięcie subskrypcji o danym IDENTYFIKATORze.

*Request*

**USUNIĘTY**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{Identyfikator subskrypcji}*? API-Version = 2017 — 04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Identyfikator subskrypcji SaaS.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-MS-identyfikator żądania     | Nie           | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta. Zaleca się użycie identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.                                                           |
| x-MS-identyfikator korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do skorelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie określona, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| autoryzacja      | Tak          | Token okaziciela sieci Web JSON (JWT).                    |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Odebrano aktywację subskrypcji SaaS dla danego planu.                   |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub treść kodu JSON jest nieprawidłowo sformułowana. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma autoryzacji do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji o podanym IDENTYFIKATORze                                  |
| 429                  | `RequestThrottleId`  | Usługa jest zajętym przetwarzaniem żądań, spróbuj ponownie później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączona. Spróbuj ponownie później.                          |
|  |  |  |

W przypadku odpowiedzi 202 postępuj zgodnie z informacjami o stanie operacji żądania w nagłówku "Operation-Location". Uwierzytelnianie jest takie samo jak w przypadku innych interfejsów API portalu Marketplace.

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-MS-identyfikator korelacji | Tak          | Identyfikator korelacji, jeśli został przesłany przez klienta. w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-MS-ActivityId    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane w przypadku wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, za pomocą którego klient może sprawdzić stan.                                                       |
| Lokalizacja operacji | Tak          | Połącz z zasobem, aby uzyskać stan operacji.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Uzyskiwanie stanu operacji

Ten punkt końcowy umożliwia użytkownikowi śledzenie stanu wyzwalanej asynchronicznej operacji (Subskrybuj/Anuluj subskrypcję/zmiana planu).

*Request*

**Pobierz**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*? API-Version = 2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unikatowy identyfikator dla wywołanej operacji.                |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Nie           | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta. Zaleca się użycie identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.   |
| x-MS-identyfikator korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do skorelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
| autoryzacja      | Tak          | Token okaziciela sieci Web JSON (JWT).                    |
|  |  |  | 

*Treść odpowiedzi*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Nazwa parametru** | **Typ danych** | **Opis**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | String        | Identyfikator operacji.                                                                      |
| status             | Wyliczenie          | Stan operacji, jeden z następujących: `In Progress`, `Succeeded`, lub. `Failed`          |
| resourceLocation   | String        | Link do subskrypcji, która została utworzona lub zmodyfikowana. Pozwala to klientowi na uzyskanie zaktualizowanego stanu operacji post. Ta wartość nie jest ustawiona dla `Unsubscribe` operacji. |
| utworzony            | DateTime      | Godzina utworzenia operacji w formacie UTC.                                                           |
| lastModified       | DateTime      | Ostatnia aktualizacja operacji w formacie UTC.                                                      |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Pomyślnie rozpoznano żądanie Get i treść zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma autoryzacji do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji o podanym IDENTYFIKATORze.                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, spróbuj ponownie później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączona, spróbuj ponownie później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-MS-identyfikator korelacji | Tak          | Identyfikator korelacji, jeśli został przesłany przez klienta. w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-MS-ActivityId    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane w przypadku wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, za pomocą którego klient może sprawdzić stan.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Pobierz subskrypcję

Akcja Pobierz w punkcie końcowym subskrybowania umożliwia użytkownikowi pobranie subskrypcji z danym identyfikatorem zasobu.

*Request*

**Pobierz**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{Identyfikator subskrypcji}*? API-Version = 2017 — 04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Identyfikator subskrypcji SaaS.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Nie           | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.                                                           |
| x-MS-identyfikator korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do skorelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| autoryzacja      | Tak          | Token okaziciela sieci Web JSON (JWT).                                                                    |
|  |  |  |

*Treść odpowiedzi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | Identyfikator zasobu subskrypcji SaaS na platformie Azure.    |
| offerId                | String        | Identyfikator oferty, do której użytkownik subskrybuje.         |
| Identyfikator planu                 | String        | Identyfikator planu, do którego użytkownik subskrybuje.          |
| saasSubscriptionName   | String        | Nazwa subskrypcji SaaS.                |
| saasSubscriptionStatus | Wyliczenie          | Stan operacji.  Jeden z poniższych programów:  <br/> - `Subscribed`: Subskrypcja jest aktywna.  <br/> - `Pending`: Użytkownik utworzył zasób, ale nie jest aktywowany przez niezależnego dostawcę oprogramowania.   <br/> - `Unsubscribed`: Użytkownik anulował subskrypcję.   <br/> - `Suspended`: Użytkownik zawiesił subskrypcję.   <br/> - `Deactivated`: Subskrypcja platformy Azure jest zawieszona.  |
| utworzony                | DateTime      | Wartość sygnatury czasowej tworzenia subskrypcji w formacie UTC. |
| lastModified           | DateTime      | Wartość sygnatury czasowej modyfikacji subskrypcji w formacie UTC. |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Pomyślnie rozpoznano żądanie Get i treść zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma autoryzacji do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji o podanym IDENTYFIKATORze                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, spróbuj ponownie później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączona, spróbuj ponownie później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-MS-identyfikator korelacji | Tak          | Identyfikator korelacji, jeśli został przesłany przez klienta. w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-MS-ActivityId    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane w przypadku wszelkich uzgodnień. |
| Ponów próbę po        | Nie           | Interwał, za pomocą którego klient może sprawdzić stan.                                                       |
| Element ETag               | Tak          | Połącz z zasobem, aby uzyskać stan operacji.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Pobierz subskrypcje

Akcja Pobierz w punkcie końcowym subskrypcji umożliwia użytkownikowi pobranie wszystkich subskrypcji dla wszystkich ofert od niezależnego dostawcy oprogramowania.

*Request*

**Pobierz**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-identyfikator żądania     | Nie           | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta. Zaleca się użycie identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.             |
| x-MS-identyfikator korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do skorelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| autoryzacja      | Tak          | Token okaziciela sieci Web JSON (JWT).                    |
|  |  |  |

*Treść odpowiedzi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | Identyfikator zasobu subskrypcji SaaS na platformie Azure    |
| offerId                | String        | Identyfikator oferty, do której użytkownik subskrybuje         |
| Identyfikator planu                 | String        | Identyfikator planu, do którego użytkownik subskrybuje          |
| saasSubscriptionName   | String        | Nazwa subskrypcji SaaS                |
| saasSubscriptionStatus | Wyliczenie          | Stan operacji.  Jeden z poniższych programów:  <br/> - `Subscribed`: Subskrypcja jest aktywna.  <br/> - `Pending`: Użytkownik utworzył zasób, ale nie jest aktywowany przez niezależnego dostawcę oprogramowania.   <br/> - `Unsubscribed`: Użytkownik anulował subskrypcję.   <br/> - `Suspended`: Użytkownik zawiesił subskrypcję.   <br/> - `Deactivated`: Subskrypcja platformy Azure jest zawieszona.  |
| utworzony                | DateTime      | Wartość sygnatury czasowej tworzenia subskrypcji w formacie UTC |
| lastModified           | DateTime      | Wartość sygnatury czasowej modyfikacji subskrypcji w formacie UTC |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Pomyślnie rozpoznano żądanie Get i treść zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma autoryzacji do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji o podanym IDENTYFIKATORze                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajętym przetwarzaniem żądań, spróbuj ponownie później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączona. Spróbuj ponownie później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-identyfikator żądania     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-MS-identyfikator korelacji | Tak          | Identyfikator korelacji, jeśli został przesłany przez klienta. w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-MS-ActivityId    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane w przypadku wszelkich uzgodnień. |
| Ponów próbę po        | Nie           | Interwał, za pomocą którego klient może sprawdzić stan.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Element webhook SaaS

Element webhook SaaS służy do aktywnego powiadamiania o zmianach w usłudze SaaS. Ten interfejs API jest nieuwierzytelniony i zostanie wywołany przez usługę firmy Microsoft. Usługa SaaS powinna wywołać interfejs API operacji w celu weryfikacji i autoryzacji przed podjęciem działania w powiadomieniu elementu webhook. 

*Treść*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | String       | Unikatowy identyfikator dla wywołanej operacji.                |
| activityId   | String        | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane w przypadku wszelkich uzgodnień.               |
| subscriptionId                     | String        | Identyfikator zasobu subskrypcji SaaS na platformie Azure.    |
| offerId                | String        | Identyfikator oferty, do której użytkownik subskrybuje. Dostępne tylko z akcją "Update".        |
| publisherId                | String        | IDENTYFIKATOR wydawcy oferty SaaS         |
| Identyfikator planu                 | String        | Identyfikator planu, do którego użytkownik subskrybuje. Dostępne tylko z akcją "Update".          |
| action                 | String        | Akcja wyzwalająca to powiadomienie. Możliwe wartości — aktywacja, usuwanie, wstrzymywanie, przywracanie, aktualizowanie          |
| Znacznik czasu                 | String        | Wartość sygnatury czasowej w formacie UTC, gdy to powiadomienie zostało wyzwolone.          |
|  |  |  |


## <a name="next-steps"></a>Następne kroki

Deweloperzy mogą również programistycznie pobierać i manipulowania obciążeniami, ofertami i profilami wydawcy przy użyciu [Portal Cloud partner interfejsów API REST](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
