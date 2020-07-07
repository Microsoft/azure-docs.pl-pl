---
title: Azure Key Vault jako źródło Event Grid
description: Opisuje właściwości i schemat udostępnione dla zdarzeń Azure Key Vault z Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 40bff9585e64163039a8847ff868c982ffb20414
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81458253"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń w [Azure Key Vault](../key-vault/index.yml), obecnie w wersji zapoznawczej. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

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

### <a name="event-examples"></a>Przykłady zdarzeń

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

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| ---------- | ----------- |---|
| identyfikator | ciąg | Identyfikator obiektu, który wyzwolił to zdarzenie |
| vaultName | ciąg | Nazwa magazynu kluczy obiektu, który wyzwolił to zdarzenie |
| objectType | ciąg | Typ obiektu, który wyzwolił to zdarzenie. |
| Obiektu | ciąg | Nazwa obiektu, który wyzwolił to zdarzenie |
| version | ciąg | Wersja obiektu, który wyzwolił to zdarzenie |
| NBF | liczba | Data nie wcześniejsza (w sekundach) od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |
| exp | liczba | Data wygaśnięcia (w sekundach) od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Monitorowanie zdarzeń Key Vault z Azure Event Grid](../key-vault/general/event-grid-overview.md) | Omówienie integrowania Key Vault z Event Grid. |
| [Samouczek: Tworzenie i monitorowanie zdarzeń Key Vault przy użyciu Event Grid](../key-vault/general/event-grid-tutorial.md) | Dowiedz się, jak skonfigurować Event Grid powiadomienia dla Key Vault. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby dowiedzieć się więcej na temat integracji Key Vault z usługą Event Grid, zobacz [monitorowanie Key Vault z Azure Event Grid (wersja zapoznawcza)](../key-vault/general/event-grid-overview.md).
* Aby zapoznać się z samouczkiem dotyczącym Key Vault integracji z Event Grid, zobacz [Odbieranie i reagowanie na powiadomienia magazynu kluczy za pomocą Azure Event Grid (wersja zapoznawcza)](../key-vault/general/event-grid-tutorial.md).
* Aby uzyskać dodatkowe wskazówki dotyczące Key Vault i Azure Automation, zobacz:
    - [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
    - [Key Vault monitorowania z Azure Event Grid (wersja zapoznawcza)](../key-vault/general/event-grid-overview.md)
    - [Odbieraj powiadomienia dotyczące magazynu kluczy i odpowiadaj na nie Azure Event Grid (wersja zapoznawcza)](../key-vault/general/event-grid-tutorial.md)
    - [Przegląd Azure Automation](../automation/index.yml)
