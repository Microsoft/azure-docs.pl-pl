---
title: Usługa Azure IoT Hub jako źródło siatki zdarzeń
description: Ten artykuł zawiera właściwości i schemat zdarzeń usługi Azure IoT Hub. Wyświetla listę dostępnych typów zdarzeń, przykładowe zdarzenie i właściwości zdarzenia.
services: iot-hub
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: f9bf807884ab5592fa320532f3ca10a223081263
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393325"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Usługa Azure IoT Hub jako źródło siatki zdarzeń
Ten artykuł zawiera właściwości i schemat zdarzeń usługi Azure IoT Hub. Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md). 

## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Azure IoT Hub emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceTworzone | Opublikowano, gdy urządzenie jest zarejestrowane w centrum IoT hub. |
| Urządzenia firmy Microsoft.Devices.DeviceDeleted | Opublikowano po usunięciu urządzenia z centrum IoT Hub. | 
| Urządzenia firmy Microsoft.Devices.DeviceConnected | Opublikowano, gdy urządzenie jest podłączone do centrum IoT Hub. |
| Urządzenia firmy Microsoft.Devices.DeviceWyłączone | Opublikowano, gdy urządzenie jest odłączone od centrum IoT hub. | 
| Microsoft.Devices.DeviceTelemetria | Opublikowano, gdy wiadomość telemetryczne jest wysyłana do centrum IoT hub. |

