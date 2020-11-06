---
title: Szybki Start — łączenie Plug and Play przykładowego kodu urządzenia Java z platformą Azure IoT Hub | Microsoft Docs
description: Szybki Start — Kompilowanie i uruchamianie przykładowego kodu urządzenia Plug and Play IoT na potrzeby połączenia z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 32e610cbd65aafecec9bafb35a78fb8f68279da5
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421604"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-java"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play działającej w systemie Windows w celu IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Przykładowa aplikacja jest zapisywana w języku Java i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Java. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki Start w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* Java SE Development Kit 8. W programie [Java długoterminowa obsługa platformy Azure i Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)w obszarze **Obsługa długoterminowa** wybierz pozycję **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Pobieranie kodu

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu Java SDK dla platformy Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT Java SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-java) GitHub do tej lokalizacji:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Kompilowanie kod

W systemie Windows przejdź do folderu głównego sklonowanego repozytorium Java SDK.

Uruchom następujące polecenie, aby skompilować przykładową aplikację:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Przejdź do folderu *\device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* .

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

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](./quickstart-service-node.md)