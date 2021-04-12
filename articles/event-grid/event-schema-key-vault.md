---
title: Azure Key Vault jako źródło Event Grid
description: Opisuje właściwości i schemat udostępnione dla zdarzeń Azure Key Vault z Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363410"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń w [Azure Key Vault](../key-vault/index.yml). Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).


## <a name="available-event-types"></a>Dostępne typy zdarzeń

Konto Azure Key Vault generuje następujące typy zdarzeń:

| Pełna nazwa zdarzenia | Nazwa wyświetlana zdarzenia | Opis |
| ---------- | ----------- |---|
| Microsoft. CertificateNewVersionCreated — magazyn. | Utworzono nową wersję certyfikatu | Wyzwalane, gdy zostanie utworzony nowy certyfikat lub Nowa wersja certyfikatu. |
| Microsoft. CertificateNearExpiry — magazyn. | Certyfikat niedługo wygaśnie | Wyzwalane, gdy bieżąca wersja certyfikatu wkrótce wygaśnie. (Zdarzenie jest wyzwalane przez 30 dni przed datą wygaśnięcia). |
| Microsoft. CertificateExpired — magazyn. | Certyfikat wygasł | Wyzwalane po wygaśnięciu certyfikatu. |
| Microsoft. KeyNewVersionCreated — magazyn. | Utworzono nową wersję klucza | Wyzwalane, gdy zostanie utworzony nowy klucz lub Nowa wersja klucza. |
| Microsoft. KeyNearExpiry — magazyn. | Klucz bliski wygaśnięcia | Wyzwalane, gdy bieżąca wersja klucza wkrótce wygaśnie. (Zdarzenie jest wyzwalane przez 30 dni przed datą wygaśnięcia). |
| Magazyn kluczy Microsoft. kluczy. wygasł | Klucz wygasł | Wyzwalane po wygaśnięciu klucza. |
| Microsoft. SecretNewVersionCreated — magazyn. | Utworzono wpis tajny nowej wersji | Wyzwalane, gdy zostanie utworzona nowa wartość tajna lub Nowa wersja wpisu tajnego. |
| Microsoft. SecretNearExpiry — magazyn. | Wpis tajny blisko wygaśnięcia | Wyzwalane, gdy bieżąca wersja wpisu tajnego wkrótce wygaśnie. (Zdarzenie jest wyzwalane przez 30 dni przed datą wygaśnięcia). |
| Microsoft. SecretExpired — magazyn. | Klucz tajny wygasł | Wyzwalane po wygaśnięciu wpisu tajnego. |
| Microsoft. VaultAccessPolicyChanged — magazyn. | Zmieniono zasady dostępu do magazynu | Wyzwalane, gdy zmieniono zasady dostępu Key Vault. Zawiera scenariusz, gdy Key Vault model uprawnień zostanie zmieniony na/z kontroli dostępu opartej na rolach na platformie Azure.   |

## <a name="event-examples"></a>Przykłady zdarzeń

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

Poniższy przykład przedstawia schemat dla elementu **Microsoft. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

Poniższy przykład przedstawia schemat dla elementu **Microsoft. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Właściwości zdarzenia

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)
Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `topic` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| `eventType` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `eventTime` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `data` | object | Dane zdarzenia konfiguracji aplikacji. |
| `dataVersion` | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| `metadataVersion` | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |


# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `source` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| `type` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `time` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `data` | object | Dane zdarzenia konfiguracji aplikacji. |
| `specversion` | ciąg | Wersja specyfikacji schematu CloudEvents. |

---
 

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| ---------- | ----------- |---|
| `id` | ciąg | Identyfikator obiektu, który wyzwolił to zdarzenie |
| `vaultName` | ciąg | Nazwa magazynu kluczy obiektu, który wyzwolił to zdarzenie |
| `objectType` | ciąg | Typ obiektu, który wyzwolił to zdarzenie. |
| `objectName` | ciąg | Nazwa obiektu, który wyzwolił to zdarzenie |
| `version` | ciąg | Wersja obiektu, który wyzwolił to zdarzenie |
| `nbf` | liczba | Data nie wcześniejsza (w sekundach) od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |
| `exp` | liczba | Data wygaśnięcia (w sekundach) od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Monitorowanie zdarzeń Key Vault z Azure Event Grid](../key-vault/general/event-grid-overview.md) | Omówienie integrowania Key Vault z Event Grid. |
| [Samouczek: Tworzenie i monitorowanie zdarzeń Key Vault przy użyciu Event Grid](../key-vault/general/event-grid-logicapps.md) | Dowiedz się, jak skonfigurować Event Grid powiadomienia dla Key Vault. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby uzyskać więcej informacji na temat Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md)

