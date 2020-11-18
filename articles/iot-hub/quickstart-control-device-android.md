---
title: Kontrolowanie urządzenia z poziomu usługi Azure IoT Hub — Szybki start (Android) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje Java. Jedna z aplikacji to aplikacja usługi, która może zdalnie kontrolować urządzenia podłączone do centrum. Druga aplikacja jest uruchamiana na urządzeniu fizycznym lub symulowanym podłączonym do centrum, które można kontrolować zdalnie.
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
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 969ae6dc1e3667bc360890c292371a0a9b1ba2dc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844595"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

W tym przewodniku szybki start użyjesz metody bezpośredniej do kontrolowania symulowanego urządzenia połączonego z platformą Azure IoT Hub. IoT Hub to usługa platformy Azure, która umożliwia zarządzanie urządzeniami IoT z chmury i pozyskiwanie dużych ilości danych telemetrycznych urządzeń do chmury w celu przechowania lub przetworzenia. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT. Ten przewodnik Szybki Start korzysta z dwóch aplikacji: symulowanej aplikacji urządzenia, która odpowiada na metody bezpośrednie wywoływane z aplikacji usługi zaplecza i aplikacji usługi, która wywołuje metodę bezpośrednią na urządzeniu z systemem Android.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio z Android SDK 27](https://developer.android.com/studio/). Aby uzyskać więcej informacji, zobacz [Install Android Studio](https://developer.android.com/studio/install).

* Usługi [git](https://git-scm.com/download/).

* [Przykładowa aplikacja dla systemu Android dla zestawu SDK urządzeń](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), dołączona do [przykładów usługi Azure IoT (Java)](https://github.com/Azure-Samples/azure-iot-samples-java).

* [Przykładowa aplikacja dla systemu Android zestawu SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)dostępna w usłudze Azure IoT Samples (Java).

* Port 8883 otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-android.md), możesz pominąć ten krok i użyć utworzonego już centrum IoT Hub.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-android.md), możesz pominąć ten krok i użyć tego samego urządzenia, które zarejestrowano w poprzednim przewodniku Szybki start.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyAndroidDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyAndroidDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="retrieve-the-service-connection-string"></a>Pobieranie parametrów połączenia usługi

Potrzebne będą także _parametry połączenia usługi_, aby umożliwić aplikacji usługi zaplecza nawiązywanie połączenia z centrum IoT Hub w celu wykonywania metod i pobierania komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

**YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Zanotuj parametry połączenia usługi, które wyglądają następująco:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Te parametry połączenia z usługą różnią się od parametrów połączenia urządzenia zanotowanych w poprzednim kroku.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Oba przykłady dla tego przewodnika Szybki Start są częścią repozytorium Azure-IoT-Samples-Java w serwisie GitHub. Pobierz lub sklonuj repozytorium [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

Przykładową aplikację dla zestawu SDK urządzenia można uruchomić na urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android. Przykład łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Symulowane urządzenie wysyła potwierdzenie z powrotem do centrum po wykonaniu metody bezpośredniej.

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

    ![Przykładowy zrzut ekranu aplikacji dla systemu Android urządzenia klienckiego](media/quickstart-control-device-android/sample-screenshot.png)

Ta aplikacja musi pozostać uruchomiona na urządzeniu fizycznym lub w emulatorze podczas wykonywania przykładu zestawu SDK usługi w celu zaktualizowania interwału telemetrii w czasie wykonywania.

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) do monitorowania komunikatów wysyłanych przez urządzenie z systemem Android.

1. Używając usługi Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać komunikaty z centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez centrum IoT Hub danych telemetrycznych wysyłanych przez urządzenie z systemem Android:

      ![Czytanie komunikatów urządzenia za pomocą interfejsu wiersza polecenia platformy Azure](media/quickstart-control-device-android/read-data.png)

Domyślnie aplikacja telemetrii wysyła dane telemetryczne z urządzenia z systemem Android co pięć sekund. W następnej sekcji użyjesz wywołania metody bezpośredniej do zaktualizowania interwału danych telemetrycznych urządzenia IoT dla systemu Android.

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja usługi łączy się z punktem końcowym po stronie usługi w centrum IoT Hub. Aplikacja wykonuje bezpośrednie wywołania metody do urządzenia za pośrednictwem Centrum IoT Hub i nasłuchuje pod kątem potwierdzeń.

Uruchom tę aplikację na oddzielnym urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android.

IoT Hub aplikacja usługi zaplecza jest zwykle uruchamiana w chmurze, w której łatwiej jest ograniczyć ryzyko związane z parametrami połączenia poufnych, które kontrolują wszystkie urządzenia na IoT Hub. W tym przykładzie uruchamiamy ją jako aplikację systemu Android tylko na potrzeby pokazu. W innych wersjach językowych tego przewodnika Szybki Start przedstawiono przykłady, które dokładnie dopasowują się do typowej aplikacji usługi zaplecza.

1. Otwórz przykładowy projekt dla systemu Android z usługi GitHub w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii [usługi Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) Repository: *\azure-IoT-Samples-java\iot-hub\Samples\service\AndroidSample*.

2. W Android Studio Otwórz *Gradle. Properties* dla przykładowego projektu. Zaktualizuj wartości właściwości **ConnectionString** i **DeviceID** przy użyciu ponotowanych wcześniej parametrów połączenia usługi i identyfikatora urządzenia z systemem Android.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. W Android Studio kliknij pozycję **plik**  >  **Synchronizacja plików z plikami Gradle**. Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu nie powiedzie się, może to być spowodowane jedną z następujących przyczyn:
   >
   > * Wersje wtyczki Gradle systemu Android i Gradle, do których odwołuje się projekt, są nieaktualne dla używanej wersji programu Android Studio. Postępuj zgodnie z [tymi instrukcjami](https://developer.android.com/studio/releases/gradle-plugin) , aby odnieść się do i zainstalować odpowiednie wersje wtyczki i Gradle dla danej instalacji.
   > * Umowa licencyjna dla Android SDK nie została podpisana. Postępuj zgodnie z instrukcjami podanymi w danych wyjściowych kompilacji w celu podpisania umowy licencyjnej i pobrania zestawu SDK.

4. Po zakończeniu kompilacji kliknij pozycję **Uruchom**  >  **Uruchom polecenie "App"**. Skonfiguruj aplikację do uruchamiania na osobnym fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji zaktualizuj ustawienie **Set Messaging Interval** (Ustaw interwał komunikatów) na wartość **1000** i kliknij pozycję **Invoke** (Wywołaj).

    Interwał telemetrii komunikatów jest podawany w milisekundach. Domyślny interwał telemetrii przykładowego urządzenia został ustawiony na 5 sekund. Ta zmiana spowoduje zaktualizowanie urządzenia IoT z systemem Android tak, aby dane telemetryczne były wysyłane co sekundę.

    ![Wprowadzanie interwału telemetrii](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Aplikacja otrzyma potwierdzenie wskazujące, czy metoda została wykonana pomyślnie, czy nie.

    ![Potwierdzenie metody bezpośredniej](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji zaplecza oraz odpowiedziano na wywołanie metody bezpośredniej w aplikacji urządzenia symulowanego.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](tutorial-routing.md)
