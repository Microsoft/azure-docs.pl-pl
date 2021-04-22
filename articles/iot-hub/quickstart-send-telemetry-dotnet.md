---
title: Szybki start — wysyłanie danych telemetrycznych do Azure IoT Hub szybki start (C#) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka C# wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: ec26e4a42cdeb7a693c8078c0f065241f32da949
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863994"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT i odczytywanie ich za pomocą aplikacji usługi (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem IoT Hub do aplikacji usługi w celu przetworzenia.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji języka C#, jednej do wysyłania danych telemetrycznych oraz jednej do odczytywania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku C#. Potrzebujesz maszyny zestaw .NET Core SDK 3.1 lub większej na komputerze dewelopera.

    Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://dotnet.microsoft.com/download).

    Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > zestaw .NET Core SDK 3.1 lub wyższa jest zalecana do skompilowania kodu usługi Event Hubs używanego do odczytywania danych telemetrycznych w tym przewodniku Szybki start.


* Pobierz przykłady dla języka C# usługi Azure IoT z [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i wyodrębnij archiwum ZIP.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym przewodniku Szybki start używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDotnetDevice:** jest to nazwa rejestrowanego urządzenia. Zaleca się użycie **myDotnetDevice, jak** pokazano. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby _pobrać_ parametrów połączenia urządzenia dla właśnie zarejestrowanego urządzenia:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

3. Potrzebujesz również punktu końcowego zgodnego z usługą _Event Hubs_,  ścieżki zgodnej z usługą _Event Hubs_ oraz klucza podstawowego usługi z centrum IoT, aby umożliwić aplikacji usługi łączenie się z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera te wartości dla Twojego centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Zanotuj te trzy wartości, których użyjesz w dalszej części tego przewodnika Szybki start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do **folderu iot-hub\Quickstarts\SimulatedDevice.**

2. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

3. W lokalnym oknie terminalu uruchom następujące polecenie, aby skompilować i uruchomić aplikację urządzenia symulowanego z zanotowane wcześniej parametrów połączenia urządzenia:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja usługi łączy się z punktem końcowym zdarzeń po **stronie** usługi na IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja IoT Hub zwykle działa w chmurze w celu odbierania i przetwarzania komunikatów z urządzenia do chmury.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do **folderu iot-hub\Quickstarts\ReadD2cMessages.**

2. W lokalnym oknie terminalu uruchom następujące polecenie, aby zainstalować wymagane biblioteki dla aplikacji:

    ```cmd/sh
    dotnet restore
    ```

3. W lokalnym oknie terminalu uruchom następujące polecenie, aby wyświetlić opcje parametrów.

    ```cmd/sh
    dotnet run
    ```

4. W lokalnym oknie terminalu uruchom jedno z następujących poleceń, aby skompilować i uruchomić aplikację za pomocą polecenia :

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    lub

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe, gdy aplikacja usługi odbiera dane telemetryczne wysyłane przez urządzenie symulowane do centrum:

    ![Uruchamianie aplikacji usługi](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie, wysłasz do centrum symulowane dane telemetryczne przy użyciu aplikacji języka C# i odczytasz dane telemetryczne z centrum przy użyciu prostej aplikacji usługi.

Aby dowiedzieć się, jak kontrolować symulowane urządzenie z aplikacji usługi, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-dotnet.md)
