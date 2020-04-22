---
title: Usługa Azure IoT Hub i siatka zdarzeń | Dokumenty firmy Microsoft
description: Użyj usługi Azure Event Grid do wyzwalania procesów na podstawie akcji, które mają miejsce w Centrum IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a67d90a0888c39938f07c294f8e161ce98fd945a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732496"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagowanie na zdarzenia usługi IoT Hub za pomocą siatki zdarzeń do wyzwalania akcji

Usługa Azure IoT Hub integruje się z usługą Azure Event Grid, dzięki czemu można wysyłać powiadomienia o zdarzeniach do innych usług i wyzwalać procesy podrzędne. Skonfiguruj aplikacje biznesowe tak, aby nasłuchiwać zdarzeń usługi IoT Hub, aby można było reagować na zdarzenia krytyczne w niezawodny, skalowalny i bezpieczny sposób.Na przykład skompiluj aplikację, która aktualizuje bazę danych, tworzy bilet roboczy i dostarcza powiadomienie e-mail za każdym razem, gdy nowe urządzenie IoT jest zarejestrowane w centrum IoT Hub.

[Usługa Azure Event Grid](../event-grid/overview.md) to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania i subskrybowania. Usługa Event Grid ma wbudowaną obsługę usług platformy Azure, takich jak [usługi Azure Functions](../azure-functions/functions-overview.md) i Usługa Azure Logic [Apps,](../logic-apps/logic-apps-what-are-logic-apps.md)i może dostarczać alerty o zdarzeniach do usług innych niż azure przy użyciu elementów webhook. Aby uzyskać pełną listę programów obsługi zdarzeń, które obsługuje usługa Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](../event-grid/overview.md).

