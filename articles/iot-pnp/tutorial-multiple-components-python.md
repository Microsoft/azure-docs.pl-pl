---
title: Połącz Plug and Play IoT przykład kod urządzenia składnika Python do IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj Plug and Play IoT przykład kod urządzenia w języku Python, który używa wielu składników i łączy się z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 084ba93baa35790da58e7765750bb79de27ed69c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578023"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-python"></a>Samouczek: łączenie przykładu IoT Plug and Play wielu aplikacji urządzeń składników IoT Hub (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku Python i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Python. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Do ukończenia tego samouczka potrzebne jest środowisko Python 3,7 na komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/). Możesz sprawdzić wersję języka Python za pomocą następującego polecenia:  

```cmd/sh
python --version
```

Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/).

## <a name="download-the-code"></a>Pobieranie kodu

Pakiet **Azure-IoT-Device** jest publikowany jako PIP.

W lokalnym środowisku języka Python Zainstaluj pakiet w następujący sposób:

```cmd/sh
pip install azure-iot-device
```

Jeśli zakończysz [Przewodnik Szybki Start: Podłącz przykładową aplikację urządzenia IoT Plug and Play działającą w systemie Windows do IoT Hub (Python)](quickstart-connect-device-python.md), masz już Sklonowane repozytorium.

Klonuj repozytorium IoT zestawu SDK języka Python:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje urządzenie kontrolera temperatury Plug and Play IoT. Model ten przykład implementuje użycie [wielu składników](concepts-components.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Folder *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* zawiera przykładowy kod dla urządzenia IoT Plug and Play. Pliki dla przykładowego kontrolera temperatury są następujące:

- temp_controller_with_thermostats. PR
- pnp_helper_preview_refresh. PR

Kontroler temperatury ma wiele składników i składnik domyślny, na podstawie modelu DTDL kontrolera temperatury.

Otwórz plik *temp_controller_with_thermostats. PR* w wybranym edytorze. Kod w tym pliku:

1. Importy `pnp_helper_preview_refresh.py` , aby uzyskać dostęp do metod pomocnika.

1. Definiuje dwa identyfikatory modelu cyfrowej przędzy (DTMIs), które jednoznacznie reprezentują dwa różne interfejsy zdefiniowane w modelu DTDL. Składniki w kontrolerze temperatury rzeczywistej powinny implementować te dwa interfejsy. Te dwa interfejsy zostały już opublikowane w centralnym repozytorium. Te DTMIs muszą być znane użytkownikowi i różnią się w zależności od scenariusza implementacji urządzenia. W przypadku bieżącego przykładu te dwa interfejsy reprezentują:

    - Termostat
    - Informacje o urządzeniu opracowane przez platformę Azure.

1. Definiuje DTMI `model_id` dla zaimplementowanego urządzenia. DTMI jest zdefiniowany przez użytkownika i musi być zgodny z DTMI w pliku modelu DTDL.

1. Definiuje nazwy nadawane składnikom w pliku DTDL. W DTDL i jednym składniku informacji o urządzeniu znajdują się dwa termostaty. Stała wywołana `serial_number` jest również zdefiniowana w składniku domyślnym. `serial_number`Nie można zmienić urządzenia.

1. Definiuje implementacje programu obsługi poleceń. Definiują to, jakie urządzenie ma wykonać po odebraniu żądania polecenia.

1. Definiuje funkcje do tworzenia odpowiedzi polecenia. Określają one, jak urządzenie reaguje na żądania polecenia. Tworzysz funkcje odpowiedzi polecenia, jeśli polecenie musi wysłać odpowiedź niestandardową z powrotem do centrum IoT Hub. Jeśli nie podano funkcji odpowiedzi dla polecenia, zostanie wysłana ogólna odpowiedź. W tym przykładzie tylko polecenie **getMaxMinReport** ma niestandardową odpowiedź.

1. Definiuje funkcję do wysyłania danych telemetrycznych z tego urządzenia. Zarówno termostaty, jak i domyślny składnik wysyłają dane telemetryczne. Ta funkcja przyjmuje opcjonalny parametr nazwy składnika, aby umożliwić mu zidentyfikowanie składnika, który wysłał dane telemetryczne.

1. Definiuje odbiornik dla żądań poleceń.

1. Definiuje odbiornik do aktualizowania żądanych właściwości.

1. Zawiera `main` funkcję, która:

    - Program używa zestawu SDK urządzenia do utworzenia klienta urządzenia i nawiązania połączenia z Centrum IoT Hub. Urządzenie wysyła do programu, `model_id` że Centrum IoT może identyfikować urządzenie jako urządzenie Plug and Play IoT.

    - Używa `create_reported_properties` funkcji w pliku pomocnika do tworzenia właściwości. Przekaż nazwę składnika i właściwości jako pary klucz wartość do tej funkcji.

    - Aktualizuje właściwości, które można odczytać dla jego składników przez wywołanie `patch_twin_reported_properties` .

    - Zaczyna nasłuchiwanie żądań poleceń przy użyciu `execute_command_listener` funkcji. Funkcja konfiguruje odbiornik dla żądań poleceń z usługi. Podczas konfigurowania odbiornika, który podajesz `method_name` , `user_command_handler` i opcjonalnie `create_user_response_handler` jako parametry.
        - `method_name`Definiuje żądanie polecenia. W tym przykładzie model definiuje **ponowny rozruch**poleceń i **getMaxMinReport**.
        - `user_command_handler`Funkcja definiuje, jakie urządzenie ma wykonać po odebraniu polecenia.
        - `create_user_response_handler`Funkcja tworzy odpowiedź do wysłania do centrum IoT Hub, gdy polecenie zostanie wykonane pomyślnie. Tę odpowiedź można wyświetlić w portalu. Jeśli ta funkcja nie zostanie podana, do usługi jest wysyłana ogólna odpowiedź.

    - Używa `execute_property_listener` do nasłuchiwania aktualizacji właściwości.

    - Zaczyna wysyłać dane telemetryczne za pomocą `send_telemetry` . Przykładowy kod używa pętli, aby wywołać trzy funkcje wysyłania danych telemetrycznych. Każdy z nich jest wywoływany co osiem sekund

    - Wyłącza wszystkie detektory i zadania i zamyka pętlę po naciśnięciu przycisku **q** lub **q**.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Użyj następującego polecenia, aby uruchomić przykład:

```cmd/sh
python temp_controller_with_thermostats.py
```

Przykładowe urządzenie wysyła komunikaty telemetryczne co kilka sekund do centrum IoT.

Zobaczysz następujące dane wyjściowe, co oznacza, że urządzenie wysyła dane telemetryczne do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości.

![Komunikaty z potwierdzeniem urządzenia](media/tutorial-multiple-components-python/multiple-component.png)

Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak połączyć urządzenie z usługą IoT Plug and Play ze składnikami do centrum IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dewelopera modelowania Plug and Play IoT](concepts-developer-guide-device-csharp.md)
