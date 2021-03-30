---
title: Zarządzanie kopią zapasową udziału plików platformy Azure za pomocą interfejsu API REST
description: Dowiedz się, jak używać interfejsu API REST do zarządzania udziałami plików platformy Azure, których kopie zapasowe są tworzone przez Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 5e2823472c6a7bdd6b3f9819db3079d7efa78c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88892851"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Zarządzanie kopią zapasową udziału plików platformy Azure za pomocą interfejsu API REST

W tym artykule opisano sposób wykonywania zadań związanych z zarządzaniem udziałami plików platformy Azure, których kopie zapasowe są tworzone przez [Azure Backup](./backup-overview.md).

## <a name="monitor-jobs"></a>Monitorowanie zadań

Usługa Azure Backup wyzwala zadania uruchamiane w tle. Obejmuje to takie scenariusze jak wyzwalanie kopii zapasowych, operacje przywracania i wyłączanie tworzenia kopii zapasowych. Te zadania mogą być śledzone przy użyciu ich identyfikatorów.

### <a name="fetch-job-information-from-operations"></a>Pobieranie informacji o zadaniu z operacji

Operacja, taka jak wyzwalanie kopii zapasowej, zawsze zwróci identyfikator jobID w odpowiedzi.

Na przykład ostateczną odpowiedzią operacji [interfejsu API Rest tworzenia kopii zapasowej wyzwalacza](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) jest następująca:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Zadanie tworzenia kopii zapasowej udziału plików platformy Azure jest identyfikowane za pomocą pola **jobId** i może być śledzone w sposób opisany w [tym miejscu](/rest/api/backup/jobdetails/) przy użyciu żądania GET.

### <a name="tracking-the-job"></a>Śledzenie zadania

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{JobName} jest "jobId" wymienionym powyżej. Odpowiedź jest zawsze "200 OK" z polem **stanu** wskazującym stan zadania. Po "ukończeniu" lub "CompletedWithWarnings" sekcja **extendedInfo** zawiera więcej informacji o zadaniu.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Reakcja

Nazwa  | Typ  |  Opis
--- | --- | ----
200 OK |  JobResource  | OK

#### <a name="response-example"></a>Przykład odpowiedzi

Po przesłaniu *identyfikatora URI zostanie* zwrócona odpowiedź 200.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Modyfikuj zasady

Aby zmienić zasady, przy użyciu których udział plików jest chroniony, można użyć tego samego formatu co włączenie ochrony. Po prostu podaj nowy identyfikator zasad w zasadach żądań i prześlij żądanie.

Na przykład: Aby zmienić zasady ochrony *testshare* z *schedule1* na *schedule2*, podaj identyfikator *schedule2* w treści żądania.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Zatrzymaj ochronę, ale Zachowaj istniejące dane

Ochronę chronionego udziału plików można usunąć, ale zachowano już kopię zapasową danych. Aby to zrobić, Usuń zasady w treści żądania użytej do[włączenia tworzenia kopii zapasowej](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) i przesyłania żądania. Po usunięciu skojarzenia z zasadami kopie zapasowe nie są już wyzwalane i nie są tworzone żadne nowe punkty odzyskiwania.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Przykładowa odpowiedź

Zatrzymywanie ochrony udziału plików jest operacją asynchroniczną. Operacja tworzy inną operację, która musi być śledzona. Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, i 200 po zakończeniu tej operacji.

Nagłówek odpowiedzi po pomyślnym zaakceptowaniu operacji:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą polecenia GET:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Treść odpowiedzi

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Zatrzymywanie ochrony i usuwanie danych

Aby usunąć ochronę chronionego udziału plików i usunąć również dane kopii zapasowej, wykonaj operację usuwania w sposób opisany [tutaj](/rest/api/backup/protecteditems/delete).

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Parametry {ContainerName} i {protectedItemName} zostały ustawione w [tym miejscu](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname).

Poniższy przykład wyzwala operację, aby zatrzymać ochronę udziału plików *testshare* chronionego za pomocą usługi *azurefilesvault*.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Odpowiedzi

Usuwanie ochrony jest operacją asynchroniczną. Operacja tworzy inną operację, która musi być śledzona oddzielnie.
Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja i 204 (NoContent) po zakończeniu tej operacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [rozwiązywać problemy podczas konfigurowania kopii zapasowej dla udziałów plików platformy Azure](troubleshoot-azure-files.md).
