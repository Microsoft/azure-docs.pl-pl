---
title: Szybki Start — wysyłanie danych telemetrycznych do platformy Azure IoT Hub szybki start (C) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka C wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: aad28c704ba8c46f4c2d0111e545dc4d2381250d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201944"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie ich z użyciem aplikacji zaplecza (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

W tym przewodniku Szybki start do wysyłania danych telemetrycznych do centrum IoT służy przykładowa aplikacja w języku C z [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Zestawy SDK urządzeń Azure IoT są zapisywane w formacie [ANSI C (C99)](https://wikipedia.org/wiki/C99) zapewniającym przenośność i zgodność na wielu platformach. Zanim uruchomisz przykładowy kod, utworzysz centrum IoT i zarejestrujesz w nim urządzenie symulowane.

Ten artykuł jest przeznaczony dla systemu Windows, ale można również wykonać ten przewodnik Szybki Start w systemie Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) z włączonym obciążeniem ["Programowanie aplikacji klasycznych w języku C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .

* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start będziesz używać [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). 

W przypadku następujących środowisk można użyć zestawu SDK, instalując następujące pakiety i biblioteki:

* **Linux**: apt-get Packages są dostępne dla Ubuntu 16,04 i 18,04 przy użyciu następujących architektur procesora CPU: amd64, arm64, armhf i i386. Aby uzyskać więcej informacji, zobacz [Tworzenie projektu klienta urządzenia w języku C przy użyciu pakietu apt-get w systemie Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: dla deweloperów tworzących aplikacje dla urządzeń na platformie mbed opublikowano bibliotekę i przykłady, które pomogą Ci rozpocząć pracę w ciągu kilku minut za pomocą usługi Azure IoT Hub. Aby uzyskać więcej informacji, zobacz [Korzystanie z biblioteki mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**.: Jeśli opracowujesz na Arduino, możesz skorzystać z biblioteki Azure IoT dostępnej w programie Arduino IDE Library Manager. Aby uzyskać więcej informacji, zobacz [Biblioteka usługi Azure IoT Hub dla środowiska Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**. Zestaw SDK urządzeń centrum IoT na potrzeby programowania urządzeń na komputerach Mac i w systemie iOS jest dostępny w postaci aplikacji CocoaPods. Aby uzyskać więcej informacji, zobacz [Przykłady z systemu iOS dotyczące usługi Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

Jednak w tym przewodniku szybki start utworzysz środowisko programistyczne służące do klonowania i kompilowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start.

1. Pobierz [system kompilacji CMAKE](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK.

3. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Użyj znacznika znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

4. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Uruchom następujące polecenie, aby skompilować wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`.

    ```cmd
    cmake ..
    ```

    Jeśli `cmake` nie znajdziesz kompilatora języka C++, może wystąpić błąd kompilacji podczas wykonywania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](/cli/azure/ext/azure-iot/iot) , aby zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyCDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyCDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła parametry połączenia jako symulowane dane telemetryczne.

1. Używając edytora tekstu, otwórz plik źródłowy iothub_convenience_sample.c i przejrzyj przykładowy kod umożliwiający wysyłanie danych telemetrycznych. Plik znajduje się w następującej lokalizacji w katalogu roboczym, w którym Sklonowano zestaw SDK języka C usługi Azure IoT:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Znajdź deklarację stałej `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Zastąp wartość `connectionString` stałą parametrami połączenia urządzenia, które zostały wykonane wcześniej w notatce. Następnie zapisz zmiany w pliku **iothub_convenience_sample.c**.

3. W lokalnym oknie terminalu przejdź do katalogu projektu *iothub_convenience_sample* w katalogu narzędzia CMake, który został utworzony w zestawie SDK usługi Azure IoT dla języka C. Wprowadź następujące polecenie w katalogu roboczym:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Uruchom narzędzie CMake w lokalnym oknie terminalu, aby skompilować przykład za pomocą zaktualizowanej wartości `connectionString`:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. W oknie terminalu lokalnego Uruchom następujące polecenie, aby uruchomić aplikację symulowanego urządzenia:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](/cli/azure/ext/azure-iot/iot) do monitorowania komunikatów urządzeń wysyłanych przez symulowane urządzenie.

1. Używając usługi Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać komunikaty z centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Czytanie komunikatów urządzenia za pomocą interfejsu wiersza polecenia platformy Azure](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do koncentratora przy użyciu aplikacji C i odczytuje dane telemetryczne z centrum przy użyciu Azure Cloud Shell.

Aby dowiedzieć się więcej na temat programowania za pomocą zestawu SDK usługi Azure IoT Hub dla języka C, przejdź do następującego przewodnika z instrukcjami:

> [!div class="nextstepaction"]
> [Programowanie przy użyciu zestawu SDK usługi Azure IoT Hub dla języka C](iot-hub-devguide-develop-for-constrained-devices.md)
