---
title: Oddziałanie do Azure Policy zdarzeń zmiany stanu
description: Użyj Azure Event Grid, aby subskrybować zdarzenia zasad aplikacji, dzięki czemu aplikacje mogą reagować na zmiany stanu bez konieczności stosowania skomplikowanego kodu.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735099"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Oddziałanie do Azure Policy zdarzeń zmiany stanu

Zdarzenia Azure Policy umożliwiają aplikacjom reagowanie na zmiany stanu. Ta integracja odbywa się bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zamiast tego zdarzenia są wypychane za pośrednictwem [Azure Event Grid](../../../event-grid/index.yml) do subskrybentów, takich jak [Azure Functions](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml)lub nawet do własnego niestandardowego odbiornika http.
W sposób krytyczny płacisz tylko za to, czego używasz.

Zdarzenia Azure Policy są wysyłane do Azure Event Grid, co zapewnia niezawodne usługi dostarczania dla aplikacji dzięki rozbudowanym zasadom ponawiania prób i dostarczaniu wiadomości utraconych. Aby dowiedzieć się więcej, zobacz [Event Grid dostarczania komunikatów i ponów próbę](../../../event-grid/delivery-and-retry.md).

Typowy scenariusz zdarzeń Azure Policy jest śledzony w przypadku zmiany stanu zgodności zasobu podczas oceny zasad. Architektura oparta na zdarzeniach to wydajny sposób na reagowanie na te zmiany zamiast skanowania stanu zgodności zasobów zgodnie z ustalonym harmonogramem.

> [!NOTE]
> Zdarzenia zmiany stanu Azure Policy są wysyłane do Event Grid po zakończeniu oceny zasobów przez [wyzwalacz szacowania](../how-to/get-compliance-data.md#evaluation-triggers) .

Aby uzyskać pełny samouczek, zobacz [zdarzenia zmiany stanu zasad trasy do Event Grid za pomocą interfejsu wiersza polecenia platformy Azure](../tutorials/route-state-change-events.md) .

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Model źródeł i procedur obsługi usługi Event Grid" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Dostępne zdarzenia Azure Policy

Funkcja Event Grid używa [subskrypcji zdarzeń](../../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Subskrypcje zdarzeń Azure Policy mogą zawierać trzy typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Uruchamiany podczas tworzenia stanu zgodności z zasadami. |
| Microsoft. PolicyInsights. PolicyStateChanged | Uruchamiany po zmianie stanu zgodności z zasadami. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Uruchamiany po usunięciu stanu zgodności z zasadami. |

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia Azure Policy zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Możesz zidentyfikować zdarzenie Azure Policy, gdy `eventType` Właściwość zaczyna się od "Microsoft. PolicyInsights".
Dodatkowe informacje na temat użycia właściwości zdarzeń Event Grid są udokumentowane w  
[Schemat zdarzenia Event Grid](../../../event-grid/event-schema.md).

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `topic` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | W pełni kwalifikowany identyfikator zasobu, dla którego jest zmieniana zmiana stanu zgodności, w tym nazwę zasobu i typ zasobu. Używa formatu, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | object | Azure Policy dane zdarzenia. |
| `data.timestamp` | ciąg | Czas (UTC), przez który zasób został zeskanowany przez Azure Policy. W przypadku określania kolejności zdarzeń Użyj tej właściwości zamiast najwyższego poziomu `eventTime` lub `time` właściwości. |
| `data.policyAssignmentId` | ciąg | Identyfikator zasobu przypisania zasad. |
| `data.policyDefinitionId` | ciąg | Identyfikator zasobu definicji zasad. |
| `data.policyDefinitionReferenceId` | ciąg | Identyfikator odwołania dla definicji zasad w definicji inicjatywy, jeśli przypisanie zasad dotyczy inicjatywy. Może być pusty. |
| `data.complianceState` | ciąg | Stan zgodności zasobu w odniesieniu do przypisania zasad. |
| `data.subscriptionId` | ciąg | Identyfikator subskrypcji zasobu. |
| `data.complianceReasonCode` | ciąg | Kod przyczyny zgodności. Może być pusty. |
| `eventType` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `eventTime` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `dataVersion` | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| `metadataVersion` | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Oto przykład zdarzenia zmiany stanu zasad:

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

Aby uzyskać więcej informacji, zobacz [schemat zdarzeń Azure Policy](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń

Aplikacje, które obsługują zdarzenia Azure Policy, powinny przestrzegać następujących zalecanych praktyk:

> [!div class="checklist"]
> - Można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, dlatego nie zakłada się, że zdarzenia pochodzą z określonego źródła. Zamiast tego Sprawdź temat wiadomości, aby upewnić się, że dla przypisania zasad, definicji zasad i zasobu jest to zdarzenie zmiany stanu.
> - Sprawdź `eventType` i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwane typy.
> - Służy `data.timestamp` do określania kolejności zdarzeń w Azure Policy, a nie najwyższego poziomu `eventTime` lub `time` właściwości.
> - Użyj pola podmiot, aby uzyskać dostęp do zasobu, który miał zmianę stanu zasad.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i Udziel Azure Policy zdarzeń zmiany stanu:

- [Zdarzenia zmiany stanu zasad trasy do Event Grid przy użyciu interfejsu wiersza polecenia platformy Azure](../tutorials/route-state-change-events.md)
- [Azure Policy szczegóły schematu Event Grid](../../../event-grid/event-schema-policy.md)
- [Event Grid — informacje](../../../event-grid/overview.md)