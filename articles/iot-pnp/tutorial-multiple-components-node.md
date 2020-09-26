---
title: Łączenie przykładowego programu IoT Plug and Play wersja zapoznawcza Node.js kod urządzenia składnika do IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj przykład Plug and Play IoT Node.js kod urządzenia, który używa wielu składników i łączy się z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js
ms.openlocfilehash: 24bfbf4199671da497844444a57e566e66eb8c90
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308235"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Samouczek: łączenie przykładowej aplikacji IoT Plug and Play w wersji zapoznawczej do IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami i interfejsem głównym, nawiązywania połączenia z usługą IoT Hub oraz przy użyciu narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana dla Node.js i jest zawarta w zestawie SDK urządzeń IoT Hub platformy Azure dla Node.js. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz Node.js na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Eksplorator IoT Azure

Aby można było korzystać z przykładowego urządzenia w drugiej części tego samouczka, należy użyć narzędzia **Azure IoT Explorer** . [Pobierz i zainstaluj najnowszą wersję programu Azure IoT Explorer](./howto-use-iot-explorer.md) dla danego systemu operacyjnego.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby pobrać _Parametry połączenia usługi IoT Hub_ dla centrum. Zanotuj te parametry połączenia w dalszej części tego samouczka:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Możesz również użyć narzędzia Azure IoT Explorer, aby znaleźć parametry połączenia usługi IoT Hub.

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla urządzenia dodanego do centrum. Zanotuj te parametry połączenia w dalszej części tego samouczka:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Pobieranie kodu

W tym samouczku przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK urządzeń IoT Hub Azure dla Node.js.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [Microsoft Azure IoT SDK dla Node.js](https://github.com/Azure/azure-iot-sdk-node) repozytorium GitHub do tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Wykonanie tej operacji może potrwać kilka minut.

## <a name="install-required-libraries"></a>Instalowanie wymaganych bibliotek

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie Plug and Play z wieloma składnikami i interfejsem głównym, który nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. W oknie terminalu lokalnego przejdź do folderu sklonowanego repozytorium i przejdź do folderu */Azure-IoT-SDK-Node/Device/Samples/PnP* . Następnie uruchom następujące polecenie, aby zainstalować wymagane biblioteki:

```cmd/sh
npm install
```

Spowoduje to zainstalowanie odpowiednich plików npm wymaganych do uruchomienia przykładów w folderze.

1. Skonfiguruj zmienną środowiskową przy użyciu parametrów połączenia urządzenia, które zostały wcześniej wykonane przez użytkownika:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>Przeglądanie kodu

Przejdź do folderu *Azure-IoT-SDK-node\device\samples\pnp* .

Folder *Azure-IoT-SDK-node\device\samples\pnp* zawiera przykładowy kod dla urządzenia kontrolera temperatury Plug and Play IoT.

Kod w pliku *pnpTemperatureController.js* implementuje urządzenie kontrolera Plug and Play IoT. Model ten przykład implementuje użycie [wielu składników](concepts-components.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Otwórz plik *pnpTemperatureController.js* w wybranym edytorze kodu. Przykładowy kod pokazuje, jak:

1. Zdefiniuj, `modelId` który jest DTMI dla zaimplementowanego urządzenia. Ten DTMI jest zdefiniowany przez użytkownika i musi być zgodny z DTMIem [kontrolera temperatury DTDL model](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

2. Zaimplementuj składniki zdefiniowane w modelu DTDL kontrolera temperatury. Składniki w kontrolerze temperatury rzeczywistej powinny implementować te dwa interfejsy. Te dwa interfejsy zostały już opublikowane w centralnym repozytorium. W tym przykładzie dwa interfejsy:
  - Sterownika
  - Informacje o urządzeniu opracowane przez platformę Azure

3. Zdefiniuj nazwy składników. Ten przykład ma dwa termostaty i jeden składnik informacji o urządzeniu.

4. Zdefiniuj nazwę polecenia. Są to polecenia, na które urządzenie reaguje.

5. Zdefiniuj `serialNumber` stałą. `serialNumber`To urządzenie zostało naprawione.

6. Zdefiniuj programy obsługi poleceń.

7. Zdefiniuj funkcje do wysyłania odpowiedzi na polecenia.

8. Zdefiniuj funkcje pomocnika do rejestrowania żądań polecenia.

9. Zdefiniuj funkcję pomocnika, aby utworzyć właściwości.

10. Zdefiniuj odbiornik do aktualizacji właściwości.

11. Zdefiniuj funkcję do wysyłania danych telemetrycznych z tego urządzenia. Oba termostaty i składnik główny wysyłają dane telemetryczne. Ta funkcja otrzymuje nazwę składnika jako parametr.

12. Zdefiniuj `main` funkcję, która:

    1. Program używa zestawu SDK urządzenia do utworzenia klienta urządzenia i nawiązania połączenia z Centrum IoT Hub. Urządzenie dostarcza, `modelId` dzięki czemu IoT Hub mogą identyfikować urządzenie jako urządzenie Plug and Play IoT.

    1. Zaczyna nasłuchiwanie żądań poleceń przy użyciu `onDeviceMethod` funkcji. Funkcja konfiguruje odbiornik dla żądań poleceń z usługi:
        - DTDL urządzenia definiuje `reboot` `getMaxMinReport` polecenia i.
        - `commandHandler`Funkcja definiuje, w jaki sposób urządzenie reaguje na polecenie.

    1. Uruchamia wysyłanie danych telemetrycznych za pomocą `setInterval` i `sendTelemetry` .

    1. Używa `helperCreateReportedPropertiesPatch` funkcji do tworzenia właściwości i `updateComponentReportedProperties` do aktualizowania właściwości.

    1. Używa `desiredPropertyPatchListener` do nasłuchiwania aktualizacji właściwości.

    1. Wyłącza wszystkie detektory i zadania i zamyka pętlę po naciśnięciu przycisku **q** lub **q**.

Teraz, gdy kod został wyświetlony, użyj następującego polecenia, aby uruchomić przykład:

```cmd\sh
node pnpTemperatureController.js
```

Zobaczysz następujące dane wyjściowe, wskazujące, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości.

![Komunikaty z potwierdzeniem urządzenia](media/tutorial-multiple-components-node/multiple-component.png)

Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak połączyć urządzenie z usługą IoT Plug and Play ze składnikami do centrum IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dla deweloperów modelu IoT Plug and Play w wersji zapoznawczej](concepts-developer-guide.md)
