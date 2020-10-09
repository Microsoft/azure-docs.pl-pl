---
title: Korzystanie z alertów w rozwiązaniu do zdalnego monitorowania — Azure | Microsoft Docs
description: Ten samouczek przedstawia sposób używania alertów w celu identyfikowania i rozwiązywania problemów z urządzeniami podłączonymi do akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 853fa2b80e04dd8d9225d023db8030fed044ed7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890920"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Samouczek: rozwiązywanie problemów z urządzeniami

W tym samouczku użyjesz akceleratora rozwiązania do monitorowania zdalnego, aby zidentyfikować i naprawić problemy z połączonymi urządzeniami IoT. Alertów na pulpicie nawigacyjnym akceleratora rozwiązania używa się do identyfikowania problemów oraz uruchamiania zdalnych zadań w celu ich rozwiązywania.

Firma Contoso testuje nowe **prototypowe** urządzenie w terenie. Jako operator firmy Contoso podczas testowania zauważasz, że **prototypowe** urządzenie nieoczekiwanie wyzwala alert o temperaturze na pulpicie nawigacyjnym. Teraz musisz zbadać zachowanie tego wadliwego **prototypowego** urządzenia i rozwiązać problem.

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Badanie alertu z urządzenia
> * Rozwiązywanie problemu z urządzeniem

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Badanie alertu

Na stronie **Pulpit nawigacyjny** możesz zauważyć, że istnieją nieoczekiwane alerty temperatury pochodzące z reguły związanej z urządzeniami **prototypowymi**:

[![Alerty wyświetlane na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Aby zbadać problem, wybierz opcję **Eksploruj alert** obok alertu:

[![Eksplorowanie alertu z poziomu pulpitu nawigacyjnego](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

Szczegółowy widok alertu zawiera następujące elementy:

* Czas wyzwolenia alertu
* Informacje o stanie urządzeń związanych z alertem
* Telemetria z urządzeń związanych z alertem

[![Szczegóły alertu](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Aby potwierdzić otrzymanie alertu, wybierz wszystkie **wystąpienia alertu**, a następnie wybierz opcję **Potwierdź**. Dzięki tej akcji inni operatorzy dowiedzą się, że użytkownik zobaczył alert i pracuje nad nim:

[![Potwierdzanie alertów](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Po potwierdzeniu alertu stan wystąpienia zmieni się na **Potwierdzono**.

Na liście urządzeń z alertami możesz zobaczyć urządzenie **Prototyp** odpowiedzialne za wyzwolenie alertu temperatury:

[![Wyświetl listę urządzeń powodujących alert](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Rozwiązywanie problemu

Aby rozwiązać problem z urządzeniem **Prototyp**, musisz wywołać metodę **DecreaseTemperature** na urządzeniu.

Aby wykonać działania na urządzeniu, wybierz je z listy urządzeń z alertami, a następnie wybierz opcję **Zadania**. Model urządzenia **Prototyp** obsługuje sześć metod:

[![Wyświetlanie metod obsługiwanych przez urządzenie](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Wybierz metodę **DecreaseTemperature** i ustaw nazwę zadania na **DecreaseTemperature**. Następnie kliknij pozycję **Zastosuj**:

[![Utwórz zadanie, aby zmniejszyć temperaturę](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Aby śledzić stan zadania, kliknij przycisk **Wyświetl stan zadania**. Użyj widoku **Zadania**, aby śledzić wszystkie zadania i wywołania metody w rozwiązaniu:

[![Monitoruj zadanie, aby zmniejszyć temperaturę](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Możesz sprawdzić, czy temperatura urządzenia zmniejszyła się, wyświetlając telemetrię na stronie **Pulpit nawigacyjny**:

[![Wyświetl spadek temperatury](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak używać alertów w celu identyfikowania problemów z urządzeniami oraz jak działać na tych urządzeniach, aby rozwiązać problemy. Aby dowiedzieć się, jak połączyć rzeczywiste urządzenie z akceleratorem rozwiązania, przejdź do artykułów z poradami.

Teraz wiesz już, jak zarządzać problemami z urządzeniami. Sugerujemy, aby w następnym kroku dowiedzieć się, jak [połączyć urządzenie z akceleratorem rozwiązania do monitorowania zdalnego](iot-accelerators-connecting-devices.md).
