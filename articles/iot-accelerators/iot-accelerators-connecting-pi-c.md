---
title: Inicjowanie obsługi Raspberry Pi na potrzeby zdalnego monitorowania przy użyciu języka C-Azure | Microsoft Docs
description: Opisuje sposób podłączenia urządzenia Raspberry Pi do akceleratora rozwiązania do monitorowania zdalnego przy użyciu aplikacji zapisaną w języku C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454507"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Połącz urządzenie Raspberry Pi z akceleratorem rozwiązania do zdalnego monitorowania (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku pokazano, jak podłączyć rzeczywiste urządzenie do akceleratora rozwiązania do monitorowania zdalnego. Podobnie jak w przypadku większości aplikacji osadzonych uruchamianych na ograniczonych urządzeniach, kod klienta dla aplikacji urządzenia Raspberry Pi jest zapisywana w C. W tym samouczku utworzysz aplikację na Raspberry Pi, na którym działa system operacyjny raspbian.

Jeśli wolisz symulować urządzenie, zobacz [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Wymagany sprzęt

Komputer stacjonarny, który umożliwia zdalne nawiązywanie połączenia z wierszem polecenia w Raspberry Pi.

[Zestaw startowy Microsoft IoT dla Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) lub równoważnych składników. W tym samouczku są stosowane następujące elementy z zestawu:

- Raspberry Pi 3
- Karta MicroSD (z NOOBS)
- Mini kabel USB
- Kabel Ethernet

### <a name="required-desktop-software"></a>Wymagane oprogramowanie komputerowe

Do uzyskania zdalnego dostępu do wiersza polecenia na Raspberry Pi jest potrzebny klient SSH na komputerze stacjonarnym.

- System Windows nie zawiera klienta SSH. Zalecamy [Korzystanie z](https://www.putty.org/)programu.
- Większość dystrybucji systemu Linux i Mac OS zawierają narzędzia SSH wiersza polecenia. Aby uzyskać więcej informacji, zobacz [SSH przy użyciu systemu Linux lub Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Wymagane oprogramowanie Raspberry Pi

W tym artykule przyjęto założenie, że zainstalowano najnowszą wersję [systemu operacyjnego raspbian na Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Poniższe kroki pokazują, jak przygotować Raspberry Pi do tworzenia aplikacji C, która łączy się z akceleratorem rozwiązania:

1. Nawiąż połączenie z Raspberry Pi przy użyciu protokołu **SSH**. Aby uzyskać więcej informacji, zobacz [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) w [witrynie sieci Web Raspberry Pi](https://www.raspberrypi.org/).

1. Aby zaktualizować Raspberry Pi, użyj następującego polecenia:

    ```sh
    sudo apt-get update
    ```

1. Aby wykonać kroki opisane w tym przewodniku z instrukcjami, wykonaj kroki opisane w sekcji [Konfigurowanie środowiska deweloperskiego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) w celu dodania wymaganych narzędzi programistycznych i bibliotek do Raspberry Pi.

## <a name="view-the-code"></a>Wyświetl kod

[Przykładowy kod](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) używany w tym przewodniku jest dostępny w repozytorium GitHub usługi Azure IoT C SDK.

### <a name="download-the-source-code-and-prepare-the-project"></a>Pobierz kod źródłowy i przygotuj projekt

Aby przygotować projekt, Sklonuj lub Pobierz [repozytorium Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z serwisu GitHub.

Przykład znajduje się w folderze **Samples/Solutions/remote_monitoring_client** .

Otwórz plik **remote_monitoring. c** w folderze **Samples/Solutions/remote_monitoring_client** w edytorze tekstów.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

W poniższych krokach opisano sposób użycia programu *CMAKE* do skompilowania aplikacji klienckiej. Aplikacja kliencka do monitorowania zdalnego jest skompilowana jako część procesu kompilacji zestawu SDK.

1. Edytuj plik **remote_monitoring. c** , aby zastąpić `<connectionstring>` parametrami połączenia urządzenia zanotowanymi na początku tego przewodnika po dodaniu urządzenia do akceleratora rozwiązania.

1. Przejdź do głównej sklonowanej kopii repozytorium [repozytorium SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) i uruchom następujące polecenia, aby skompilować aplikację kliencką:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Uruchom aplikację kliencką i Wyślij dane telemetryczne do IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    W konsoli programu są wyświetlane komunikaty jako:

    - Aplikacja wysyła przykładowe dane telemetryczne do akceleratora rozwiązania.
    - Odpowiada na metody wywoływane z pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
