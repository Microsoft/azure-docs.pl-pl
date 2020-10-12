---
title: Interfejsy API usługi pomiaru — Microsoft Commercial Marketplace
description: Interfejs API zdarzeń użycia umożliwia emitowanie zdarzeń użycia dla ofert SaaS w Microsoft AppSource i witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ac48973653e89d43521979a5606a8a3a3c2e1346
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319987"
---
# <a name="marketplace-metered-billing-apis"></a>Interfejsy API rozliczeń mierzonych w witrynie Marketplace

Należy używać mierzonych interfejsów API rozliczeń, gdy wydawca tworzy niestandardowe wymiary pomiaru dla oferty do opublikowania w centrum partnerskim. Integracja z mierzonymi interfejsami API rozliczeń jest wymagana dla każdej zakupionej oferty, która ma co najmniej jeden plan z wymiarami niestandardowymi do emisji zdarzeń użycia.

Aby uzyskać więcej informacji na temat tworzenia niestandardowych wymiarów pomiarowych dla SaaS, zobacz [SaaS naliczanie opłat](saas-metered-billing.md).

Aby uzyskać więcej informacji na temat tworzenia niestandardowych wymiarów pomiaru dla oferty aplikacji platformy Azure z zarządzanym planem aplikacji, zobacz [sekcję konfiguracja techniczna w temacie Tworzenie nowej oferty aplikacji platformy Azure](create-new-azure-apps-offer.md#technical-configuration)).

## <a name="enforcing-tls-12-note"></a>Wymuszanie uwagi dotyczącej protokołu TLS 1,2

Wersja 1,2 protokołu TLS jest wymuszana jako minimalna wersja komunikacji HTTPS. Upewnij się, że używasz tej wersji protokołu TLS w kodzie. Protokoły TLS w wersji 1,0 i 1,1 są przestarzałe, a próby połączenia będą odrzucane.

## <a name="metered-billing-single-usage-event"></a>Pojedyncze zdarzenie użycia rozliczeń taryfowych

Interfejs API zdarzeń użycia powinien zostać wywołany przez wydawcę, aby emitować zdarzenia użycia względem aktywnego zasobu (subskrybowane) dla planu zakupionego przez określonego klienta. Zdarzenie użycia jest emitowane osobno dla każdego niestandardowego wymiaru planu zdefiniowanego przez wydawcę podczas publikowania oferty.

Dla każdej godziny dnia kalendarzowego może być emitowane tylko jedno zdarzenie użycia. Na przykład na 8:5:15 dzisiaj można emitować jedno zdarzenie użycia. Jeśli to zdarzenie zostanie zaakceptowane, następne zdarzenie użycia zostanie zaakceptowane z 9:00 dzisiaj. Jeśli wyślesz dodatkowe zdarzenie z zakresu od 8:15 do 8:59:59 dzisiaj, zostanie ono odrzucone jako duplikat. Należy zbierać wszystkie jednostki zużyte w ciągu godziny, a następnie emitować je w jednym zdarzeniu.

Dla każdej godziny dnia kalendarzowego dla każdego zasobu może być emitowane tylko jedno zdarzenie użycia. Jeśli więcej niż jedna jednostka jest zużywana w ciągu godziny, należy zbierać wszystkie jednostki używane w ciągu godziny, a następnie emitować je w pojedynczym zdarzeniu. Zdarzenia użycia mogą być emitowane tylko przez ostatnie 24 godziny. Jeśli wyemitujesz zdarzenie użycia w dowolnym momencie od 8:00 do 8:59:59 (i zostanie ono zaakceptowane) i wyślesz dodatkowe zdarzenie dla tego samego dnia od 8:00 do 8:59:59, zostanie ono odrzucone jako duplikat.

**Wpis**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametry zapytania:*

| Parametru | Zalecenie          |
| ---------- | ---------------------- |
| `ApiVersion` | Użyj 2018-08-31. |
| | |

*Nagłówki żądania:*

| Typ zawartości       | Korzystanie z polecenia `application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`   | Unikatowy token dostępu identyfikujący niezależnego dostawcy oprogramowania, który wywołuje to wywołanie interfejsu API. Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem dla <br> <ul> <li> SaaS w polu [Pobierz token z wpisem http](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Zarządzana aplikacja w [strategiach uwierzytelniania](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |

*Przykład treści żądania:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` ma inne znaczenie dla aplikacji SaaS i dla aplikacji zarządzanej, która emituje licznik niestandardowy. 

W przypadku planów aplikacji zarządzanych przez aplikacje platformy Azure `resourceId` jest to `resourceUsageId` znaleziono w obszarze `billingDetails` obiektu metadanych aplikacji zarządzanej. Przykładowy skrypt do pobrania można znaleźć w temacie [using the Azure-Managed tożsamośćs token](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

W przypadku ofert SaaS `resourceId` jest to identyfikator subskrypcji SaaS. Aby uzyskać więcej informacji o subskrypcjach SaaS, zobacz [list subscriptions](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Odpowiedzi

Kod: 200<br>
OK. Emisja użycia została zaakceptowana i zapisana na stronie firmy Microsoft w celu dalszej obróbki i rozliczenia.

Przykład ładunku odpowiedzi: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Kod: 400 <br>
Nieprawidłowe żądanie.

* Podano brakujące lub nieprawidłowe dane żądania.
* `effectiveStartTime` w przeszłości przekracza 24 godziny. Zdarzenie wygasło.
* Subskrypcja SaaS nie jest w stanie subskrybowanym.

Przykład ładunku odpowiedzi: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kod: 403<br>

Zabrania. Nie podano tokenu autoryzacji, jest on nieprawidłowy lub wygasł.  Lub żądanie próbuje uzyskać dostęp do subskrypcji oferty, która została opublikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD z klucza użytego do utworzenia tokenu autoryzacji.

Kod: 409<br>
Kolizj. Zdarzenie użycia zostało już pomyślnie zgłoszone dla określonego identyfikatora zasobu, daty i godziny efektywnego użycia.

Przykład ładunku odpowiedzi: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Zdarzenie użycia partii mierzonych rozliczeń

Interfejs API zdarzeń użycia usługi Batch umożliwia emitowanie zdarzeń użycia dla więcej niż jednego zakupionego zasobu jednocześnie. Umożliwia również emitowanie kilku zdarzeń użycia dla tego samego zasobu, o ile są one przeznaczone dla różnych godzin kalendarzowych. Maksymalna liczba zdarzeń w pojedynczej partii to 25.

**Wpis:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametry zapytania:*

| Parametr  | Zalecenie     |
| ---------- | -------------------- |
| `ApiVersion` | Użyj 2018-08-31. |

*Nagłówki żądania:*

| Typ zawartości       | Korzystanie z polecenia `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie określona, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`      | Unikatowy token dostępu identyfikujący niezależnego dostawcy oprogramowania, który wywołuje to wywołanie interfejsu API. Format to, `Bearer <access_token>` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem dla <br> <ul> <li> SaaS w polu [Pobierz token z wpisem http](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Zarządzana aplikacja w [strategiach uwierzytelniania](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |


*Przykład treści żądania:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` ma inne znaczenie dla aplikacji SaaS i dla aplikacji zarządzanej, która emituje licznik niestandardowy. 

W przypadku planów aplikacji zarządzanych przez aplikacje platformy Azure `resourceId` jest to `resourceUsageId` znaleziono w obszarze `billingDetails` obiektu metadanych aplikacji zarządzanej. Przykładowy skrypt do pobrania można znaleźć w temacie [using the Azure-Managed tożsamośćs token](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

W przypadku ofert SaaS `resourceId` jest to identyfikator subskrypcji SaaS. Aby uzyskać więcej informacji o subskrypcjach SaaS, zobacz [list subscriptions](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Odpowiedzi

Kod: 200<br>
OK. Emisja użycia wsadowego została zaakceptowana i zapisana na stronie firmy Microsoft w celu dalszej obróbki i rozliczenia. Lista odpowiedzi jest zwracana ze stanem poszczególnych zdarzeń w partii. Należy wykonać iterację w ładunku odpowiedzi, aby zrozumieć odpowiedzi dla każdego indywidualnego zdarzenia użycia wysyłanego jako część zdarzenia usługi Batch.

Przykład ładunku odpowiedzi: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Opis kodu stanu przywoływany w `BatchUsageEvent` odpowiedzi interfejsu API:

| Kod stanu  | Opis |
| ---------- | -------------------- |
| `Accepted` | Przyjmować. |
| `Expired` | Wygasłe użycie. |
| `Duplicate` | Podano zduplikowane użycie. |
| `Error` | Kod błędu. |
| `ResourceNotFound` | Podany zasób użycia jest nieprawidłowy. |
| `ResourceNotAuthorized` | Nie masz uprawnień do zapewnienia użycia dla tego zasobu. |
| `InvalidDimension` | Wymiar, dla którego jest przesyłane użycie, jest nieprawidłowy dla tej oferty/planu. |
| `InvalidQuantity` | Przenoszona ilość jest mniejsza lub równa 0. |
| `BadArgument` | Brak danych wejściowych lub jest nieprawidłowo sformułowany. |

Kod: 400<br>
Nieprawidłowe żądanie. Partia zawiera więcej niż 25 zdarzeń użycia.

Kod: 403<br>
Zabrania. Nie podano tokenu autoryzacji, jest on nieprawidłowy lub wygasł.  Lub żądanie próbuje uzyskać dostęp do subskrypcji oferty, która została opublikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD z klucza użytego do utworzenia tokenu autoryzacji.

## <a name="development-and-testing-best-practices"></a>Najlepsze rozwiązania związane z programowaniem i testowaniem

Aby przetestować niestandardową emisję licznika, należy wdrożyć integrację z interfejsem API zliczania, utworzyć plan dla opublikowanej oferty SaaS z niestandardowymi wymiarami zdefiniowanymi w nim z zerową ceną za jednostkę. I Opublikuj tę ofertę jako wersję zapoznawczą, aby tylko ograniczonych użytkowników mogła uzyskać dostęp do integracji i przetestować ją.

Możesz również użyć planu prywatnego dla istniejącej oferty na żywo, aby ograniczyć dostęp do tego planu podczas testowania do ograniczonej grupy odbiorców.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Postępuj zgodnie z instrukcjami w temacie [obsługa komercyjnego programu Marketplace w centrum partnerskim](./support.md) , aby poznać opcje pomocy technicznej wydawcy i otworzyć bilet pomocy technicznej w firmie Microsoft.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsów API usługi pomiarowej, zobacz temat [często zadawane pytania dotyczące interfejsów API usługi pomiaru Marketplace](./marketplace-metering-service-apis-faq.md).
