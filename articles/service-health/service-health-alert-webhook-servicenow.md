---
title: Wysyłanie alertów usługi Azure Service Health z usługi ServiceNow
description: Uzyskaj spersonalizowane powiadomienia o zdarzeniach kondycji usługi do wystąpienia usługi ServiceNow.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: df6596dd9853a792b5bfdb333361a2b9cd02a347
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588195"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Wysyłanie alertów usługi Azure Service Health za pomocą usługi ServiceNow przy użyciu elementów webhook

W tym artykule pokazano, jak zintegrować alerty usługi Azure Service Health z usługą usługi ServiceNow przy użyciu elementu webhook. Po skonfigurowaniu integracji elementu webhook z wystąpieniem usługi usługi ServiceNow uzyskasz alerty za pomocą istniejącej infrastruktury powiadomień w przypadku problemów z usługą platformy Azure. Za każdym razem, gdy Azure Service Health alert, wywołuje element webhook za pomocą interfejsu API REST skryptu usługi ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Tworzenie interfejsu API REST w skrypcie w usługi ServiceNow

1.  Upewnij się, że zarejestrowano Cię w usłudze i zalogowano się na koncie usługi [usługi ServiceNow](https://www.servicenow.com/) .

1.  Przejdź do sekcji **systemowe usługi sieci Web** w usługi ServiceNow i wybierz **interfejsy API REST inicjowane przez skrypty**.

    ![Sekcja "usługa sieci Web w skrypcie" w usługi ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Wybierz pozycję **Nowy** , aby utworzyć nową usługę REST w skrypcie.
 
    ![Przycisk "nowy interfejs API REST w skrypcie" w usługi ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Dodaj **nazwę** do interfejsu API REST i ustaw **identyfikator interfejsu API** na `azureservicehealth` .

1.  Wybierz pozycję **Prześlij**.

    !["Ustawienia interfejsu API REST" w usługi ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Wybierz utworzony interfejs API REST, a następnie na karcie **zasoby** wybierz pozycję **Nowy**.

    !["Karta zasobów" w usługi ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nazwij** nowy zasób `event` i Zmień **metodę http** na `POST` .

1.  W sekcji **skrypt** Dodaj następujący kod JavaScript:

    >[!NOTE]
    >Należy zaktualizować `<secret>` `<group>` wartości, i `<email>` w poniższym skrypcie.
    >* `<secret>` powinien być ciągiem losowym, takim jak identyfikator GUID
    >* `<group>` powinna być grupą usługi ServiceNow, do której chcesz przypisać zdarzenie
    >* `<email>` musi być określoną osobą, do której ma zostać przypisane zdarzenie (opcjonalnie)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Na karcie Zabezpieczenia Usuń zaznaczenie pola wyboru **wymaga uwierzytelniania** i wybierz pozycję **Prześlij**. `<secret>`Ustawiono ochronę tego interfejsu API.

    ![Pole wyboru "wymaga uwierzytelniania" w usługi ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Z powrotem w sekcji skryptowe interfejsy API REST należy znaleźć **podstawową ścieżkę interfejsu API** dla nowego interfejsu API REST:

     !["Podstawowa ścieżka interfejsu API" w usługi ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Adres URL pełnej integracji wygląda następująco:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Tworzenie alertu przy użyciu usługi ServiceNow w Azure Portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [tym artykule](./alerts-activity-log-service-notifications-portal.md) , aby utworzyć alert z nową grupą akcji.

1. Zdefiniuj na liście **akcji**:

    a. **Typ akcji:** *element webhook*

    b. **Szczegóły:** **Adres URL integracji** usługi ServiceNow, który został wcześniej zapisany.

    c. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz pozycję **Zapisz** po zakończeniu, aby utworzyć alert.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Monitoruj**.

1. W sekcji **Ustawienia** wybierz pozycję **grupy akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcji**:

    a. **Typ akcji:** *element webhook*

    b. **Szczegóły:** **Adres URL integracji** usługi ServiceNow, który został wcześniej zapisany.

    c. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz pozycję **Zapisz** po zakończeniu, aby zaktualizować grupę akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji elementu webhook za pośrednictwem żądania HTTP POST
1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Możesz znaleźć przykładowy ładunek elementu webhook kondycji usługi w [elementach webhook dla alertów dziennika aktywności platformy Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinna zostać wyświetlona `200 OK` odpowiedź z komunikatem "utworzono zdarzenie".

1. Przejdź do [usługi ServiceNow](https://www.servicenow.com/) , aby upewnić się, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](./service-notifications.md).
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/alerts/action-groups.md).
