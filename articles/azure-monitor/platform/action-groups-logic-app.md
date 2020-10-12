---
title: Jak wyzwolić złożone akcje z alertami Azure Monitor
description: Dowiedz się, jak utworzyć akcję aplikacji logiki w celu przetworzenia alertów Azure Monitor.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: f76d28018fdf55314593dabc44ef1e9a1dab9494
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403130"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Jak wyzwolić złożone akcje z alertami Azure Monitor

W tym artykule opisano sposób konfigurowania i wyzwalania aplikacji logiki w celu utworzenia konwersacji w usłudze Microsoft Teams, gdy zostanie wyzwolony alert.

## <a name="overview"></a>Omówienie

Po wyzwoleniu alertu Azure Monitor jest on wywoływany przez [grupę akcji](./action-groups.md). Grupy akcji umożliwiają wywoływanie co najmniej jednej akcji w celu powiadomienia innych o alercie, a także ich korygowania.

Ogólny proces to:

-   Utwórz aplikację logiki dla odpowiedniego typu alertu.

-   Zaimportuj przykładowy ładunek dla odpowiedniego typu alertu do aplikacji logiki.

-   Zdefiniuj zachowanie aplikacji logiki.

-   Skopiuj punkt końcowy HTTP aplikacji logiki do grupy akcji platformy Azure.

Ten proces jest podobny, jeśli chcesz, aby aplikacja logiki wykonywała inną akcję.

## <a name="create-an-activity-log-alert-administrative"></a>Tworzenie alertu dziennika aktywności: administracyjne

1.  W Azure Portal wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

2.  Wyszukaj i wybierz pozycję **aplikacja logiki**, a następnie wybierz pozycję **Utwórz**.

3.  Nadaj **nazwę**aplikacji logiki, wybierz **grupę zasobów**i tak dalej.

    ![Tworzenie aplikacji logiki](media/action-groups-logic-app/create-logic-app-dialog.png "Tworzenie aplikacji logiki")

4.  Wybierz pozycję **Utwórz** , aby utworzyć aplikację logiki. Komunikat podręczny wskazuje, że utworzono aplikację logiki. Wybierz pozycję **Uruchom zasób** , aby otworzyć **projektanta Logic Apps**.

5.  Wybierz wyzwalacz: **po odebraniu żądania HTTP**.

    ![Wyzwalacze aplikacji logiki](media/action-groups-logic-app/logic-app-triggers.png "Wyzwalacze aplikacji logiki")

