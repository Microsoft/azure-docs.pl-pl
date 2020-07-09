---
title: Odbieraj alerty dziennika aktywności w powiadomieniach usługi platformy Azure przy użyciu Azure Portal
description: Otrzymuj powiadomienia za pośrednictwem wiadomości SMS, poczty e-mail lub elementu webhook w przypadku wystąpienia usługi platformy Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a8723698cddfb519687525820475517b93219a4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568700"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Tworzenie alertów dziennika aktywności dla powiadomień usługi przy użyciu Azure Portal
## <a name="overview"></a>Omówienie

W tym artykule przedstawiono sposób użycia Azure Portal do konfigurowania alertów dziennika aktywności dla powiadomień o kondycji usługi przy użyciu Azure Portal.  

Powiadomienia o kondycji usługi są przechowywane w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md) z uwzględnieniem prawdopodobnie dużej ilości informacji przechowywanych w dzienniku aktywności, istnieje oddzielny interfejs użytkownika, który ułatwia wyświetlanie i Konfigurowanie alertów dotyczących powiadomień o kondycji usługi. 

Możesz otrzymać Alert, gdy platforma Azure wyśle powiadomienia o kondycji usługi do subskrypcji platformy Azure. Alert można skonfigurować na podstawie:

- Klasa powiadomień o kondycji usługi (problemy z usługą, planowana konserwacja, klasyfikatory kondycji).
- Objęta subskrypcją.
- Uwzględnione usługi.
- Uwzględnione regiony.

> [!NOTE]
> Powiadomienia o kondycji usługi nie wysyłają alertów dotyczących zdarzeń związanych z kondycją zasobów.

Możesz również skonfigurować osobę, do której ma być wysyłany Alert:

- Wybierz istniejącą grupę akcji.
- Utwórz nową grupę akcji (która może być używana w przyszłych alertach).

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

Aby uzyskać informacje dotyczące sposobu konfigurowania alertów powiadomień o kondycji usługi przy użyciu szablonów Azure Resource Manager, zobacz [szablony Menedżer zasobów](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Obejrzyj film wideo na temat konfigurowania pierwszego alertu Azure Service Health

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Alert i Nowa grupa akcji przy użyciu Azure Portal
1. W [portalu](https://portal.azure.com)wybierz pozycję **Service Health**.

    ![Usługa "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. W sekcji **alerty** wybierz pozycję **alerty dotyczące kondycji**.

    ![Karta "alerty kondycji"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Wybierz pozycję **Utwórz alert kondycji usługi** i wypełnij pola.

    ![Polecenie "Utwórz alert kondycji usługi"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Wybierz **subskrypcję**, **usługi**i **regiony** , dla których chcesz otrzymywać alerty.

    ![Okno dialogowe Dodawanie alertu dziennika aktywności](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Ta subskrypcja służy do zapisywania alertu dziennika aktywności. Zasób alertu jest wdrażany w tej subskrypcji i monitoruje zdarzenia w dzienniku aktywności.

1. Wybierz **typy zdarzeń** , dla których chcesz otrzymywać alerty: problem z *usługą*, *Planowana konserwacja*i *klasyfikatory kondycji* 

1. Zdefiniuj szczegóły alertu, wprowadzając nazwę i **Opis** **reguły alertu** .

1. Wybierz **grupę zasobów** , w której ma zostać zapisany alert.

1. Utwórz nową grupę akcji, wybierając pozycję **Nowa grupa akcji**. Wprowadź nazwę w polu **Nazwa grupy akcji** , a następnie wprowadź nazwę w polu **krótka nazwa** . Krótka nazwa jest przywoływana w powiadomieniach, które są wysyłane, gdy ten alert zostanie wyzwolony.

    ![Utwórz nową grupę akcji](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Zdefiniuj listę odbiorników, dostarczając:

    a. **Nazwa**: Wprowadź nazwę, alias lub identyfikator odbiorcy.

    b. **Typ akcji**: wybierz pozycję SMS, wiadomość e-mail, element webhook, aplikację platformy Azure i nie tylko.

    c. **Szczegóły**: w zależności od wybranego typu akcji wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook itd.

1. Wybierz **przycisk OK** , aby utworzyć grupę akcji, a następnie **Utwórz regułę alertu** w celu ukończenia alertu.

W ciągu kilku minut alert jest aktywny i rozpocznie się wyzwalanie na podstawie warunków określonych podczas tworzenia.

Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md). Aby uzyskać informacje na temat schematu elementu webhook dla alertów dziennika aktywności, zobacz elementy [webhook dla alertów dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Grupa akcji zdefiniowana w tych krokach jest wielokrotnego użytku jako istniejąca grupa akcji dla wszystkich przyszłych definicji alertów.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Zgłoś alert z istniejącą grupą akcji przy użyciu Azure Portal

1. Wykonaj kroki od 1 do 6 w poprzedniej sekcji, aby utworzyć powiadomienie dotyczące kondycji usługi. 

1. W obszarze **Definiowanie grupy akcji**kliknij przycisk **Wybierz grupę akcji** . Wybierz odpowiednią grupę akcji.

1. Wybierz pozycję **Dodaj** , aby dodać grupę akcji, a następnie **Utwórz regułę alertu** w celu ukończenia alertu.

W ciągu kilku minut alert jest aktywny i rozpocznie się wyzwalanie na podstawie warunków określonych podczas tworzenia.


## <a name="next-steps"></a>Następne kroki
- Poznaj [najlepsze rozwiązania dotyczące konfigurowania Azure Service Health alertów](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Dowiedz się, jak [skonfigurować mobilne powiadomienia wypychane dla Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](service-notifications.md).
- Dowiedz się więcej na temat [ograniczania liczby powiadomień](../azure-monitor/platform/alerts-rate-limiting.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Zapoznaj się z [omówieniem alertów dziennika aktywności](../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/platform/action-groups.md).
