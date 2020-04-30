---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (Python) | Microsoft Docs
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
ms.date: 10/17/2019
ms.openlocfilehash: 6346b305889c6cb6d33e15c156423ed9702dbaec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770007"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie ich z użyciem aplikacji zaplecza (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

W tym przewodniku szybki start wyślesz dane telemetryczne z aplikacji symulowanego urządzenia za pośrednictwem platformy Azure IoT Hub do aplikacji zaplecza do przetwarzania. IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. Ten przewodnik Szybki Start używa wstępnie nadanej aplikacji w języku Python do wysłania telemetrii i narzędzia interfejsu wiersza polecenia w celu odczytania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7 +](https://www.python.org/downloads/). W przypadku innych obsługiwanych wersji języka Python zobacz [funkcje urządzenia IoT Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Przykładowy projekt w języku Python](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Port 8883 otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Dodawanie rozszerzenia usługi Azure IoT

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IoT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    **MyPythonDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyPythonDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.py** w wybranym edytorze tekstów.

    Zastąp wartość `CONNECTION_STRING` zmiennej parametrami połączenia urządzenia, które zostały wykonane wcześniej w notatce. Następnie Zapisz zmiany w **SimulatedDevice.py**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-python/SimulatedDevice.png)


## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --hub-name {YourIoTHubName} --device-id MyPythonDevice 
```

Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez rozszerzenie danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do koncentratora przy użyciu aplikacji języka Python, a następnie odczytuje dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-python.md)
