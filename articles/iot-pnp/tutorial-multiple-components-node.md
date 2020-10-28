---
title: Połącz przykładowy Plug and Play IoT Node.js kod urządzenia składnika do IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj Plug and Play IoT Node.js kod urządzenia, który używa wielu składników i łączy się z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2f0bf3b4b6e7091222b98e1743f0fb4f431fdd12
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736052"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-nodejs"></a>Samouczek: łączenie przykładu IoT Plug and Play wielu aplikacji urządzeń składników IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana dla Node.js i jest zawarta w zestawie SDK urządzeń IoT Hub platformy Azure dla Node.js. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby ukończyć ten samouczek, musisz Node.js na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Pobieranie kodu

Po ukończeniu [szybkiego startu: połączenie przykładowej aplikacji urządzenia IoT Plug and Play działającej w systemie Windows z IoT Hub (węzeł)](quickstart-connect-device-node.md)zostało już Sklonowane repozytorium.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [Microsoft Azure IoT SDK dla Node.js](https://github.com/Azure/azure-iot-sdk-node) repozytorium GitHub do tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Instalowanie wymaganych bibliotek

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie Plug and Play z wieloma składnikami, które łączą się z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. W oknie terminalu lokalnego przejdź do folderu sklonowanego repozytorium i przejdź do folderu */Azure-IoT-SDK-Node/Device/Samples/PnP* . Następnie uruchom następujące polecenie, aby zainstalować wymagane biblioteki:

```cmd/sh
npm install
```

Spowoduje to zainstalowanie odpowiednich plików npm wymaganych do uruchomienia przykładów w folderze.

## <a name="review-the-code"></a>Przeglądanie kodu

Przejdź do folderu *Azure-IoT-SDK-node\device\samples\pnp* .

Folder *Azure-IoT-SDK-node\device\samples\pnp* zawiera przykładowy kod dla urządzenia kontrolera temperatury Plug and Play IoT.

Kod w pliku *pnpTemperatureController.js* implementuje urządzenie kontrolera Plug and Play IoT. Model ten przykład implementuje użycie [wielu składników](concepts-components.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Otwórz plik *pnpTemperatureController.js* w wybranym edytorze kodu. Przykładowy kod pokazuje, jak:

- Zdefiniuj, `modelId` który jest DTMI dla zaimplementowanego urządzenia. Ten DTMI jest zdefiniowany przez użytkownika i musi być zgodny z DTMIem [kontrolera temperatury DTDL model](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

- Zaimplementuj składniki zdefiniowane w modelu DTDL kontrolera temperatury. Składniki w kontrolerze temperatury rzeczywistej powinny implementować te dwa interfejsy. Te dwa interfejsy zostały już opublikowane w centralnym repozytorium. W tym przykładzie dwa interfejsy:

  - Sterownika
  - Informacje o urządzeniu opracowane przez platformę Azure

- Zdefiniuj nazwy składników. Ten przykład ma dwa termostaty i jeden składnik informacji o urządzeniu.

- Zdefiniuj nazwę polecenia. Są to polecenia, na które urządzenie reaguje.

- Zdefiniuj `serialNumber` stałą. `serialNumber`To urządzenie zostało naprawione.

- Zdefiniuj programy obsługi poleceń.

- Zdefiniuj funkcje do wysyłania odpowiedzi na polecenia.

- Zdefiniuj funkcje pomocnika do rejestrowania żądań polecenia.

- Zdefiniuj funkcję pomocnika, aby utworzyć właściwości.

- Zdefiniuj odbiornik do aktualizacji właściwości.

- Zdefiniuj funkcję do wysyłania danych telemetrycznych z tego urządzenia. Oba termostaty i domyślny składnik wysyłają dane telemetryczne. Ta funkcja otrzymuje nazwę składnika jako parametr.

- Zdefiniuj `main` funkcję, która:

  - Program używa zestawu SDK urządzenia do utworzenia klienta urządzenia i nawiązania połączenia z Centrum IoT Hub. Urządzenie dostarcza, `modelId` dzięki czemu IoT Hub mogą identyfikować urządzenie jako urządzenie Plug and Play IoT.

  - Zaczyna nasłuchiwanie żądań poleceń przy użyciu `onDeviceMethod` funkcji. Funkcja konfiguruje odbiornik dla żądań poleceń z usługi:

    - DTDL urządzenia definiuje `reboot` `getMaxMinReport` polecenia i.
    - `commandHandler`Funkcja definiuje, w jaki sposób urządzenie reaguje na polecenie.

  - Uruchamia wysyłanie danych telemetrycznych za pomocą `setInterval` i `sendTelemetry` .

  - Używa `helperCreateReportedPropertiesPatch` funkcji do tworzenia właściwości i `updateComponentReportedProperties` do aktualizowania właściwości.

  - Używa `desiredPropertyPatchListener` do nasłuchiwania aktualizacji właściwości.

  - Wyłącza wszystkie detektory i zadania i zamyka pętlę po naciśnięciu przycisku **q** lub **q** .

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

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
> [Przewodnik dewelopera modelowania Plug and Play IoT](concepts-developer-guide-device-csharp.md)
