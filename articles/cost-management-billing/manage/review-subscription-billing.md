---
title: Przeglądanie danych rozliczeń subskrypcji platformy Azure za pomocą interfejsu API REST
description: Dowiedz się, jak używać interfejsów API REST platformy Azure do przeglądania szczegółów rozliczeń subskrypcji. Możesz dostosować wyniki przy użyciu filtrów.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f23beeb735c83cdf2d42a01ff684510dbd0d9feb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058837"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Przegląd rozliczeń subskrypcji przy użyciu interfejsów API REST

Interfejsy API usługi Azure Reporting ułatwiają przeglądanie kosztów korzystania z platformy Azure i zarządzanie nimi.

Filtry pomagają dostosować wyniki do Twoich potrzeb.

Tutaj dowiesz się, jak używać interfejsu API REST, aby uzyskać szczegóły rozliczeń subskrypcji dla danego zakresu dat.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Tworzenie żądania

Parametr `{subscriptionID}` jest wymagany i identyfikuje subskrypcję docelową.

Parametr `{billingPeriod}` jest wymagany i określa bieżący [okres rozliczeniowy](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

Parametry `${startDate}` i `${endDate}` są wymagane w tym przykładzie, ale opcjonalne dla punktu końcowego. Określają one zakres dat jako ciągi w formacie RRRR-MM-DD (np. `'20180501'` i `'20180615'`).

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|
|--------------------|-----------------|
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|
|*Authorization:*|Wymagany. Ustaw na prawidłowy [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

## <a name="response"></a>Odpowiedź

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów dla Twojego konta.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Każdy element w sekcji **value** reprezentuje szczegóły dotyczące korzystania z usługi:

|Właściwość odpowiedzi|Opis|
|----------------|----------|
|**subscriptionGuid** | Globalnie unikatowy identyfikator subskrypcji. |
|**startDate** | Data rozpoczęcia użycia. |
|**endDate** | Data zakończenia użycia. |
|**useageQuantity** | Wykorzystana ilość. |
|**billableQuantity** | Ilość rzeczywiście rozliczona. |
|**pretaxCost** | Zafakturowany koszt przed naliczeniem należnych podatków. |
|**meterDetails** | Szczegółowe informacje na temat użycia. |
|**nextLink**| Po ustawieniu określa adres URL dla następnej „strony” ze szczegółami. Właściwość pozostaje pusta, gdy jest to ostatnia strona. |

Ten przykład jest skrócony. Zobacz [Szczegóły użycia listy](/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy), aby uzyskać pełny opis każdego pola odpowiedzi.

Inne kody stanu wskazują na błędy. W takich przypadkach obiekt odpowiedzi wyjaśnia, dlaczego żądanie nie powiodło się.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki
- Przejrzyj temat [Przegląd raportowania korporacyjnego](./enterprise-api.md)
- Zapoznaj się z tematem [Interfejs API REST rozliczania korporacyjnego](/rest/api/billing/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)