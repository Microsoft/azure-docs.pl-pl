---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95510611"
---
IoT Plug and Play upraszcza IoT, umożliwiając korzystanie z możliwości urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak za pomocą Node.js połączyć się z urządzeniem IoT Plug and Play i kontrolować je, które jest połączone z rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki Start, musisz Node.js na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klonowanie repozytorium zestawu SDK przy użyciu przykładowego kodu

Sklonuj przykłady z [repozytorium zestawu SDK węzła](https://github.com/Azure/azure-iot-sdk-node). Otwórz okno terminalu w wybranym folderze. Uruchom następujące polecenie, aby sklonować [Microsoft Azure IoT SDK dla Node.js](https://github.com/Azure/azure-iot-sdk-node) repozytorium GitHub:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

W tym przewodniku szybki start użyto przykładowego urządzenia z termostatem, które jest zapisywana w Node.js jako urządzenie IoT Plug and Play. Aby uruchomić przykładowe urządzenie:

1. Otwórz okno terminalu i przejdź do folderu lokalnego zawierającego Microsoft Azure IoT SDK for Node.js repozytorium sklonowane z usługi GitHub.

1. To okno terminalu służy jako terminal **urządzenia** . Przejdź do folderu sklonowanego repozytorium i przejdź do folderu */Azure-IoT-SDK-Node/Device/Samples/PnP* . Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Uruchom przykładowe urządzenie termostat przy użyciu następującego polecenia:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zakończyło się w trybie online. Te komunikaty wskazują, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego terminalu, musisz sprawdzić, czy przykład usługi działa.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W obszarze [Konfigurowanie środowiska dla usługi IoT Plug and Play Przewodniki Szybki Start i samouczki](../articles/iot-pnp/set-up-environment.md) zostały utworzone dwie zmienne środowiskowe w celu skonfigurowania przykładu w celu połączenia z Centrum IoT i urządzeniem:

* **IOTHUB_CONNECTION_STRING**: parametry połączenia usługi IoT Hub, dla których wykonano wcześniej adnotację.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w Node.js, aby móc korzystać z przykładowego urządzenia.

1. Otwórz inne okno terminalu, które ma być używane jako terminal **usługi** .

1. W repozytorium zestawu SDK sklonowanego węzła przejdź do folderu */Azure-IoT-SDK-Node/Service/Samples/JavaScript* . Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Odczytaj Właściwość

1. Po uruchomieniu przykładowego urządzenia z termostatem w terminalu **urządzenia** zobaczysz następujące komunikaty wskazujące jego stan online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Przejdź do terminalu **usługi** i użyj następującego polecenia, aby uruchomić przykład do odczytu informacji o urządzeniu:

    ```cmd/sh
    node twin.js
    ```

1. W danych wyjściowych terminalu **usługi** należy zwrócić uwagę na odpowiedź z urządzenia. Zobaczysz identyfikator modelu urządzenia i powiązane właściwości, które zostały zgłoszone:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. Poniższy fragment kodu przedstawia kod w *twin.js* , który pobiera identyfikator modelu sznurka urządzenia:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

W tym scenariuszu dane wyjściowe `Model Id: dtmi:com:example:Thermostat;1` .

> [!NOTE]
> Te przykłady usługi używają klasy **Registry** z **klienta usługi IoT Hub**. Aby dowiedzieć się więcej na temat interfejsów API, w tym Digital bliźniaczych reprezentacji API, zobacz [Przewodnik dla deweloperów usług](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Aktualizowanie właściwości z możliwością zapisu

1. Otwórz plik *twin.js* w edytorze kodu.

1. Zapoznaj się z przykładowym kodem, który pokazuje dwa sposoby aktualizowania sznurka urządzenia. Aby użyć pierwszej metody, należy zmodyfikować `twinPatch` zmienną w następujący sposób:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature`Właściwość jest definiowana jako właściwości zapisywalne w modelu urządzenia termostatu.

1. W terminalu **usługi** Użyj następującego polecenia, aby uruchomić przykład do aktualizacji właściwości:

    ```cmd/sh
    node twin.js
    ```

1. W terminalu **urządzenia** zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. W terminalu **usługi** Uruchom następujące polecenie, aby potwierdzić, że właściwość została zaktualizowana:

    ```cmd/sh
    node twin.js
    ```

1. W danych wyjściowych terminalu **usługi** w `reported` sekcji właściwości zostanie wyświetlona zaktualizowana docelowa temperatura. Ukończenie aktualizacji może zająć trochę czasu. Powtórz ten krok, dopóki urządzenie nie przetworzyło aktualizacji właściwości:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Wywołaj polecenie

1. Otwórz plik *device_method.js* i Przejrzyj kod.

1. Przejdź do terminalu **usługi** . Użyj następującego polecenia, aby uruchomić próbkę wywołującą polecenie:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. Dane wyjściowe w terminalu **usługi** przedstawiają następujące potwierdzenie:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. W terminalu **urządzenia** zobaczysz polecenie potwierdzone:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