6.  Wybierz pozycję **Edytuj** , aby zmienić wyzwalacz żądania HTTP.

    ![Wyzwalacze żądań HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Wyzwalacze żądań HTTP")

7.  Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

    ![Użyj przykładowego ładunku](media/action-groups-logic-app/use-sample-payload-button.png "Użyj przykładowego ładunku")

8.  Skopiuj i wklej następujący przykładowy ładunek do okna dialogowego:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. **Projektant aplikacji logiki** wyświetli okno podręczne, które przypomina o tym, że żądanie wysyłane do aplikacji logiki musi ustawić nagłówek **Content-Type** do **Application/JSON**. Zamknij okno podręczne. Alert Azure Monitor ustawia nagłówek.

    ![Ustawianie nagłówka Content-Type](media/action-groups-logic-app/content-type-header.png "Ustawianie nagłówka Content-Type")

10. Wybierz pozycję **+** **nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.

    ![Dodawanie akcji](media/action-groups-logic-app/add-action.png "Dodawanie akcji")

11. Wyszukaj i wybierz łącznik usługi Microsoft Teams. Wybierz akcję **Microsoft Teams-post Message** .

    ![Akcje programu Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Akcje programu Microsoft Teams")

12. Skonfiguruj akcję Microsoft Teams. **Projektant Logic Apps** prosi o uwierzytelnienie na koncie służbowym. Wybierz **Identyfikator zespołu** i **Identyfikator kanału** , do którego ma zostać wysłana wiadomość.

13. Skonfiguruj komunikat przy użyciu kombinacji tekstu statycznego i odwołań do \<fields\> zawartości dynamicznej. Skopiuj i wklej następujący tekst do pola **Message** :

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Następnie wyszukaj i Zastąp \<fields\> Tagi z zawartością dynamiczną o tej samej nazwie.

    > [!NOTE]
    > Istnieją dwa pola dynamiczne o nazwie **status**. Dodaj oba te pola do komunikatu. Użyj pola znajdującego się w zbiorze właściwości **activityLog** i Usuń inne pole. Umieść kursor nad polem **stanu** , aby wyświetlić w pełni kwalifikowane odwołanie do pola, jak pokazano na poniższym zrzucie ekranu:

    ![Akcja Microsoft Teams: Publikowanie wiadomości](media/action-groups-logic-app/teams-action-post-message.png "Akcja Microsoft Teams: Publikowanie wiadomości")

14. W górnej części **projektanta Logic Apps**wybierz pozycję **Zapisz** , aby zapisać aplikację logiki.

15. Otwórz istniejącą grupę akcji i Dodaj akcję odwołującą się do aplikacji logiki. Jeśli nie masz istniejącej grupy akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal,](./action-groups.md) aby je utworzyć. Nie zapomnij zapisać zmian.

    ![Aktualizowanie grupy akcji](media/action-groups-logic-app/update-action-group.png "Aktualizowanie grupy akcji")

Następnym razem, gdy alert wywoła grupę akcji, wywoływana jest aplikacja logiki.

## <a name="create-a-service-health-alert"></a>Tworzenie alertu dotyczącego kondycji usługi

Wpisy Azure Service Health są częścią dziennika aktywności. Proces tworzenia alertu jest podobny do [tworzenia alertu dziennika aktywności](#create-an-activity-log-alert-administrative), ale z kilkoma zmianami:

- Kroki od 1 do 7 są takie same.
- W kroku 8 Użyj następującego przykładowego ładunku dla wyzwalacza żądania HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Kroki 9 i 10 są takie same.
-  Dla kroków od 11 do 14 wykonaj następujący proces:

   1. Wybierz pozycję **+** **nowy krok** , a następnie wybierz pozycję **Dodaj warunek**. Ustaw następujące warunki, aby aplikacja logiki była uruchamiana tylko wtedy, gdy dane wejściowe pasują do poniższych wartości.  Gdy wprowadzasz wartość wersji do pola tekstowego, umieść cudzysłowy wokół niego ("0.1.1"), aby upewnić się, że jest ono oceniane jako ciąg, a nie typ liczbowy.  W przypadku powrotu do strony w systemie nie są wyświetlane cudzysłowy, ale kod źródłowy nadal utrzymuje typ ciągu.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health warunek ładunku"](media/action-groups-logic-app/service-health-payload-condition.png "Service Health warunek ładunku")

   1. W przypadku warunku **if true** postępuj zgodnie z instrukcjami podanymi w sekcji kroki od 11 do 13 w temacie [Tworzenie alertu dziennika aktywności](#create-an-activity-log-alert-administrative) , aby dodać akcję Microsoft Teams.

   1. Zdefiniuj komunikat przy użyciu kombinacji HTML i zawartości dynamicznej. Skopiuj i wklej następującą zawartość do pola **wiadomości** . Zastąp `[incidentType]` `[trackingID]` pola,, `[title]` i `[communication]` ze znacznikami zawartości dynamicznej o tej samej nazwie:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       ![Akcja post "Service Health prawdy warunku"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Akcja po akcji post Service Health true Condition")

   1. W przypadku warunku w **przypadku wartości false** Podaj przydatny komunikat:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       ![Akcja post warunku "Service Health wartość false"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Akcja post Service Health warunku false")

- Krok 15 jest taki sam. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupę akcji.

## <a name="create-a-metric-alert"></a>Tworzenie alertu metryki

Proces tworzenia alertu dotyczącego metryki jest podobny do [tworzenia alertu dziennika aktywności](#create-an-activity-log-alert-administrative), ale z kilkoma zmianami:

- Kroki od 1 do 7 są takie same.
- W kroku 8 Użyj następującego przykładowego ładunku dla wyzwalacza żądania HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Kroki 9 i 10 są takie same.
- Dla kroków od 11 do 14 wykonaj następujący proces:

  1. Wybierz pozycję **+** **nowy krok** , a następnie wybierz pozycję **Dodaj warunek**. Ustaw następujące warunki, aby aplikacja logiki była uruchamiana tylko wtedy, gdy dane wejściowe pasują do poniższych wartości. Gdy wprowadzasz wartość wersji do pola tekstowego, umieść cudzysłowy wokół niego ("2,0"), aby upewnić się, że jest ono oceniane jako ciąg, a nie typ liczbowy.  W przypadku powrotu do strony w systemie nie są wyświetlane cudzysłowy, ale kod źródłowy nadal utrzymuje typ ciągu. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Warunek ładunku alertu metryki"](media/action-groups-logic-app/metric-alert-payload-condition.png "Warunek ładunku alertu metryki")

  1. W warunku **if true** Dodaj pętlę **for each** i akcję Microsoft Teams. Zdefiniuj komunikat przy użyciu kombinacji HTML i zawartości dynamicznej.

      !["Akcja post stanu alertu metryki"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Akcja post stanu alertu metryki")

  1. W warunku w **przypadku wartości false** Zdefiniuj akcję Microsoft Teams, aby komunikować się, że alert metryki nie jest zgodny z oczekiwaniami aplikacji logiki. Uwzględnij ładunek JSON. Zwróć uwagę, jak odwoływać się do `triggerBody` zawartości dynamicznej w `json()` wyrażeniu.

      !["Akcja post stanu alertu metryki"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Akcja post stanu alertu o wartości false")

- Krok 15 jest taki sam. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupę akcji.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Wywoływanie innych aplikacji poza programem Microsoft Teams
Logic Apps ma wiele różnych łączników, które umożliwiają wyzwalanie akcji w szerokim zakresie aplikacji i baz danych. Przykłady czasu, SQL Server, Oracle, Salesforce, są tylko przykładami. Aby uzyskać więcej informacji na temat łączników, zobacz [Łączniki aplikacji logiki](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się [z omówieniem alertów dziennika aktywności platformy Azure](./alerts-overview.md) i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [skonfigurować alerty po opublikowaniu powiadomienia Azure Service Health](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Dowiedz się więcej na temat [grup akcji](./action-groups.md).

