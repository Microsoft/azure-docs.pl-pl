---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (C#) | Microsoft Docs
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
ms.openlocfilehash: 21410f7137a76b43f57ca7a1e037908410eae365
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844526"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie ich z użyciem aplikacji zaplecza (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji języka C#, jednej do wysyłania danych telemetrycznych oraz jednej do odczytywania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku C#. Na komputerze deweloperskim jest wymagany zestaw .NET Core SDK 3,0 lub nowszy.

    Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://www.microsoft.com/net/download/all).

    Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > Aby skompilować kod usługi Event Hubs używany do odczytywania danych telemetrycznych w tym przewodniku Szybki Start, zaleca się użycie zestaw .NET Core SDK 3,0 lub nowszej. Możesz użyć zestaw .NET Core SDK 2,1, jeśli ustawisz wersję językową dla kodu usługi na podgląd, jak to zanotowano w sekcji [odczytywanie danych telemetrycznych z centrum](#read-the-telemetry-from-your-hub) .


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
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

3. Wymagany jest również _Event Hubs punkt końcowy zgodny_ ze standardem _Event Hubs_ i _klucz podstawowy usługi_ z Centrum IoT, aby umożliwić aplikacji zaplecza łączenie się z Centrum IoT Hub i pobieranie komunikatów. Następujące polecenie pobiera te wartości dla Twojego centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Zanotuj te trzy wartości, których będziesz używać w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

2. Otwórz plik **SimulatedDevice.cs** w wybranym edytorze.

    Zastąp wartość `s_connectionString` zmiennej parametrami połączenia urządzenia, które zostały wykonane wcześniej w notatce. Następnie Zapisz zmiany w **SimulatedDevice.cs**.

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **iot-hub\Quickstarts\read-d2c-messages**.

2. Otwórz plik **ReadDeviceToCloudMessages.cs** w wybranym edytorze. Zaktualizuj następujące zmienne i zapisz zmiany w pliku.

    | Zmienna | Wartość |
    | -------- | ----------- |
    | `EventHubsCompatibleEndpoint` | Zastąp wartość zmiennej tym punktem końcowym zgodnym z Event Hubs, który został wcześniej zanotowany. |
    | `EventHubName`                | Zastąp wartość zmiennej ścieżką zgodną Event Hubs zanotowaną wcześniej. |
    | `IotHubSasKey`                | Zastąp wartość zmiennej kluczem podstawowym usługi sporządzonym wcześniej w notatce. |

    > [!NOTE]
    > W przypadku korzystania z zestaw .NET Core SDK 2,1 należy ustawić wersję językową do podglądu, aby skompilować kod. Aby to zrobić, Otwórz plik **Read-D2C-messages. csproj** i ustaw wartość `<LangVersion>` elementu na `preview` .

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji zaplecza:

    ```cmd/sh
    dotnet restore
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację zaplecza:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez aplikację zaplecza danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do centrum przy użyciu aplikacji języka C# i odczytuje dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-dotnet.md)
