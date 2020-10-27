---
title: Sprawdzanie łączności urządzenia z usługą Azure IoT Hub
description: Użyj narzędzi usługi IoT Hub do rozwiązywania problemów z łącznością urządzenia z usługą IoT Hub w czasie programowania.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom:
- mvc
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: cf1c558474cfde85dd2c9ba8c85dc553fe5d9b56
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547507"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Samouczek: testowanie łączności z centrum IoT za pomocą urządzenia symulowanego

W tym samouczku użyjesz narzędzi portalu usługi Azure IoT Hub i poleceń interfejsu wiersza polecenia platformy Azure, aby przetestować łączność urządzeń. W tym samouczku został użyty również prosty symulator urządzenia działający na komputerze stacjonarnym.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Sprawdzanie uwierzytelniania urządzenia
> * Sprawdzanie łączności urządzenia z chmurą
> * Sprawdzanie łączności chmury z urządzeniem
> * Sprawdzanie synchronizacji bliźniaczej reprezentacji urządzenia

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Skrypty interfejsu wiersza polecenia uruchamiane w tym samouczku wymagają [rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md). Aby zainstalować to rozszerzenie, uruchom następujące polecenie interfejsu wiersza polecenia:

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Uruchamiana w tym samouczku aplikacja symulatora urządzenia została napisana przy użyciu środowiska Node.js. Potrzebujesz Node.js v10. x. x lub nowszego na komputerze deweloperskim.

Node.js można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

Pobierz przykładowy projekt symulatora urządzenia Node.js ze strony https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip i wyodrębnij archiwum ZIP.

Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli podczas pracy z poprzednim przewodnikiem Szybki start lub samouczkiem zostało już utworzone centrum IoT w warstwie Bezpłatna lub Standardowa, możesz pominąć ten krok.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Sprawdzenie uwierzytelniania urządzenia

Urządzenie musi zostać uwierzytelnione w centrum, zanim możliwa będzie wymiana danych z centrum. Możesz użyć narzędzia **Urządzenia IoT** w sekcji **Zarządzanie urządzeniami** w portalu, aby zarządzać urządzeniami i sprawdzić używane klucze uwierzytelniania. W tej części samouczka dodasz nowe urządzenie testowe, pobierzesz jego klucz i sprawdzisz, czy urządzenie testowe może nawiązać połączenie z centrum. W dalszej części zresetujesz klucz uwierzytelniania w celu zaobserwowania, co się stanie, gdy urządzenie spróbuje użyć nieaktualnego klucza. W tej części tego samouczka będziesz korzystać z witryny Azure Portal w celu utworzenia urządzenia i przykładowego symulatora urządzenia Node.js, monitorowania ich i zarządzania nimi.

Zaloguj się w portalu i przejdź do swojego centrum IoT. Następnie przejdź do narzędzia **Urządzenia IoT** :

![Narzędzie Urządzenia IoT](media/tutorial-connectivity/iot-devices-tool.png)

Aby zarejestrować nowe urządzenie, kliknij przycisk **+ Dodaj** , w polu **Identyfikator urządzenia** ustaw wartość **MyTestDevice** i kliknij przycisk **Zapisz** :

![Dodawanie nowego urządzenia](media/tutorial-connectivity/add-device.png)

Aby pobrać parametry połączenia dla urządzenia **MyTestDevice** , kliknij je na liście urządzeń, a następnie skopiuj wartość **Parametry połączenia — klucz podstawowy** . Te parametry połączenia zawierają *klucz dostępu współdzielonego* dla urządzenia.

![Pobieranie parametrów połączenia urządzenia](media/tutorial-connectivity/copy-connection-string.png)

Aby symulować wysyłanie danych telemetrycznych przez urządzenie **MyTestDevice** do centrum IoT, uruchom pobraną wcześniej aplikację urządzenia symulowanego Node.js.

W oknie terminalu na komputerze deweloperskim przejdź do folderu głównego pobranego projektu przykładowego Node.js. Następnie przejdź do folderu **iot-hub\Tutorials\ConnectivityTests** .

W oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić aplikację urządzenia symulowanego. Użyj parametrów połączenia urządzenia, zanotowanych podczas dodawania urządzenia w portalu.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Podczas próby nawiązania połączenia z centrum w oknie terminalu zostaną wyświetlone następujące informacje:

