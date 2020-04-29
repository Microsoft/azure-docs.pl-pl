---
title: Pobieranie zdarzeń usługi Azure Resource Health przy użyciu interfejsu API REST | Microsoft Docs
description: Użyj interfejsów API REST platformy Azure, aby pobrać zdarzenia dotyczące kondycji zasobów platformy Azure.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77654005"
---
# <a name="get-resource-health-using-the-rest-api"></a>Uzyskiwanie Resource Health przy użyciu interfejsu API REST 

W tym przykładowym artykule pokazano, jak pobrać listę zdarzeń dotyczących kondycji zasobów platformy Azure w ramach subskrypcji za pomocą [interfejsu API REST platformy Azure](/rest/api/azure/).

Kompletna dokumentacja referencyjna i dodatkowe przykłady dla interfejsu API REST są dostępne w temacie [Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Tworzenie żądania

Użyj następującego `GET` żądania HTTP, aby wyświetlić listę zdarzeń kondycji dla subskrypcji w zakresie czasu między `2018-05-16` i. `2018-06-20`

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw na prawidłowy  [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, który identyfikuje subskrypcję platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> Ten dokument obejmuje interfejs API- `2015-04-01`Version uwzględniony w powyższym adresie URL.  |
| $filter | Opcja filtrowania w celu zmniejszenia zestawu zwracanych wyników. Dozwolone wzorce dla tego parametru są dostępne [w odniesieniu do operacji dzienników aktywności](/rest/api/monitor/activitylogs/list#uri-parameters). Pokazany przykład przechwytuje wszystkie zdarzenia w przedziale czasu od 2018-05-16 do 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Żadna treść żądania nie jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany z listą wartości zdarzeń dotyczących kondycji odpowiadającej parametrowi filtru wraz z `nextlink` identyfikatorem URI w celu pobrania następnej strony wyników.

## <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
