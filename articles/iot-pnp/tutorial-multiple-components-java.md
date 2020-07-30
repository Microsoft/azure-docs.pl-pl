---
title: Połącz przykład IoT Plug and Play wersja zapoznawcza kod urządzenia składnika Java do IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj Podgląd Plug and Play IoT przykład kod urządzenia Java, który używa wielu składników i łączy się z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1d16d8c54939c4f659b6a1530e2d360b957a09ad
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352848"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-java"></a>Samouczek: łączenie przykładowej aplikacji IoT Plug and Play w wersji zapoznawczej do IoT Hub (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, nawiązywania połączenia z Centrum IoT Hub i używania interfejsu wiersza polecenia platformy Azure do wyświetlania wysyłanych danych telemetrycznych. Przykładowa aplikacja jest zapisywana w języku Java i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Java. Konstruktor rozwiązań może korzystać z interfejsu wiersza polecenia platformy Azure, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami i interfejsem głównym, nawiązywania połączenia z usługą IoT Hub oraz przy użyciu narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku Java i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Java. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* Java SE Development Kit 8. W programie [Java długoterminowa obsługa platformy Azure i Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)w obszarze **Obsługa długoterminowa**wybierz pozycję **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="azure-iot-explorer"></a>Eksplorator IoT Azure

Aby móc korzystać z przykładowego urządzenia w drugiej części tego przewodnika Szybki Start, użyj narzędzia **Azure IoT Explorer** . [Pobierz i zainstaluj najnowszą wersję programu Azure IoT Explorer](./howto-use-iot-explorer.md) dla danego systemu operacyjnego.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby pobrać _Parametry połączenia usługi IoT Hub_ dla centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Możesz również użyć narzędzia Azure IoT Explorer, aby znaleźć parametry połączenia usługi IoT Hub.

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla urządzenia dodanego do centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Pobieranie kodu

W tym samouczku przygotujesz środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu Java SDK dla platformy Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT Java SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-java) GitHub do tej lokalizacji:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Oczekiwanie na ukończenie tej operacji.

## <a name="build-the-code"></a>Kompilowanie kod

W systemie Windows przejdź do folderu głównego sklonowanego repozytorium Java SDK. Następnie przejdź do folderu *\device\iot-Device-samples\pnp-Device-sample\temerature-Controller-Device-Sample* .

Uruchom następujące polecenie, aby skompilować przykładową aplikację:

```java
mvn clean package
```

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Utwórz zmienną środowiskową o nazwie **IOTHUB_DEVICE_CONNECTION_STRING** do przechowywania parametrów połączenia urządzenia, które zostały wcześniej wykonane.

Aby uruchomić przykładową aplikację, uruchom następujące polecenie:

```java
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje urządzenie kontrolera temperatury Plug and Play IoT. Model ten przykład implementuje użycie [wielu składników](concepts-components.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Kod urządzenia używa standardowej `DeviceClient` klasy do nawiązywania połączenia z Centrum IoT Hub. Urządzenie wysyła identyfikator modelu DTDL, który implementuje w żądaniu połączenia. Urządzenie, które wysyła identyfikator modelu, jest urządzeniem Plug and Play IoT:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

Identyfikator modelu jest przechowywany w kodzie, jak pokazano w poniższym fragmencie kodu:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Po połączeniu urządzenia z usługą IoT Hub kod rejestruje programy obsługi poleceń.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Istnieją osobne programy obsługi dla wymaganych aktualizacji właściwości na dwóch składnikach termostatu:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Przykładowy kod wysyła dane telemetryczne z każdego składnika termostatu:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading`Metoda używa `PnpHhelper` klasy do tworzenia komunikatów dla każdego składnika:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper`Klasa zawiera inne przykładowe metody, których można użyć z modelem wielu składników.

Użyj narzędzia Azure IoT Explorer, aby wyświetlić dane telemetryczne i właściwości z dwóch składników termostatu:

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Wiele urządzeń składników w programie Azure IoT Explorer":::

Możesz również użyć narzędzia Azure IoT Explorer do wywołania poleceń w dowolnym z dwóch składników termostatu lub w interfejsie głównym.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak połączyć urządzenie z usługą IoT Plug and Play ze składnikami do centrum IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dla deweloperów modelu IoT Plug and Play w wersji zapoznawczej](concepts-developer-guide.md)
