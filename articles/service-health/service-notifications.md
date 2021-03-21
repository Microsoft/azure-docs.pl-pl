---
title: Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal
description: Wyświetl powiadomienia dotyczące kondycji usługi w Azure Portal. Powiadomienia o kondycji usługi są publikowane przez infrastrukturę platformy Azure w dzienniku aktywności platformy Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 9f9f3e7b10d9aa0014e4e00e7bfa72c9dc66e142
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587998"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomienia o kondycji usługi są publikowane przez infrastrukturę platformy Azure w [dzienniku aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).  Powiadomienia zawierają informacje o zasobach w ramach subskrypcji. W przypadku potencjalnie dużej ilości informacji przechowywanych w dzienniku aktywności istnieje oddzielny interfejs użytkownika ułatwiający wyświetlanie i Konfigurowanie alertów dotyczących powiadomień o kondycji usługi. 

Powiadomienia o kondycji usługi mogą być informacyjne lub funkcjonalne w zależności od klasy.

Aby uzyskać więcej informacji na temat różnych klas powiadomień dotyczących kondycji usługi, zobacz [właściwości powiadomień o kondycji usługi](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Wyświetl powiadomienia dotyczące kondycji usługi w Azure Portal

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Monitoruj**.

    ![Zrzut ekranu przedstawiający menu Azure Portal z wybranym monitorem](./media/service-notifications/home-monitor.png)

    Azure Monitor łączą wszystkie ustawienia monitorowania i dane w jednym skonsolidowanym widoku. Na początku widoczna jest sekcja **Dziennik aktywności**.

1. Wybierz pozycję **alerty**.

    ![Zrzut ekranu przedstawiający dziennik aktywności monitora z wybranymi alertami](./media/service-notifications/service-health-summary.png)

1. Wybierz pozycję **+ Dodaj alert dziennika aktywności** i skonfiguruj alert, aby upewnić się, że otrzymujesz powiadomienie o przyszłych powiadomieniach dotyczących usług. Aby uzyskać więcej informacji, zobacz [tworzenie alertów dziennika aktywności w powiadomieniach usługi](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md).
