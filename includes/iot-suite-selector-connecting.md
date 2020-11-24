---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d4b93685160250a5abe7c050db89978ae8ceb1a9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559636"
---
> [!div class="op_single_selector"]
> * [C w systemie Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C w systemie Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (ogólne)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js na urządzeniu Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md)

W tym samouczku zaimplementowano urządzenie **chłodzenia** , które wysyła następujące dane telemetryczne do [akceleratora rozwiązania](../articles/iot-accelerators/about-iot-accelerators.md)do monitorowania zdalnego:

* Temperatura
* Ciśnienie
* Wilgotność

Dla uproszczenia kod generuje przykładowe wartości telemetrii dla **chłodzenia**. Można zwiększyć przykład, łącząc prawdziwe czujniki z urządzeniem i wysyłając prawdziwe dane telemetryczne.

Przykładowe urządzenie również:

* Wysyła metadane do rozwiązania w celu opisania jego możliwości.
* Reaguje na akcje wyzwalane na stronie **urządzenia** w rozwiązaniu.
* Odpowiada na zmiany konfiguracji wysyłane ze strony **urządzenia** w rozwiązaniu.

Do wykonania kroków tego samouczka jest potrzebne aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed zapisaniem dowolnego kodu dla urządzenia Wdróż Akcelerator rozwiązania do monitorowania zdalnego i Dodaj nowe rzeczywiste urządzenie do rozwiązania.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Wdróż Akcelerator rozwiązania do monitorowania zdalnego

Urządzenie **chłodnicze** utworzone w tym samouczku wysyła dane do wystąpienia akceleratora rozwiązania do [monitorowania zdalnego](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) . Jeśli akcelerator rozwiązania do monitorowania zdalnego nie został jeszcze zainicjowany na koncie platformy Azure, zobacz [wdrażanie akceleratora rozwiązania do monitorowania zdalnego](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Po zakończeniu procesu wdrażania rozwiązania do monitorowania zdalnego kliknij pozycję **Uruchom** , aby otworzyć pulpit nawigacyjny rozwiązania w przeglądarce.

![Pulpit nawigacyjny rozwiązania](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Dodawanie urządzenia do rozwiązania do zdalnego monitorowania

> [!NOTE]
> Jeśli urządzenie zostało już dodane do rozwiązania, możesz pominąć ten krok. Następnym krokiem jest jednak wymaganie parametrów połączenia urządzenia. Parametry połączenia urządzenia można pobrać z [Azure Portal](https://portal.azure.com) lub przy użyciu polecenia [AZ IoT](/cli/azure/iot?view=azure-cli-latest) CLI.

Aby urządzenie łączyło się z akceleratorem rozwiązania, musi zidentyfikować się do IoT Hub przy użyciu prawidłowych poświadczeń. Istnieje możliwość zapisania parametrów połączenia urządzenia, które zawierają te poświadczenia po dodaniu urządzenia do rozwiązania. W dalszej części tego samouczka dołączysz parametry połączenia urządzenia w aplikacji klienckiej.

Aby dodać urządzenie do rozwiązania do zdalnego monitorowania, wykonaj następujące czynności na stronie **Device Explorer** w rozwiązaniu:

1. Wybierz pozycję **+ nowe urządzenie**, a następnie wybierz pozycję **rzeczywista** jako **Typ urządzenia**:

    ![Dodawanie rzeczywistego urządzenia](media/iot-suite-selector-connecting/devicesprovision.png)

1. Wprowadź **fizyczny-chłodner** jako identyfikator urządzenia. Wybierz opcję **klucz symetryczny** i **automatycznie Generuj klucze** :

    ![Wybierz opcje urządzenia](media/iot-suite-selector-connecting/devicesoptions.png)

1. Wybierz pozycję **Zastosuj**. Następnie zanotuj wartość **Identyfikator urządzenia**, **klucz podstawowy** i **Parametry połączenia klucza podstawowego** :

    ![Pobieranie poświadczeń](media/iot-suite-selector-connecting/credentials.png)

Rzeczywiste urządzenie zostało już dodane do akceleratora rozwiązania do monitorowania zdalnego i zanotowano jego parametry połączenia urządzenia. W poniższych sekcjach opisano implementację aplikacji klienckiej, która używa parametrów połączenia urządzenia do nawiązania połączenia z rozwiązaniem.

Aplikacja kliencka implementuje wbudowany model urządzenia **chłodzenia** . Model urządzenia akceleratora rozwiązań określa następujące informacje o urządzeniu:

* Właściwości, które urządzenie raportuje do rozwiązania. Na przykład urządzenie **chłodnicze** zgłasza informacje o jego oprogramowaniu układowym i lokalizacji.
* Typy telemetrii wysyłane przez urządzenie do rozwiązania. Na przykład urządzenie **chłodzenia** wysyła wartości temperatury, wilgotności i ciśnienia.
* Metody, które można zaplanować od rozwiązania do uruchomienia na urządzeniu. Na przykład urządzenie **chłodzenia** musi implementować metody **reboot**, **FirmwareUpdate**, **EmergencyValveRelease** i **IncreasePressure** .