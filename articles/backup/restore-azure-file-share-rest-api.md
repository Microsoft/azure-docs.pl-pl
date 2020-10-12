---
title: Przywracanie udziałów plików platformy Azure za pomocą interfejsu API REST
description: Dowiedz się, jak używać interfejsu API REST do przywracania udziałów plików platformy Azure lub określonych plików z punktu przywracania utworzonego przez Azure Backup
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 60c73caa5db684e38b94b4d5786f2fd24aa65d08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88761801"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Przywracanie udziałów plików platformy Azure przy użyciu interfejsu API REST

W tym artykule wyjaśniono, jak przywrócić cały udział plików lub określone pliki z punktu przywracania utworzonego przez [Azure Backup](./backup-overview.md) przy użyciu interfejsu API REST.

Na końcu tego artykułu dowiesz się, jak wykonywać następujące operacje przy użyciu interfejsu API REST:

* Wyświetl punkty przywracania dla kopii zapasowej udziału plików platformy Azure.
* Przywróć pełny udział plików platformy Azure.
* Przywróć pojedyncze pliki lub foldery.

## <a name="prerequisites"></a>Wymagania wstępne

Przyjęto założenie, że masz już udział plików, którego kopię zapasową chcesz przywrócić. Jeśli tego nie zrobisz, Sprawdź Tworzenie [kopii zapasowej udziału plików platformy Azure przy użyciu interfejsu API REST](backup-azure-file-share-rest-api.md) , aby dowiedzieć się, jak go utworzyć.

W tym artykule będziemy używać następujących zasobów:

* **RecoveryServicesVault**: *azurefilesvault*
* **Grupa zasobów**: *migracji pamięci*
* **Konto magazynu**: *afsaccount*
* **Udział plików**: *migracji pamięci*

## <a name="fetch-containername-and-protecteditemname"></a>Pobierz ContainerName i ProtectedItemName

W przypadku większości wywołań interfejsu API związanych z przywracaniem należy przekazać wartości parametrów identyfikatora URI {ContainerName} i {protectedItemName}. Użyj atrybutu ID w treści odpowiedzi operacji [Get backupprotectableitems](/rest/api/backup/protecteditems/get) , aby pobrać wartości dla tych parametrów. W naszym przykładzie identyfikator udziału plików, który chcemy chronić, to:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Dlatego wartości są tłumaczone w następujący sposób:

* {ContainerName}- *storagecontainer; Storage; migracji pamięci; afsaccount*
* {protectedItemName}- *azurefileshare; migracji pamięci*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Pobierz punkty odzyskiwania dla kopii zapasowej udziału plików platformy Azure

Aby przywrócić kopię zapasową udziału plików lub plików, najpierw wybierz punkt odzyskiwania, aby wykonać operację przywracania. Dostępne punkty odzyskiwania elementu kopii zapasowej można wyświetlić za pomocą wywołania interfejsu API REST z [listą punktów odzyskiwania](/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) . Jest to operacja pobrania ze wszystkimi odpowiednimi wartościami.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Ustaw wartości identyfikatora URI w następujący sposób:

* {fabricname}: *Azure*
* {magazynname}: *azurefilesvault*
* {ContainerName}: *storagecontainer; Storage; migracji pamięci; afsaccount*
* {protectedItemName}: *azurefileshare; migracji pamięci*
* {ResourceGroupName}: *migracji pamięci*

Identyfikator URI GET zawiera wszystkie wymagane parametry. Nie ma potrzeby dodatkowej treści żądania.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response-for-fetch-recovery-points"></a>Przykładowa odpowiedź dla punktów odzyskiwania pobierania

Po przesłaniu identyfikatora URI zostanie zwrócona odpowiedź 200:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

Punkt odzyskiwania jest identyfikowany za pomocą pola {Name} w powyższej odpowiedzi.

## <a name="full-share-recovery-using-rest-api"></a>Odzyskiwanie pełnego udziału przy użyciu interfejsu API REST

