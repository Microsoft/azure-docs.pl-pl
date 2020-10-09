---
title: Udostępnianie urządzeń z systemem Windows do zdalnego monitorowania w języku C-Azure | Microsoft Docs
description: Opisuje sposób podłączenia urządzenia do akceleratora rozwiązania do monitorowania zdalnego przy użyciu aplikacji zapisaną w języku C działającej w systemie Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "61450236"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Łączenie urządzenia z akceleratorem rozwiązania do zdalnego monitorowania (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku pokazano, jak podłączyć rzeczywiste urządzenie do akceleratora rozwiązania do monitorowania zdalnego.

Podobnie jak w przypadku większości aplikacji osadzonych, które działają na urządzeniach z ograniczeniami, kod klienta aplikacji urządzenia jest zapisywana w C. W tym samouczku utworzysz aplikację kliencką urządzenia na komputerze z systemem Windows.

Jeśli wolisz symulować urządzenie, zobacz [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, wykonaj kroki opisane w temacie [Konfigurowanie środowiska projektowego systemu Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) w celu dodania wymaganych narzędzi programistycznych i bibliotek do komputera z systemem Windows.

## <a name="view-the-code"></a>Wyświetl kod

[Przykładowy kod](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) używany w tym przewodniku jest dostępny w repozytorium GitHub usługi Azure IoT C SDK.

### <a name="download-the-source-code-and-prepare-the-project"></a>Pobierz kod źródłowy i przygotuj projekt

Aby przygotować projekt, [Sklonuj repozytorium usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) z witryny GitHub.

Przykład znajduje się w folderze **Samples/Solutions/remote_monitoring_client** .

Otwórz plik **remote_monitoring. c** w folderze **Samples/Solutions/remote_monitoring_client** w edytorze tekstów.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Edytuj plik **remote_monitoring. c** , aby zastąpić `<connectionstring>` parametrami połączenia urządzenia zanotowanymi na początku tego przewodnika po dodaniu urządzenia do akceleratora rozwiązania.

1. Postępuj zgodnie z instrukcjami w temacie [Tworzenie zestawu SDK języka C w systemie Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) , aby skompilować zestaw SDK i aplikację kliencką do monitorowania zdalnego.

1. W wierszu polecenia użytym do skompilowania rozwiązania Uruchom polecenie:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    W konsoli programu są wyświetlane komunikaty jako:

    - Aplikacja wysyła przykładowe dane telemetryczne do akceleratora rozwiązania.
    - Odpowiada na metody wywoływane z pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
