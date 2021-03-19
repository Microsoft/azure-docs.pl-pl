---
title: Tworzenie kopii zapasowych udziałów plików platformy Azure przy użyciu interfejsu API REST
description: Dowiedz się, jak używać interfejsu API REST do tworzenia kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88824869"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Tworzenie kopii zapasowej udziału plików platformy Azure przy użyciu Azure Backup za pomocą interfejsu API REST

W tym artykule opisano sposób tworzenia kopii zapasowej udziału plików platformy Azure przy użyciu Azure Backup za pośrednictwem interfejsu API REST.

W tym artykule przyjęto założenie, że utworzono już magazyn Recovery Services i zasady dotyczące konfigurowania kopii zapasowej udziału plików. Jeśli nie, zapoznaj się z samouczkiem [Tworzenie magazynu](./backup-azure-arm-userestapi-createorupdatevault.md) i interfejs API REST [zasad](./backup-azure-arm-userestapi-createorupdatepolicy.md) , aby utworzyć nowe magazyny i zasady.

W tym artykule będziemy używać następujących zasobów:

- **RecoveryServicesVault**: *azurefilesvault*

- **Zasady:** *schedule1*

- **Grupa zasobów**: *migracji pamięci*

- **Konto magazynu**: *testvault2*

- **Udział plików**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Konfigurowanie kopii zapasowej dla niechronionego udziału plików platformy Azure przy użyciu interfejsu API REST

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Odnajdywanie kont magazynu z niechronionymi udziałami plików platformy Azure

Magazyn musi odnaleźć wszystkie konta usługi Azure Storage w ramach subskrypcji z udziałami plików, których kopie zapasowe mogą być tworzone w magazynie Recovery Services. Jest to wyzwalane przy użyciu [operacji odświeżania](/rest/api/backup/protectioncontainers/refresh). Jest to asynchroniczna operacja *post* , która zapewnia, że magazyn otrzymuje najnowszą listę wszystkich niechronionych udziałów plików platformy Azure w bieżącej subskrypcji i "pamięci podręcznej". Gdy udział plików jest buforowany, usługi odzyskiwania mogą uzyskać dostęp do udziału plików i chronić go.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

Identyfikator URI wpisu zawiera `{subscriptionId}` `{vaultName}` Parametry,, `{vaultresourceGroupName}` i `{fabricName}` . W naszym przykładzie wartość dla różnych parametrów będzie następująca:

- `{fabricName}` jest *platformą Azure*

- `{vaultName}` jest *azurefilesvault*

- `{vaultresourceGroupName}` jest *migracji pamięci*

- $filter = backupManagementType EQ "AzureStorage"

Ponieważ wszystkie wymagane parametry są określone w identyfikatorze URI, nie ma potrzeby oddzielnej treści żądania.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Odpowiedzi na operację odświeżania

Operacja "Refresh" jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, a 200 (OK) po zakończeniu tej operacji.

##### <a name="example-responses-to-the-refresh-operation"></a>Przykładowe odpowiedzi na operację odświeżania

Po przesłaniu żądania *post* zostaje zwrócona odpowiedź 202 (zaakceptowana).

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Śledzenie wyniku operacji przy użyciu nagłówka "Location" z prostym poleceniem *Get*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Po odnalezieniu wszystkich kont usługi Azure Storage polecenie GET zwróci odpowiedź 200 (brak zawartości). Magazyn umożliwia teraz odnajdywanie dowolnego konta magazynu z udziałami plików, których kopie zapasowe mogą być tworzone w ramach subskrypcji.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Pobierz listę kont magazynu z udziałami plików, których kopię zapasową można utworzyć za pomocą magazynu Recovery Services

Aby upewnić się, że "buforowanie" zostało wykonane, Wyświetl listę wszystkich kont magazynu w subskrypcji z udziałami plików, których kopię zapasową można utworzyć za pomocą magazynu Recovery Services. Następnie Znajdź odpowiednie konto magazynu w odpowiedzi. Odbywa się to przy użyciu operacji [Get ProtectableContainers](/rest/api/backup/protectablecontainers/list) .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

Identyfikator URI *Get* zawiera wszystkie wymagane parametry. Żadna dodatkowa treść żądania nie jest wymagana.

