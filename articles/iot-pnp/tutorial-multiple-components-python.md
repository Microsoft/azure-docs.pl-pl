---
title: Połącz Plug and Play IoT wersja zapoznawcza przykładowy kod urządzenia składnika w języku Python do IoT Hub | Microsoft Docs
description: Kompilowanie i uruchamianie programu IoT Plug and Play Podgląd przykładowego kodu urządzenia w języku Python, który używa wielu składników i łączy się z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0cde9caa2f2b68b1e75eac635a81865cc4b6b33c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352839"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>Samouczek: łączenie przykładowej aplikacji IoT Plug and Play w wersji zapoznawczej do IoT Hub (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami i interfejsem głównym, nawiązywania połączenia z usługą IoT Hub oraz przy użyciu narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku Python i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka Python. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne jest środowisko Python 3,7 na komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/). Możesz sprawdzić wersję języka Python za pomocą następującego polecenia:  

```cmd/sh
python --version
```

Najnowszą zalecaną wersję można pobrać dla wielu platform z [Python.org](https://www.python.org/).

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

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Ten pakiet jest publikowany jako PIP dla publicznej wersji zapoznawczej. Wersja pakietu powinna być Najnowsza lub`2.1.4`

W lokalnym środowisku języka Python Zainstaluj plik w następujący sposób:

```cmd/sh
pip install azure-iot-device
```

Sklonuj repozytorium IoT SDK środowiska Python i sprawdź wyjście **PnP-Preview-Refresh**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje urządzenie kontrolera temperatury Plug and Play IoT. Model ten przykład implementuje użycie [wielu składników](concepts-components.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Folder *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* zawiera przykładowy kod dla urządzenia IoT Plug and Play. Pliki dla przykładowego kontrolera temperatury są następujące:

- pnp_temp_controller_with_thermostats. PR
- pnp_helper_preview_refresh. PR

Kontroler temperatury ma wiele składników i interfejs główny w oparciu o model DTDL kontrolera temperatury.

Otwórz plik *pnp_temp_controller_with_thermostats. PR* w wybranym edytorze. Kod w tym pliku:

1. Importy `pnp_helper_preview_refresh.py` , aby uzyskać dostęp do metod pomocnika.

1. Definiuje dwa identyfikatory modelu cyfrowej przędzy (DTMIs), które jednoznacznie reprezentują dwa różne interfejsy zdefiniowane w modelu DTDL. Składniki w kontrolerze temperatury rzeczywistej powinny implementować te dwa interfejsy. Te dwa interfejsy zostały już opublikowane w centralnym repozytorium. Te DTMIs muszą być znane użytkownikowi i różnią się w zależności od scenariusza implementacji urządzenia. W przypadku bieżącego przykładu te dwa interfejsy reprezentują:

  - Termostat
  - Informacje o urządzeniu opracowane przez platformę Azure.

. Definiuje DTMI `model_id` dla zaimplementowanego urządzenia. DTMI jest zdefiniowany przez użytkownika i musi być zgodny z DTMI w pliku modelu DTDL.

1. Definiuje nazwy nadawane składnikom w pliku DTDL. W DTDL i jednym składniku informacji o urządzeniu znajdują się dwa termostaty. Stała wywołana `serial_number` jest również zdefiniowana w interfejsie głównym. `serial_number`Nie można zmienić urządzenia.

1. Definiuje implementacje programu obsługi poleceń. Definiują to, jakie urządzenie ma wykonać po odebraniu żądania polecenia.

1. Definiuje funkcje do tworzenia odpowiedzi polecenia. Określają one, jak urządzenie reaguje na żądania polecenia. Tworzysz funkcje odpowiedzi polecenia, jeśli polecenie musi wysłać odpowiedź niestandardową z powrotem do centrum IoT Hub. Jeśli nie podano funkcji odpowiedzi dla polecenia, zostanie wysłana ogólna odpowiedź. W tym przykładzie tylko polecenie **getMaxMinReport** ma niestandardową odpowiedź.

1. Definiuje funkcję do wysyłania danych telemetrycznych z tego urządzenia. Zarówno termostaty, jak i interfejs główny wysyłają dane telemetryczne. Ta funkcja przyjmuje opcjonalny parametr nazwy składnika, aby umożliwić mu zidentyfikowanie składnika, który wysłał dane telemetryczne.

1. Definiuje odbiornik dla żądań poleceń.

1. Definiuje odbiornik do aktualizowania żądanych właściwości.

1. Zawiera `main` funkcję, która:

    1. Program używa zestawu SDK urządzenia do utworzenia klienta urządzenia i nawiązania połączenia z Centrum IoT Hub. Urządzenie wysyła do programu, `model_id` że Centrum IoT może identyfikować urządzenie jako urządzenie Plug and Play IoT.

    1. Używa `create_reported_properties` funkcji w pliku pomocnika do tworzenia właściwości. Przekaż nazwę składnika i właściwości jako pary klucz wartość do tej funkcji.

    1. Aktualizuje właściwości, które można odczytać dla jego składników przez wywołanie `patch_twin_reported_properties` .

    1. Zaczyna nasłuchiwanie żądań poleceń przy użyciu `execute_command_listener` funkcji. Funkcja konfiguruje odbiornik dla żądań poleceń z usługi. Podczas konfigurowania odbiornika, który podajesz `method_name` , `user_command_handler` i opcjonalnie `create_user_response_handler` jako parametry.
        - `method_name`Definiuje żądanie polecenia. W tym przykładzie model definiuje **ponowny rozruch**poleceń i **getMaxMinReport**.
        - `user_command_handler`Funkcja definiuje, jakie urządzenie ma wykonać po odebraniu polecenia.
        - `create_user_response_handler`Funkcja tworzy odpowiedź do wysłania do centrum IoT Hub, gdy polecenie zostanie wykonane pomyślnie. Tę odpowiedź można wyświetlić w portalu. Jeśli ta funkcja nie zostanie podana, do usługi jest wysyłana ogólna odpowiedź.

    1. Używa `execute_property_listener` do nasłuchiwania aktualizacji właściwości.

    1. Zaczyna wysyłać dane telemetryczne za pomocą `send_telemetry` . Przykładowy kod używa pętli, aby wywołać trzy funkcje wysyłania danych telemetrycznych. Każdy z nich jest wywoływany co osiem sekund

    1. Wyłącza wszystkie detektory i zadania i zamyka pętlę po naciśnięciu przycisku **q** lub **q**.

Teraz, gdy kod został wyświetlony, Utwórz zmienną środowiskową o nazwie **IOTHUB_DEVICE_CONNECTION_STRING** do przechowywania parametrów połączenia urządzenia, które zostały wcześniej wykonane przez użytkownika. Użyj następującego polecenia, aby uruchomić przykład:

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

Przykładowe urządzenie wysyła komunikaty telemetryczne co kilka sekund do centrum IoT.

Zobaczysz następujące dane wyjściowe, co oznacza, że urządzenie wysyła dane telemetryczne do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości.

![Komunikaty z potwierdzeniem urządzenia](media/tutorial-multiple-components-python/multiple-component.png)

Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak połączyć urządzenie z usługą IoT Plug and Play ze składnikami do centrum IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dla deweloperów modelu IoT Plug and Play w wersji zapoznawczej](concepts-developer-guide.md)
