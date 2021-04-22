---
title: Szybki start — kontrolowanie urządzenia za pomocą Azure IoT Hub Szybki start (Android) | Microsoft Docs
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
ms.openlocfilehash: 5946220a688206a35d0d68cd4efd4c356d27d7ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868547"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

W tym przewodniku Szybki start użyjemy metody bezpośredniej do kontrolowania symulowanego urządzenia podłączonego do Azure IoT Hub. IoT Hub to usługa platformy Azure, która umożliwia zarządzanie urządzeniami IoT z chmury i pozyskanie dużych ilości danych telemetrycznych urządzeń do chmury w celu przechowywania lub przetwarzania. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT. W tym przewodniku Szybki start są używane dwie aplikacje: aplikacja urządzenia symulowanego, która reaguje na metody bezpośrednie wywoływane z aplikacji usługi back-end i aplikację usługi, która wywołuje metodę bezpośrednią na urządzeniu z systemem Android.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio z Android SDK 27](https://developer.android.com/studio/). Aby uzyskać więcej informacji, zobacz [Instalowanie Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Przykładowa aplikacja systemu Android dla zestawu SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)urządzenia uwzględniona w [przykładach usługi Azure IoT (Java)](https://github.com/Azure-Samples/azure-iot-samples-java).

* [Przykładowa aplikacja systemu Android dla zestawu SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)usługi uwzględniona w przykładach usługi Azure IoT (Java).

* Port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym przewodniku Szybki start używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)).

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

   **MyAndroidDevice:** jest to nazwa rejestrowanego urządzenia. Zaleca się używanie urządzenia **MyAndroidDevice w pokazany** sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
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
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Zanotuj parametry połączenia usługi, które wyglądają następująco:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Te ciągi połączenia usługi różnią się od parametrów połączenia urządzenia zanotowych w poprzednim kroku.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Oba przykłady dla tego przewodnika Szybki start są częścią repozytorium azure-iot-samples-java w witrynie GitHub. Pobierz lub sklonuj repozytorium [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

Przykładową aplikację dla zestawu SDK urządzenia można uruchomić na urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android. Przykład łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Symulowane urządzenie wysyła potwierdzenie z powrotem do centrum po wykonaniu metody bezpośredniej.

1. Otwórz przykładowy projekt GitHub dla systemu Android w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [azure-iot-sample-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample.*

2. W Android Studio otwórz element *gradle.properties* przykładowego projektu i zastąp symbol zastępczy **Device_Connection_String** zanotować wcześniej zanot innymi zanotymi wartościami parametrów połączenia urządzenia.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. W Android Studio pozycję **File**  >  **Sync Project with Gradle Files (Projekt synchronizacji plików z plikami gradle).** Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu zakończy się niepowodzeniem, może to być jedną z następujących przyczyn:
   >
   > * Wersje wtyczki Android Gradle i programu Gradle, do których odwołuje się projekt, są aktualne dla Twojej wersji Android Studio. Postępuj [zgodnie z tymi instrukcjami,](https://developer.android.com/studio/releases/gradle-plugin) aby odwołać się i zainstalować poprawne wersje wtyczki i programu Gradle dla instalacji.
   > * Umowa licencyjna dla Android SDK nie została podpisana. Postępuj zgodnie z instrukcjami w danych wyjściowych kompilacji, aby podpisać umowę licencyjną i pobrać zestaw SDK.

4. Po zakończeniu kompilacji kliknij pozycję  >  **Uruchom polecenie Uruchom "aplikację".** Skonfiguruj aplikację do uruchamiania na fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji kliknij przycisk **Start**, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT Hub:

    ![Przykładowy zrzut ekranu aplikacji dla systemu Android na urządzeniu klienckim](media/quickstart-control-device-android/sample-screenshot.png)

Ta aplikacja musi być uruchomiona na urządzeniu fizycznym lub w emulatorze podczas wykonywania przykładowego zestawu SDK usługi w celu zaktualizowania interwału telemetrii w czasie wykonywania.

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji użyjemy rozszerzenia Azure Cloud Shell [IoT](/cli/azure/iot) do monitorowania komunikatów wysyłanych przez urządzenie z systemem Android.

1. Używając usługi Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać komunikaty z centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez centrum IoT Hub danych telemetrycznych wysyłanych przez urządzenie z systemem Android:

      ![Czytanie komunikatów urządzenia za pomocą interfejsu wiersza polecenia platformy Azure](media/quickstart-control-device-android/read-data.png)

Domyślnie aplikacja telemetrii wysyła dane telemetryczne z urządzenia z systemem Android co pięć sekund. W następnej sekcji użyjesz wywołania metody bezpośredniej do zaktualizowania interwału danych telemetrycznych urządzenia IoT dla systemu Android.

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja usługi łączy się z punktem końcowym po stronie usługi w centrum IoT Hub. Aplikacja wykonuje wywołania metod bezpośrednich do urządzenia za pośrednictwem centrum IoT i nasłuchuje potwierdzeń.

Uruchom tę aplikację na oddzielnym urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android.

Aplikacja IoT Hub usługi wewnętrznej zwykle działa w chmurze, gdzie łatwiej jest ograniczyć ryzyko związane z poufnymi ciągami połączenia, które steruje wszystkimi urządzeniami w IoT Hub. W tym przykładzie uruchamiamy ją jako aplikację systemu Android tylko na potrzeby pokazu. Inne wersje tego przewodnika Szybki start zawierają przykłady, które są bardziej dopasowane do typowej aplikacji usługi back-end.

1. Otwórz przykładowy projekt dla systemu Android z usługi GitHub w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [azure-iot-sample-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\service\AndroidSample.*

2. W Android Studio otwórz *okno gradle.properties* dla przykładowego projektu. Zaktualizuj wartości właściwości **ConnectionString** i **DeviceId** przy użyciu zanotowanych wcześniej parametrów połączenia usługi i zarejestrowanego identyfikatora urządzenia z systemem Android.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. W Android Studio kliknij pozycję File Sync Project with Gradle Files **(Projekt**  >  **synchronizacji plików z plikami gradle).** Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu zakończy się niepowodzeniem, może to być jedną z następujących przyczyn:
   >
   > * Wersje wtyczki Android Gradle i programu Gradle, do których odwołuje się projekt, są aktualne dla Twojej wersji Android Studio. Postępuj [zgodnie z tymi instrukcjami,](https://developer.android.com/studio/releases/gradle-plugin) aby odwołać się do odpowiedniej wersji wtyczki i programu Gradle i zainstalować ją.
   > * Umowa licencyjna dla Android SDK nie została podpisana. Postępuj zgodnie z instrukcjami w danych wyjściowych kompilacji, aby podpisać umowę licencyjną i pobrać zestaw SDK.

4. Po zakończeniu kompilacji kliknij pozycję  >  **Uruchom polecenie Uruchom "aplikację".** Skonfiguruj aplikację do uruchamiania na osobnym fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

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
