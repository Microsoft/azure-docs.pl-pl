---
title: Kontrolowanie urządzenia z poziomu usługi Azure IoT Hub — Szybki start (.NET) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje C#. Jedna z aplikacji to aplikacja usługi, która może zdalnie kontrolować urządzenia podłączone do centrum. Druga z aplikacji symuluje urządzenie podłączone do centrum, które można kontrolować zdalnie.
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
ms.date: 03/04/2020
ms.openlocfilehash: 39cfa64b756ef6bf20f8cbf3d6e8f8a25e81c674
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092880"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia zarządzanie urządzeniami IoT z chmury oraz pozyskiwanie dużych ilości danych telemetrycznych urządzeń do chmury w celu przechowania lub przetworzenia. W tym przewodniku Szybki start użyjesz *metody bezpośredniej* do kontrolowania urządzenia symulowanego podłączonego do centrum IoT. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji .NET:

* Aplikacja symulowanego urządzenia, która reaguje na metody bezpośrednie wywoływane z aplikacji usługi. Aby odbierać wywołania metod bezpośrednich, ta aplikacja łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT.

* Aplikacja usługi, która wywołuje metody bezpośrednie na symulowanym urządzeniu. Aby wywoływać metody bezpośrednie na urządzeniu, ta aplikacja łączy się z punktem końcowym po stronie usługi w centrum IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku C#. Na komputerze deweloperskim jest wymagany zestaw .NET Core SDK 3,1 lub nowszy.

    Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://www.microsoft.com/net/download/all).

    Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

    ```cmd/sh
    dotnet --version
    ```
* Jeśli jeszcze tego nie zrobiono, Pobierz przykłady w języku C# usługi Azure IoT z programu https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip i Wyodrębnij archiwum zip.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-dotnet.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-dotnet.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDotnetDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyDotnetDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="retrieve-the-service-connection-string"></a>Pobieranie parametrów połączenia usługi

Potrzebne są również _Parametry połączenia usługi_ IoT Hub, aby umożliwić aplikacji usługi łączenie się z centrum i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Zanotuj parametry połączenia usługi, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Te parametry połączenia z usługą różnią się od parametrów połączenia urządzenia zanotowanych w poprzednim kroku.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Aplikacja urządzenia symulowanego łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Symulowane urządzenie wysyła potwierdzenie z powrotem do centrum po wykonaniu metody bezpośredniej.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **IoT-hub\Quickstarts\SimulatedDeviceWithCommand** .

2. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

3. W oknie terminalu lokalnego Uruchom następujące polecenie, aby skompilować i uruchomić aplikację symulowanego urządzenia, zastępując `{DeviceConnectionString}` ją zanotowanymi wcześniej parametrami połączenia urządzenia:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja usługi łączy się z punktem końcowym po stronie usługi w centrum IoT Hub. Aplikacja wykonuje bezpośrednie wywołania metody do urządzenia za pośrednictwem Centrum IoT Hub i nasłuchuje pod kątem potwierdzeń. Aplikacja usługi IoT Hub jest zwykle uruchamiana w chmurze.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **IoT-hub\Quickstarts\InvokeDeviceMethod** .

2. W oknie terminalu lokalnego Uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji usługi:

    ```cmd/sh
    dotnet restore
    ```

3. W oknie lokalnego terminala Uruchom następujące polecenia, aby skompilować i uruchomić aplikację usługi, zastępując `{ServiceConnectionString}` ją ponotowanymi wcześniej parametrami połączenia usługi:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Na poniższym zrzucie ekranu przedstawiono dane wyjściowe, gdy aplikacja wysyła wywołanie metody bezpośredniej do urządzenia i otrzymuje potwierdzenie:

    ![Uruchom aplikację usługi](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Po uruchomieniu aplikacji usługi zobaczysz komunikat w oknie konsoli, na którym uruchomiono symulowane urządzenie, i szybkość, z jaką są wysyłane zmiany komunikatów:

    ![Zmiana w kliencie symulowanym](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji usługi i odpowiedziałem na wywołanie metody bezpośredniej w aplikacji symulowanego urządzenia.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](tutorial-routing.md)
