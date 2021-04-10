---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 3bf5ac4e01bca3bfc3cc8720a068bc53830b4747
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612375"
---
W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, nawiązywania połączenia z Centrum IoT Hub i używania interfejsu wiersza polecenia platformy Azure do wyświetlania wysyłanych danych telemetrycznych. Przykładowa aplikacja jest zapisywana w języku Java i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Java. Konstruktor rozwiązań może korzystać z interfejsu wiersza polecenia platformy Azure, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku Java i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Java. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Pobierz przykładowy kod.
> * Kompiluj przykładowy kod.
> * Uruchom przykładową aplikację urządzenia i sprawdź, czy nawiązuje połączenie z Centrum IoT Hub.
> * Przejrzyj kod źródłowy.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Aby ukończyć ten samouczek w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* Java SE Development Kit 8. W programie [Java długoterminowa obsługa platformy Azure i Azure Stack](/java/azure/jdk/)w obszarze **Obsługa długoterminowa** wybierz pozycję **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Pobieranie kodu

Jeśli zakończysz [Przewodnik Szybki Start: Podłącz przykładową aplikację urządzenia IoT Plug and Play działającą w systemie Windows do IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md), repozytorium zostało już sklonowane.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT Java SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-java) GitHub do tej lokalizacji:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Oczekiwanie na ukończenie tej operacji.

## <a name="build-the-code"></a>Kompilowanie kod

W systemie Windows przejdź do folderu głównego sklonowanego repozytorium Java SDK. Uruchom następujące polecenie, aby skompilować zależności:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Aby uruchomić przykładową aplikację, przejdź do folderu *\device\iot-Device-samples\pnp-Device-sample\temperature-Controller-Device-Sample* i uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje urządzenie kontrolera temperatury Plug and Play IoT. Model ten przykład implementuje użycie [wielu składników](../articles/iot-pnp/concepts-modeling-guide.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

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

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Wiele urządzeń składników w programie Azure IoT Explorer":::

Możesz również użyć narzędzia Azure IoT Explorer do wywołania poleceń z jednego z dwóch składników termostatu lub w składniku domyślnym.
