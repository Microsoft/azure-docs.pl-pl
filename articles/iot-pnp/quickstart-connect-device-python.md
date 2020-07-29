---
title: Łączenie przykładowego kodu urządzenia w języku Python z usługą Azure Plug and Play w wersji zapoznawczej IoT Hub Microsoft Docs
description: Użyj języka Python do kompilowania i uruchamiania przykładowego kodu urządzenia w usłudze IoT Plug and Play w wersji zapoznawczej, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352942"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej do IoT Hub (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Przykładowa aplikacja jest zapisywana dla języka Python i jest zawarta w zestawie SDK urządzeń IoT Hub platformy Azure dla języka Python. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz mieć Język Python 3,7 na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/). Możesz sprawdzić wersję języka Python za pomocą następującego polecenia:  

```cmd/sh
python --version
```

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

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Ten pakiet jest publikowany jako PIP dla publicznej wersji zapoznawczej. Wersja pakietu powinna być Najnowsza lub`2.1.4`

W lokalnym środowisku języka Python Zainstaluj plik w następujący sposób:

```cmd/sh
pip install azure-iot-device
```

Sklonuj repozytorium IoT zestawu SDK języka Python i Wyewidencjonuj **serwer główny**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

Folder *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* zawiera przykładowy kod dla urządzenia IoT Plug and Play. Ten przewodnik Szybki Start używa pliku *pnp_thermostat. PR* . Ten przykładowy kod implementuje urządzenie zgodne Plug and Play IoT i korzysta z biblioteki klienta urządzenia usługi Azure IoT Python.

Utwórz zmienną środowiskową o nazwie **IOTHUB_DEVICE_CONNECTION_STRING** do przechowywania parametrów połączenia urządzenia, które zostały wcześniej wykonane.

Otwórz plik **pnp_thermostat. PR** w edytorze tekstów. Zwróć uwagę, jak to zrobić:

1. Definiuje pojedynczy identyfikator modelu sznurka urządzenia (DTMI), który jednoznacznie reprezentuje [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). DTMI musi być znany użytkownikowi i różnić się zależnie od scenariusza implementacji urządzenia. W przypadku bieżącego przykładu model reprezentuje termostat z danymi telemetrycznymi, właściwościami i poleceniami związanymi z temperaturą monitorowania.

1. Ma funkcje do definiowania implementacji programu obsługi poleceń. Należy napisać te procedury obsługi, aby określić, jak urządzenie reaguje na żądania poleceń.

1. Zawiera funkcję służącą do definiowania odpowiedzi na polecenie. Tworzysz funkcje odpowiedzi polecenia, aby wysłać odpowiedź z powrotem do centrum IoT Hub.

1. Definiuje funkcję odbiornika klawiatury wejściowej, która umożliwia zamknięcie aplikacji.

1. Ma **główną** funkcję. Funkcja **Main** :

    1. Program używa zestawu SDK urządzenia do utworzenia klienta urządzenia i nawiązania połączenia z Centrum IoT Hub.

    1. Aktualizuje właściwości. Model używany, **termostat**, definiuje `targetTemperature` i `maxTempSinceLastReboot` jako dwie właściwości dla naszego termostatu, tak, że będzie używany. Właściwości są aktualizowane przy użyciu `patch_twin_reported_properties` metody zdefiniowanej w `device_client` .

    1. Zaczyna nasłuchiwanie żądań poleceń przy użyciu funkcji **execute_command_listener** . Funkcja konfiguruje "odbiornik", aby nasłuchiwać poleceń przychodzących z usługi. Po skonfigurowaniu odbiornika `method_name` , `user_command_handler` i `create_user_response_handler` . 
        - `user_command_handler`Funkcja definiuje, jakie urządzenie ma wykonać po odebraniu polecenia. Na przykład jeśli alarm zostanie wyłączony, efekt odebrania tego polecenia zostanie wznowiony. Należy to traktować jako "efekt" wywoływanego polecenia.
        - `create_user_response_handler`Funkcja tworzy odpowiedź do wysłania do centrum IoT Hub, gdy polecenie zostanie wykonane pomyślnie. Na przykład jeśli alarm jest wyłączony, możesz odpowiedzieć przez naciśnięcie pozycji Odłóż, która jest opiniami dotyczącymi usługi. Zastanów się tak jak w odpowiedzi na usługę. Tę odpowiedź można wyświetlić w portalu.

    1. Uruchamia wysyłanie danych telemetrycznych. **Pnp_send_telemetry** jest zdefiniowany w pliku pnp_methods. pr. Przykładowy kod używa pętli do wywołania tej funkcji co osiem sekund.

    1. Wyłącza wszystkie detektory i zadania i istnieje pętla po naciśnięciu przycisku **q** lub **q**.

Teraz, gdy kod został wyświetlony, użyj następującego polecenia, aby uruchomić przykład:

```cmd/sh
python pnp_thermostat.py
```

Zobaczysz następujące dane wyjściowe, co oznacza, że urządzenie wysyła dane telemetryczne do centrum i teraz jest gotowe do odbierania poleceń i aktualizacji właściwości:

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Współdziałanie z urządzeniem z systemem IoT Plug and Play w wersji zapoznawczej, które jest połączone z rozwiązaniem](quickstart-service-python.md)
