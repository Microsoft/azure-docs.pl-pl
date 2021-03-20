---
title: Azure IoT Hub i Event Grid | Microsoft Docs
description: Użyj Azure Event Grid, aby wyzwolić procesy w oparciu o akcje wykonywane w programie IoT Hub.
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
- 'Role: Cloud Development'
ms.openlocfilehash: 0e0ca8a787145fb40087a2d99be85607404eebfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152135"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagowanie na zdarzenia IoT Hub przy użyciu Event Grid do wyzwalania akcji

Usługa Azure IoT Hub jest zintegrowana z usługą Azure Event Grid, co umożliwia wysyłanie powiadomień o zdarzeniach do innych usług i wyzwalanie dalszych procesów. Skonfiguruj aplikacje biznesowe do nasłuchiwania zdarzeń usługi IoT Hub, aby móc reagować na zdarzenia o krytycznym znaczeniu w niezawodny, skalowalny i bezpieczny sposób. Możesz na przykład opracować aplikację, która zaktualizuje bazę danych, utworzy bilet pracy i dostarczy wiadomość e-mail z powiadomieniem zawsze, gdy w centrum IoT zostanie zarejestrowane nowe urządzenie IoT.

[Azure Event Grid](../event-grid/overview.md) to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania/subskrybowania. Event Grid ma wbudowaną obsługę usług platformy Azure, takich jak [Azure Functions](../azure-functions/functions-overview.md) i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), i mogą dostarczać alerty zdarzeń do usług innych niż Azure przy użyciu elementów webhook. Aby zapoznać się z pełną listą programów obsługi zdarzeń obsługiwanych przez Event Grid, zobacz [wprowadzenie do Azure Event Grid](../event-grid/overview.md).

