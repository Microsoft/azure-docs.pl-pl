---
title: Wysyłanie alertów usługi Azure Service Health za pomocą OpsGenie przy użyciu elementów webhook
description: Uzyskaj spersonalizowane powiadomienia o zdarzeniach kondycji usługi do wystąpienia OpsGenie.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 112774cb1f9e16b08225471e8dbc1bb79b1bd37d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86529101"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Wysyłanie alertów usługi Azure Service Health za pomocą OpsGenie przy użyciu elementów webhook

W tym artykule opisano sposób konfigurowania alertów usługi Azure Service Health za pomocą OpsGenie przy użyciu elementu webhook. Za pomocą integracji Azure Service Health [OpsGenie](https://www.opsgenie.com/)można przesłać dalej Azure Service Health alerty do OpsGenie. OpsGenie może określić odpowiednie osoby do powiadamiania na podstawie harmonogramów związanych z wywołaniami, korzystania z poczty e-mail, wiadomości SMS, połączeń telefonicznych, powiadomień wypychanych systemu iOS & systemu Android oraz powiadamiania o alertach do momentu potwierdzenia lub zamknięcia alertu.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Tworzenie adresu URL integracji usługi Service Health w OpsGenie
1.  Upewnij się, że zarejestrowano Cię w usłudze i zalogowano się na koncie usługi [OpsGenie](https://www.opsgenie.com/) .

1.  Przejdź do sekcji **Integrations** w OpsGenie.

    ![Sekcja "Integrations" w OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Wybierz przycisk **Azure Service Health** integrację.

    !["Azure Service Health przycisk" w OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nazwij** alert i Określ pole **Assigned to Team** .

1.  Wypełnij inne pola, takie jak **Adresaci**, **włączone**i **Pomijaj powiadomienia**.

1.  Skopiuj i Zapisz **adres URL integracji**, który powinien już zawierać `apiKey` dołączony do końca.

    !["Adres URL integracji" w OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Wybierz pozycję **Zapisz integrację**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Tworzenie alertu przy użyciu OpsGenie w Azure Portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w temacie [Tworzenie alertu w ramach powiadomienia o kondycji usługi dla nowej grupy akcji przy użyciu Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. Zdefiniuj na liście **akcji**:

    a. **Typ akcji:** *element webhook*

    b. **Szczegóły:** **Adres URL integracji** OpsGenie, który został wcześniej zapisany.

    c. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz pozycję **Zapisz** po zakończeniu, aby utworzyć alert.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Monitoruj**.

1. W sekcji **Ustawienia** wybierz pozycję **grupy akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcji**:

    a. **Typ akcji:** *element webhook*

    b. **Szczegóły:** **Adres URL integracji** OpsGenie, który został wcześniej zapisany.

    c. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz pozycję **Zapisz** po zakończeniu, aby zaktualizować grupę akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji elementu webhook za pośrednictwem żądania HTTP POST
1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Możesz znaleźć przykładowy ładunek elementu webhook kondycji usługi w [elementach webhook dla alertów dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Należy odebrać `200 OK` odpowiedź z komunikatem o stanie "powodzenie".

1. Przejdź do [OpsGenie](https://www.opsgenie.com/) , aby upewnić się, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](./service-notifications.md).
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/platform/action-groups.md).