![Architektura usługi Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Dostępność regionalna

Integracja z siatką zdarzeń jest dostępna dla centrów IoT znajdujących się w regionach, w których jest obsługiwana siatka zdarzeń. Aby uzyskać najnowszą listę regionów, zobacz [Wprowadzenie do usługi Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Typy zdarzeń

Usługa IoT Hub publikuje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceTworzone | Opublikowano, gdy urządzenie jest zarejestrowane w centrum IoT hub. |
| Urządzenia firmy Microsoft.Devices.DeviceDeleted | Opublikowano po usunięciu urządzenia z centrum IoT Hub. |
| Urządzenia firmy Microsoft.Devices.DeviceConnected | Opublikowano, gdy urządzenie jest podłączone do centrum IoT Hub. |
| Urządzenia firmy Microsoft.Devices.DeviceWyłączone | Opublikowano, gdy urządzenie jest odłączone od centrum IoT hub. |
| Microsoft.Devices.DeviceTelemetria | Opublikowano po wysłaniu do centrum IoT hub komunikatu telemetrycznego urządzenia |

Użyj witryny Azure portal lub interfejsu wiersza polecenia platformy Azure, aby skonfigurować zdarzenia, które mają być publikowane z każdego centrum IoT Hub. Na przykład spróbuj samouczek [Wysyłanie powiadomień e-mail o zdarzeniach usługi Azure IoT Hub przy użyciu aplikacji logiki](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia usługi IoT Hub zawierają wszystkie informacje potrzebne do reagowania na zmiany w cyklu życia urządzenia. Zdarzenie Centrum IoT można zidentyfikować, sprawdzając, czy właściwość eventType rozpoczyna się od aplikacji **Microsoft.Devices.** Aby uzyskać więcej informacji na temat używania [Event Grid event schema](../event-grid/event-schema.md)właściwości zdarzenia w ucho.

### <a name="device-connected-schema"></a>Schemat podłączony do urządzenia

Poniższy przykład przedstawia schemat zdarzenia podłączonego do urządzenia:

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Schemat telemetrii urządzenia

Komunikat telemetryczny urządzenia musi być w prawidłowym formacie JSON z contentType ustawiony na **application/json** i contentEncoding ustawiony na **UTF-8** we [właściwościach systemu](iot-hub-devguide-routing-query-syntax.md#system-properties)wiadomości . Obie te właściwości są niewrażliwe na wielkości liter. Jeśli kodowanie zawartości nie jest ustawione, centrum IoT hub zapisze wiadomości w formacie zakodowanym w podstawowej 64.

Można wzbogacić zdarzenia telemetryczne urządzenia przed ich opublikowaniem w usztywnieniu zdarzeń, wybierając punkt końcowy jako siatkę zdarzeń. Aby uzyskać więcej informacji, zobacz [Omówienie wzbogacania wiadomości](iot-hub-message-enrichments-overview.md).

W poniższym przykładzie przedstawiono schemat zdarzenia telemetrii urządzenia:

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

### <a name="device-created-schema"></a>Schemat utworzony przez urządzenie

Poniższy przykład przedstawia schemat zdarzenia utworzonego przez urządzenie:

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
      "deviceEtag":"null",
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

Aby uzyskać szczegółowy opis każdej właściwości, zobacz [schemat zdarzeń usługi Azure Event Grid dla usługi IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrowanie zdarzeń

Subskrypcje zdarzeń usługi IoT Hub można filtrować zdarzenia na podstawie typu zdarzenia, zawartości danych i tematu, który jest nazwą urządzenia.

Usługa Event Grid umożliwia [filtrowanie](../event-grid/event-filtering.md) typów zdarzeń, tematów i zawartości danych. Podczas tworzenia subskrypcji usługi Event Grid, można wybrać, aby subskrybować wybrane zdarzenia IoT. Filtry tematów w pracy siatki zdarzeń na podstawie **dopasowania Rozpoczyna się** od (prefiks) i Kończy **się** (sufiks). Filtr używa `AND` operatora, więc zdarzenia z tematem, które pasują zarówno prefiks i sufiks są dostarczane do subskrybenta.

Temat zdarzeń IoT używa formatu:

```json
devices/{deviceId}
```

Usługa Event Grid umożliwia również filtrowanie atrybutów każdego zdarzenia, w tym zawartości danych. Dzięki temu można wybrać, jakie zdarzenia są dostarczane na podstawie zawartości wiadomości telemetrii. Zobacz [zaawansowane filtrowanie,](../event-grid/event-filtering.md#advanced-filtering) aby wyświetlić przykłady. W celu filtrowania w treści wiadomości telemetrii należy ustawić contentType na **application/json** i contentEncoding na **UTF-8** we [właściwościach systemu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)wiadomości . Obie te właściwości są niewrażliwe na wielkości liter.

W przypadku zdarzeń innych niż telemetryczne, takich jak DeviceConnected, DeviceDisconnected, DeviceCreated i DeviceDeleted, filtrowanie siatki zdarzeń może być używane podczas tworzenia subskrypcji. W przypadku zdarzeń telemetrycznych, oprócz filtrowania w siatce zdarzeń, użytkownicy mogą również filtrować bliźniacze urządzenia, właściwości wiadomości i treść za pośrednictwem kwerendy routingu wiadomości. 

Podczas subskrybowania zdarzeń telemetrycznych za pośrednictwem siatki zdarzeń usługa IoT Hub tworzy domyślną trasę komunikatów w celu wysyłania komunikatów urządzenia typu źródło danych do siatki zdarzeń. Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz [Routing wiadomości w centrum IoT](iot-hub-devguide-messages-d2c.md). Ta trasa będzie widoczna w portalu w obszarze Usługa IoT Hub > routingu wiadomości. Tylko jedna trasa do usługi Event Grid jest tworzony niezależnie od liczby subskrypcji EG utworzonych dla zdarzeń telemetrycznych. Tak, jeśli potrzebujesz kilka subskrypcji z różnych filtrów, można użyć operatora OR w tych kwerend na tej samej trasie. Tworzenie i usuwanie trasy jest kontrolowane za pośrednictwem subskrypcji zdarzeń telemetrycznych za pośrednictwem usługi Event Grid. Nie można utworzyć ani usunąć trasy do siatki zdarzeń przy użyciu usługi IoT Hub Message Routing.

Aby filtrować wiadomości przed wysłaniem danych telemetrycznych, można zaktualizować [kwerendę routingu](iot-hub-devguide-routing-query-syntax.md). Należy zauważyć, że kwerenda routingu można zastosować do treści wiadomości tylko wtedy, gdy treść jest JSON. Należy również ustawić contentType na **application/json** i contentEncoding na **UTF-8** we [właściwościach systemu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)wiadomości .

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ograniczenia dotyczące zdarzeń podłączania i odłączania urządzeń

Aby odbierać zdarzenia stanu połączenia urządzenia, urządzenie musi wykonać operację "D2C Send Telemetry" lub operację "C2D Receive Message" z Iot Hub. Należy jednak pamiętać, że jeśli urządzenie używa protokołu AMQP do łączenia się z Iot Hub, zaleca się wykonanie operacji "C2D Receive Message", w przeciwnym razie powiadomienia o stanie połączenia mogą być opóźnione o kilka minut. Jeśli urządzenie korzysta z protokołu MQTT, centrum IoT Hub będzie utrzymywać łącze C2D otwarte. W przypadku usługi AMQP można otworzyć łącze C2D, wywołując [interfejs API Receive Async](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), dla SDK języka C# usługi IoT Hub lub [klienta urządzenia dla usługi AMQP](iot-hub-amqp-support.md#device-client).

Łącze D2C jest otwarte w przypadku wysyłania danych telemetrycznych. 

Jeśli połączenie urządzenia migocze, co oznacza, że urządzenie często łączy się i rozłącza, nie wysyłamy każdego stanu połączenia, ale opublikujemy bieżący stan połączenia wykonany w okresowej migawce, dopóki migotanie nie będzie kontynuowane. Odbieranie tego samego zdarzenia stanu połączenia z różnymi numerami sekwencji lub zdarzeniami stanu innego połączenia oznaczają, że nastąpiła zmiana stanu połączenia urządzenia.

## <a name="tips-for-consuming-events"></a>Wskazówki dotyczące spożywania zdarzeń

Aplikacje obsługujące zdarzenia usługi IoT Hub powinny stosować się do tych sugerowanych rozwiązań:

* Wiele subskrypcji można skonfigurować do kierowania zdarzeń do tego samego programu obsługi zdarzeń, więc nie zakładaj, że zdarzenia pochodzą z określonego źródła. Zawsze sprawdź temat wiadomości, aby upewnić się, że pochodzi z centrum IoT hub, którego oczekujesz.

* Nie zakładaj, że wszystkie zdarzenia, które otrzymujesz są typy, które można oczekiwać. Zawsze sprawdź eventType przed przetworzeniem wiadomości.

* Wiadomości mogą być dostarczane poza kolejnością lub po opóźnieniu. Użyj pola etag, aby dowiedzieć się, czy informacje o obiektach są aktualne dla zdarzeń utworzonych przez urządzenie lub usunięte przez urządzenie.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj samouczek zdarzeń usługi IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Dowiedz się, jak porządkować zdarzenia podłączania i odłączania urządzeń](iot-hub-how-to-order-connection-state-events.md)

* [Dowiedz się więcej o siatce zdarzeń](../event-grid/overview.md)

* [Porównanie różnic między zdarzeniami i komunikatami usługi IoT Hub routingu](iot-hub-event-grid-routing-comparison.md)

* [Dowiedz się, jak zaimplementować analizę przestrzenną IoT przy użyciu usługi Azure Maps za pomocą zdarzeń telemetrycznych IoT](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
