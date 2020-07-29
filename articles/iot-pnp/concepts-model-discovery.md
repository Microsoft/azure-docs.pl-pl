---
title: Wdrożenie odnajdywania modelu w usłudze IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: Jako Konstruktor rozwiązań dowiesz się, jak wdrożyć odnajdywanie modeli Plug and Play IoT w rozwiązaniu.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337385"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Wdrażanie odnajdywania modelu Plug and Play IoT w wersji zapoznawczej w rozwiązaniu IoT

W tym artykule opisano sposób, w jaki Konstruktor rozwiązań umożliwia wdrożenie odnajdywania modelu w programie IoT Plug and Play w ramach rozwiązania IoT. Funkcja odnajdywania modeli opisuje, jak:

- Urządzenia Plug and Play IoT rejestrują swój identyfikator modelu.
- Rozwiązanie IoT pobiera interfejsy zaimplementowane przez urządzenie.

Istnieją dwie szerokie kategorie rozwiązania IoT:

- *Rozwiązanie IoT utworzone specjalnie* dla danego celu współpracuje z znanym zestawem modeli urządzeń Plug and Play IoT.

- *Oparte na modelu rozwiązanie IoT* może współpracowało z dowolnym urządzeniem Plug and Play IoT. Tworzenie rozwiązań opartych na modelu jest bardziej skomplikowane, ale korzyść polega na tym, że rozwiązanie działa z dowolnymi urządzeniami, które zostały dodane w przyszłości.

    Aby utworzyć rozwiązanie IoT oparte na modelu, należy utworzyć logikę dla podstawowych elementów interfejsu Plug and Play IoT: telemetrii, właściwości i poleceń. Logika rozwiązania reprezentuje urządzenie, łącząc wiele funkcji telemetrii, właściwości i poleceń.

W tym artykule opisano sposób implementacji odnajdywania modelu w obu typach rozwiązań.

## <a name="model-discovery"></a>Odnajdywanie modelu

Aby odnaleźć model, który implementuje urządzenie, rozwiązanie może uzyskać identyfikator modelu przy użyciu odnajdywania opartego na zdarzeniach lub odnajdywania z użyciem sznurka:

### <a name="event-based-discovery"></a>Odnajdywanie oparte na zdarzeniach

Gdy urządzenie IoT Plug and Play nawiązuje połączenie z IoT Hub, rejestruje model, który implementuje. Ta rejestracja skutkuje [dwucyfrowym powiadamianiem o zdarzeniu zmiany](concepts-digital-twin.md#digital-twin-change-events) . Aby dowiedzieć się, jak włączyć routing dla zdarzeń cyfrowych przędzy, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Rozwiązanie może użyć zdarzenia pokazanego w poniższym fragmencie kodu, aby dowiedzieć się więcej o urządzeniu IoT Plug and Play, które nawiązuje połączenie i pobrać jego identyfikator modelu:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

To zdarzenie jest wyzwalane w przypadku dodania lub zaktualizowania identyfikatora modelu urządzenia.

### <a name="twin-based-discovery"></a>Odnajdywanie na podstawie sznurów

Jeśli rozwiązanie chce wiedzieć o możliwościach danego urządzenia, może użyć interfejsu API do pobierania [cyfrowych sznurów](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) do pobrania informacji.

W poniższym fragmencie kodu wieloosiowego `$metadata.$model` zawiera identyfikator modelu urządzenia Plug and Play IoT:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Rozwiązanie może również używać narzędzia **Get bliźniaczy** do pobierania identyfikatora modelu z sznurka urządzenia, jak pokazano w poniższym fragmencie kodu:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Rozdzielczość modelu

Rozwiązanie korzysta z rozwiązania modelu w celu uzyskania dostępu do interfejsów, które tworzą model z identyfikatora modelu. 

- Rozwiązania mogą zdecydować się na zapisanie tych interfejsów jako plików w folderze lokalnym. 
- Rozwiązania mogą korzystać z [repozytorium modeli](concepts-model-repository.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak odkrywać rozwiązanie IoT, Dowiedz się więcej o [platformie Azure IoT](overview-iot-plug-and-play.md) , aby korzystać z innych możliwości rozwiązania.

- [Korzystanie z urządzenia z rozwiązania](quickstart-service-node.md)
- [Interfejs API REST cyfrowego przędzy IoT](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Eksplorator IoT Azure](howto-use-iot-explorer.md)
