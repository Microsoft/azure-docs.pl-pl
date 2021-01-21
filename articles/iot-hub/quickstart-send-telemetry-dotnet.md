---
title: Szybki Start — wysyłanie danych telemetrycznych do platformy Azure IoT Hub szybki start (C#) | Microsoft Docs
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
ms.openlocfilehash: 914df5b80dee7da041b268a3aaf25ac493d0cf5b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624443"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie ich za pomocą aplikacji usługi (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku szybki start wyślesz dane telemetryczne z aplikacji symulowanego urządzenia za pośrednictwem IoT Hub do aplikacji usługi w celu przetworzenia.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji języka C#, jednej do wysyłania danych telemetrycznych oraz jednej do odczytywania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku C#. Na komputerze deweloperskim jest wymagany zestaw .NET Core SDK 3,1 lub nowszy.

    Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://www.microsoft.com/net/download/all).

    Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > Aby skompilować kod usługi Event Hubs używany do odczytywania danych telemetrycznych w tym przewodniku Szybki Start, zaleca się użycie zestaw .NET Core SDK 3,1 lub nowszej.


* Pobierz przykłady w języku C# usługi Azure IoT z programu [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i Wyodrębnij archiwum zip.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDotnetDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyDotnetDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

3. Wymagany jest również _Event Hubs punkt końcowy zgodny_ ze standardem _Event Hubs_ i _klucz podstawowy usługi_ z Centrum IoT, aby umożliwić aplikacji usługi łączenie się z Centrum IoT Hub i pobieranie komunikatów. Następujące polecenie pobiera te wartości dla Twojego centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Zanotuj te trzy wartości, których będziesz używać w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **IoT-hub\Quickstarts\SimulatedDevice** .

2. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

3. W oknie terminalu lokalnego Uruchom następujące polecenie, aby skompilować i uruchomić aplikację symulowanego urządzenia z parametrami połączenia urządzenia, które zostały wykonane wcześniej:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja usługi nawiązuje połączenie z punktem końcowym **zdarzeń** po stronie usługi w IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja usługi IoT Hub jest zwykle uruchamiana w chmurze, aby odbierać i przetwarzać komunikaty z urządzenia do chmury.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **IoT-hub\Quickstarts\ReadD2cMessages** .

2. W oknie terminalu lokalnego Uruchom następujące polecenie, aby zainstalować wymagane biblioteki dla aplikacji:

    ```cmd/sh
    dotnet restore
    ```

3. W oknie terminalu lokalnego Uruchom następujące polecenie, aby wyświetlić opcje parametru.

    ```cmd/sh
    dotnet run
    ```

4. W oknie terminalu lokalnego Uruchom jedno z następujących poleceń, aby skompilować i uruchomić aplikację z:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    lub

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe, gdy aplikacja usługi odbiera telemetrię wysłaną przez symulowane urządzenie do centrum:

    ![Uruchom aplikację usługi](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do centrum przy użyciu aplikacji języka C# i odczytuje dane telemetryczne z centrum przy użyciu prostej aplikacji usługi.

Aby dowiedzieć się, jak sterować symulowanym urządzeniem z poziomu aplikacji usługi, przejdź do następnego przewodnika Szybki Start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-dotnet.md)
