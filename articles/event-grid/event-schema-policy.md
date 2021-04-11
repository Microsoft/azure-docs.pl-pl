---
title: Azure Policy jako źródło Event Grid
description: W tym artykule opisano sposób użycia Azure Policy jako źródła zdarzeń Event Grid. Zawiera schemat i linki do samouczka oraz artykuły z artykułami.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735082"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Azure Policy jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń [Azure Policy](../governance/policy/index.yml) . Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](./event-schema.md). Zawiera również listę przewodników Szybki Start i samouczków do użycia Azure Policy jako źródło zdarzenia.

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Azure Policy emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Uruchamiany podczas tworzenia stanu zgodności z zasadami. |
| Microsoft. PolicyInsights. PolicyStateChanged | Uruchamiany po zmianie stanu zgodności z zasadami. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Uruchamiany po usunięciu stanu zgodności z zasadami. |

## <a name="example-event"></a>Przykładowe zdarzenie

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)
W poniższym przykładzie przedstawiono schemat zdarzenia utworzonego przez stan zasad: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Schemat zdarzenia zmiany stanu zasad jest podobny: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

W poniższym przykładzie przedstawiono schemat zdarzenia utworzonego przez stan zasad: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

Schemat zdarzenia zmiany stanu zasad jest podobny: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Właściwości zdarzenia

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `topic` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | W pełni kwalifikowany identyfikator zasobu, dla którego jest zmieniana zmiana stanu zgodności, w tym nazwę zasobu i typ zasobu. Używa formatu, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `eventTime` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `data` | object | Azure Policy dane zdarzenia. |
| `dataVersion` | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| `metadataVersion` | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `source` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | W pełni kwalifikowany identyfikator zasobu, dla którego jest zmieniana zmiana stanu zgodności, w tym nazwę zasobu i typ zasobu. Używa formatu, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `time` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `data` | object | Azure Policy dane zdarzenia. |
| `specversion` | ciąg | Wersja specyfikacji schematu CloudEvents. |

---

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `timestamp` | ciąg | Czas (UTC), przez który zasób został zeskanowany przez Azure Policy. W przypadku określania kolejności zdarzeń Użyj tej właściwości zamiast najwyższego poziomu `eventTime` lub `time` właściwości. |
| `policyAssignmentId` | ciąg | Identyfikator zasobu przypisania zasad. |
| `policyDefinitionId` | ciąg | Identyfikator zasobu definicji zasad. |
| `policyDefinitionReferenceId` | ciąg | Identyfikator odwołania dla definicji zasad w definicji inicjatywy, jeśli przypisanie zasad dotyczy inicjatywy. Może być pusty. |
| `complianceState` | ciąg | Stan zgodności zasobu w odniesieniu do przypisania zasad. |
| `subscriptionId` | ciąg | Identyfikator subskrypcji zasobu. |
| `complianceReasonCode` | ciąg | Kod przyczyny zgodności. Może być pusty. |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe instrukcje routingu Azure Policy zdarzenia zmiany stanu, zobacz [używanie Event Grid do powiadomień o zmianach stanu zasad](../governance/policy/tutorials/route-state-change-events.md).
- Aby zapoznać się z omówieniem integracji Azure Policy z Event Grid, zobacz temat [reagowanie na zdarzenia Azure Policy przy użyciu Event Grid](../governance/policy/concepts/event-overview.md).
- Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](./overview.md)
- Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](./subscription-creation-schema.md).