![Architektura Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Dostępność regionalna

Integracja Event Grid jest dostępna dla centrów IoT znajdujących się w regionach, w których Event Grid jest obsługiwana. Aby uzyskać najnowszą listę regionów, zapoznaj [się z wprowadzeniem do Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Typy zdarzeń

IoT Hub publikuje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Opublikowano, gdy urządzenie jest zarejestrowane w usłudze IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Opublikowano, gdy urządzenie zostanie usunięte z Centrum IoT Hub. |
| Microsoft.Devices.DeviceConnected | Opublikowano, gdy urządzenie jest połączone z usługą IoT Hub. |
| Microsoft.Devices.DeviceDisconnected | Opublikowano po odłączeniu urządzenia od centrum IoT Hub. |
| Microsoft.Devices.DeviceTelemetry | Publikowane w przypadku wysłania komunikatu telemetrii urządzenia do centrum IoT |

Użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby skonfigurować zdarzenia do opublikowania w każdym Centrum IoT Hub. Przykładowo Wypróbuj samouczek [wysyłanie powiadomień e-mail dotyczących zdarzeń IoT Hub platformy Azure przy użyciu Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia IoT Hub zawierają wszystkie informacje potrzebne do reagowania na zmiany w cyklu życia urządzenia. Można rozpoznać zdarzenia usługi IoT Hub po tym, że właściwość eventType zaczyna się od ciągu **Microsoft.Devices**. Aby uzyskać więcej informacji o sposobach używania właściwości zdarzenia Event Grid, zobacz [Event Grid schematu zdarzeń](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schemat połączony z urządzeniem

Poniższy przykład przedstawia schemat zdarzenia połączonego z urządzeniem:

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

Komunikat telemetrii urządzenia musi znajdować się w prawidłowym formacie JSON z atrybutem ContentType ustawionym na wartość **Application/JSON** i ContentEncoding na **UTF-8** we [właściwościach systemu](iot-hub-devguide-routing-query-syntax.md#system-properties)komunikatów. W obu tych właściwościach jest rozróżniana wielkość liter. Jeśli kodowanie zawartości nie jest ustawione, IoT Hub będzie zapisywać komunikaty w standardowym formacie 64.

Zdarzenia telemetrii urządzeń można wzbogacać przed ich opublikowaniem w Event Grid, wybierając punkt końcowy jako Event Grid. Aby uzyskać więcej informacji, zobacz [Omówienie wzbogacania komunikatów](iot-hub-message-enrichments-overview.md).

Poniższy przykład przedstawia schemat zdarzenia telemetrii urządzenia:

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


> [!WARNING]
> *Dane przędzy* skojarzone z zdarzeniem tworzenia urządzenia są konfiguracją domyślną i nie *powinny* być opierane na rzeczywistych `authenticationType` i innych właściwościach urządzenia w nowo utworzonym urządzeniu. W przypadku `authenticationType` i innych właściwościach urządzenia na nowo utworzonym urządzeniu należy użyć interfejsu API Menedżera rejestru dostępnego w zestawach SDK usługi Azure IoT.

Aby uzyskać szczegółowy opis każdej właściwości, zobacz [Azure Event Grid schemacie zdarzeń dla IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrowanie zdarzeń

IoT Hub subskrypcje zdarzeń mogą filtrować zdarzenia na podstawie typu zdarzenia, zawartości danych i tematu, który jest nazwą urządzenia.

Event Grid włącza [filtrowanie](../event-grid/event-filtering.md) typów zdarzeń, tematów i zawartości danych. Podczas tworzenia subskrypcji Event Grid możesz wybrać opcję subskrybowania wybranych zdarzeń IoT. Filtry podmiotu w Event Grid pracy oparte na parametrach **zaczynają** się od (prefix) i **kończą się znakiem** (sufiksem). Filtr używa `AND` operatora, dlatego zdarzenia z podmiotem, które pasują do prefiksu i sufiksu, są dostarczane do subskrybenta.

Temat zdarzeń IoT używa formatu:

```json
devices/{deviceId}
```

Event Grid umożliwia także filtrowanie atrybutów poszczególnych zdarzeń, w tym zawartości danych. Dzięki temu można wybrać, jakie zdarzenia są dostarczane na podstawie zawartości komunikatu telemetrii. Zobacz [Zaawansowane filtrowanie](../event-grid/event-filtering.md#advanced-filtering) , aby wyświetlić przykłady. W celu filtrowania treści wiadomości telemetrycznych należy ustawić Właściwość ContentType na wartość **Application/JSON** i ContentEncoding na **UTF-8** we [właściwościach systemu](./iot-hub-devguide-routing-query-syntax.md#system-properties)komunikatów. W obu tych właściwościach jest rozróżniana wielkość liter.

W przypadku zdarzeń telemetrii, takich jak DeviceConnected, DeviceDisconnected, DeviceCreated i DeviceDeleted, można użyć filtrowania Event Grid podczas tworzenia subskrypcji. W przypadku zdarzeń telemetrii oprócz filtrowania w Event Grid użytkownicy mogą również odfiltrować bliźniaczych reprezentacji urządzeń, właściwości komunikatów i treści za pomocą zapytania routingu wiadomości. 

Gdy subskrybujesz zdarzenia telemetryczne za pośrednictwem Event Grid, IoT Hub tworzy domyślną trasę komunikatów do wysyłania komunikatów urządzeń typu źródło danych do Event Grid. Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz [IoT Hub Routing komunikatów](iot-hub-devguide-messages-d2c.md). Ta trasa będzie widoczna w portalu w obszarze IoT Hub > Routing komunikatów. Tylko jedna trasa do Event Grid jest tworzona bez względu na liczbę PRZYKŁADowych subskrypcji utworzonych dla zdarzeń telemetrii. Dlatego jeśli potrzebujesz kilku subskrypcji z różnymi filtrami, możesz użyć operatora OR w tych zapytaniach w tej samej trasie. Tworzenie i usuwanie trasy jest kontrolowane przez subskrypcję zdarzeń telemetrii za pośrednictwem Event Grid. Nie można utworzyć lub usunąć trasy do Event Grid przy użyciu IoT Hub Routing komunikatów.

Aby filtrować komunikaty przed wysłaniem danych telemetrycznych, można zaktualizować [zapytanie routingu](iot-hub-devguide-routing-query-syntax.md). Należy pamiętać, że zapytanie routingu można zastosować do treści wiadomości tylko wtedy, gdy treść jest JSON. Należy również ustawić Właściwość ContentType na wartość **Application/JSON** i ContentEncoding na **UTF-8** we [właściwościach systemu](./iot-hub-devguide-routing-query-syntax.md#system-properties)komunikatów.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ograniczenia dotyczące zdarzeń podłączania i odłączania urządzeń

Aby można było odbierać zdarzenia stanu połączenia z urządzeniem, urządzenie musi wykonać operację "C2D" lub "Odbierz komunikat" w usłudze IoT Hub. Należy jednak pamiętać, że jeśli urządzenie korzysta z protokołu AMQP do nawiązywania połączenia z usługą IoT Hub, zaleca się wykonanie operacji "C2D odbieranie komunikatu" w przeciwnym razie powiadomienia o stanie połączeń mogą zostać opóźnione o kilka minut. Jeśli urządzenie korzysta z protokołu MQTT, IoT Hub będzie mieć otwarte łącze C2D. W przypadku AMQP można otworzyć łącze C2D, wywołując asynchroniczny interfejs API odbioru dla IoT Hub C# SDK lub [klienta urządzenia dla AMQP](iot-hub-amqp-support.md#device-client).

Link D2C jest otwarty w przypadku wysyłania danych telemetrycznych. 

Jeśli połączenie z urządzeniem jest migotane, co oznacza, że urządzenie łączy się i rozłącza często, nie wyśle wszystkich stanów pojedynczego połączenia, ale opublikuje bieżący stan połączenia, który zostanie pobrany na okresową migawkę, aż do kontynuowania migotania. Odbiór tego samego zdarzenia stanu połączenia z różnymi numerami sekwencji lub różnymi zdarzeniami stanu połączenia oznacza, że w stanie połączenia urządzenia wprowadzono zmianę.

## <a name="tips-for-consuming-events"></a>Porady dotyczące korzystania ze zdarzeń

Aplikacje obsługujące zdarzenia usługi IoT Hub powinny być zgodne z następującymi sugerowanymi rozwiązaniami:

* Można skonfigurować wiele subskrypcji kierujących zdarzenia do tego samego procesu obsługi zdarzeń, a więc nie należy zakładać, że zdarzenia pochodzą z określonego źródła. Zawsze należy sprawdzić temat komunikatu, aby potwierdzić, że pochodzi on z oczekiwanego centrum IoT.

* Nie zakładaj, że wszystkie otrzymywane zdarzenia należą do oczekiwanego typu. Zawsze sprawdzaj typ zdarzenia przed przetworzeniem komunikatu.

* Komunikaty mogą przychodzić w niewłaściwej kolejności lub z opóźnieniem. Użyj pola ETag, aby zrozumieć, czy informacje o obiektach są aktualne dla zdarzeń utworzonych przez urządzenie lub urządzenia.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj samouczek zdarzeń IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Dowiedz się, jak porządkować zdarzenia podłączania i odłączania urządzeń](iot-hub-how-to-order-connection-state-events.md)

* [Dowiedz się więcej o Event Grid](../event-grid/overview.md)

* [Porównanie różnic między zdarzeniami i komunikatami IoT Hub routingu](iot-hub-event-grid-routing-comparison.md)

* [Dowiedz się, jak używać zdarzeń telemetrii IoT do implementowania analizy przestrzennej usługi IoT przy użyciu Azure Maps](../azure-maps/tutorial-iot-hub-maps.md)