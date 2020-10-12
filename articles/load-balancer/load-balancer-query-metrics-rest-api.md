---
title: Pobieranie metryk przy użyciu interfejsu API REST
titleSuffix: Azure Load Balancer
description: W tym artykule Zacznij korzystać z interfejsów API REST platformy Azure, aby zbierać metryki dotyczące kondycji i użycia dla Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 3b5aedb20bc7a8d2aa6f3aa3d8691a71af4cd3a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808369"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Uzyskiwanie metryk użycia Load Balancer przy użyciu interfejsu API REST

Zbierz liczbę bajtów przetworzonych przez [Usługa Load Balancer w warstwie Standardowa](/azure/load-balancer/load-balancer-standard-overview) przez przedział czasu przy użyciu [interfejsu API REST platformy Azure](/rest/api/azure/).

Kompletna dokumentacja referencyjna i dodatkowe przykłady dla interfejsu API REST są dostępne w temacie [Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Tworzenie żądania

Użyj następującego żądania GET, aby zebrać [metrykę byteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) z usługa Load Balancer w warstwie Standardowa. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw na prawidłowy [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, który identyfikuje subskrypcję platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Nazwa grupy zasobów zawierającej zasób. Tę wartość można uzyskać za pomocą interfejsu API Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| loadBalancerName | Nazwa Azure Load Balancer. |
| nazwy metryk | Rozdzielana przecinkami lista prawidłowych  [metryk Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> Ten dokument obejmuje interfejs API-Version `2018-01-01` uwzględniony w powyższym adresie URL.  |
| zakres czasu | Przedział czasu zapytania. Jest to ciąg o następującym formacie `startDateTime_ISO/endDateTime_ISO` . Ten opcjonalny parametr ma ustawioną wartość zwracającą wartość dnia w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Żadna treść żądania nie jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany, gdy lista wartości metryk zostanie zwrócona pomyślnie. Pełną listę kodów błędów można znaleźć w [dokumentacji referencyjnej](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
