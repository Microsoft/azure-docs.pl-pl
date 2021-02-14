---
title: IoT Hub platformy Azure jako źródło Event Grid
description: Ten artykuł zawiera właściwości i schemat zdarzeń usługi Azure IoT Hub. Wyświetla listę dostępnych typów zdarzeń, zdarzenie przykładowe i właściwości zdarzenia.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 5f43b9d0041fa5842bc2557a61c5145ce588758a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363530"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>IoT Hub platformy Azure jako źródło Event Grid
Ten artykuł zawiera właściwości i schemat zdarzeń usługi Azure IoT Hub. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). 

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Azure IoT Hub emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Opublikowano, gdy urządzenie jest zarejestrowane w usłudze IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Opublikowano, gdy urządzenie zostanie usunięte z Centrum IoT Hub. | 
| Microsoft.Devices.DeviceConnected | Opublikowano, gdy urządzenie jest połączone z usługą IoT Hub. |
| Microsoft.Devices.DeviceDisconnected | Opublikowano po odłączeniu urządzenia od centrum IoT Hub. | 
| Microsoft.Devices.DeviceTelemetry | Opublikowano, gdy komunikat telemetrii zostanie wysłany do centrum IoT Hub. |

## <a name="example-event"></a>Przykładowe zdarzenie

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

Schemat dla zdarzeń DeviceConnected i DeviceDisconnected ma tę samą strukturę. To przykładowe zdarzenie pokazuje schemat zdarzenia wywoływanego, gdy urządzenie jest połączone z Centrum IoT:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

Zdarzenie DeviceTelemetry jest zgłaszane w przypadku wysłania zdarzenia telemetrii do IoT Hub. Poniżej przedstawiono przykładowy schemat dla tego zdarzenia.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Schemat dla zdarzeń DeviceCreated i DeviceDeleted ma tę samą strukturę. To przykładowe zdarzenie pokazuje schemat zdarzenia wywoływanego, gdy urządzenie jest zarejestrowane w usłudze IoT Hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

Schemat dla zdarzeń DeviceConnected i DeviceDisconnected ma tę samą strukturę. To przykładowe zdarzenie pokazuje schemat zdarzenia wywoływanego, gdy urządzenie jest połączone z Centrum IoT:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceConnected", 
  "time": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "specversion": "1.0"
}]
```

Zdarzenie DeviceTelemetry jest zgłaszane w przypadku wysłania zdarzenia telemetrii do IoT Hub. Poniżej przedstawiono przykładowy schemat dla tego zdarzenia.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceTelemetry",
  "time": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "specversion": "1.0"
}]
```

Schemat dla zdarzeń DeviceCreated i DeviceDeleted ma tę samą strukturę. To przykładowe zdarzenie pokazuje schemat zdarzenia wywoływanego, gdy urządzenie jest zarejestrowane w usłudze IoT Hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "type": "Microsoft.Devices.DeviceCreated",
  "time": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Właściwości zdarzenia

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

Wszystkie zdarzenia zawierają te same dane najwyższego poziomu: 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `topic` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| `eventType` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `eventTime` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `data` | object | IoT Hub dane zdarzenia.  |
| `dataVersion` | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| `metadataVersion` | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

Wszystkie zdarzenia zawierają te same dane najwyższego poziomu: 


| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `source` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| `type` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `time` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `data` | object | IoT Hub dane zdarzenia.  |
| `specversion` | ciąg | Wersja specyfikacji schematu CloudEvents. |

---

Dla wszystkich zdarzeń IoT Hub obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `hubName` | ciąg | Nazwa IoT Hub, w którym urządzenie zostało utworzone lub usunięte. |
| `deviceId` | ciąg | Unikatowy identyfikator urządzenia. Ten ciąg z rozróżnianiem wielkości liter może mieć długość do 128 znaków i obsługuje znaki alfanumeryczne ASCII 7-bitowe oraz następujące znaki specjalne: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |

Zawartość obiektu danych różni się w zależności od wydawcy zdarzeń. 

