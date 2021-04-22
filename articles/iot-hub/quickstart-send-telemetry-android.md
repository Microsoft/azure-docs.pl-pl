---
title: Szybki start — wysyłanie danych telemetrycznych do Azure IoT Hub szybki start (Android) | Microsoft Docs
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
ms.openlocfilehash: 4bc8dfb879f362463153819cdf99869522c55c1b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863825"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Szybki start: przesyłanie danych telemetrycznych usługi IoT z urządzenia z systemem Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

W tym przewodniku Szybki start wyślesz telemetrię do Azure IoT Hub z aplikacji systemu Android uruchomionej na urządzeniu fizycznym lub symulowanym. IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start do wysyłania danych telemetrycznych jest używana wstępnie napisana aplikacja systemu Android. Dane telemetryczne będzie można odczytać z centrum IoT Hub przy użyciu usługi Azure Cloud Shell. Przed uruchomieniem aplikacji należy utworzyć centrum IoT Hub i zarejestrować urządzenie w centrum.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio z Android SDK 27](https://developer.android.com/studio/). Aby uzyskać więcej informacji, zobacz [android-installation](https://developer.android.com/studio/install). W przykładzie w tym artykule jest używany zestaw Android SDK 27.

* [Przykładowa aplikacja systemu Android.](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) Ten przykład jest częścią [repozytorium azure-iot-samples-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

* Port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym przewodniku Szybki start używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyAndroidDevice:** jest to nazwa rejestrowanego urządzenia. Zaleca się użycie urządzenia **MyAndroidDevice w pokazany** sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby _pobrać_ parametrów połączenia urządzenia dla właśnie zarejestrowanego urządzenia:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start, aby wysyłać dane telemetryczne.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

1. Otwórz przykładowy projekt GitHub dla systemu Android w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [azure-iot-sample-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample.*

2. W Android Studio otwórz *element gradle.properties* dla przykładowego projektu i zastąp symbol zastępczy **Device_Connection_String** zanotuj zanotować wcześniej parametrów połączenia urządzenia.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. W Android Studio kliknij pozycję File Sync Project with Gradle Files **(Projekt**  >  **synchronizacji plików z plikami gradle).** Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu zakończy się niepowodzeniem, może to być jedną z następujących przyczyn:
   >
   > * Wersje wtyczki Android Gradle i programu Gradle, do których odwołuje się projekt, są aktualne dla Twojej wersji Android Studio. Postępuj [zgodnie z tymi instrukcjami,](https://developer.android.com/studio/releases/gradle-plugin) aby odwołać się do odpowiedniej wersji wtyczki i programu Gradle i zainstalować ją.
   > * Umowa licencyjna dla Android SDK nie została podpisana. Postępuj zgodnie z instrukcjami w danych wyjściowych kompilacji, aby podpisać umowę licencyjną i pobrać zestaw SDK.

4. Po zakończeniu kompilacji kliknij pozycję  >  **Uruchom polecenie Uruchom "aplikację".** Skonfiguruj aplikację do uruchamiania na fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji kliknij przycisk **Start**, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT Hub:

    ![Aplikacja](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji, używając usługi Azure Cloud Shell z [rozszerzeniem IoT](/cli/azure/iot), będziesz monitorować komunikaty urządzenia wysyłane przez urządzenie z systemem Android.

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

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie, wysłasz do centrum symulowane dane telemetryczne przy użyciu aplikacji systemu Android i odczytasz dane telemetryczne z centrum przy użyciu Azure Cloud Shell.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-android.md)
