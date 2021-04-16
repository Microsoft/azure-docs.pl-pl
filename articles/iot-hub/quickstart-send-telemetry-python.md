---
title: Szybki start — wysyłanie danych telemetrycznych do Azure IoT Hub szybki start (Python) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz przykładową aplikację języka Python wysyłającą symulowane dane telemetryczne do centrum IoT oraz używającą narzędzia do odczytywania danych telemetrycznych z centrum IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/16/2020
ms.openlocfilehash: 18163c3e5cdf83d736cdb52a3de028bf8890e728
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568577"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT i odczytywanie ich za pomocą aplikacji back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem Azure IoT Hub do aplikacji back-end w celu przetworzenia. IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start są używane dwie wstępnie napisane aplikacje języka Python: jedna do wysyłania danych telemetrycznych i jedna do odczytywania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.7+](https://www.python.org/downloads/). Aby uzyskać informacje o innych obsługiwanych wersjach języka Python, [zobacz Funkcje urządzeń Azure IoT.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)

* [Przykładowy projekt języka Python z](https://github.com/Azure-Samples/azure-iot-samples-python/) repozytorium github. Pobierz lub sklonuj przykłady przy użyciu **przycisku Kod** w repozytorium GitHub.

* Port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym przewodniku Szybki start używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    **MyPythonDevice:** jest to nazwa rejestrowanego urządzenia. Zaleca się używanie **myPythonDevice, jak** pokazano. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Uruchom następujące polecenie w Azure Cloud Shell, aby _pobrać_ parametrów połączenia urządzenia dla zarejestrowanego urządzenia:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

1. Potrzebujesz również punktu końcowego zgodnego z usługą _Event Hubs_,  ścieżki zgodnej z usługą _Event Hubs_ oraz klucza podstawowego usługi z centrum IoT, aby umożliwić aplikacji serwera końcowego nawiązywanie połączenia z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera te wartości dla Twojego centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Zanotuj te trzy wartości, których użyjesz w dalszej części tego przewodnika Szybki start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. Pobierz lub sklonuj repozytorium azure-iot-samples-python przy użyciu przycisku Kod na stronie [repozytorium azure-iot-samples-python.](https://github.com/Azure-Samples/azure-iot-samples-python/) 

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.py** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej zanot innymi zanotymi wartościami `CONNECTION_STRING` połączenia urządzenia. Następnie zapisz zmiany w **SimulatedDevice.py**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

> [!NOTE]
> W poniższych krokach jest synchroniczna **próbka, read_device_to_cloud_messages_sync.py.** Te same kroki można wykonać za pomocą przykładu asynchronicznego, **read_device_to_cloud_messages_async.py.**

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **iot-hub\Quickstarts\read-d2c-messages**.

2. Otwórz **plik read_device_to_cloud_messages_sync.py** w edytorze tekstów. Zaktualizuj następujące zmienne i zapisz zmiany w pliku.

    | Zmienna | Wartość |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Zastąp wartość zmiennej punktem końcowym zgodnym Event Hubs, który zanotował wcześniej. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Zastąp wartość zmiennej ścieżką zgodną Event Hubs zanotową wcześniej. |
    | `IOTHUB_SAS_KEY`                | Zastąp wartość zmiennej kluczem podstawowym usługi, który zanotował wcześniej. |

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji zaplecza:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację zaplecza:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez aplikację zaplecza danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie, wysłasz do centrum symulowane dane telemetryczne przy użyciu aplikacji języka Python i odczytasz dane telemetryczne z centrum przy użyciu prostej aplikacji back-end.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-python.md)