Przykład treści odpowiedzi:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Ponieważ firma Microsoft może zlokalizować konto magazynu *testvault2* w treści odpowiedzi o przyjaznej nazwie, wykonana powyżej operacja odświeżania powiodła się. Magazyn Recovery Services może teraz pomyślnie odnajdywać konta magazynu z udziałami plików niechronionymi w tej samej subskrypcji.

### <a name="register-storage-account-with-recovery-services-vault"></a>Rejestrowanie konta magazynu w magazynie Recovery Services

Ten krok jest wymagany tylko wtedy, gdy konto magazynu zostało wcześniej zarejestrowane w magazynie. Magazyn można zarejestrować za pomocą [operacji ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Ustaw zmienne dla identyfikatora URI w następujący sposób:

- {resourceGroupName} — *migracji pamięci*
- {fabricname} — *Azure*
- {magazynname} — *azurefilesvault*
- {ContainerName} — jest to atrybut Name w treści odpowiedzi operacji GET ProtectableContainers.
   W naszym przykładzie jest to *StorageContainer; Chowan Migracji pamięci; testvault2*

>[!NOTE]
> Zawsze należy przyjmować atrybut name odpowiedzi i wypełnić go w ramach tego żądania. Nie należy wykodować ani utworzyć formatu nazwy kontenera. Jeśli utworzysz lub sformatujesz go, wywołanie interfejsu API zakończy się niepowodzeniem, jeśli format nazwy kontenera zmieni się w przyszłości.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Treść żądania Create jest następująca:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Aby uzyskać pełną listę definicji treści żądania i innych szczegółów, zobacz [ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Jest to operacja asynchroniczna i zwraca dwie odpowiedzi: "202 zaakceptowane", gdy operacja zostanie zaakceptowana i "200 OK", gdy operacja zostanie ukończona.  Aby śledzić stan operacji, użyj nagłówka lokalizacji w celu uzyskania najnowszego stanu operacji.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Przykład treści odpowiedzi po zakończeniu operacji:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Możesz sprawdzić, czy rejestracja zakończyła się pomyślnie z wartości parametru *registrationstatus* w treści odpowiedzi. W naszym przypadku pokazuje stan zarejestrowany dla *testvault2*, więc operacja rejestracji zakończyła się pomyślnie.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Zapytanie dotyczące wszystkich niechronionych udziałów plików w ramach konta magazynu

Aby uzyskać informacje na temat elementów podlegających ochronie na koncie magazynu, należy wykonać operację zapytania dotyczącego [kontenerów ochrony](/rest/api/backup/protectioncontainers/inquire) . Jest to operacja asynchroniczna, a wyniki powinny być śledzone przy użyciu nagłówka lokalizacji.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Ustaw zmienne dla powyższego identyfikatora URI w następujący sposób:

- {magazynname} — *azurefilesvault*
- {fabricname} — *Azure*
- {ContainerName} — odwołaj się do atrybutu Name w treści odpowiedzi operacji GET ProtectableContainers. W naszym przykładzie jest to *StorageContainer; Chowan Migracji pamięci; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Gdy żądanie zakończy się pomyślnie, zwraca kod stanu "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Wybierz udział plików, dla którego chcesz utworzyć kopię zapasową

Można wyświetlić listę wszystkich elementów podlegających ochronie w ramach subskrypcji i zlokalizować żądany udział plików, którego kopia zapasowa ma zostać utworzona przy użyciu operacji [Get backupprotectableItems](/rest/api/backup/backupprotectableitems/list) .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Utwórz identyfikator URI w następujący sposób:

- {magazynname} — *azurefilesvault*
- {$filter} — *backupManagementType EQ "AzureStorage"*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Przykładowa odpowiedź:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Odpowiedź zawiera listę wszystkich niechronionych udziałów plików i zawiera wszystkie informacje wymagane przez usługę Azure Recovery w celu skonfigurowania kopii zapasowej.

### <a name="enable-backup-for-the-file-share"></a>Włącz tworzenie kopii zapasowej udziału plików

Gdy odpowiedni udział plików jest "zidentyfikowany" z przyjazną nazwą, wybierz zasady do ochrony. Aby dowiedzieć się więcej na temat istniejących zasad w magazynie, zapoznaj się z tematem [interfejs API zasad listy](/rest/api/backup/backuppolicies/list). Następnie wybierz [odpowiednie zasady](/rest/api/backup/protectionpolicies/get) , odwołując się do nazwy zasad. Aby utworzyć zasady, zobacz [samouczek Tworzenie zasad](./backup-azure-arm-userestapi-createorupdatepolicy.md).

Włączenie ochrony jest asynchroniczną operacją *Put* , która tworzy "chroniony element".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Ustaw zmienne **ContainerName** i **protecteditemname** przy użyciu atrybutu ID w treści odpowiedzi operacji get backupprotectableitems.

W naszym przykładzie identyfikator udziału plików, który chcemy chronić, to:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {ContainerName}- *storagecontainer; Storage; migracji pamięci; testvault2*
- {protectedItemName}- *azurefileshare; testshare*

Lub można odwołać się do atrybutu **name** kontenera ochrony i funkcji chronionych przez elementy.

>[!NOTE]
>Zawsze należy przyjmować atrybut name odpowiedzi i wypełnić go w ramach tego żądania. Nie należy wykodować kodu ani utworzyć formatu nazwy kontenera lub chronionej nazwy elementu. Jeśli utworzysz lub sformatujesz go, wywołanie interfejsu API zakończy się niepowodzeniem w przypadku zmiany formatu nazwy kontenera lub chronionej nazwy elementu w przyszłości.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Utwórz treść żądania:

Następująca treść żądania definiuje właściwości wymagane do utworzenia chronionego elementu.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**Identyfikator sourceresourceid** jest **parentcontainerFabricID** w odpowiedzi na pobieranie backupprotectableItems.

Przykładowa odpowiedź

Tworzenie chronionego elementu jest operacją asynchroniczną, która tworzy kolejną operację, która musi być śledzona. Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja i 200 (OK) po zakończeniu tej operacji.

Po przesłaniu żądania *Put* dotyczącego tworzenia lub aktualizowania chronionego elementu początkowa odpowiedź to 202 (zaakceptowana) z nagłówkiem lokalizacji.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą polecenia  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Po zakończeniu operacji zwraca 200 (OK) z zawartością chronionego elementu w treści odpowiedzi.

Przykładowa treść odpowiedzi:

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

Pozwala to upewnić się, że ochrona jest włączona dla udziału plików, a pierwsza kopia zapasowa zostanie wyzwolona zgodnie z harmonogramem zasad.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Wyzwalanie kopii zapasowej na żądanie dla udziału plików

Gdy udział plików platformy Azure jest skonfigurowany do tworzenia kopii zapasowych, kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Możesz poczekać na pierwszą zaplanowaną kopię zapasową lub wyzwolić kopię zapasową na żądanie w dowolnym momencie.

Wyzwalanie kopii zapasowej na żądanie jest operacją POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{ContainerName} i {protectedItemName} są tak skonstruowane jak powyżej podczas włączania tworzenia kopii zapasowych. W naszym przykładzie jest to tłumaczone na:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Utwórz treść żądania

Aby wyzwolić kopię zapasową na żądanie, poniżej przedstawiono składniki treści żądania.

| Nazwa       | Typ                       | Opis                       |
| ---------- | -------------------------- | --------------------------------- |
| Właściwości | AzurefilesharebackupReques | Właściwości BackupRequestResource |

Aby zapoznać się z pełną listą definicji treści żądania i innych szczegółów, zapoznaj się z tematem [wyzwalanie kopii zapasowych dla dokumentów interfejsu API REST elementów chronionych](/rest/api/backup/backups/trigger#request-body).

Przykład treści żądania

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Odpowiedzi na operację tworzenia kopii zapasowej na żądanie

Wyzwalanie kopii zapasowej na żądanie jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja i 200 (OK) po zakończeniu tej operacji.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Przykładowe odpowiedzi na operację tworzenia kopii zapasowej na żądanie

Po przesłaniu żądania *post* do kopii zapasowej na żądanie początkowa odpowiedź to 202 (zaakceptowana) z nagłówkiem lokalizacji lub z nagłówkiem Azure-Async-header.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą polecenia  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Po zakończeniu operacji zwraca 200 (OK) z IDENTYFIKATORem wynikowego zadania tworzenia kopii zapasowej w treści odpowiedzi.

#### <a name="sample-response-body"></a>Przykładowa treść odpowiedzi

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Ponieważ zadanie tworzenia kopii zapasowej jest długotrwałą operacją, musi być śledzone zgodnie z opisem w [dokumencie monitorowanie zadań przy użyciu interfejsu API REST](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przywrócić udziały plików platformy Azure przy użyciu interfejsu API REST](restore-azure-file-share-rest-api.md).