Wszystkie zdarzenia urządzenia z wyjątkiem zdarzeń telemetrycznych urządzenia są ogólnie dostępne we wszystkich regionach obsługiwanych przez siatkę zdarzeń. Zdarzenie telemetryczne urządzenia jest w publicznej wersji zapoznawczej i jest dostępne we wszystkich regionach z wyjątkiem wschodnich stanów USA, zachodnich stanów USA, Europy Zachodniej, [platformy Azure Government,](../azure-government/documentation-government-welcome.md) [Azure China 21Vianet](/azure/china/china-welcome)i [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

### <a name="example-event"></a>Przykładowe zdarzenie

Schemat zdarzeń DeviceConnected i DeviceDisconnected ma tę samą strukturę. To przykładowe zdarzenie pokazuje schemat zdarzenia wywoływanego, gdy urządzenie jest połączone z centrum IoT hub:

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

Zdarzenie DeviceTelemetry jest wywoływane, gdy zdarzenie telemetryczne jest wysyłane do centrum IoT Hub. Poniżej przedstawiono przykładowy schemat dla tego zdarzenia.

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

Schemat devicecreated i DeviceDeleted zdarzenia mają taką samą strukturę. To przykładowe zdarzenie pokazuje schemat zdarzenia wywoływanego, gdy urządzenie jest zarejestrowane w centrum IoT hub:

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

### <a name="event-properties"></a>Właściwości zdarzenia

Wszystkie zdarzenia zawierają te same dane najwyższego poziomu: 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| dane | obiekt | Dane zdarzeń usługi IoT Hub.  |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

W przypadku wszystkich zdarzeń usługi IoT Hub obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| nazwa centrum | ciąg | Nazwa centrum IoT Hub, w którym urządzenie zostało utworzone lub usunięte. |
| deviceId | ciąg | Unikatowy identyfikator urządzenia. Ten ciąg z uwzględnieniem wielkości liter może mieć do 128 znaków i obsługuje 7-bitowe znaki `- : . + % _ # * ? ! ( ) , = @ ; $ '`alfanumeryczne ASCII oraz następujące znaki specjalne: . |

Zawartość obiektu danych są różne dla każdego wydawcy zdarzeń. 

W przypadku zdarzeń Usługi IoT Hub **podłączone do urządzenia** i **odłączonego urządzenia** obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| identyfikator modułu | ciąg | Unikatowy identyfikator modułu. To pole jest wyprowadzane tylko dla urządzeń modułowych. Ten ciąg z uwzględnieniem wielkości liter może mieć do 128 znaków i obsługuje 7-bitowe znaki `- : . + % _ # * ? ! ( ) , = @ ; $ '`alfanumeryczne ASCII oraz następujące znaki specjalne: . |
| deviceConnectionStateEventInfo | obiekt | Informacje o stanie połączenia urządzenia
| Sequencenumber | ciąg | Numer, który pomaga wskazać kolejność zdarzeń podłączonych do urządzenia lub odłączonych urządzenia. Najnowsze zdarzenie będzie miało numer sekwencyjny, który jest wyższy niż poprzednie zdarzenie. Liczba ta może ulec zmianie o więcej niż 1, ale jest ściśle wzrasta. Zobacz, [jak używać numeru sekwencyjnyego](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

W przypadku zdarzenia Centrum IoT **telemetrii urządzenia** obiekt danych zawiera komunikat urządzenia z chmurą w [formacie komunikatu centrum IoT](../iot-hub/iot-hub-devguide-messages-construct.md) i ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| body | ciąg | Zawartość wiadomości z urządzenia. |
| properties | ciąg | Właściwości aplikacji są ciągami zdefiniowanymi przez użytkownika, które można dodać do wiadomości. Te pola są opcjonalne. |
| właściwości systemu | ciąg | [Właściwości systemu](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) pomagają zidentyfikować zawartość i źródło wiadomości. Komunikat telemetryczny urządzenia musi być w prawidłowym formacie JSON z contentType ustawiony na JSON i contentEncoding ustawiony na UTF-8 we właściwościach systemu wiadomości. Jeśli nie jest ustawiona, następnie IoT Hub zapisze wiadomości w formacie zakodowanym w podstawowej 64.  |

W przypadku zdarzeń Usługi IoT usługi **Device Created** i **Device Deleted** IoT Hub obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Pokój typu twin | obiekt | Informacje o bliźniaczej reprezentacji urządzenia, która jest reprezentacją w chmurze metadanych urządzenia aplikacji. | 
| Deviceid | ciąg | Unikatowy identyfikator bliźniaczej reprezentacji urządzenia. | 
| Etag | ciąg | Walidator zapewniający spójność aktualizacji bliźniaczej reprezentacji urządzenia. Każdy etag jest gwarantowany jako unikalny na bliźniaczego urządzenia. |  
| deviceEtag (tag)| ciąg | Walidator zapewniający spójność aktualizacji rejestru urządzeń. Każdy deviceEtag jest gwarantowane, aby być unikatowy dla każdego rejestru urządzenia. |
| status | ciąg | Czy bliźniacza reprezentacja urządzenia jest włączona czy wyłączona. | 
| statusUpdateTime | ciąg | Sygnatura czasowa ISO8601 ostatniej aktualizacji stanu bliźniaczej reprezentacji urządzenia. |
| connectionState (Stan połączenia) | ciąg | Czy urządzenie jest podłączone, czy odłączone. | 
| lastAktywnośćCzas | ciąg | Sygnatura czasowa ISO8601 ostatniego działania. | 
| chmuraToDeviceMessageCount | liczba całkowita | Liczba komunikatów z chmury do urządzenia wysyłanych do tego urządzenia. | 
| authenticationType | ciąg | Typ uwierzytelniania używany dla `SAS`tego `SelfSigned`urządzenia: , , lub `CertificateAuthority`. |
| x509Druk | ciąg | Odcisk palca jest unikatową wartością certyfikatu x509, powszechnie używanego do znajdowania określonego certyfikatu w magazynie certyfikatów. Odcisk palca jest generowany dynamicznie przy użyciu algorytmu SHA1 i fizycznie nie istnieje w certyfikacie. | 
| primaryThumbprint (druk podstawowy) | ciąg | Podstawowy odcisk palca dla certyfikatu x509. |
| wtórnyDruk | ciąg | Dodatkowy odcisk palca dla certyfikatu x509. | 
| version | liczba całkowita | Liczba całkowita, która jest zwiększana o jeden za każdym razem, gdy bliźniacza reprezentacja urządzenia jest aktualizowana. |
| Żądaną | obiekt | Część właściwości, które mogą być zapisywane tylko przez zaplecze aplikacji i odczytywane przez urządzenie. | 
| Zgłoszone | obiekt | Część właściwości, które mogą być zapisywane tylko przez urządzenie i odczytywane przez zaplecze aplikacji. |
| lastUpdated | ciąg | Sygnatura czasowa ISO8601 ostatniej aktualizacji właściwości bliźniaczej reprezentacji urządzenia. | 

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Wysyłanie powiadomień e-mail o zdarzeniach usługi Azure IoT Hub przy użyciu usługi Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie jest dodawane do centrum IoT Hub. |
| [Reagowanie na zdarzenia usługi IoT Hub za pomocą siatki zdarzeń do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md) | Omówienie integracji Usługi IoT Hub z siatką zdarzeń. |
| [Zamów podłączone urządzenie i zdarzenia odłączone od urządzenia](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Pokazuje, jak zamówić zdarzenia stanu połączenia urządzenia. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby dowiedzieć się, jak centrum IoT Hub i siatka zdarzeń współpracują ze sobą, zobacz [Reagowanie na zdarzenia usługi IoT Hub przy użyciu siatki zdarzeń w celu wyzwolenia akcji](../iot-hub/iot-hub-event-grid.md).