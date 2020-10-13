---
title: Tworzenie magazynów Recovery Services przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak zarządzać operacją tworzenia kopii zapasowej i przywracania kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu interfejsu API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: a37808548ec58977b7d6af16c75b94b7b5efe446
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271600"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Tworzenie magazynu Recovery Services platformy Azure przy użyciu interfejsu API REST

Procedurę tworzenia magazynu usługi Azure Recovery Services przy użyciu interfejsu API REST opisano w temacie Tworzenie dokumentacji [interfejsu API REST magazynu](/rest/api/recoveryservices/vaults/createorupdate) . Skorzystajmy z tego dokumentu jako odniesienia do tworzenia magazynu o nazwie "testVault" w "zachodnie stany USA".

Aby utworzyć lub zaktualizować magazyn usługi Azure Recovery Services, użyj następującej operacji *Put* .

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Utwórz żądanie

Aby można było utworzyć żądanie *Put* , `{subscription-id}` parametr jest wymagany. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli). Należy zdefiniować `{resourceGroupName}` i `{vaultName}` dla zasobów wraz z `api-version` parametrem. W tym artykule `api-version=2016-06-01` .

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Content-Type:*  | Wymagany. Ustaw wartość `application/json`. |
| *Authorization:* | Wymagany. Ustaw na prawidłowy [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

Aby uzyskać więcej informacji na temat sposobu tworzenia żądania, zobacz [składniki żądania/odpowiedzi interfejsu API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do kompilowania treści żądania:

|Nazwa  |Wymagany  |Typ  |Opis  |
|---------|---------|---------|---------|
|Element ETag     |         |   Ciąg      |  Opcjonalny element eTag       |
|location     |  true       |Ciąg         |   Lokalizacja zasobu      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Właściwości magazynu       |
|sku     |         |  [Magazyn](/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identyfikuje unikatowy identyfikator systemowy dla poszczególnych zasobów platformy Azure     |
|tags     |         | Obiekt        |     Tagi zasobów    |

Należy pamiętać, że nazwa magazynu i nazwa grupy zasobów są podane w identyfikatorze URI. Treść żądania definiuje lokalizację.

## <a name="example-request-body"></a>Przykładowa treść żądania

Poniższy Przykładowa treść służy do tworzenia magazynu w regionie "zachodnie stany USA". Określ lokalizację. Jednostka SKU jest zawsze "Standardowa".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Odpowiedzi

Istnieją dwie Pomyślne odpowiedzi na operację tworzenia lub aktualizowania magazynu Recovery Services:

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |   [Magazyn](/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Utworzono     | [Magazyn](/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Utworzone      |

Aby uzyskać więcej informacji na temat odpowiedzi interfejsu API REST, zobacz [Przetwarzanie komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Przykładowa odpowiedź

Skrócona odpowiedź *201 utworzona* na podstawie poprzedniego przykładowej treści żądania pokazuje, że *Identyfikator* został przypisany, a *provisioningState* *powiodło*się:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Następne kroki

[Utwórz zasady tworzenia kopii zapasowej dla tworzenia kopii zapasowej maszyny wirtualnej platformy Azure w tym magazynie](backup-azure-arm-userestapi-createorupdatepolicy.md).

Aby uzyskać więcej informacji na temat interfejsów API REST platformy Azure, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usługi Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
