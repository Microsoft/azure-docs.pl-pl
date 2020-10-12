---
title: Usługa Azure Signaler jako źródło Event Grid
description: Opisuje właściwości, które są dostępne dla zdarzeń usługi Azure Signal z Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2ac391f366c4b9a82741a1b6b3135f5d7b5fe331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86106655"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Schemat zdarzeń Azure Event Grid dla usługi sygnalizującego

Ten artykuł zawiera właściwości i schemat zdarzeń usługi sygnalizującej.Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). Zawiera również listę przewodników Szybki Start i samouczków umożliwiających korzystanie z usługi Azure Signal jako źródła zdarzeń.

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa sygnalizująca emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | Uruchamiany, gdy połączenie z klientem zostało nawiązane. |
| Microsoft. SignalRService. ClientConnectionDisconnected | Uruchamiany, gdy połączenie z klientem zostało rozłączone. |

### <a name="example-event"></a>Przykładowe zdarzenie

Poniższy przykład przedstawia schemat zdarzenia połączonego z połączeniem klienta: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia rozłączenia połączenia klienta jest podobny do tego: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Dane zdarzenia usługi sygnalizującej. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| sygnatura czasowa | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| hubName | ciąg | Centrum, do którego należy połączenie z klientem. |
| connectionId | ciąg | Unikatowy identyfikator połączenia z klientem. |
| userId | ciąg | Identyfikator użytkownika zdefiniowany w ramach żądania. |
| errorMessage | ciąg | Błąd powodujący rozłączenie połączenia. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł | Opis |
|---------|---------|
| [Reagowanie na zdarzenia usługi Azure sygnalizujące przy użyciu Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Omówienie integrowania usługi Azure Signal z Event Grid. |
| [Jak wysyłać zdarzenia usługi Azure sygnalizacyjne do Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Pokazuje, w jaki sposób wysyłać zdarzenia usługi Azure Signal do aplikacji za pomocą Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
