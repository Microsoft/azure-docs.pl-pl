---
title: Przeglądanie użycia zasobów usług platformy Azure przy użyciu interfejsu API REST
description: Dowiedz się, jak używać interfejsów API REST platformy Azure do przeglądania użycia zasobów usług platformy Azure. Utwórz raport zarządzania kosztami i filtruj pod katem określonych typów zasobów.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 018e61fbc74091fa25211b8a25ce067862a0cc7d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132401"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Przeglądanie użycia zasobów platformy Azure przy użyciu interfejsu API REST

Interfejsy API usługi Azure Cost Management ułatwiają przeglądanie użycia zasobów platformy Azure i zarządzanie nim.

Z tego artykułu dowiesz się, jak utworzyć dzienny raport, który wygeneruje dokument z wartościami rozdzielanymi przecinkami zawierający informacje o użyciu godzinowym. Dowiesz się również, jak używać filtrów do dostosowywania raportu, aby można było wykonywać zapytania dotyczące użycia maszyn wirtualnych, baz danych i otagowanych zasobów w grupie zasobów platformy Azure.

>[!NOTE]
> Interfejs API usługi Cost Management jest obecnie dostępny w prywatnej wersji zapoznawczej.

## <a name="create-a-basic-cost-management-report"></a>Tworzenie podstawowego raportu zarządzania kosztami

Użyj operacji `reports` w interfejsie API usługi Cost Management, aby określić sposób generowania raportowania kosztów i miejsce, w którym raporty będą publikowane.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{subscriptionGuid}` jest wymagany i powinien zawierać identyfikator subskrypcji, który można odczytać przy użyciu poświadczeń podanych w tokenie interfejsu API. 

Parametr `{reportName}` określa nazwę raportu. Aby uzyskać listę nazw raportów, można użyć operacji Reports_List: `/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`. Wyświetl przykładowe dane wyjściowe w [witrynie GitHub](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json).

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*| Wymagany. Ustaw wartość `application/json`. |  
|*Authorization:*| Wymagany. Ustaw prawidłowy token `Bearer`. |

Skonfiguruj parametry raportu w treści żądania HTTP. W poniższym przykładzie raport jest ustawiany tak, aby był generowany w każdym dniu aktywności. Jest to plik CSV zapisany w kontenerze obiektów blob usługi Azure Storage i zawiera godzinowe informacje o kosztach dla wszystkich zasobów w grupie zasobów `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Dla zasobu

## <a name="filtering-reports"></a>Filtrowanie raportów

Sekcja `filter` i `dimensions` treści żądania podczas tworzenia raportu umożliwia skoncentrowanie się na kosztach dla określonych typów zasobów. W treści poprzedniego żądania pokazano, jak filtrować według wszystkich zasobów w regionie.

### <a name="get-all-compute-usage"></a>Pobieranie całego użycia obliczeniowego

Użyj wymiaru `ResourceType` do raportowania kosztów maszyn wirtualnych platformy Azure w ramach subskrypcji we wszystkich regionach.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines",
                "Microsoft.Compute/virtualMachines"
        ]
    }
}
```

### <a name="get-all-database-usage"></a>Pobieranie użycia wszystkich baz danych

Użyj wymiaru `ResourceType` do raportowania kosztów maszyn wirtualnych bazy danych Azure SQL Database w ramach subskrypcji we wszystkich regionach.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ]
    }
}
```

### <a name="report-on-specific-instances"></a>Raportowanie dotyczące konkretnych wystąpień

Wymiar `Resource` umożliwia raportowanie kosztów określonych zasobów.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Zmienianie przedziałów czasu

Ustaw definicję `timeframe` na `Custom`, aby ustawić przedział czasu inny niż wbudowane opcje „Od początku tygodnia” i „Od początku miesiąca”.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Następne kroki
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)