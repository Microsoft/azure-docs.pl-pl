---
title: Łączenie przykładowego kodu urządzenia w języku Python Plug and Play z usługą Azure IoT Hub | Microsoft Docs
description: Użyj języka Python, aby skompilować i uruchomić przykładowy kod urządzenia Plug and Play IoT, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 065e65c0c8ccf61dd67da19fd5a94d4506db73ff
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737312"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-python"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play do IoT Hub (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Przykładowa aplikacja jest zapisywana dla języka Python i jest zawarta w zestawie SDK urządzeń IoT Hub platformy Azure dla języka Python. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki Start, musisz mieć Język Python 3,7 na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/). Możesz sprawdzić wersję języka Python za pomocą następującego polecenia:  

```cmd/sh
python --version
```

W lokalnym środowisku języka Python Zainstaluj pakiet w następujący sposób:

```cmd/sh
pip install azure-iot-device
```

Sklonuj repozytorium IoT zestawu SDK języka Python i Wyewidencjonuj **serwer główny** :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

Folder *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* zawiera przykładowy kod dla urządzenia IoT Plug and Play. Ten przewodnik Szybki Start używa pliku *simple_thermostat. PR* . Ten przykładowy kod implementuje urządzenie zgodne Plug and Play IoT i korzysta z biblioteki klienta urządzenia usługi Azure IoT Python.

Otwórz plik **simple_thermostat. PR** w edytorze tekstów. Zwróć uwagę, jak to zrobić:

1. Definiuje pojedynczy identyfikator modelu sznurka urządzenia (DTMI), który jednoznacznie reprezentuje [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). DTMI musi być znany użytkownikowi i różnić się zależnie od scenariusza implementacji urządzenia. W przypadku bieżącego przykładu model reprezentuje termostat z danymi telemetrycznymi, właściwościami i poleceniami związanymi z temperaturą monitorowania.

1. Ma funkcje do definiowania implementacji programu obsługi poleceń. Należy napisać te procedury obsługi, aby określić, jak urządzenie reaguje na żądania poleceń.

1. Zawiera funkcję służącą do definiowania odpowiedzi na polecenie. Tworzysz funkcje odpowiedzi polecenia, aby wysłać odpowiedź z powrotem do centrum IoT Hub.

1. Definiuje funkcję odbiornika klawiatury wejściowej, która umożliwia zamknięcie aplikacji.

1. Ma **główną** funkcję. Funkcja **Main** :

    1. Program używa zestawu SDK urządzenia do utworzenia klienta urządzenia i nawiązania połączenia z Centrum IoT Hub.

    1. Aktualizuje właściwości. Model używany, **termostat** , definiuje `targetTemperature` i `maxTempSinceLastReboot` jako dwie właściwości dla naszego termostatu, tak, że będzie używany. Właściwości są aktualizowane przy użyciu `patch_twin_reported_properties` metody zdefiniowanej w `device_client` .

    1. Zaczyna nasłuchiwanie żądań poleceń przy użyciu funkcji **execute_command_listener** . Funkcja konfiguruje "odbiornik", aby nasłuchiwać poleceń przychodzących z usługi. Po skonfigurowaniu odbiornika `method_name` , `user_command_handler` i `create_user_response_handler` .
        - `user_command_handler`Funkcja definiuje, jakie urządzenie ma wykonać po odebraniu polecenia. Na przykład jeśli alarm zostanie wyłączony, efekt odebrania tego polecenia zostanie wznowiony. Należy to traktować jako "efekt" wywoływanego polecenia.
        - `create_user_response_handler`Funkcja tworzy odpowiedź do wysłania do centrum IoT Hub, gdy polecenie zostanie wykonane pomyślnie. Na przykład jeśli alarm jest wyłączony, możesz odpowiedzieć przez naciśnięcie pozycji Odłóż, która jest opiniami dotyczącymi usługi. Zastanów się tak jak w odpowiedzi na usługę. Tę odpowiedź można wyświetlić w portalu.

    1. Uruchamia wysyłanie danych telemetrycznych. **Pnp_send_telemetry** jest zdefiniowany w pliku pnp_methods. pr. Przykładowy kod używa pętli do wywołania tej funkcji co osiem sekund.

    1. Wyłącza wszystkie detektory i zadania i istnieje pętla po naciśnięciu przycisku **q** lub **q** .

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Teraz, gdy kod został wyświetlony, użyj następującego polecenia, aby uruchomić przykład:

```cmd/sh
python simple_thermostat.py
```

Zobaczysz następujące dane wyjściowe, co oznacza, że urządzenie wysyła dane telemetryczne do centrum i teraz jest gotowe do odbierania poleceń i aktualizacji właściwości:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Korzystanie z urządzenia Plug and Play IoT, które jest połączone z rozwiązaniem](quickstart-service-python.md)
