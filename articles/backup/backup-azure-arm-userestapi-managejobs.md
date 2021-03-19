---
title: Zarządzanie zadaniami tworzenia kopii zapasowej przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak śledzić i zarządzać zadaniami tworzenia kopii zapasowych i przywracania Azure Backup przy użyciu interfejsu API REST.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: ced0e0020fe955734bf6cc767480fbadd6eaffc1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88890284"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Śledzenie zadań tworzenia kopii zapasowej i przywracania za pomocą interfejsu API REST

Usługa Azure Backup wyzwala zadania uruchamiane w tle w różnych scenariuszach, takich jak wyzwalanie kopii zapasowej, operacje przywracania i wyłączanie tworzenia kopii zapasowych. Te zadania mogą być śledzone przy użyciu ich identyfikatorów.

## <a name="fetch-job-information-from-operations"></a>Pobieranie informacji o zadaniu z operacji

Operacja, taka jak wyzwalanie kopii zapasowej, zawsze zwróci identyfikator zadania. Na przykład: końcowa odpowiedź [operacji interfejsu API REST wykonywania kopii zapasowej wyzwalacza](backup-azure-arm-userestapi-backupazurevms.md#example-responses-for-on-demand-backup) jest następująca:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Zadanie kopii zapasowej maszyny wirtualnej platformy Azure jest identyfikowane w polu "jobId" i może być śledzone w sposób opisany w [tym miejscu](/rest/api/backup/jobdetails/) przy użyciu prostego żądania *Get* .

## <a name="tracking-the-job"></a>Śledzenie zadania

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

`{jobName}`Jest to "jobId" wymienione powyżej. Odpowiedź jest zawsze 200 OK z polem "status" wskazującą bieżący stan zadania. Po "zakończeniu" lub "CompletedWithWarnings" w sekcji "extendedInfo" znajduje się więcej informacji o zadaniu.

### <a name="response"></a>Reakcja

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     | [JobResource](/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Przykładowa odpowiedź

Po przesłaniu *identyfikatora URI zostanie* zwrócona odpowiedź 200 (ok).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
