---
title: Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal
description: Powiadomienia o kondycji usługi umożliwiają wyświetlanie komunikatów kondycji usługi opublikowanych przez Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 9f8297ae708d3f4e7921221f2c4bacee12a7a2b1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089570"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomienia o kondycji usługi są publikowane przez infrastrukturę platformy Azure w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).  Powiadomienia zawierają informacje o zasobach w ramach subskrypcji. W przypadku potencjalnie dużej ilości informacji przechowywanych w dzienniku aktywności istnieje oddzielny interfejs użytkownika ułatwiający wyświetlanie i Konfigurowanie alertów dotyczących powiadomień o kondycji usługi. 

Powiadomienia o kondycji usługi mogą być informacyjne lub funkcjonalne w zależności od klasy.

Aby uzyskać więcej informacji na temat różnych klas powiadomień dotyczących kondycji usługi, zobacz [właściwości powiadomień o kondycji usługi](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Wyświetl powiadomienia dotyczące kondycji usługi w Azure Portal

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Monitoruj**.

    ![Zrzut ekranu przedstawiający menu Azure Portal z wybranym monitorem](./media/service-notifications/home-monitor.png)

    Azure Monitor łączą wszystkie ustawienia monitorowania i dane w jednym skonsolidowanym widoku. Na początku widoczna jest sekcja **Dziennik aktywności**.

1. Wybierz pozycję **alerty**.

    ![Zrzut ekranu przedstawiający dziennik aktywności monitora z wybranymi alertami](./media/service-notifications/service-health-summary.png)

1. Wybierz pozycję **+ Dodaj alert dziennika aktywności**i skonfiguruj alert, aby upewnić się, że otrzymujesz powiadomienie o przyszłych powiadomieniach dotyczących usług. Aby uzyskać więcej informacji, zobacz [tworzenie alertów dziennika aktywności w powiadomieniach usługi](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md).
