---
title: Wstrzymywanie, wznawianie i skalowanie przy użyciu interfejsów API REST dla dedykowanej puli SQL (dawniej SQL DW)
description: Zarządzanie mocą obliczeniową dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics za pomocą interfejsów API REST.
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
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375905"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Interfejsy API REST dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

Interfejsy API REST do zarządzania obliczeniami dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

Aby zmienić jednostki magazynu danych, należy użyć interfejsu API REST [tworzenia lub aktualizacji bazy danych](/rest/api/sql/databases/createorupdate) . Poniższy przykład ustawia jednostki magazynu danych wartości DW1000 dla bazy danych MySQLDW, która jest hostowana na serwerze Server. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby wstrzymać bazę danych, należy użyć interfejsu API REST usługi [Pause Database](/rest/api/sql/databases/pause) . Poniższy przykład wstrzymuje bazę danych o nazwie Database02 hostowaną na serwerze o nazwie Serwer01. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Wznów Obliczanie

Aby uruchomić bazę danych, należy użyć interfejsu API REST [wznawiania bazy danych](/rest/api/sql/databases/resume) . W poniższym przykładzie jest uruchamiana baza danych o nazwie Database02 hostowana na serwerze o nazwie Serwer01. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Sprawdź stan bazy danych

> [!NOTE]
> Obecnie sprawdzanie stanu bazy danych może zwrócić do trybu ONLINE, gdy baza danych kończy przepływ pracy w trybie online, co powoduje błędy połączeń. W przypadku korzystania z tego wywołania interfejsu API w celu wyzwalania prób połączenia może być konieczne dodanie 2 do 3 minut opóźnienia w kodzie aplikacji.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Pobierz harmonogram konserwacji

Sprawdź harmonogram konserwacji, który został ustawiony dla dedykowanej puli SQL (dawniej SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Ustaw harmonogram konserwacji

Aby ustawić i zaktualizować harmonogram konserwacji dla istniejącej dedykowanej puli SQL (dawniej SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md).
