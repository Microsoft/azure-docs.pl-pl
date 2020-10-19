---
title: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak konfigurować i inicjować operacje tworzenia kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu interfejsu API REST oraz zarządzać nimi.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171451"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu Azure Backup za pośrednictwem interfejsu API REST

W tym artykule opisano sposób zarządzania kopiami zapasowymi maszyny wirtualnej platformy Azure przy użyciu Azure Backup za pośrednictwem interfejsu API REST. Skonfiguruj ochronę po raz pierwszy w przypadku wcześniej niechronionej maszyny wirtualnej platformy Azure, wyzwól kopię zapasową na żądanie dla chronionej maszyny wirtualnej platformy Azure i Modyfikuj właściwości kopii zapasowej maszyny wirtualnej z kopią zapasową za pośrednictwem interfejsu API REST, jak wyjaśniono tutaj.

Zapoznaj się z artykułem [Tworzenie magazynu](backup-azure-arm-userestapi-createorupdatevault.md) i tworzenie SAMOUCZKÓW interfejsu API REST [zasad](backup-azure-arm-userestapi-createorupdatepolicy.md) , które umożliwiają tworzenie nowych magazynów i zasad.

Załóżmy, że chcesz chronić maszynę wirtualną "testVM" w grupie zasobów "testRG" do magazynu Recovery Services "testVault" w grupie zasobów "testVaultRG" z zasadami domyślnymi (o nazwie "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurowanie kopii zapasowej niechronionej maszyny wirtualnej platformy Azure przy użyciu interfejsu API REST

### <a name="discover-unprotected-azure-vms"></a>Odnajdywanie niechronionych maszyn wirtualnych platformy Azure

Najpierw magazyn powinien być w stanie identyfikować maszynę wirtualną platformy Azure. Jest to wyzwalane przy użyciu [operacji odświeżania](/rest/api/backup/protectioncontainers/refresh). Jest to asynchroniczna operacja *post*  , która zapewnia, że magazyn otrzymuje najnowszą listę wszystkich niechronionych maszyn wirtualnych w bieżącej subskrypcji i "pamięci podręcznej". Po zapisaniu maszyny wirtualnej usługi odzyskiwania będą mogły uzyskać dostęp do maszyny wirtualnej i chronić ją.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Identyfikator URI wpisu ma `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` , `{fabricName}` parametrów. `{fabricName}`Jest to "Azure". Zgodnie z naszym Przykładem `{vaultName}` jest "testVault" i `{vaultresourceGroupName}` jest "testVaultRG". Ponieważ wszystkie wymagane parametry są określone w identyfikatorze URI, nie ma potrzeby oddzielnej treści żądania.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>Odpowiedzi na operację odświeżania

Operacja "Refresh" jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|204 Brak zawartości     |         |  OK bez zwracanej zawartości      |
|202 zaakceptowane     |         |     Zaakceptowano    |

##### <a name="example-responses-to-refresh-operation"></a>Przykładowe odpowiedzi na operację odświeżania

Po przesłaniu żądania *post* zostaje zwrócona odpowiedź 202 (zaakceptowana).

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Śledzenie wyniku operacji przy użyciu nagłówka "Location" z prostym poleceniem *Get*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Po odnalezieniu wszystkich maszyn wirtualnych platformy Azure polecenie GET zwróci odpowiedź 204 (brak zawartości). Magazyn może teraz odnajdywać dowolną maszynę wirtualną w ramach subskrypcji.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Wybieranie odpowiedniej maszyny wirtualnej platformy Azure

 Aby potwierdzić, że "buforowanie" jest wykonywane, należy wyświetlić [listę wszystkich elementów podlegających ochronie](/rest/api/backup/backupprotectableitems/list) w ramach subskrypcji i zlokalizować ŻĄDAną maszynę wirtualną w odpowiedzi. [Odpowiedź tej operacji](#example-responses-to-get-operation) zawiera również informacje dotyczące sposobu, w jaki Recovery Services identyfikuje maszynę wirtualną.  Po zapoznaniu się ze wzorcem możesz pominąć ten krok i bezpośrednio przejść do [włączania ochrony](#enabling-protection-for-the-azure-vm).

Ta operacja jest operacją *pobierania* .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

Identyfikator URI *Get* zawiera wszystkie wymagane parametry. Żadna dodatkowa treść żądania nie jest wymagana.

#### <a name="responses-to-get-operation"></a>Odpowiedzi na pobieranie operacji

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Przykładowe odpowiedzi na potrzeby pobrania operacji

Po przesłaniu żądania *Get* zostanie zwrócona odpowiedź 200 (ok).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Liczba wartości w odpowiedzi *Get* jest ograniczona do 200 dla strony. Użyj pola "nextLink", aby uzyskać adres URL dla następnego zestawu odpowiedzi.

Odpowiedź zawiera listę wszystkich niechronionych maszyn wirtualnych platformy Azure, a każda z nich `{value}` zawiera wszystkie informacje wymagane przez usługę Azure Recovery w celu skonfigurowania kopii zapasowej. Aby skonfigurować kopię zapasową, zanotuj `{name}` pola i `{virtualMachineId}` pola w `{properties}` sekcji. Utwórz dwie zmienne z tych wartości pól, jak wspomniano poniżej.

- ContainerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "VM;" + `{name}`
- `{virtualMachineId}` jest używany w dalszej [części treści żądania](#example-request-body)

W tym przykładzie powyższe wartości przekładają się na:

- ContainerName = "iaasvmcontainer; iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "VM; iaasvmcontainerv2; testRG; testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Włączanie ochrony maszyny wirtualnej platformy Azure

Gdy odpowiednia maszyna wirtualna jest "buforowana" i "zidentyfikowana", wybierz zasady do ochrony. Aby dowiedzieć się więcej na temat istniejących zasad w magazynie, zapoznaj się z tematem [interfejs API zasad listy](/rest/api/backup/backuppolicies/list). Następnie wybierz [odpowiednie zasady](/rest/api/backup/protectionpolicies/get) , odwołując się do nazwy zasad. Aby utworzyć zasady, zobacz [samouczek Tworzenie zasad](backup-azure-arm-userestapi-createorupdatepolicy.md). W poniższym przykładzie wybrano wartość "DefaultPolicy".

Włączenie ochrony jest asynchroniczną operacją *Put* , która tworzy "chroniony element".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}`I `{protectedItemName}` są tak skonstruowane jak powyżej. `{fabricName}`Jest to "Azure". W naszym przykładzie jest to tłumaczone na:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Tworzenie treści żądania

Aby utworzyć chroniony element, poniżej przedstawiono składniki treści żądania.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Właściwości zasobów ProtectedItem         |

Aby uzyskać pełną listę definicji treści żądania i innych szczegółów, zobacz [dokument interfejsu API Rest tworzenia chronionego elementu](/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Przykładowa treść żądania

Następująca treść żądania definiuje właściwości wymagane do utworzenia chronionego elementu.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}`Jest to `{virtualMachineId}` wymienione powyżej z odpowiedzi na [listę elementów podlegających ochronie](#example-responses-to-get-operation).

#### <a name="responses-to-create-protected-item-operation"></a>Odpowiedzi w celu utworzenia operacji chronionego elementu

Tworzenie chronionego elementu jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 zaakceptowane     |         |     Zaakceptowano    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Przykładowe odpowiedzi na operację tworzenia chronionego elementu

Po przesłaniu żądania *Put* dotyczącego tworzenia lub aktualizowania chronionego elementu początkowa odpowiedź to 202 (zaakceptowane) z nagłówkiem lokalizacji lub z nagłówkiem Azure-Async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą prostego polecenia *Get* .

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po zakończeniu operacji zwraca 200 (OK) z zawartością chronionego elementu w treści odpowiedzi.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Pozwala to upewnić się, że włączono ochronę dla maszyny wirtualnej, a pierwsza kopia zapasowa zostanie wyzwolona zgodnie z harmonogramem zasad.

### <a name="excluding-disks-in-azure-vm-backup"></a>Wykluczanie dysków z kopii zapasowej maszyny wirtualnej platformy Azure

Azure Backup umożliwia również wybiórcze tworzenie kopii zapasowej podzbioru dysków na maszynie wirtualnej platformy Azure. Więcej szczegółów znajduje się w [tym miejscu](selective-disk-backup-restore.md). Jeśli chcesz selektywnie tworzyć kopie zapasowe kilku dysków podczas włączania ochrony, w [trakcie włączania ochrony](#example-request-body)należy użyć poniższego fragmentu kodu.

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

W powyższej treści żądania Lista dysków, których kopia zapasowa ma zostać utworzona, znajduje się w sekcji Właściwości rozszerzone.

|Właściwość  |Wartość  |
|---------|---------|
|diskLunList     | Lista LUN dysku jest listą *jednostek LUN dysków z danymi*. **Kopia zapasowa dysku systemu operacyjnego jest zawsze tworzona i nie trzeba go wymieniać**.        |
|IsInclusionList     | W przypadku tworzenia kopii zapasowej należy mieć **wartość true** . Jeśli wartość jest **równa false**, wymienione numery LUN zostaną wykluczone.         |

Dlatego, jeśli wymagana jest kopia zapasowa tylko dysku systemu operacyjnego, _wszystkie_ dyski z danymi powinny zostać wykluczone. Łatwiejszym sposobem jest to, że nie należy uwzględniać dysków z danymi. W związku z tym lista numerów LUN dysku będzie pusta i **IsInclusionList** będzie mieć **wartość true**. Podobnie należy zastanowić się, jak łatwiej jest wybrać podzestaw: kilka dysków powinno być zawsze wykluczone lub należy uwzględnić kilka dysków. Wybierz odpowiednio listę LUN i wartość zmiennej Boolean.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Wyzwalanie kopii zapasowej na żądanie dla chronionej maszyny wirtualnej platformy Azure

Po skonfigurowaniu kopii zapasowej maszyny wirtualnej platformy Azure kopie zapasowe są wykonywane zgodnie z harmonogramem zasad. Możesz poczekać na pierwszą zaplanowaną kopię zapasową lub wyzwolić kopię zapasową na żądanie w dowolnym momencie. Przechowywanie kopii zapasowych na żądanie jest niezależne od przechowywania zasad tworzenia kopii zapasowych i można je określić w określonym dniu. Jeśli nie zostanie określony, zakłada się, że jest to 30 dni od dnia wyzwalacza kopii zapasowej na żądanie.

Wyzwalanie kopii zapasowej na żądanie jest operacją *post* .

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}`I `{protectedItemName}` są tak skonstruowane jak [powyżej](#responses-to-get-operation). `{fabricName}`Jest to "Azure". W naszym przykładzie jest to tłumaczone na:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>Tworzenie treści żądania na potrzeby tworzenia kopii zapasowych na żądanie

Aby wyzwolić kopię zapasową na żądanie, poniżej przedstawiono składniki treści żądania.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Właściwości BackupRequestResource         |

Aby zapoznać się z pełną listą definicji treści żądania i innych szczegółów, zapoznaj się z tematem [wyzwalanie kopii zapasowych dla dokumentów interfejsu API REST elementów chronionych](/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body-for-on-demand-backup"></a>Przykładowa treść żądania na potrzeby tworzenia kopii zapasowych na żądanie

Następująca treść żądania definiuje właściwości wymagane do wyzwolenia kopii zapasowej chronionego elementu. Jeśli przechowywanie nie jest określone, będzie przechowywane przez 30 dni od momentu wyzwolenia zadania tworzenia kopii zapasowej.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Odpowiedzi na kopie zapasowe na żądanie

Wyzwalanie kopii zapasowej na żądanie jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|202 zaakceptowane     |         |     Zaakceptowano    |

#### <a name="example-responses-for-on-demand-backup"></a>Przykładowe odpowiedzi dla kopii zapasowej na żądanie

Po przesłaniu żądania *post* do kopii zapasowej na żądanie początkowa odpowiedź to 202 (zaakceptowana) z nagłówkiem lokalizacji lub z nagłówkiem Azure-Async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą prostego polecenia *Get* .

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po zakończeniu operacji zwraca 200 (OK) z IDENTYFIKATORem wynikowego zadania tworzenia kopii zapasowej w treści odpowiedzi.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Ponieważ zadanie tworzenia kopii zapasowej jest długotrwałą operacją, musi być śledzone zgodnie z opisem w [dokumencie monitorowanie zadań przy użyciu interfejsu API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modyfikowanie konfiguracji kopii zapasowej chronionej maszyny wirtualnej platformy Azure

### <a name="changing-the-policy-of-protection"></a>Zmiana zasad ochrony

Aby zmienić zasady, za pomocą których maszyna wirtualna jest chroniona, można użyć tego samego formatu co [włączenie ochrony](#enabling-protection-for-the-azure-vm). Po prostu podaj nowy identyfikator zasad w [treści żądania](#example-request-body) i prześlij żądanie. Na przykład: Aby zmienić zasady testVM z "DefaultPolicy" na "ProdPolicy", podaj identyfikator "ProdPolicy" w treści żądania.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Odpowiedź będzie zgodna z tym samym formatem, jak wspomniano [w celu włączenia ochrony](#responses-to-create-protected-item-operation)

#### <a name="excluding-disks-during-azure-vm-protection"></a>Wykluczanie dysków podczas ochrony maszyny wirtualnej platformy Azure

Jeśli kopia zapasowa maszyny wirtualnej platformy Azure została już utworzona, możesz określić listę dysków, których kopia zapasowa ma zostać utworzona lub wykluczona, zmieniając zasady ochrony. Po prostu Przygotuj żądanie w tym samym formacie co [wyłączenie dysków podczas włączania ochrony](#excluding-disks-in-azure-vm-backup)

> [!IMPORTANT]
> Powyższa treść żądania jest zawsze końcową kopią dysków danych do wykluczenia lub dołączenia. To nie powoduje *dodania* do poprzedniej konfiguracji. Przykład: Jeśli najpierw zaktualizujesz ochronę jako "Wyklucz dane dysk 1", a następnie powtarzasz polecenie "Exclude Data Disk 2", *tylko dysk danych 2 zostanie wykluczony* w kolejnych kopiach zapasowych, a dysk danych 1 zostanie uwzględniony. Jest to zawsze Ostatnia lista, która zostanie uwzględniona/wykluczona w kolejnych kopiach zapasowych.

Aby uzyskać bieżącą listę wykluczonych lub uwzględnionych dysków, Pobierz informacje o chronionym elemencie, jak wspomniano [tutaj](/rest/api/backup/protecteditems/get). Odpowiedź będzie zawierać listę jednostek LUN dysku danych i wskazuje, czy są one dołączone lub wykluczone.

### <a name="stop-protection-but-retain-existing-data"></a>Zatrzymaj ochronę, ale Zachowaj istniejące dane

Aby usunąć ochronę chronionej maszyny wirtualnej, ale zachować już kopię zapasową danych, Usuń zasady z treści żądania i prześlij żądanie. Po usunięciu skojarzenia z zasadami kopie zapasowe nie są już wyzwalane i nie są tworzone żadne nowe punkty odzyskiwania.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Odpowiedź będzie zgodna z tym samym formatem, jak wspomniano w [przypadku wyzwalania kopii zapasowej na żądanie](#example-responses-for-on-demand-backup). Zadanie wynikowe powinno być śledzone zgodnie z opisem w [dokumencie monitorowanie zadań przy użyciu interfejsu API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Zatrzymywanie ochrony i usuwanie danych

Aby usunąć ochronę chronionej maszyny wirtualnej i usunąć również dane kopii zapasowej, wykonaj operację usuwania w sposób opisany [tutaj](/rest/api/backup/protecteditems/delete).

Zatrzymywanie ochrony i usuwanie danych jest operacją *usuwania* .

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}`I `{protectedItemName}` są tak skonstruowane jak [powyżej](#responses-to-get-operation). `{fabricName}` to "Azure". W naszym przykładzie jest to tłumaczone na:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Odpowiedzi dotyczące usuwania ochrony

*Usuwanie* ochrony jest [operacją asynchroniczną](../azure-resource-manager/management/async-operations.md). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane), gdy tworzona jest inna operacja, a następnie 204 (NoContent) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 zaakceptowane     |         |     Zaakceptowano    |

> [!IMPORTANT]
> Aby można było chronić przed przypadkowym usunięciem scenariuszy, [dostępna jest funkcja usuwania nietrwałego](use-restapi-update-vault-properties.md#soft-delete-state) dla magazynu Recovery Services. Jeśli stan nietrwałego usuwania magazynu jest ustawiony na włączone, operacja usuwania nie spowoduje natychmiastowego usunięcia danych. Będzie ono przechowywane przez 14 dni, a następnie trwale przeczyszczane. Nie jest naliczana opłata za magazyn w ciągu 14 dni. Aby cofnąć operację usuwania, zapoznaj się z [sekcją cofanie usunięcia](#undo-the-deletion).

### <a name="undo-the-deletion"></a>Cofnij usunięcie

Cofnięcie przypadkowego usunięcia jest podobne do tworzenia elementu kopii zapasowej. Po cofnięciu usunięcia element jest zachowywany, ale nie są wyzwalane żadne przyszłe kopie zapasowe.

Cofanie usunięcia jest operacją *Put* , która jest bardzo podobna do [zmiany zasad](#changing-the-policy-of-protection) i/lub [włączenia ochrony](#enabling-protection-for-the-azure-vm). Po prostu zażądaj, aby cofnąć usunięcie przy użyciu zmiennej *isRehydrate*  w [treści żądania](#example-request-body) i przesłać żądanie. Na przykład: Aby cofnąć usunięcie elementu testVM, należy użyć następującej treści żądania.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Odpowiedź będzie zgodna z tym samym formatem, jak wspomniano w [przypadku wyzwalania kopii zapasowej na żądanie](#example-responses-for-on-demand-backup). Zadanie wynikowe powinno być śledzone zgodnie z opisem w [dokumencie monitorowanie zadań przy użyciu interfejsu API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Następne kroki

[Przywróć dane z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Aby uzyskać więcej informacji na temat Azure Backup interfejsów API REST, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usługi Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)