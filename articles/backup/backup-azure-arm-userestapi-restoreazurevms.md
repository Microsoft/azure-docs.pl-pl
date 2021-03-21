---
title: Przywracanie maszyn wirtualnych platformy Azure przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak zarządzać operacjami przywracania kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu interfejsu API REST.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: da6b4cd6134f0cd1fd3d6e04e814bbf8aec9b07d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452156"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Przywracanie maszyn wirtualnych platformy Azure przy użyciu interfejsu API REST

Po zakończeniu wykonywania kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu Azure Backup jeden może przywrócić wszystkie maszyny wirtualne lub dyski lub pliki z tej samej kopii zapasowej platformy Azure. W tym artykule opisano, jak przywrócić maszynę wirtualną lub dyski platformy Azure przy użyciu interfejsu API REST.

Dla każdej operacji przywracania należy najpierw zidentyfikować odpowiedni punkt odzyskiwania.

## <a name="select-recovery-point"></a>Wybierz punkt odzyskiwania

Dostępne punkty odzyskiwania elementu kopii zapasowej można wyświetlić za pomocą [interfejsu API REST punktu odzyskiwania](/rest/api/backup/recoverypoints/list). Jest to prosta operacja *pobrania* ze wszystkimi odpowiednimi wartościami.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}`I `{protectedItemName}` są tak skonstruowane w [tym miejscu](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` to "Azure".

Identyfikator URI *Get* zawiera wszystkie wymagane parametry. Nie ma potrzeby dodatkowej treści żądania.

### <a name="responses"></a>Odpowiedzi

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Przykładowa odpowiedź

Po przesłaniu *identyfikatora URI zostanie* zwrócona odpowiedź 200 (ok).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Punkt odzyskiwania jest identyfikowany z `{name}` polem w powyższej odpowiedzi.

## <a name="restore-operations"></a>Operacje przywracania

Po wybraniu [odpowiedniego punktu przywracania](#select-recovery-point)należy wykonać operację przywracania.

***Wszystkie operacje przywracania na elemencie kopii zapasowej są wykonywane przy użyciu tego samego, *końcowego* interfejsu API. Tylko treść żądania zmienia się z scenariuszami przywracania.***

> [!IMPORTANT]
> Wszystkie szczegóły dotyczące różnych opcji przywracania i ich zależności są wymienione [tutaj](./backup-azure-arm-restore-vms.md#restore-options). Przejrzyj przed kontynuowaniem, aby wyzwolić te operacje.

Wyzwalanie operacji przywracania jest żądaniem *post* . Aby dowiedzieć się więcej o interfejsie API, zapoznaj się z [interfejsem API REST "Wyzwól przywracanie"](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}`I `{protectedItemName}` są tak skonstruowane w [tym miejscu](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` to "Azure", a to `{recoveryPointId}` `{name}` pole jest polem punktu odzyskiwania wymienionego [powyżej](#example-response).

Po uzyskaniu punktu odzyskiwania musimy skonstruować treść żądania dla odpowiedniego scenariusza przywracania. W poniższych sekcjach opisano treść żądania dla każdego scenariusza.

- [Przywróć dyski](#restore-disks)
- [Zastąp dyski](#replace-disks-in-a-backed-up-virtual-machine)
- [Przywróć jako nową maszynę wirtualną](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Przywróć odpowiedź

Wyzwalacz operacji przywracania jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|202 zaakceptowane     |         |     Zaakceptowano    |

#### <a name="example-responses"></a>Przykładowe odpowiedzi

*Po przesłaniu identyfikatora URI* dla wyzwalania dysków przywracania początkowa odpowiedź to 202 (zaakceptowane) z nagłówkiem lokalizacji lub z nagłówkiem Azure-Async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą prostego polecenia *Get* .

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Po zakończeniu operacji zwraca 200 (OK) z IDENTYFIKATORem wynikowego zadania przywracania w treści odpowiedzi.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Ponieważ zadanie przywracania jest długotrwałą operacją, powinno być śledzone zgodnie z opisem w [dokumencie monitorowanie zadań przy użyciu interfejsu API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Przywróć dyski

Jeśli istnieje potrzeba dostosowania tworzenia maszyny wirtualnej na podstawie danych kopii zapasowej, można po prostu przywrócić dyski na wybrane konto magazynu i utworzyć maszynę wirtualną na podstawie tych dysków zgodnie z ich wymaganiami. Konto magazynu powinno znajdować się w tym samym regionie co magazyn Recovery Services i nie powinno być nadmiarowe strefy. Dyski, a także Konfiguracja kopii zapasowej maszyny wirtualnej ("vmconfig.json") będą przechowywane na danym koncie magazynu. Zgodnie z [powyższymi](#restore-operations)szczegółami poniżej przedstawiono odpowiednią treść żądania dla dysków przywracania.

#### <a name="create-request-body"></a>Utwórz treść żądania

Aby wyzwolić przywracanie dysku z kopii zapasowej maszyny wirtualnej platformy Azure, poniżej przedstawiono składniki treści żądania.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Pełną listę definicji treści żądania oraz inne szczegóły znajdują się w [dokumencie wyzwalacze przywracania interfejsu API REST](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Przykładowe żądanie

Następująca treść żądania definiuje właściwości wymagane do wyzwolenia przywracania dysku.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Selektywne przywracanie dysków

W przypadku [selektywnego tworzenia kopii zapasowych dysków](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup), aktualna lista dysków kopii zapasowej znajduje się w [podsumowaniu punktu odzyskiwania](#select-recovery-point) i [szczegółowej odpowiedzi](/rest/api/backup/recoverypoints/get). Możesz również wybiórczo przywracać dyski i więcej szczegółów można znaleźć [tutaj](selective-disk-backup-restore.md#selective-disk-restore). Aby wybiórczo przywrócić dysk między listą kopii zapasowych dysków, Znajdź numer LUN dysku z odpowiedzi punktu odzyskiwania i Dodaj właściwość **restoreDiskLunList** do [treści żądania powyżej](#example-request) , jak pokazano poniżej.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Po przeprowadzeniu śledzenia odpowiedzi zgodnie z [powyższym](#responses)opisem, gdy długotrwałe zadanie jest ukończone, dyski i Konfiguracja kopii zapasowej maszyny wirtualnej ("VMConfig.json") będą obecne na danym koncie magazynu.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Zastępowanie dysków w kopii zapasowej maszyny wirtualnej

Podczas przywracania dysków tworzone są dyski z punktu odzyskiwania, zastąp dyski zastępuje bieżące dyski maszyny wirtualnej kopii zapasowej z dyskami z punktu odzyskiwania. Zgodnie z [powyższym](#restore-operations)opisem należy określić odpowiednią treść żądania dla zastępowania dysków.

#### <a name="create-request-body"></a>Utwórz treść żądania

Aby wyzwolić zastąpienie dysku z kopii zapasowej maszyny wirtualnej platformy Azure, poniżej przedstawiono składniki treści żądania.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Pełną listę definicji treści żądania oraz inne szczegóły znajdują się w [dokumencie wyzwalacze przywracania interfejsu API REST](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Przykładowe żądanie

Następująca treść żądania definiuje właściwości wymagane do wyzwolenia przywracania dysku.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Przywróć jako inną maszynę wirtualną

Jak wyjaśniono [powyżej](#restore-operations), następująca treść żądania definiuje właściwości wymagane do wyzwolenia przywracania maszyny wirtualnej.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
     }
 }
```

Odpowiedź powinna być obsługiwana w taki sam sposób, jak [wyjaśniono powyżej w przypadku przywracania dysków](#responses).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Backup interfejsów API REST, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usługi Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
