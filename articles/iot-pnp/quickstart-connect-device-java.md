---
title: Łączenie Plug and Play IoT przykład kod urządzenia Java w wersji zapoznawczej IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj przykładowe kod urządzenia w usłudze IoT Plug and Play w wersji zapoznawczej, który łączy się z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b89c92e675ab505878f350e9716af95050ce28b2
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352935"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-java"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej w systemie Windows do IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Przykładowa aplikacja jest zapisywana w języku Java i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Java. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

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

## <a name="download-the-code"></a>Pobieranie kodu

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu Java SDK dla platformy Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT Java SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-java) GitHub do tej lokalizacji:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="build-the-code"></a>Kompilowanie kod

W systemie Windows przejdź do folderu głównego sklonowanego repozytorium Java SDK. Następnie przejdź do folderu *\device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* .

Uruchom następujące polecenie, aby skompilować przykładową aplikację:

```cmd
mvn clean package
```

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Utwórz zmienną środowiskową o nazwie **IOTHUB_DEVICE_CONNECTION_STRING** do przechowywania parametrów połączenia urządzenia, które zostały wcześniej wykonane.

Aby uruchomić przykładową aplikację, uruchom następujące polecenie:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje proste urządzenie z termostatem Plug and Play. Model tego przykładowego implementuje nie korzysta ze [składników](concepts-components.md)Plug and Play IoT. [Plik modelu DTDL dla urządzenia termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

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

Kod, który aktualizuje właściwości, obsługuje polecenia i wysyła dane telemetryczne, jest identyczny z kodem dla urządzenia, które nie korzysta z Konwencji Plug and Play IoT.

Przykład używa biblioteki JSON do analizowania obiektów JSON w ładunków wysyłanych z Centrum IoT:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