Użyj tej opcji przywracania, aby przywrócić pełny udział plików w lokalizacji oryginalnej lub alternatywnej.
Wyzwalanie przywracania jest żądaniem POST i można wykonać tę operację przy użyciu interfejsu API REST [przywracania wyzwalacza](/rest/api/backup/restores/trigger) .

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Wartości {ContainerName} i {protectedItemName} są ustawione w [tym miejscu](#fetch-containername-and-protecteditemname) , a recoveryPointID to pole {Name} w powyższym punkcie odzyskiwania.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Utwórz treść żądania

Aby wyzwolić przywracanie dla udziału plików platformy Azure, poniżej przedstawiono składniki treści żądania:

Nazwa |  Typ   |   Opis
--- | ---- | ----
Właściwości | AzureFileShareRestoreRequest | Właściwości RestoreRequestResource

Aby uzyskać pełną listę definicji treści żądania i innych szczegółów, zobacz [dokument interfejsu API REST przywracania wyzwalacza](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Przywróć do oryginalnej lokalizacji

#### <a name="request-body-example-for-restore-to-original-location"></a>Przykład treści żądania przywracania do oryginalnej lokalizacji

Następująca treść żądania definiuje właściwości wymagane do wyzwolenia przywracania udziału plików platformy Azure:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Przywróć do lokalizacji alternatywnej

Określ następujące parametry odzyskiwania lokalizacji alternatywnej:

* **element targetresourceid**: konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **Nazwa**: udział plików w docelowym koncie magazynu, do którego zostanie przywrócona zawartość kopii zapasowej.
* **targetFolderPath**: folder w udziale plików, do którego przywracane są dane.

#### <a name="request-body-example-for-restore-to-alternate-location"></a>Przykład treści żądania przywracania do lokalizacji alternatywnej

Następująca treść żądania przywraca udział plików *migracji pamięci* na koncie magazynu *afsaccount* do udziału plików *azurefiles1* na koncie magazynu *afaccount1* .

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Reakcja

Wyzwalacz operacji przywracania jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Ta operacja tworzy inną operację, która musi być śledzona oddzielnie.
Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, a 200 (OK) po zakończeniu tej operacji.

#### <a name="response-example"></a>Przykład odpowiedzi

*Po przesłaniu identyfikatora URI* dla wyzwalania przywracania, początkowa odpowiedź to 202 (zaakceptowane) z nagłówkiem lokalizacji lub z nagłówkiem Azure-Async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą polecenia GET.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Po zakończeniu operacji zwraca 200 (OK) z IDENTYFIKATORem wynikowego zadania przywracania w treści odpowiedzi.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

W przypadku odzyskiwania do lokalizacji alternatywnej treść odpowiedzi będzie wyglądać następująco:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Ponieważ zadanie tworzenia kopii zapasowej jest długotrwałą operacją, powinno być śledzone w sposób opisany w [dokumencie monitorowanie zadań przy użyciu interfejsu API REST](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="item-level-recovery-using-rest-api"></a>Odzyskiwanie na poziomie elementu przy użyciu interfejsu API REST

Ta opcja przywracania służy do przywracania pojedynczych plików lub folderów w lokalizacji oryginalnej lub alternatywnej.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Wartości {ContainerName} i {protectedItemName} są ustawione w [tym miejscu](#fetch-containername-and-protecteditemname) , a recoveryPointID to pole {Name} w powyższym punkcie odzyskiwania.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body-for-item-level-recovery-using-rest-api"></a>Tworzenie treści żądania odzyskiwania na poziomie elementu przy użyciu interfejsu API REST

Aby wyzwolić przywracanie dla udziału plików platformy Azure, poniżej przedstawiono składniki treści żądania:

Nazwa |  Typ   |   Opis
--- | ---- | ----
Właściwości | AzureFileShareRestoreRequest | Właściwości RestoreRequestResource

Aby uzyskać pełną listę definicji treści żądania i innych szczegółów, zobacz [dokument interfejsu API REST przywracania wyzwalacza](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location-for-item-level-recovery-using-rest-api"></a>Przywróć do oryginalnej lokalizacji odzyskiwania na poziomie elementu przy użyciu interfejsu API REST

Następująca treść żądania polega na przywróceniu pliku *Restoretest.txt* w udziale plików *migracji pamięci* na koncie magazynu *afsaccount* .

Utwórz treść żądania

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location-for-item-level-recovery-using-rest-api"></a>Przywracanie do alternatywnej lokalizacji odzyskiwania na poziomie elementu przy użyciu interfejsu API REST

Następująca treść żądania polega na przywróceniu pliku *Restoretest.txt* w udziale plików *migracji pamięci* na koncie magazynu *afsaccount* do folderu *restoredata* w udziale plików *azurefiles1* na koncie magazynu *afaccount1* .

Utwórz treść żądania

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

Odpowiedź powinna być obsługiwana w taki sam sposób, jak wyjaśniono powyżej, aby można było [przywrócić pełne udostępnianie](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zarządzać kopiami zapasowymi udziałów plików platformy Azure przy użyciu interfejsu API REST](manage-azure-file-share-rest-api.md).
