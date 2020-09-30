---
title: Aktualizowanie konfiguracji magazynu Recovery Services przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak zaktualizować konfigurację magazynu za pomocą interfejsu API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 19a335d17ee0aa5ff9f989556656f5cf20d2b1a9
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567829"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Aktualizowanie konfiguracji magazynu Recovery Services platformy Azure przy użyciu interfejsu API REST

W tym artykule opisano sposób aktualizowania konfiguracji związanych z kopiami zapasowymi w usłudze Azure Recovery Services magazyn przy użyciu interfejsu API REST.

## <a name="soft-delete-state"></a>Stan usuwania nietrwałego

Usuwanie kopii zapasowych chronionego elementu jest istotną operacją, która musi być monitorowana. Aby chronić przed przypadkowym usunięciem, magazyn usługi Azure Recovery Services ma możliwość usuwania nietrwałego. Ta funkcja umożliwia przywrócenie usuniętych kopii zapasowych, w razie potrzeby, w czasie po usunięciu.

Ale istnieją scenariusze, w których ta funkcja nie jest wymagana. Nie można usunąć magazynu usługi Azure Recovery Services, jeśli w nim znajdują się elementy kopii zapasowej, nawet nietrwałe usunięte. Może to stanowić problem, jeśli magazyn musi być natychmiast usunięty. Na przykład: operacje wdrażania często czyści utworzone zasoby w tym samym przepływie pracy. Wdrożenie może utworzyć magazyn, skonfigurować kopie zapasowe dla elementu, wykonać przywracanie testowe, a następnie wykonać operację usuwania elementów kopii zapasowych i magazynu. Jeśli usunięcie magazynu nie powiedzie się, całe wdrożenie może się nie powieść. Wyłączenie usuwania nietrwałego jest jedynym sposobem na zagwarantowanie natychmiastowego usunięcia.

W zależności od scenariusza należy uważnie wybierać, czy wyłączyć usuwanie nietrwałe dla określonego magazynu. Aby uzyskać więcej informacji, zobacz [artykuł usuwanie nietrwałe](backup-azure-security-feature-cloud.md).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Pobieranie stanu nietrwałego usuwania przy użyciu interfejsu API REST

Domyślnie stan usuwania nietrwałego zostanie włączony dla nowo utworzonego magazynu Recovery Services. Aby pobrać/zaktualizować stan usuwania nietrwałego dla magazynu, użyj [dokumentu interfejsu API REST](/rest/api/backup/backupresourcevaultconfigs) powiązanego z konfiguracją magazynu kopii zapasowych.

Aby pobrać bieżący stan usuwania nietrwałego dla magazynu, użyj następującej operacji *Get*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Identyfikator URI Get zawiera `{subscriptionId}` `{vaultName}` `{vaultresourceGroupName}` parametry. W tym przykładzie `{vaultName}` jest to "testVault" i `{vaultresourceGroupName}` ma wartość "testVaultRG". Ponieważ wszystkie wymagane parametry są określone w identyfikatorze URI, nie ma potrzeby oddzielnej treści żądania.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Odpowiedzi

Poniższa odpowiedź dla operacji "GET" została pokazana poniżej:

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Przykładowa odpowiedź

Po przesłaniu żądania "GET" zostanie zwrócona odpowiedź 200 (powodzenie).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Zaktualizuj stan usuwania nietrwałego za pomocą interfejsu API REST

Aby zaktualizować stan nietrwałego usuwania magazynu Recovery Services przy użyciu interfejsu API REST, należy użyć następującej operacji *Put*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Identyfikator URI Put ma wartość `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` parametr. W tym przykładzie `{vaultName}` jest to "testVault" i `{vaultresourceGroupName}` ma wartość "testVaultRG". Jeśli zamienimy identyfikator URI o powyższe wartości, identyfikator URI będzie wyglądać następująco.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do tworzenia treści żądania

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu API REST](/rest/api/backup/backupresourcevaultconfigs/update#request-body) .

|Nazwa  |Wymagany  |Typ  |Opis  |
|---------|---------|---------|---------|
|Element ETag     |         |   Ciąg      |  Opcjonalny element eTag       |
|location     |  true       |Ciąg         |   Lokalizacja zasobu      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Właściwości magazynu       |
|tags     |         | Obiekt        |     Tagi zasobów    |

#### <a name="example-request-body"></a>Przykładowa treść żądania

Poniższy przykład służy do aktualizowania stanu nietrwałego usuwania do "Disabled".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>Odpowiedzi dla operacji PATCH

Poniższa odpowiedź dla operacji "PATCH" została pokazana poniżej:

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response-for-the-patch-operation"></a>Przykładowa odpowiedź dla operacji PATCH

Po przesłaniu żądania "Poprawka" zostanie zwrócona odpowiedź 200 (powodzenie).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Następne kroki

[Utwórz zasady tworzenia kopii zapasowej dla tworzenia kopii zapasowej maszyny wirtualnej platformy Azure w tym magazynie](backup-azure-arm-userestapi-createorupdatepolicy.md).

Aby uzyskać więcej informacji na temat interfejsów API REST platformy Azure, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usługi Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