![Nawiązywanie połączenia przez urządzenie symulowane](media/tutorial-connectivity/sim-1-connected.png)

Urządzenie zostało pomyślnie uwierzytelnione przy użyciu klucza urządzenia wygenerowanego przez centrum IoT.

### <a name="reset-keys"></a>Resetowanie kluczy

W tej sekcji zresetujesz klucz urządzenia i zaobserwujesz błąd, który wystąpi, gdy urządzenie symulowane spróbuje nawiązać połączenie.

Aby zresetować podstawowy klucz urządzenia dla urządzenia **MyTestDevice** , uruchom następujące polecenia:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-iot

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

W oknie terminalu na komputerze deweloperskim uruchom ponownie aplikację urządzenia symulowanego:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Tym razem, gdy aplikacja spróbuje nawiązać połączenie, wyświetlony zostanie komunikat o błędzie uwierzytelniania:

![Błąd połączenia](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Generowanie tokenu sygnatury dostępu współdzielonego (SAS)

Jeśli urządzenie korzysta z jednego z zestawów SDK urządzeń usługi IoT Hub, kod biblioteki SDK generuje token SAS używany do uwierzytelniania w centrum. Token SAS jest generowany przy użyciu nazwy centrum, nazwy urządzenia oraz klucza urządzenia.

W niektórych scenariuszach, na przykład w przypadku bramy protokołu w chmurze lub w ramach niestandardowego schematu uwierzytelniania, może być konieczne samodzielne wygenerowanie tokenu SAS. Aby rozwiązać problemy z kodem generującym token SAS, warto wygenerować znany sprawny token SAS do użycia podczas testów.

> [!NOTE]
> Przykładowy plik SimulatedDevice-2.js zawiera przykłady generowania tokenu sygnatury dostępu współdzielonego (SAS) z zestawem SDK i bez niego.

Aby wygenerować znany sprawny token SAS przy użyciu interfejsu wiersza polecenia, należy uruchomić następujące polecenie:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Zanotuj pełen tekst wygenerowanego tokenu SAS. Token SAS wygląda następująco: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

W oknie terminalu na komputerze deweloperskim przejdź do folderu głównego pobranego projektu przykładowego Node.js. Następnie przejdź do folderu **iot-hub\Tutorials\ConnectivityTests** .

W oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić aplikację urządzenia symulowanego:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

Podczas próby nawiązania połączenia z centrum przy użyciu tokenu SAS w oknie terminalu zostaną wyświetlone następujące informacje:

![Nawiązywanie połączenia przez urządzenie symulowane przy użyciu tokenu](media/tutorial-connectivity/sim-2-connected.png)

Urządzenie zostało pomyślnie uwierzytelnione przy użyciu tokenu SAS wygenerowanego za pomocą polecenia interfejsu wiersza polecenia. Plik **SimulatedDevice-2.js** zawiera kod przykładowy, który pokazuje, w jaki sposób wygenerować token SAS w kodzie.

### <a name="protocols"></a>Protokoły

Urządzenie może korzystać z dowolnego z następujących protokołów w celu nawiązania połączenia z centrum IoT:

| Protokół | Port wychodzący |
| --- | --- |
| MQTT |8883 |
| MQTT za pośrednictwem obiektów WebSocket |443 |
| AMQP |5671 |
| AMQP za pośrednictwem obiektów WebSocket |443 |
| HTTPS |443 |

Jeśli port wychodzący jest blokowany przez zaporę, urządzenie nie może nawiązać połączenia:

![Port zablokowany](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Sprawdzanie łączności urządzenia z chmurą

Po nawiązaniu połączenia urządzenie zwykle próbuje wysłać dane telemetryczne do centrum IoT. W tej sekcji przedstawiono sposób sprawdzania, czy dane telemetryczne wysyłane przez urządzenie docierają do centrum.

Najpierw należy pobrać aktualne parametry połączenia urządzenia symulowanego, używając następującego polecenia:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Aby uruchomić symulowane urządzenie, które wysyła komunikaty, przejdź do folderu **IoT-hub\Tutorials\ConnectivityTests** w pobranym kodzie.

W oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić aplikację urządzenia symulowanego:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

Podczas wysyłania danych telemetrycznych do centrum w oknie terminalu zostaną wyświetlone te informacje:

![Wysyłanie komunikatów przez symulowane urządzenie](media/tutorial-connectivity/sim-3-sending.png)

Możesz użyć **metryk** w portalu, aby sprawdzić, czy komunikaty telemetryczne docierają do centrum IoT. Wybierz centrum IoT w menu rozwijanym **Zasób** , a następnie wybierz metrykę **Komunikaty telemetrii wysłane** i wybierz przedział czasowy **Ostatnia godzina** . Wykres pokazuje zagregowaną liczbę komunikatów wysłanych przez urządzenie symulowane:

![Wyświetlanie metryk usługi IoT Hub](media/tutorial-connectivity/metrics-portal.png)

Po uruchomieniu urządzenia symulowanego udostępnienie metryk zajmuje kilka minut.

## <a name="check-cloud-to-device-connectivity"></a>Sprawdzanie łączności chmury z urządzeniem

W tej sekcji przedstawiono sposób tworzenia testowego wywołania metody bezpośredniej do urządzenia w celu sprawdzenia łączności chmury z urządzeniem. W tym celu należy uruchomić na komputerze deweloperskim urządzenie symulowane nasłuchujące wywołań metody bezpośredniej z centrum.

W oknie terminalu użyj następującego polecenia, aby uruchomić aplikację urządzenia symulowanego:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Użyj polecenia interfejsu wiersza polecenia, aby wywołać metodę bezpośrednią na urządzeniu:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

Po otrzymaniu wywołania metody bezpośredniej urządzenie symulowane drukuje komunikat w konsoli:

![Otrzymanie wywołania metody bezpośredniej przez urządzenie symulowane](media/tutorial-connectivity/receive-method-call.png)

Gdy urządzenie symulowane pomyślnie otrzyma wywołanie metody bezpośredniej, wysyła potwierdzenie z powrotem do centrum:

![Otrzymanie potwierdzenia metody bezpośredniej](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Sprawdzanie synchronizacji bliźniaczej reprezentacji urządzenia

Bliźniacze reprezentacje urządzeń są używane do synchronizowania stanu pomiędzy urządzeniem a centrum. W tej sekcji użyjesz poleceń interfejsu wiersza polecenia, aby wysłać _żądane właściwości_ do urządzenia i odczytać _zgłoszone właściwości_ wysłane przez urządzenie.

Urządzenie symulowane używane w tej sekcji wysyła zgłoszone właściwości do centrum za każdym razem, gdy jest uruchamiane, i drukuje żądane właściwości w konsoli za każdym razem, gdy je otrzyma.

W oknie terminalu użyj następującego polecenia, aby uruchomić aplikację urządzenia symulowanego:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Aby sprawdzić, czy centrum otrzymało zgłoszone właściwości z urządzenia, użyj następującego polecenia interfejsu wiersza polecenia:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

W sekcji „reported properties” (zgłoszone właściwości) w danych wyjściowych polecenia jest widoczna właściwość **devicelaststarted** . Ta właściwość pokazuje, kiedy ostatnio uruchomiono urządzenie symulowane.

![Wyświetlanie zgłaszanych właściwości](media/tutorial-connectivity/reported-properties.png)

Aby sprawdzić, czy centrum może wysyłać żądane wartości właściwości do urządzenia, użyj następującego polecenia interfejsu wiersza polecenia:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Po otrzymaniu aktualizacji żądanej właściwości z centrum urządzenie symulowane drukuje komunikat:

![Otrzymywanie żądanych właściwości](media/tutorial-connectivity/desired-properties.png)

Oprócz otrzymywania na bieżąco żądanych zmian właściwości urządzenie symulowane automatycznie sprawdza dostępność żądanych właściwości przy uruchomieniu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już tego centrum IoT, usuń je oraz grupę zasobów z poziomu portalu. Aby to zrobić, wybierz grupę zasobów **tutorials-iot-hub-rg** zawierającą centrum IoT Hub, a następnie kliknij przycisk **Usuń** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób sprawdzania kluczy urządzenia, sprawdzania łączności urządzenia z chmurą i chmury z urządzeniem oraz sprawdzania synchronizacji bliźniaczej reprezentacji urządzenia. Aby dowiedzieć się więcej na temat monitorowania centrum IoT, zapoznaj się z artykułem z instrukcjami dotyczącymi monitorowania usługi IoT Hub.

> [!div class="nextstepaction"]
> [Monitoruj IoT Hub](monitor-iot-hub.md)
