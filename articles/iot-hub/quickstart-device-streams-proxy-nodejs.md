---
title: Szybki Start — strumienie urządzeń z systemem Azure IoT Hub Node.js przewodnika Szybki Start dla protokołów SSH i RDP
description: W tym przewodniku szybki start uruchomisz przykładową aplikację Node.js, która działa jako serwer proxy, aby umożliwić scenariusze SSH i RDP za pośrednictwem IoT Hub strumieni urządzeń.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: ef45a6277adeff09a34fe22b7abeb21d3e603167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624358"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Szybki Start: Włączanie protokołu SSH i RDP przez strumień urządzeń IoT Hub przy użyciu aplikacji Node.js proxy (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

W tym przewodniku szybki start włączysz ruch Secure Shell (SSH) i Remote Desktop Protocol (RDP) do wysłania do urządzenia przez strumień urządzeń. Strumienie urządzeń z systemem Azure IoT Hub umożliwiają aplikacjom usługi i urządzeniu komunikowanie się w sposób bezpieczny i przyjazny dla zapory. Ten przewodnik Szybki Start opisuje wykonywanie Node.js aplikacji serwera proxy, która jest uruchomiona po stronie usługi. W publicznej wersji zapoznawczej zestaw SDK Node.js obsługuje strumienie urządzeń tylko po stronie usługi. W efekcie w tym przewodniku szybki start przedstawiono instrukcje dotyczące uruchamiania tylko aplikacji lokalnego serwera proxy usługi.

## <a name="prerequisites"></a>Wymagania wstępne

* Po zakończeniu [włączania strumieni urządzeń SSH i RDP przez IoT Hub przy użyciu aplikacji serwera proxy języka C](./quickstart-device-streams-proxy-c.md) lub [włączania strumieni urządzeń SSH i RDP przez IoT Hub za pomocą aplikacji serwera proxy C#](./quickstart-device-streams-proxy-csharp.md).

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    Bieżącą wersję Node.js na komputerze deweloperskim można sprawdzić za pomocą następującego polecenia:

    ```cmd/sh
    node --version
    ```

* [Przykładowy projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub obecnie obsługuje strumienie urządzeń jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Podgląd strumieni urządzeń jest obecnie obsługiwany tylko dla centrów IoT utworzonych w następujących regionach:
>
> * Central US
> * Środkowe stany USA — EUAP
> * Europa Północna
> * Azja Południowo-Wschodnia

### <a name="add-azure-iot-extension"></a>Dodawanie rozszerzenia usługi Azure IoT

Dodaj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell, uruchamiając następujące polecenie. Rozszerzenie IoT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono [Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md), można pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.
   > * W przypadku nazwy urządzenia, które rejestrujesz, zaleca się użycie *urządzenia* w sposób przedstawiony. Jeśli wybierzesz inną nazwę urządzenia, Użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby umożliwić aplikacji zaplecza łączenie się z Centrum IoT Hub i pobieranie komunikatów, potrzebne są również *Parametry połączenia z usługą*. Następujące polecenie pobiera ciąg dla Centrum IoT Hub:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Zwróć uwagę na zwrócone parametry połączenia usługi do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

Ta sekcja umożliwia ustanowienie kompleksowego strumienia do tunelowania ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

Jak wspomniano wcześniej, zestaw SDK IoT Hub Node.js obsługuje strumienie urządzeń tylko po stronie usługi. W przypadku aplikacji lokalnej urządzenia Użyj aplikacji serwera proxy urządzenia, która jest dostępna w jednym z następujących przewodników szybki start:

   * [Włączanie strumieni urządzeń SSH i RDP przez IoT Hub przy użyciu aplikacji serwera proxy języka C](./quickstart-device-streams-proxy-c.md)
   * [Włączanie strumieni urządzeń SSH i RDP przez IoT Hub przy użyciu aplikacji serwera proxy języka C#](./quickstart-device-streams-proxy-csharp.md) 

Przed przejściem do następnego kroku upewnij się, że aplikacja lokalnego serwera proxy jest uruchomiona. Aby zapoznać się z omówieniem instalacji, zobacz [lokalny serwer proxy — przykład](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

W tym artykule opisano konfigurację protokołu SSH (przy użyciu portu 22), a następnie opisano sposób modyfikowania konfiguracji dla połączeń RDP (która używa portu 3389). Ponieważ strumienie urządzeń są typu Application-and Protocol-niezależny od, można zmodyfikować ten sam przykład, aby pomieścić inne typy ruchu aplikacji klient-serwer, zazwyczaj modyfikując port komunikacyjny.

Po uruchomieniu aplikacji serwera proxy lokalnego urządzenia Uruchom aplikację lokalnego serwera proxy, która jest zapisywana w Node.js, wykonując następujące czynności w lokalnym oknie terminalu:

1. W przypadku zmiennych środowiskowych podaj poświadczenia usługi, identyfikator urządzenia docelowego, na którym działa demon SSH i numer portu serwera proxy uruchomionego na urządzeniu.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Zmień symbol zastępczy serviceconnectionstring w taki sposób, aby pasował do parametrów połączenia z usługą, a **urządzenie** jest zgodne z identyfikatorem urządzenia, jeśli podałeś inną nazwę.

1. Przejdź do `Quickstarts/device-streams-service` katalogu w folderze niespakowanym projektu. Użyj następującego kodu, aby uruchomić aplikację lokalnego serwera proxy usługi:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W systemie Linux Uruchom protokół SSH przy użyciu programu `ssh $USER@localhost -p 2222` na terminalu. W systemie Windows użyj ulubionego klienta SSH (na przykład.

Dane wyjściowe konsoli lokalnej usługi po ustanowieniu sesji SSH (aplikacja lokalnego serwera proxy nasłuchuje na porcie 2222):

![Dane wyjściowe terminalu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Dane wyjściowe konsoli aplikacji klienta SSH (klient SSH komunikuje się z demonem SSH przez połączenie z portem 22, w którym aplikacja lokalnego serwera proxy nasłuchuje):

![Dane wyjściowe klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Teraz należy używać aplikacji klienckiej RDP i nawiązać połączenie z serwerem proxy usługi na porcie 2222, wybranym wcześniej porcie.

> [!NOTE]
> Upewnij się, że serwer proxy urządzenia jest poprawnie skonfigurowany dla protokołu RDP i skonfigurowany przy użyciu portu RDP 3389.

![Klient RDP nawiązuje połączenie z aplikacją lokalnego serwera proxy usługi.](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie i wdrożono aplikację serwera proxy usługi, aby włączyć protokół RDP i SSH na urządzeniu IoT. Ruch RDP i SSH zostanie przetunelowany za pośrednictwem strumienia urządzenia za pośrednictwem Centrum IoT. Ten proces eliminuje konieczność bezpośredniej łączności z urządzeniem.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
