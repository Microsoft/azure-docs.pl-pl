---
title: Współpracuj z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej podłączonym do rozwiązania Azure IoT (Node.js) | Microsoft Docs
description: Użyj Node.js, aby nawiązać połączenie z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej i korzystać z niego, które jest połączone z rozwiązaniem Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 07/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 511a61fb1069ce10e94e24ecd3ba6d60470ca40f
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424447"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Szybki Start: współdziałanie z urządzeniem IoT Plug and Play w wersji zapoznawczej, które jest połączone z rozwiązaniem (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Wersja zapoznawcza Plug and Play IoT upraszcza IoT, umożliwiając współpracę z możliwościami urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak za pomocą Node.js połączyć się z urządzeniem IoT Plug and Play i kontrolować je, które jest połączone z rozwiązaniem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz Node.js na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

Zainstaluj [zestaw SDK usługi Node Service z obsługą Plug and Play IoT](https://www.npmjs.com/package/azure-iot-digitaltwins-service) , uruchamiając następujące polecenie:

```cmd/sh
npm i azure-iot-digitaltwins-service
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby pobrać _Parametry połączenia usługi IoT Hub_ dla centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla urządzenia dodanego do centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku szybki start można użyć przykładowego urządzenia z termostatem, które jest zapisywana w Node.js jako urządzenie transPlug and Play IoT. Aby uruchomić przykładowe urządzenie:

1. Otwórz okno terminalu w wybranym folderze. Uruchom następujące polecenie, aby sklonować [Microsoft Azure IoT SDK dla Node.js](https://github.com/Azure/azure-iot-sdk-node) repozytorium GitHub do tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. To okno terminalu służy jako terminal **urządzenia** . Przejdź do folderu sklonowanego repozytorium i przejdź do folderu */Azure-IoT-SDK-Node/Device/Samples/PnP* . Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Uruchom przykładowe urządzenie termostat przy użyciu następującego polecenia:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zakończyło się w trybie online. Te komunikaty wskazują, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego terminalu, musisz sprawdzić, czy przykład usługi działa.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w Node.js, aby móc korzystać z przykładowego urządzenia.

1. Otwórz inne okno terminalu, które ma być używane jako terminal **usługi** . Zestaw SDK usługi jest w wersji zapoznawczej, dlatego należy sklonować próbki z [gałęzi w wersji zapoznawczej zestawu SDK węzła](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b public-preview-pnp
    ```

1. Przejdź do folderu sklonowanej gałęzi repozytorium i przejdź do folderu */Azure-IoT-Samples-Node/Digital-Twins/Samples/Service/JavaScript* . Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj zmienne środowiskowe dla identyfikatora urządzenia i _IoT Hub parametry połączenia_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Odczytaj Właściwość

1. Po uruchomieniu przykładowego urządzenia z termostatem w terminalu **urządzenia** zobaczysz następujące komunikaty wskazujące jego stan online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Przejdź do terminalu **usługi** i użyj następującego polecenia, aby uruchomić przykład do odczytu informacji o urządzeniu:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. W danych wyjściowych terminalu **usługi** należy zwrócić uwagę na odpowiedź na dwuosiową cyfrę. Zobaczysz identyfikator modelu urządzenia i powiązane właściwości, które zostały zgłoszone:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. Poniższy fragment kodu przedstawia kod w *get_digital_twin.js* , który pobiera identyfikator modelu sznurka urządzenia:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

W tym scenariuszu dane wyjściowe `Model Id: dtmi:com:example:Thermostat;1` .

### <a name="update-a-writable-property"></a>Aktualizowanie właściwości z możliwością zapisu

1. Otwórz plik *update_digital_twin_property.js* w edytorze kodu.

1. Zapoznaj się z przykładowym kodem. Możesz zobaczyć, jak utworzyć poprawkę JSON, aby zaktualizować dwuosiową cyfrę urządzenia. W tym przykładzie kod zastępuje temperaturę termostatu wartością 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: 'targetTemperature',
        value: '42'
      }]
    ```

1. W terminalu **usługi** Użyj następującego polecenia, aby uruchomić przykład do aktualizacji właściwości:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. Dane wyjściowe terminalu **usługi** przedstawiają zaktualizowane informacje o urządzeniu. Przewiń do `thermostat1` składnika, aby wyświetlić nową `targetTemperature` wartość 42:

    ```json
    "modelId": "dtmi:com:example:Thermostat;1",
        "version": 12,
        "properties": {
            "desired": {
                "targetTemperature": "42",
                "$metadata": {
                    "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                    "$lastUpdatedVersion": 5,
                    "targetTemperature": {
                        "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                        "$lastUpdatedVersion": 5
                    }
                },
                "$version": 5
            },
            "reported": {
                "serialNumber": "123abc",
                "maxTempSinceLastReboot": 32.279942997143785,
                "targetTemperature": {
                    "value": "42",
                    "ac": 200,
                    "ad": "Successfully executed patch for targetTemperature",
                    "av": 2
                },
    ```

1. W terminalu **urządzenia** zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    Received an update for targetTemperature: 42
    updated the property
    ```

1. W terminalu **usługi** Uruchom następujące polecenie, aby potwierdzić, że właściwość została zaktualizowana:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. W danych wyjściowych terminalu **usługi** w odpowiedzi Digital bliźniaczy w obszarze `thermostat1` składnika zostanie wyświetlona zaktualizowana temperatura docelowa. Ukończenie aktualizacji może zająć trochę czasu. Powtórz ten krok, dopóki urządzenie nie przetworzyło aktualizacji właściwości:

    ```json
    "$model": "dtmi:com:example:Thermostat;1",
    "targetTemperature": {
      "desiredValue": 42,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch for targetTemperature",
      "lastUpdateTime": "2020-07-09T13:55:30.5062641Z"
    }
    ```

### <a name="invoke-a-command"></a>Wywołaj polecenie

1. Otwórz plik *invoke_command.js* i Przejrzyj kod.

1. Przejdź do terminalu **usługi** . Użyj następującego polecenia, aby uruchomić próbkę wywołującą polecenie:

    ```cmd/sh
    node invoke_command.js
    ```

1. Dane wyjściowe w terminalu **usługi** przedstawiają następujące potwierdzenie:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. W terminalu **urządzenia** zobaczysz polecenie potwierdzone:

    ```cmd/sh
    MaxMinReport [object Object]
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w rozwiązaniu IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dla deweloperów modelu IoT Plug and Play w wersji zapoznawczej](concepts-developer-guide.md)
