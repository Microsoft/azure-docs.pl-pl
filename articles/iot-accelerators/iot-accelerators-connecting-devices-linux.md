---
title: Udostępnianie urządzeń z systemem Linux do zdalnego monitorowania w języku C-Azure | Microsoft Docs
description: Opisuje sposób podłączenia urządzenia do akceleratora rozwiązania do monitorowania zdalnego przy użyciu aplikacji w języku C działającej w systemie Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454502"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Łączenie urządzenia z akceleratorem rozwiązania do zdalnego monitorowania (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku pokazano, jak podłączyć rzeczywiste urządzenie do akceleratora rozwiązania do monitorowania zdalnego.

Podobnie jak w przypadku większości aplikacji osadzonych, które działają na urządzeniach z ograniczeniami, kod klienta aplikacji urządzenia jest zapisywana w C. W tym samouczku utworzysz aplikację na maszynie z systemem Ubuntu (Linux).

Jeśli wolisz symulować urządzenie, zobacz [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz mieć urządzenie z systemem Ubuntu w wersji 15,04 lub nowszej. Przed kontynuowaniem [Skonfiguruj środowisko deweloperskie systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
