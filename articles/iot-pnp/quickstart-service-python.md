---
title: Korzystanie z urządzenia Plug and Play IoT połączonego z rozwiązaniem Azure IoT (Python) | Microsoft Docs
description: Użyj języka Python, aby nawiązać połączenie z urządzeniem IoT Plug and Play i korzystać z niego, które jest połączone z rozwiązaniem usługi Azure IoT.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574974"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Szybki Start: współdziałanie z urządzeniem IoT Plug and Play, które jest połączone z rozwiązaniem (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play upraszcza IoT, umożliwiając współpracę z modelem urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać języka Python do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest połączony z rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki Start, musisz mieć Język Python 3,7 na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/). Możesz sprawdzić wersję języka Python za pomocą następującego polecenia:  

```cmd/sh
python --version
```

Pakiet **Azure-IoT-Device** jest publikowany jako PIP.

W lokalnym środowisku języka Python Zainstaluj pakiet w następujący sposób:

```cmd/sh
pip install azure-iot-device
```

Zainstaluj pakiet **Azure-IoT-Hub** , uruchamiając następujące polecenie:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

W tym przewodniku szybki start użyjesz przykładowego urządzenia z termostatem zapisaną w języku Python jako urządzenia Plug and Play IoT. Aby uruchomić przykładowe urządzenie:

1. Otwórz okno terminalu w wybranym folderze. Uruchom następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-python) w tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. To okno terminalu służy jako terminal **urządzenia** . Przejdź do folderu sklonowanego repozytorium i przejdź do folderu */Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PnP* .

1. Uruchom przykładowe urządzenie termostat przy użyciu następującego polecenia:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zakończyło się w trybie online. Te komunikaty wskazują, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego terminalu, musisz sprawdzić, czy przykład usługi działa.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w języku Python, aby można było korzystać z właśnie skonfigurowanego przykładowego urządzenia.

1. Otwórz inne okno terminalu, które ma być używane jako terminal **usługi** . 

1. Przejdź do folderu */Azure-IoT-SDK-Python/Azure-IoT-Hub/Samples* sklonowanego REPOZYTORIUM zestawu SDK języka Python.

1. W folderze Samples znajdują się cztery przykładowe pliki demonstrujące operacje za pomocą klasy Menedżer cyfrowych sznurów: *get_digital_twin_sample. PR, update_digitial_twin_sample. PR, invoke_command_sample. PR i invoke_component_command_sample-. PR*.  W poniższych przykładach pokazano, jak używać poszczególnych interfejsów API do współpracy z urządzeniami Plug and Play IoT:

### <a name="get-digital-twin"></a>Uzyskaj wieloosiową cyfrę

W obszarze [Konfigurowanie środowiska dla usługi IoT Plug and Play Przewodniki Szybki Start i samouczki](set-up-environment.md) zostały utworzone dwie zmienne środowiskowe w celu skonfigurowania przykładu w celu połączenia z Centrum IoT i urządzeniem:

* **IOTHUB_CONNECTION_STRING**: parametry połączenia usługi IoT Hub, dla których wykonano wcześniej adnotację.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Aby uruchomić ten przykład, użyj następującego polecenia w terminalu **usługi** :

```cmd/sh
python get_digital_twin_sample.py
```

Dane wyjściowe przedstawiają cyfrowy dwuosiowy urządzenia i drukują swój identyfikator modelu:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

Poniższy fragment kodu przedstawia przykładowy kod z *get_digital_twin_sample. PR*:

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

Ten przykład pokazuje, w jaki sposób używać *poprawki* do aktualizowania właściwości za pomocą wielocyfrowego wielosieciowego urządzenia. Poniższy fragment kodu z *update_digital_twin_sample. PR* pokazuje, jak utworzyć poprawkę:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Aby uruchomić ten przykład, użyj następującego polecenia w terminalu **usługi** :

```cmd/sh
python update_digital_twin_sample.py
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

Aby wywołać polecenie, uruchom przykład *invoke_command_sample. PR* . Ten przykład pokazuje, jak wywołać polecenie w prostym termostatie. Przed uruchomieniem tego przykładu Ustaw `IOTHUB_COMMAND_NAME` `IOTHUB_COMMAND_PAYLOAD` zmienne środowiskowe i w terminalu **usługi** :

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

W terminalu **usługi** Użyj następującego polecenia, aby uruchomić przykład:
  
```cmd/sh
python invoke_command_sample.py
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

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w rozwiązaniu IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dewelopera modelowania Plug and Play IoT](concepts-developer-guide-device-csharp.md)
