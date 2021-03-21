---
title: Szybki Start — wysyłanie danych telemetrycznych do platformy Azure IoT Hub szybki start (Android) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz przykładową aplikację systemu Android wysyłającą symulowane dane telemetryczne do centrum IoT Hub oraz odczytującą dane telemetryczne z centrum IoT Hub na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e1e44f5aa4250d63c2addafd2661b09bae2b38e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199921"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Szybki start: przesyłanie danych telemetrycznych usługi IoT z urządzenia z systemem Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

W tym przewodniku szybki start wysyłasz dane telemetryczne do IoT Hub platformy Azure z aplikacji systemu Android działającej na urządzeniu fizycznym lub symulowanym. IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. Ten przewodnik Szybki Start korzysta z gotowej do wysłania telemetrii aplikacji systemu Android. Dane telemetryczne będzie można odczytać z centrum IoT Hub przy użyciu usługi Azure Cloud Shell. Przed uruchomieniem aplikacji należy utworzyć centrum IoT Hub i zarejestrować urządzenie w centrum.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio z Android SDK 27](https://developer.android.com/studio/). Aby uzyskać więcej informacji, zobacz [Android-Installation](https://developer.android.com/studio/install). W przykładzie w tym artykule jest używany zestaw Android SDK 27.

* [Przykładowa aplikacja dla systemu Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Ten przykład jest częścią repozytorium [Azure-IoT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) .

* Port 8883 otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyAndroidDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyAndroidDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki Start, aby wysłać dane telemetryczne.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

1. Otwórz przykładowy projekt GitHub dla systemu Android w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii [usługi Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) Repository: *\azure-IoT-Samples-java\iot-hub\Samples\device\AndroidSample*.

2. W Android Studio Otwórz *Gradle. Properties* dla przykładowego projektu i Zastąp symbol zastępczy **Device_Connection_String** parametrami połączenia urządzenia, które zostały wykonane wcześniej.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. W Android Studio kliknij pozycję **plik**  >  **Synchronizacja plików z plikami Gradle**. Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu nie powiedzie się, może to być spowodowane jedną z następujących przyczyn:
   >
   > * Wersje wtyczki Gradle systemu Android i Gradle, do których odwołuje się projekt, są nieaktualne dla używanej wersji programu Android Studio. Postępuj zgodnie z [tymi instrukcjami](https://developer.android.com/studio/releases/gradle-plugin) , aby odnieść się do i zainstalować odpowiednie wersje wtyczki i Gradle dla danej instalacji.
   > * Umowa licencyjna dla Android SDK nie została podpisana. Postępuj zgodnie z instrukcjami podanymi w danych wyjściowych kompilacji w celu podpisania umowy licencyjnej i pobrania zestawu SDK.

4. Po zakończeniu kompilacji kliknij pozycję **Uruchom**  >  **Uruchom polecenie "App"**. Skonfiguruj aplikację do uruchamiania na fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji kliknij przycisk **Start**, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT Hub:

    ![Aplikacja](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji, używając usługi Azure Cloud Shell z [rozszerzeniem IoT](/cli/azure/ext/azure-iot/iot), będziesz monitorować komunikaty urządzenia wysyłane przez urządzenie z systemem Android.

1. Używając usługi Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać komunikaty z centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez centrum IoT Hub danych telemetrycznych wysyłanych przez urządzenie z systemem Android:

      ![Czytanie komunikatów urządzenia za pomocą interfejsu wiersza polecenia platformy Azure](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do koncentratora przy użyciu aplikacji systemu Android, i odczytuje dane telemetryczne z centrum przy użyciu Azure Cloud Shell.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-android.md)