W przypadku **urządzeń podłączonych** i **odłączonych** IoT Hub zdarzeń obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `moduleId` | ciąg | Unikatowy identyfikator modułu. To pole jest wyprowadzane tylko dla urządzeń modułowych. Ten ciąg z rozróżnianiem wielkości liter może mieć długość do 128 znaków i obsługuje znaki alfanumeryczne ASCII 7-bitowe oraz następujące znaki specjalne: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |
| `deviceConnectionStateEventInfo` | object | Informacje o zdarzeniu stanu połączenia urządzenia
| `sequenceNumber` | ciąg | Liczba, która pomaga wskazać kolejność zdarzeń podłączonych do urządzenia lub odłączonych do urządzenia. Najnowsze zdarzenie będzie miało numer sekwencyjny większy niż poprzednie zdarzenie. Ta liczba może ulec zmianie o więcej niż 1, ale jest ściśle większa. Zobacz [, jak używać numeru sekwencyjnego](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

W przypadku IoT Hub zdarzeń **telemetrii urządzenia** obiekt danych zawiera komunikat z urządzenia do chmury w [formacie komunikatu usługi IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md) i ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `body` | ciąg | Zawartość komunikatu z urządzenia. |
| `properties` | ciąg | Właściwości aplikacji są ciągami zdefiniowanymi przez użytkownika, które można dodać do wiadomości. Te pola są opcjonalne. |
| `system properties` | ciąg | [Właściwości systemu](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) pomagają identyfikować zawartość i źródło komunikatów. Komunikat telemetrii urządzenia musi znajdować się w prawidłowym formacie JSON z atrybutem ContentType ustawionym na wartość JSON i contentEncoding ustawionym na UTF-8 we właściwościach systemu komunikatów. Jeśli ta wartość nie jest ustawiona, IoT Hub będzie zapisywać komunikaty w standardowym formacie 64.  |

W przypadku urządzeń, które zostały **utworzone** i zostały **usunięte** IoT Hub zdarzenia, obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `twin` | object | Informacje o bliźniaczych urządzeniach, które są reprezentacją metadanych urządzeń aplikacji w chmurze. | 
| `deviceID` | ciąg | Unikatowy identyfikator sznurka urządzenia. | 
| `etag` | ciąg | Moduł sprawdzania poprawności służący do zapewnienia spójności aktualizacji dla sznurka urządzenia. Każdy element ETag jest gwarantowany jako unikatowy dla poszczególnych sznurów urządzeń. |  
| `deviceEtag` | ciąg | Moduł sprawdzania poprawności służący do zapewnienia spójności aktualizacji rejestru urządzeń. Każdy deviceEtag ma być unikatowy dla każdego rejestru urządzenia. |
| `status` | ciąg | Czy dwuosiowy urządzenie jest włączone czy wyłączone. | 
| `statusUpdateTime` | ciąg | ISO8601 sygnatura czasowa ostatniej aktualizacji stanu przędzy urządzenia. |
| `connectionState` | ciąg | Czy urządzenie jest połączone czy odłączone. | 
| `lastActivityTime` | ciąg | Sygnatura czasowa ISO8601 ostatniego działania. | 
| `cloudToDeviceMessageCount` | liczba całkowita | Liczba komunikatów z chmury do urządzeń wysyłanych do tego urządzenia. | 
| `authenticationType` | ciąg | Typ uwierzytelniania używany dla tego urządzenia: `SAS` , `SelfSigned` lub `CertificateAuthority` . |
| `x509Thumbprint` | ciąg | Odcisk palca to unikatowa wartość certyfikatu x509, często używana do znajdowania określonego certyfikatu w magazynie certyfikatów. Odcisk palca jest generowany dynamicznie przy użyciu algorytmu SHA1 i nie istnieje fizycznie w certyfikacie. | 
| `primaryThumbprint` | ciąg | Podstawowy odcisk palca certyfikatu x509. |
| `secondaryThumbprint` | ciąg | Pomocniczy odcisk palca certyfikatu x509. | 
| `version` | liczba całkowita | Liczba całkowita, która jest zwiększana o jeden po każdej aktualizacji sznurka urządzenia. |
| `desired` | object | Część właściwości, które mogą być zapisywane tylko przez zaplecze aplikacji i odczytywane przez urządzenie. | 
| `reported` | object | Część właściwości, które mogą być zapisywane tylko przez urządzenie i odczytywana przez zaplecze aplikacji. |
| `lastUpdated` | ciąg | Sygnatura czasowa ISO8601 ostatniej aktualizacji właściwości sznurka urządzenia. | 

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Wysyłanie powiadomień e-mail o zdarzeniach usługi Azure IoT Hub przy użyciu usługi Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie zostanie dodane do IoT Hub. |
| [Reagowanie na zdarzenia IoT Hub przy użyciu Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md) | Omówienie integrowania IoT Hub z Event Grid. |
| [Zamawianie połączonych urządzeń i zdarzeń odłączonych do urządzenia](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Pokazuje, jak zamówić zdarzenia stanu połączenia urządzenia. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby dowiedzieć się, jak IoT Hub i Event Grid współdziałać, zobacz temat [reagowanie na zdarzenia IoT Hub przy użyciu Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md).