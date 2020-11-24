---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95510608"
---
IoT Plug and Play upraszcza IoT, umożliwiając współpracę z modelem urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać języka Python do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest połączony z rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

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

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

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

1. Otwórz plik *registry_manager_pnp_sample. PR* i Przejrzyj kod. Ten przykład pokazuje, jak używać klasy **IoTHubRegistryManager** w celu współdziałania z urządzeniem IoT Plug and Play.

> [!NOTE]
> Te przykłady usługi używają klasy **IoTHubRegistryManager** z poziomu **klienta usługi IoT Hub**. Aby dowiedzieć się więcej na temat interfejsów API, w tym Digital bliźniaczych reprezentacji API, zobacz [Przewodnik dla deweloperów usług](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Pobieranie sznurka urządzenia

W obszarze [Konfigurowanie środowiska dla usługi IoT Plug and Play Przewodniki Szybki Start i samouczki](../articles/iot-pnp/set-up-environment.md) zostały utworzone dwie zmienne środowiskowe w celu skonfigurowania przykładu w celu połączenia z Centrum IoT i urządzeniem:

* **IOTHUB_CONNECTION_STRING**: parametry połączenia usługi IoT Hub, dla których wykonano wcześniej adnotację.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Aby uruchomić ten przykład, użyj następującego polecenia w terminalu **usługi** :

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Jeśli używasz tego przykładu w systemie Linux, użyj `export` zamiast `set` .

Dane wyjściowe przedstawiają sznurki urządzenia i drukują swój identyfikator modelu:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

Poniższy fragment kodu przedstawia przykładowy kod z *registry_manager_pnp_sample. PR*:

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Aktualizowanie sznurka urządzenia

W tym przykładzie przedstawiono sposób aktualizowania `targetTemperature` Właściwości zapisywalnej na urządzeniu:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

Aby sprawdzić, czy aktualizacja została zastosowana w terminalu **urządzenia** , można wyświetlić następujące dane wyjściowe:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

Terminal **usługi** potwierdza, że poprawka zakończyła się pomyślnie:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Wywołaj polecenie

Następnie próbka wywołuje polecenie:

Terminal **usługi** pokazuje komunikat z potwierdzeniem z urządzenia:

```cmd/sh
The device method has been successfully invoked
```

W terminalu **urządzenia** zostanie wyświetlone następujące polecenie:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
