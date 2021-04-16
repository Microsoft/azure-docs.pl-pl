---
title: Szybki start — Azure IoT Hub strumieni urządzeń Node.js szybki start dla protokołu SSH i RDP
description: W tym przewodniku Szybki start uruchamiasz przykładową aplikację Node.js, która działa jako serwer proxy, aby włączyć scenariusze SSH i RDP za pośrednictwem strumieni IoT Hub urządzeń.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 16daee2855b8e493521c1b40468e7068dac9e759
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477948"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Szybki start: włączanie protokołu SSH i RDP IoT Hub strumieniu urządzenia przy użyciu Node.js serwera proxy (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

W tym przewodniku Szybki start włączysz Secure Shell (SSH) i Remote Desktop Protocol (RDP) do wysłania do urządzenia za pośrednictwem strumienia urządzenia. Azure IoT Hub strumienie urządzeń umożliwiają aplikacjom usług i urządzeń komunikowanie się w bezpieczny i przyjazny dla zapory sposób. W tym przewodniku Szybki start opisano wykonywanie Node.js serwera proxy działającej po stronie usługi. W publicznej wersji zapoznawczej zestaw SDK Node.js obsługuje strumienie urządzeń tylko po stronie usługi. W związku z tym ten przewodnik Szybki start zawiera instrukcje uruchamiania tylko aplikacji serwera proxy usługi lokalnej.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie włączania [protokołu SSH](./quickstart-device-streams-proxy-c.md) i RDP przez strumienie urządzeń IoT Hub przy użyciu aplikacji serwera proxy języka C lub włączanie protokołu SSH i RDP przez strumienie urządzeń IoT Hub przy użyciu aplikacji serwera proxy języka [C#.](./quickstart-device-streams-proxy-csharp.md)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

    Bieżącą wersję pakietu można sprawdzić Node.js na komputerze dewelopera za pomocą następującego polecenia:

    ```cmd/sh
    node --version
    ```

* [Przykładowy Node.js projektu .](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub obsługuje strumienie urządzeń jako funkcję w [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!IMPORTANT]
> Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT Hub utworzonych w następujących regionach:
>
> * Central US
> * Środkowe stany USA — EUAP
> * Europa Północna
> * Azja Południowo-Wschodnia

### <a name="add-azure-iot-extension"></a>Dodawanie rozszerzenia usługi Azure IoT

Dodaj rozszerzenie usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure Cloud Shell wystąpieniu, uruchamiając następujące polecenie. Rozszerzenie IoT dodaje IoT Hub, IoT Edge i usługę IoT Device Provisioning Service (DPS) do interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono przewodnik Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum [IoT,](quickstart-send-telemetry-node.md)możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell do zarejestrowania symulowanego urządzenia.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.
   > * W przypadku nazwy rejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice w* pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby umożliwić aplikacji back-end nawiązywanie połączenia z centrum IoT i pobieranie komunikatów, potrzebne są również *ciągi połączenia usługi*. Następujące polecenie pobiera ciąg dla centrum IoT:

   > [!NOTE]
   > Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Zanotuj zwrócone ciągi połączenia usługi do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji zostanie ustanawiany strumień typu end-to-end do tunelowania ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

Jak wspomniano wcześniej, zestaw SDK IoT Hub Node.js obsługuje strumienie urządzeń tylko po stronie usługi. W przypadku aplikacji urządzenia lokalnego użyj aplikacji serwera proxy urządzenia, która jest dostępna w jednym z następujących przewodników Szybki start:

   * [Włączanie protokołu SSH i RDP IoT Hub strumieni urządzeń przy użyciu aplikacji serwera proxy języka C](./quickstart-device-streams-proxy-c.md)
   * [Włączanie protokołu SSH i RDP IoT Hub strumieni urządzeń przy użyciu aplikacji serwera proxy języka C#](./quickstart-device-streams-proxy-csharp.md) 

Przed kontynuowaniem do następnego kroku upewnij się, że aplikacja serwera proxy urządzenia lokalnego jest uruchomiona. Aby uzyskać omówienie konfiguracji, zobacz Przykład [lokalnego serwera proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

W tym artykule opisano konfigurację protokołu SSH (przy użyciu portu 22), a następnie opisano sposób modyfikowania konfiguracji protokołu RDP (który używa portu 3389). Strumienie urządzeń są niezależne od aplikacji i protokołów, dlatego można zmodyfikować ten sam przykład w celu obsługi innych typów ruchu aplikacji klient-serwer, zwykle modyfikując port komunikacyjny.

Po uruchomieniu aplikacji serwera proxy urządzenia lokalnego uruchom aplikację serwera proxy usługi lokalnej napisaną w Node.js, wykonując następujące czynności w lokalnym oknie terminalu:

1. W przypadku zmiennych środowiskowych podaj poświadczenia usługi, identyfikator urządzenia docelowego, na którym działa demon SSH, oraz numer portu serwera proxy uruchomionego na urządzeniu.

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

   Zmień symbol zastępczy ServiceConnectionString, aby dopasować je do parametrów połączenia usługi, a **myDevice** tak, aby był zgodne z Twoim identyfikatorem urządzenia, jeśli nadasz Innej nazwie.

1. Przejdź do `Quickstarts/device-streams-service` katalogu w folderze rozpakowanego projektu. Użyj następującego kodu, aby uruchomić aplikację serwera proxy usługi lokalnej:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W systemie Linux uruchom program SSH przy `ssh $USER@localhost -p 2222` użyciu w terminalu. W systemie Windows użyj ulubionego klienta SSH (na przykład PuTTY).

Dane wyjściowe konsoli w usłudze lokalnej po nawiązyniu sesji SSH (aplikacja serwera proxy usługi lokalnej nasłuchuje na porcie 2222):

![Dane wyjściowe terminalu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Dane wyjściowe konsoli aplikacji klienckiej SSH (klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, na którym nasłuchuje aplikacja serwera proxy usługi lokalnej):

![Dane wyjściowe klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Teraz użyj aplikacji klienckiej RDP i połącz się z serwerem proxy usługi na porcie 2222 — dowolnym porcie, który został wybrany wcześniej.

> [!NOTE]
> Upewnij się, że serwer proxy urządzenia jest poprawnie skonfigurowany dla protokołu RDP i skonfigurowany przy użyciu portu RDP 3389.

![Klient RDP łączy się z aplikacją serwera proxy usługi lokalnej](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie i wdrożysz aplikację serwera proxy usługi w celu włączenia protokołu RDP i SSH na urządzeniu IoT. Ruch RDP i SSH będzie tunelowany za pośrednictwem strumienia urządzenia za pośrednictwem centrum IoT. Ten proces eliminuje potrzebę bezpośredniej łączności z urządzeniem.

Aby dowiedzieć się więcej na temat strumieni urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
