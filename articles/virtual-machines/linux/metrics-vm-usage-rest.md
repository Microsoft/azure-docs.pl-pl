---
title: Pobieranie danych użycia maszyn wirtualnych platformy Azure przy użyciu interfejsu API REST
description: Użyj interfejsów API REST platformy Azure, aby zbierać metryki wykorzystania dla maszyny wirtualnej.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: a7237bfc82a932b774b4b6ef293c242a84fd75af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591217"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Pobieranie metryk użycia maszyn wirtualnych przy użyciu interfejsu API REST

Ten przykład pokazuje, jak pobrać użycie procesora dla maszyny wirtualnej z systemem Linux przy użyciu [interfejsu API REST platformy Azure](/rest/api/azure/).

Kompletna dokumentacja referencyjna i dodatkowe przykłady dla interfejsu API REST są dostępne w temacie [Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Tworzenie żądania

Użyj następującego żądania GET, aby zebrać [procentową metrykę procesora CPU](../../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) z maszyny wirtualnej

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| subscriptionId | Identyfikator subskrypcji, który identyfikuje subskrypcję platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli). |
| resourceGroupName | Nazwa grupy zasobów platformy Azure skojarzonej z zasobem. Tę wartość można uzyskać za pomocą interfejsu API Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| VMName | Nazwa maszyny wirtualnej platformy Azure. |
| metricnames | Rozdzielana przecinkami lista prawidłowych  [metryk Load Balancer](../../load-balancer/load-balancer-standard-diagnostics.md). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> Ten dokument obejmuje interfejs API-Version `2018-01-01` uwzględniony w powyższym adresie URL.  |
| zakres czasu | Ciąg o następującym formacie `startDateTime_ISO/endDateTime_ISO` , który definiuje zakres czasu zwracanych metryk. Ten opcjonalny parametr ma ustawioną wartość zwracającą wartość dnia w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Żadna treść żądania nie jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany, gdy lista wartości metryk zostanie zwrócona pomyślnie. Pełną listę kodów błędów można znaleźć w [dokumentacji referencyjnej](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
