---
title: Wysyłanie alertów usługi Azure Service Health z usługi PagerDuty
description: Uzyskaj spersonalizowane powiadomienia o zdarzeniach kondycji usługi do wystąpienia usługi PagerDuty.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7ae5141f33ebf4ec6e32029625af3a199a04d67b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594533"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Wysyłanie alertów usługi Azure Service Health za pomocą usługi PagerDuty przy użyciu elementów webhook

W tym artykule opisano sposób konfigurowania powiadomień o kondycji usługi platformy Azure za pośrednictwem usługi PagerDuty przy użyciu elementu webhook. Korzystając z niestandardowego typu integracji Microsoft Azure [usługi PagerDuty](https://www.pagerduty.com/), można bezproblemowo dodawać alerty Service Health do nowych lub istniejących usług usługi PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Tworzenie adresu URL integracji usługi Service Health w usługi PagerDuty
1.  Upewnij się, że zarejestrowano Cię w usłudze i zalogowano się na koncie usługi [usługi PagerDuty](https://www.pagerduty.com/) .

1.  Przejdź do sekcji **usługi** w usługi PagerDuty.

    ![Sekcja "usługi" w usługi PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Wybierz pozycję **Dodaj nową usługę** lub Otwórz istniejącą usługę, która została skonfigurowana.

1.  W obszarze **Ustawienia integracji** wybierz następujące opcje:

    a. **Typ integracji**: Microsoft Azure

    b. **Nazwa integracji**: \<Name\>

    !["Ustawienia integracji" w usługi PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Wypełnij wszystkie inne wymagane pola i wybierz pozycję **Dodaj**.

1.  Otwórz tę nową integrację i skopiuj i Zapisz **adres URL integracji**.

    !["Adres URL integracji" w usługi PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Tworzenie alertu przy użyciu usługi PagerDuty w Azure Portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w temacie [Tworzenie alertu w ramach powiadomienia o kondycji usługi dla nowej grupy akcji przy użyciu Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. Zdefiniuj na liście **akcji**:

    a. **Typ akcji:** *element webhook*

    b. **Szczegóły:** **Adres URL integracji** usługi PagerDuty, który został wcześniej zapisany.

    c. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz pozycję **Zapisz** po zakończeniu, aby utworzyć alert.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Monitoruj**.

1. W sekcji **Ustawienia** wybierz pozycję **grupy akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcji**:

    a. **Typ akcji:** *element webhook*

    b. **Szczegóły:** **Adres URL integracji** usługi PagerDuty, który został wcześniej zapisany.

    c. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz pozycję **Zapisz** po zakończeniu, aby zaktualizować grupę akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji elementu webhook za pośrednictwem żądania HTTP POST
1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Możesz znaleźć przykładowy ładunek elementu webhook kondycji usługi w [elementach webhook dla alertów dziennika aktywności platformy Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinna zostać wyświetlona `202 Accepted` wiadomość z komunikatem zawierającym identyfikator zdarzenia.

1. Przejdź do [usługi PagerDuty](https://www.pagerduty.com/) , aby upewnić się, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](./service-notifications.md).
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/alerts/action-groups.md).
