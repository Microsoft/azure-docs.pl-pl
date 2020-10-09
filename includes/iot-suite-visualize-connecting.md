---
title: plik dołączany
description: plik dołączany
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183665"
---
## <a name="view-device-telemetry"></a>Wyświetlanie danych telemetrycznych z urządzenia

Dane telemetryczne wysyłane z urządzenia można wyświetlić na stronie **Device Explorer** w rozwiązaniu.

1. Wybierz urządzenie, którego zainicjowano na liście urządzeń na stronie **Device Explorer** . Panel wyświetla informacje o urządzeniu, w tym wykres telemetrii urządzenia:

    ![Zobacz szczegóły urządzenia](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Wybierz pozycję **nacisk** , aby zmienić wyświetlanie danych telemetrycznych:

    ![Wyświetlanie telemetrii nacisku](media/iot-suite-visualize-connecting/devicespressure.png)

1. Aby wyświetlić informacje diagnostyczne o urządzeniu, przewiń w dół do wersji **Diagnostyka**:

    ![Wyświetlanie diagnostyki urządzenia](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Działanie na urządzeniu

Aby wywoływać metody na urządzeniach, użyj strony **Device Explorer** w rozwiązaniu do zdalnego monitorowania. Na przykład w urządzeniach do monitorowania zdalnego rozwiązania do **chłodzenia** Zaimplementuj metodę **ponownego uruchamiania** .

1. Wybierz pozycję **urządzenia** , aby przejść do strony **Device Explorer** w rozwiązaniu.

1. Wybierz urządzenie, którego zainicjowano na liście urządzeń na stronie **Device Explorer** :

    ![Wybierz rzeczywiste urządzenie](media/iot-suite-visualize-connecting/devicesselect.png)

1. Aby wyświetlić listę metod, które można wywołać na urządzeniu, wybierz kolejno pozycje **zadania**i **metody**. Aby zaplanować zadanie do uruchomienia na wielu urządzeniach, na liście można wybrać wiele urządzeń. Panel **zadania** pokazuje typy metod wspólnych dla wszystkich wybranych urządzeń.

1. Wybierz pozycję **Uruchom ponownie**, Ustaw nazwę zadania na **RebootPhysicalChiller** , a następnie wybierz pozycję **Zastosuj**:

    ![Zaplanuj aktualizację oprogramowania układowego](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Sekwencja komunikatów jest wyświetlana w konsoli programu, na której uruchomiono kod urządzenia, gdy symulowane urządzenie obsługuje metodę.

> [!NOTE]
> Aby śledzić stan zadania w rozwiązaniu, wybierz pozycję **Wyświetl stan zadania**.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób dostosowywania [akceleratora rozwiązania do monitorowania zdalnego](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) , który opisuje sposób dostosowywania akceleratora rozwiązań.