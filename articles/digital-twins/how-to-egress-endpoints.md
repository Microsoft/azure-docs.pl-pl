---
title: Wyjście i punkty końcowe — Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się, jak tworzyć i wychodzące punkty końcowe zdarzeń w usłudze Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76511573"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Wyjście i punkty końcowe w usłudze Azure Digital bliźniaczych reprezentacji

*Punkty końcowe* usługi Azure Digital bliźniaczych reprezentacji reprezentują komunikat lub brokera zdarzeń w ramach subskrypcji platformy Azure użytkownika. Zdarzenia i komunikaty mogą być wysyłane do tematów Event Hubs, Azure Event Grid i Azure Service Bus platformy Azure.

Zdarzenia są kierowane do punktów końcowych zgodnie ze wstępnie zdefiniowanymi preferencjami routingu. Użytkownicy określają, które *typy zdarzeń* mogą otrzymać każdy punkt końcowy.

Aby dowiedzieć się więcej o zdarzeniach, routingu i typach zdarzeń, zobacz [zdarzenia routingu i komunikaty w usłudze Azure Digital bliźniaczych reprezentacji](./concepts-events-routing.md).

## <a name="events"></a>Zdarzenia

Zdarzenia są wysyłane przez obiekty IoT (takie jak urządzenia i czujniki) do przetwarzania przez brokerów komunikatów i zdarzeń platformy Azure. Zdarzenia są definiowane przez następujące [Azure Event Grid odwołanie do schematu zdarzenia](../event-grid/event-schema.md).

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Atrybut | Typ | Opis |
| --- | --- | --- |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| dane | obiekt | Dane zdarzenia specyficzne dla dostawcy zasobów. |
| Klasę | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |

Aby uzyskać więcej informacji o schemacie zdarzeń Event Grid:

- Przejrzyj [Informacje o schemacie zdarzenia Azure Event Grid](../event-grid/event-schema.md).
- Przeczytaj [Informacje o zestawie SDK środowiska Node. js w usłudze Azure EventGrid](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Typy zdarzeń

Typy zdarzeń klasyfikują charakter zdarzenia i są ustawiane w polu **EventType** . Dostępne typy zdarzeń są określone przez następującą listę:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Formaty zdarzeń dla każdego typu zdarzenia są opisane w poniższych podsekcjach.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** dotyczy zmian grafu. Właściwość **subject** określa typ obiektu, którego to dotyczy. Następujące typy obiektów mogą wyzwolić to zdarzenie:

- Urządzenie
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- Magazynu kluczy
- Raport
- Definicji
- Czujnik
- SensorBlobMetadata
- SensorExtendedProperty
- Miejsce
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- System
- Użytkownik
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Przykład

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** to zdarzenie wysyłane przez funkcję zdefiniowaną przez użytkownika (UDF).
  
> [!IMPORTANT]  
> To zdarzenie musi być jawnie wysłane z samego formatu UDF.

#### <a name="example"></a>Przykład

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="sensorchange"></a>SensorChange

**SensorChange** to aktualizacja stanu czujnika oparta na zmianach telemetrycznych.

#### <a name="example"></a>Przykład

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** to aktualizacja stanu miejsca oparta na zmianach telemetrycznych.

#### <a name="example"></a>Przykład

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="devicemessage"></a>DeviceMessage

Za pomocą **DeviceMessage**można określić połączenie **EventHub** , do którego mogą być kierowane zdarzenia danych telemetrycznych również z usługi Azure Digital bliźniaczych reprezentacji.

> [!NOTE]
> - **DeviceMessage** jest kombinacją tylko z **centrum EventHub**. Nie można połączyć **DeviceMessage** z żadnym innym typem zdarzenia.
> - Można określić tylko jeden punkt końcowy kombinacji typu **EventHub** lub **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Zarządzanie punktami końcowymi odbywa się za pomocą interfejsu API punktów końcowych.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

W poniższych przykładach pokazano, jak skonfigurować obsługiwane punkty końcowe.

>[!IMPORTANT]
> Zwróć szczególną uwagę na atrybut **eventtypes** . Definiuje, które typy zdarzeń są obsługiwane przez punkt końcowy, a tym samym określają jego Routing.

Uwierzytelnione żądanie HTTP POST względem:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Kierowanie do Service Bus typów zdarzeń **SensorChange**, **SpaceChange**i **TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_NAMESPACE | Przestrzeń nazw punktu końcowego |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

- Kierowanie do Event Grid typów zdarzeń **SensorChange**, **SpaceChange**i **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania|
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

- Kierowanie do Event Hubs typów zdarzeń **SensorChange**, **SpaceChange**i **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_NAMESPACE | Przestrzeń nazw punktu końcowego |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_EVENT_HUB_NAME | Nazwa centrum zdarzeń |

- Roześlij do Event Hubs typu zdarzenia **DeviceMessage**. Włączenie elementu `EntityPath` **ConnectionString** jest obowiązkowe:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_NAMESPACE | Przestrzeń nazw punktu końcowego |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_EVENT_HUB_NAME | Nazwa centrum zdarzeń |

> [!NOTE]  
> Po utworzeniu nowego punktu końcowego może upłynąć do 5 – 10 minut od rozpoczęcia otrzymywania zdarzeń w punkcie końcowym.

## <a name="primary-and-secondary-connection-keys"></a>Podstawowe i pomocnicze klucze połączenia

Gdy klucz połączenia podstawowego zostanie autoryzowany, system automatycznie próbuje pomocniczy klucz połączenia. Zapewnia to kopię zapasową i umożliwia bezpieczne uwierzytelnianie klucza podstawowego i aktualizowanie go za pomocą interfejsu API punktów końcowych.

Jeśli zarówno podstawowy, jak i pomocniczy klucz połączenia są nieautoryzowane, system wprowadzi wykładniczy czas oczekiwania wynoszący maksymalnie 30 minut. Zdarzenia są porzucane po każdym wyzwoleniu czasu oczekiwania na wycofanie.

Zawsze, gdy system jest w stanie oczekiwania na wycofanie, aktualizacja kluczy połączeń za pomocą interfejsu API punktów końcowych może zająć do 30 minut.

## <a name="unreachable-endpoints"></a>Nieosiągalne punkty końcowe

Gdy punkt końcowy jest nieosiągalny, system wprowadza wykładniczy czas oczekiwania wynoszący maksymalnie 30 minut. Zdarzenia są porzucane po każdym wyzwoleniu czasu oczekiwania na wycofanie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak używać usługi Azure Digital bliźniaczych reprezentacji Swagger](how-to-use-swagger.md).

- Dowiedz się więcej na temat [routingu zdarzeń i komunikatów](concepts-events-routing.md) w usłudze Azure Digital bliźniaczych reprezentacji.
