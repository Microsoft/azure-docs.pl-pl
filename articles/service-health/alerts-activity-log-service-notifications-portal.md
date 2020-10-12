---
title: Odbieraj alerty dziennika aktywności w powiadomieniach usługi platformy Azure przy użyciu Azure Portal
description: Informacje na temat używania Azure Portal do konfigurowania alertów dziennika aktywności dla powiadomień o kondycji usługi przy użyciu Azure Portal.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 88fbdeeb8bdcc239f1591e053aaf4fb1c36b9b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289790"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Tworzenie alertów dziennika aktywności dla powiadomień usługi przy użyciu Azure Portal
## <a name="overview"></a>Omówienie

W tym artykule przedstawiono sposób użycia Azure Portal do konfigurowania alertów dziennika aktywności dla powiadomień o kondycji usługi przy użyciu Azure Portal.  

Powiadomienia o kondycji usługi są przechowywane w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md). W przypadku dużej ilości informacji przechowywanych w dzienniku aktywności istnieje oddzielny interfejs użytkownika ułatwiający wyświetlanie i Konfigurowanie alertów dotyczących powiadomień o kondycji usługi. 

Możesz otrzymać Alert, gdy platforma Azure wyśle powiadomienia o kondycji usługi do subskrypcji platformy Azure. Alert można skonfigurować na podstawie:

- Klasa powiadomień o kondycji usługi (problemy z usługą, planowana konserwacja, klasyfikatory kondycji, Klasyfikatory zabezpieczeń).
- Objęta subskrypcją.
- Uwzględnione usługi.
- Uwzględnione regiony.

> [!NOTE]
> Powiadomienia o kondycji usługi nie wysyłają alertów dotyczących zdarzeń dotyczących kondycji zasobów.

Możesz również skonfigurować osobę, do której ma być wysyłany Alert:

- Wybierz istniejącą grupę akcji.
- Utwórz nową grupę akcji (która może być używana w przyszłych alertach).

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

Aby uzyskać informacje dotyczące sposobu konfigurowania alertów powiadomień o kondycji usługi przy użyciu szablonów Azure Resource Manager, zobacz [szablony Menedżer zasobów](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Obejrzyj film wideo na temat konfigurowania pierwszego alertu Azure Service Health

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Tworzenie alertu Service Health przy użyciu Azure Portal
1. W [portalu](https://portal.azure.com)wybierz pozycję **Service Health**.

    ![Usługa "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. W sekcji **alerty** wybierz pozycję **alerty dotyczące kondycji**.

    ![Karta "alerty kondycji"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Wybierz pozycję **Dodaj alert kondycji usługi** i wypełnij pola.

    ![Polecenie "Utwórz alert kondycji usługi"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Wybierz **subskrypcję**, **usługi**i **regiony** , dla których chcesz otrzymywać alerty.

    [![Okno dialogowe Dodawanie alertu dziennika aktywności](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Ta subskrypcja służy do zapisywania alertu dziennika aktywności. Zasób alertu jest wdrażany w tej subskrypcji i monitoruje zdarzenia w dzienniku aktywności.

5. Wybierz **typy zdarzeń** , dla których chcesz otrzymywać alerty: problem z *usługą*, *zaplanowaną konserwację*, *klasyfikatory kondycji*i klasyfikator *zabezpieczeń*.

6. Kliknij pozycję **Wybierz grupę akcji** , aby wybrać istniejącą grupę akcji lub utworzyć nową grupę akcji. Aby uzyskać więcej informacji na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](../azure-monitor/platform/action-groups.md).


7. Zdefiniuj szczegóły alertu, wprowadzając nazwę i **Opis** **reguły alertu** .

8. Wybierz **grupę zasobów** , w której ma zostać zapisany alert.



W ciągu kilku minut alert jest aktywny i rozpocznie się wyzwalanie na podstawie warunków określonych podczas tworzenia.

Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md). Aby uzyskać informacje na temat schematu elementu webhook dla alertów dziennika aktywności, zobacz elementy [webhook dla alertów dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Następne kroki
- Poznaj [najlepsze rozwiązania dotyczące konfigurowania Azure Service Health alertów](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Dowiedz się, jak [skonfigurować mobilne powiadomienia wypychane dla Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](service-notifications.md).
- Dowiedz się więcej na temat [ograniczania liczby powiadomień](../azure-monitor/platform/alerts-rate-limiting.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Zapoznaj się z [omówieniem alertów dziennika aktywności](../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/platform/action-groups.md).
