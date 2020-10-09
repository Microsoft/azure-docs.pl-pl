---
title: Wstrzymywanie, wznawianie i skalowanie przy użyciu interfejsów API REST
description: Zarządzanie mocą obliczeniową w magazynie danych usługi Azure Synapse Analytics za pomocą interfejsów API REST.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: bbb8e82710b4c8ca7736b53d427b3880faf2be05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213282"
---
# <a name="rest-apis-for-azure-synapse-analytics-data-warehouse"></a>Interfejsy API REST dla usługi Azure Synapse Analytics Data Warehouse

Interfejsy API REST do zarządzania obliczeniami w usłudze Azure Synapse Analytics Data Warehouse.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

Aby zmienić jednostki magazynu danych, należy użyć interfejsu API REST [tworzenia lub aktualizacji bazy danych](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Poniższy przykład ustawia jednostki magazynu danych wartości DW1000 dla bazy danych MySQLDW, która jest hostowana na serwerze Server. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby wstrzymać bazę danych, należy użyć interfejsu API REST usługi [Pause Database](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Poniższy przykład wstrzymuje bazę danych o nazwie Database02 hostowaną na serwerze o nazwie Serwer01. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Wznów Obliczanie

Aby uruchomić bazę danych, należy użyć interfejsu API REST [wznawiania bazy danych](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . W poniższym przykładzie jest uruchamiana baza danych o nazwie Database02 hostowana na serwerze o nazwie Serwer01. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Sprawdź stan bazy danych

> [!NOTE]
> Obecnie sprawdzanie stanu bazy danych może zwrócić do trybu ONLINE, gdy baza danych kończy przepływ pracy w trybie online, co powoduje błędy połączeń. W przypadku korzystania z tego wywołania interfejsu API w celu wyzwalania prób połączenia może być konieczne dodanie 2 do 3 minut opóźnienia w kodzie aplikacji.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Pobierz harmonogram konserwacji

Sprawdź harmonogram konserwacji, który został ustawiony dla hurtowni danych.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Ustaw harmonogram konserwacji

Ustawianie i aktualizowanie harmonogramu konserwacji w istniejącym magazynie danych.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md).
