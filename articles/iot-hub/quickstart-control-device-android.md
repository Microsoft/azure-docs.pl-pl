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
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 8c3a9c6c5e835104675239882d2a1929a3c07c82
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771032"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

W tym przewodniku Szybki start można użyć metody bezpośredniej do kontrolowania symulowanego urządzenia połączonego z usługą Azure IoT Hub. Usługa IoT Hub to usługa platformy Azure, która umożliwia zarządzanie urządzeniami IoT z chmury i pozyskiwania dużych ilości danych telemetrycznych urządzeń do chmury w celu przechowywania lub przetwarzania. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT. Ten przewodnik Szybki start używa dwóch aplikacji: symulowanej aplikacji urządzenia, która odpowiada na bezpośrednie metody wywoływane z aplikacji usługi zaplecza i aplikacji usługi, która wywołuje metodę bezpośrednią na urządzeniu z systemem Android.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio z Androidem SDK 27](https://developer.android.com/studio/). Aby uzyskać więcej informacji, zobacz [Instalowanie Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Przykładowa aplikacja SDK urządzenia dla systemu Android,](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)zawarta w [przykładowych przykładach usługi Azure IoT Samples (Java)](https://github.com/Azure-Samples/azure-iot-samples-java).

* [Przykładowa aplikacja SDK usługi dla systemu Android,](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)uwzględniona w przykładowych przykładach usługi Azure IoT (Java).

* Port 8883 otwarty w zaporze. Próbka urządzenia w tym przewodniku Szybki start korzysta z protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Dodawanie rozszerzenia IoT platformy Azure

Uruchom następujące polecenie, aby dodać rozszerzenie IoT usługi Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IoT dodaje IoT Hub, IoT Edge i Usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS) polecenia do interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-android.md), możesz pominąć ten krok i użyć utworzonego już centrum IoT Hub.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-android.md), możesz pominąć ten krok i użyć tego samego urządzenia, które zarejestrowano w poprzednim przewodniku Szybki start.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyAndroidDevice**: To jest nazwa urządzenia, które rejestrujesz. Zaleca się stosowanie **MyAndroidDevice,** jak pokazano na rysunku. Jeśli wybierzesz inną nazwę urządzenia, musisz również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

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

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

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

Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Ten ciąg połączenia usługi różni się od ciągu połączenia urządzenia, który został odnotowany w poprzednim kroku.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Oba przykłady dla tego przewodnika Szybki start są częścią repozytorium azure-iot-samples-java w usłudze GitHub. Pobierz lub sklonuj repozytorium [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

Przykładową aplikację dla zestawu SDK urządzenia można uruchomić na urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android. Przykład łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Symulowane urządzenie wysyła potwierdzenie z powrotem do koncentratora po wykonaniu metody bezpośredniej.

1. Otwórz przykładowy projekt GitHub dla systemu Android w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. W programie Android Studio otwórz *gradle.properties* dla przykładowego projektu i zastąp symbol zastępczy **Device_Connection_String** ciągiem połączenia urządzenia, o których wcześniej zanotowano.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. W programie Android Studio kliknij pozycję Projekt synchronizacji **plików** > **z plikami gradle**. Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu nie powiedzie się, może to być z jednego z następujących powodów:
   >
   > * Wersje wtyczki Android Gradle i Gradle, do których odwołuje się projekt, są nieaktualne dla Twojej wersji Android Studio. Postępuj zgodnie z [tymi instrukcjami,](https://developer.android.com/studio/releases/gradle-plugin) aby odwołać się i zainstalować odpowiednie wersje wtyczki i Gradle dla instalacji.
   > * Umowa licencyjna dla sdk systemu Android nie została podpisana. Postępuj zgodnie z instrukcjami w kompilacji wyjściowej, aby podpisać umowę licencyjną i pobrać zestaw SDK.

4. Po zakończeniu kompilacji kliknij przycisk **Uruchom** > **polecenie Uruchom 'app'**. Skonfiguruj aplikację do uruchamiania na fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji kliknij przycisk **Start**, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT Hub:

    ![Przykładowy zrzut ekranu aplikacji android na urządzeniu klienckim](media/quickstart-control-device-android/sample-screenshot.png)

Ta aplikacja musi pozostać uruchomiony na urządzeniu fizycznym lub emulatorze podczas wykonywania próbki SDK usługi, aby zaktualizować interwał telemetrii w czasie wykonywania.

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji użyjesz usługi Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) do monitorowania wiadomości wysyłanych przez urządzenie z systemem Android.

1. Używając usługi Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać komunikaty z centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez centrum IoT Hub danych telemetrycznych wysyłanych przez urządzenie z systemem Android:

      ![Czytanie komunikatów urządzenia za pomocą interfejsu wiersza polecenia platformy Azure](media/quickstart-control-device-android/read-data.png)

Domyślnie aplikacja telemetrii wysyła dane telemetryczne z urządzenia z systemem Android co pięć sekund. W następnej sekcji użyjesz wywołania metody bezpośredniej do zaktualizowania interwału danych telemetrycznych urządzenia IoT dla systemu Android.

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja usługi łączy się z punktem końcowym po stronie usługi w centrum IoT Hub. Aplikacja wykonuje bezpośrednie wywołania metody do urządzenia za pośrednictwem centrum IoT hub i nasłuchuje potwierdzeń.

Uruchom tę aplikację na oddzielnym urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android.

Aplikacja usługi zaplecza usługi Usługi Usługi IoT Hub zazwyczaj działa w chmurze, gdzie łatwiej jest ograniczyć ryzyko związane z poufnym ciągiem połączenia, który kontroluje wszystkie urządzenia w Centrum IoT Hub. W tym przykładzie uruchamiamy ją jako aplikację systemu Android tylko na potrzeby pokazu. Inne wersje językowe tego przewodnika Szybki start zawierają przykłady, które są bardziej zgodne z typową aplikacją usługi zaplecza.

1. Otwórz przykładowy projekt dla systemu Android z usługi GitHub w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. W programie Android Studio otwórz *gradle.properties* dla przykładowego projektu. Zaktualizuj wartości dla **ConnectionString** i **DeviceId** właściwości z ciągu połączenia usługi, który został opisany wcześniej i identyfikator urządzenia z systemem Android, który został zarejestrowany.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. W programie Android Studio kliknij pozycję Projekt synchronizacji **plików** > **z plikami gradle**. Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu nie powiedzie się, może to być z jednego z następujących powodów:
   >
   > * Wersje wtyczki Android Gradle i Gradle, do których odwołuje się projekt, są nieaktualne dla Twojej wersji Android Studio. Postępuj zgodnie z [tymi instrukcjami,](https://developer.android.com/studio/releases/gradle-plugin) aby odwołać się i zainstalować odpowiednie wersje wtyczki i Gradle dla instalacji.
   > * Umowa licencyjna dla sdk systemu Android nie została podpisana. Postępuj zgodnie z instrukcjami w kompilacji wyjściowej, aby podpisać umowę licencyjną i pobrać zestaw SDK.

4. Po zakończeniu kompilacji kliknij przycisk **Uruchom** > **polecenie Uruchom 'app'**. Skonfiguruj aplikację do uruchamiania na osobnym fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji zaktualizuj ustawienie **Set Messaging Interval** (Ustaw interwał komunikatów) na wartość **1000** i kliknij pozycję **Invoke** (Wywołaj).

    Interwał telemetrii komunikatów jest podawany w milisekundach. Domyślny interwał telemetrii przykładowego urządzenia został ustawiony na 5 sekund. Ta zmiana spowoduje zaktualizowanie urządzenia IoT z systemem Android tak, aby dane telemetryczne były wysyłane co sekundę.

    ![Wprowadzanie interwału telemetrii](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Aplikacja otrzyma potwierdzenie wskazujące, czy metoda została wykonana pomyślnie, czy nie.

    ![Potwierdzenie metody bezpośredniej](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji zaplecza oraz odpowiedziano na wywołanie metody bezpośredniej w aplikacji urządzenia symulowanego.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](tutorial-routing.md)
