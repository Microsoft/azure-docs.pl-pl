---
title: 'Szybki Start: wysyłanie danych telemetrycznych do usługi Azure IoT Hub przy użyciu języka Java'
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka Java wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: lizross
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 01/27/2021
ms.openlocfilehash: df6673f6e8e448b0208ee53c17cf86f55d6d9f8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219169"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Szybki Start: wysyłanie danych telemetrycznych do usługi Azure IoT Hub i odczytywanie ich za pomocą aplikacji Java

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

W tym przewodniku szybki start wysyłasz dane telemetryczne do usługi Azure IoT Hub i odczytujemy je za pomocą aplikacji Java. IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki Start są wykorzystywane dwie gotowe aplikacje języka Java: jeden do wysłania telemetrii i jeden w celu odczytania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. W programie [Java długoterminowa obsługa platformy Azure i Azure Stack](/java/azure/jdk/)w obszarze **Obsługa długoterminowa** wybierz pozycję **Java 8**.

    Możesz sprawdzić bieżącą wersję języka Java na komputerze deweloperskim przy użyciu następującego polecenia:

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi).

    Możesz sprawdzić bieżącą wersję narzędzia Maven na komputerze deweloperskim przy użyciu następującego polecenia:

    ```cmd/sh
    mvn --version
    ```

* Pobierz lub Sklonuj repozytorium Azure-IoT-Samples-Java za pomocą przycisku **Code** na [stronie Azure-IoT-Samples-Java Repository](https://github.com/Azure-Samples/azure-iot-samples-java). 

    W tym artykule są wykorzystywane przykłady [symulowanych urządzeń](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/simulated-device) i [Read-D2C-messages](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) z repozytorium.

* Port 8883 otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyJavaDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyJavaDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

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

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** w wybranym edytorze tekstów.

    Zastąp wartość `connString` zmiennej parametrami połączenia urządzenia, które zostały wykonane wcześniej w notatce. Następnie Zapisz zmiany w **SimulatedDevice. Java**.

    ```java
    public class SimulatedDevice {
      // The device connection string to authenticate the device with your IoT hub.
      // Using the Azure CLI:
      // az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table

      //private static String connString = "{Your device connection string here}";    
      private static String connString = "HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}";    
     ```

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację urządzenia symulowanego:

    ```cmd/sh
    mvn clean package
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Dane wyjściowe z telemetrii wysyłanej przez urządzenie do centrum IoT Hub](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\read-d2c-messages**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** w wybranym edytorze tekstów. Zaktualizuj następujące zmienne i zapisz zmiany w pliku.

    | Zmienna | Wartość |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | Zastąp wartość zmiennej tym punktem końcowym zgodnym z Event Hubs, który został wcześniej zanotowany. |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | Zastąp wartość zmiennej ścieżką zgodną Event Hubs zanotowaną wcześniej. |
    | `IOT_HUB_SAS_KEY`                | Zastąp wartość zmiennej kluczem podstawowym usługi sporządzonym wcześniej w notatce. |

    ```java
    public class ReadDeviceToCloudMessages {
    
      private static final String EH_COMPATIBLE_CONNECTION_STRING_FORMAT = "Endpoint=%s/;EntityPath=%s;"
          + "SharedAccessKeyName=%s;SharedAccessKey=%s";
    
      // az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_ENDPOINT = "{your Event Hubs compatible endpoint}";
    
      // az iot hub show --query properties.eventHubEndpoints.events.path --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_PATH = "{your Event Hubs compatible name}";
    
      // az iot hub policy show --name service --query primaryKey --hub-name {your IoT Hub name}
      private static final String IOT_HUB_SAS_KEY = "{your service primary key}";    
    ```


3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację zaplecza:

    ```cmd/sh
    mvn clean package
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację zaplecza:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez aplikację zaplecza danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

    ![Dane wyjściowe w postaci aplikacji zaplecza odbierają Telemetria wysyłane do centrum IoT Hub](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłałeś symulowane dane telemetryczne do centrum przy użyciu aplikacji Java i odczytujesz dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-java.md)
