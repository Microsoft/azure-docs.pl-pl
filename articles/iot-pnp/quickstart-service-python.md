---
title: Korzystanie z urządzenia usługi IoT Plug and Play w wersji zapoznawczej połączonego z rozwiązaniem Azure IoT (Python) | Microsoft Docs
description: Użyj języka Python, aby nawiązać połączenie z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej i korzystać z niego, które jest połączone z rozwiązaniem Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352955"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Szybki Start: współdziałanie z urządzeniem IoT Plug and Play w wersji zapoznawczej, które jest połączone z rozwiązaniem (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Wersja zapoznawcza Plug and Play IoT upraszcza IoT, umożliwiając współpracę z modelem urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać języka Python do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest połączony z rozwiązaniem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz mieć Język Python 3,7 na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/). Możesz sprawdzić wersję języka Python za pomocą następującego polecenia:  

```cmd/sh
python --version
```

Zainstaluj [pakiet w wersji zapoznawczej zestawu SDK usługi Python](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) , uruchamiając następujące polecenie:

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
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

W tym przewodniku szybki start użyjesz przykładowego urządzenia z termostatem zapisaną w języku Python jako urządzenia Plug and Play IoT. Aby uruchomić przykładowe urządzenie:

1. Otwórz okno terminalu w wybranym folderze. Uruchom następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-python) w tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. To okno terminalu służy jako terminal **urządzenia** . Przejdź do folderu sklonowanego repozytorium i przejdź do folderu */Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PnP* .

1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Uruchom przykładowe urządzenie termostat przy użyciu następującego polecenia:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zakończyło się w trybie online. Te komunikaty wskazują, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego terminalu, musisz sprawdzić, czy przykład usługi działa.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w języku Python, aby można było korzystać z właśnie skonfigurowanego przykładowego urządzenia.

1. Otwórz inne okno terminalu, które ma być używane jako terminal **usługi** . Zestaw SDK usługi jest w wersji zapoznawczej, dlatego należy sklonować próbki z [gałęzi w wersji zapoznawczej zestawu SDK języka Python](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Przejdź do folderu sklonowanej gałęzi repozytorium i przejdź do folderu */Azure-IoT-SDK-Python/Azure-IoT-Hub/Samples* .

1. Skonfiguruj zmienne środowiskowe dla identyfikatora urządzenia i _IoT Hub parametry połączenia_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. W folderze Samples istnieją cztery przykładowe pliki z `pnp` prefiksem. W poniższych przykładach pokazano, jak używać poszczególnych interfejsów API do współpracy z urządzeniami Plug and Play IoT:

### <a name="get-digital-twin"></a>Uzyskaj wieloosiową cyfrę

Aby uruchomić ten przykład, użyj następującego polecenia w terminalu **usługi** :

```cmd/sh
python pnp_get_digital_twin_sample.py
```

Dane wyjściowe przedstawiają cyfrowy dwuosiowy urządzenia i drukują swój identyfikator modelu:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

Poniższy fragment kodu przedstawia przykładowy kod z *pnp_get_digital_twin_sample. PR*:

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Aktualizowanie wieloosiowej cyfrowej

Ten przykład pokazuje, w jaki sposób używać *poprawki* do aktualizowania właściwości za pomocą wielocyfrowego wielosieciowego urządzenia. Poniższy fragment kodu z *pnp_update_digital_twin_sample. PR* pokazuje, jak utworzyć poprawkę:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Aby uruchomić ten przykład, użyj następującego polecenia w terminalu **usługi** :

```cmd/sh
python pnp_update_digital_twin_sample.py
```

Aby sprawdzić, czy aktualizacja została zastosowana w terminalu **urządzenia** , można wyświetlić następujące dane wyjściowe:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

Terminal **usługi** potwierdza, że poprawka zakończyła się pomyślnie:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Wywołaj polecenie

Aby wywołać polecenie, uruchom przykład *pnp_invoke_command_sample. PR* . Ten przykład pokazuje, jak wywołać polecenie w prostym termostatie. Przed uruchomieniem tego przykładu Ustaw `IOTHUB_COMMAND_NAME` `IOTHUB_COMMAND_PAYLOAD` zmienne środowiskowe i w terminalu **usługi** :

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

W terminalu **usługi** Użyj następującego polecenia, aby uruchomić przykład:
  
```cmd/sh
python pnp_invoke_command_sample.py
```

Terminal **usługi** pokazuje komunikat z potwierdzeniem z urządzenia:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

W terminalu **urządzenia** zostanie wyświetlone następujące polecenie:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w rozwiązaniu IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dla deweloperów modelu IoT Plug and Play w wersji zapoznawczej](concepts-developer-guide.md)
