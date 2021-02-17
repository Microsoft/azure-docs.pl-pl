---
title: Monitorowanie aplikacji logiki przy użyciu dzienników Azure Monitor
description: Rozwiązywanie problemów z aplikacjami logiki przez skonfigurowanie Azure Monitor dzienników i zbieranie danych diagnostycznych dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: d72e3ffc8698d9f3a2df170bd87f2969475ca9ed
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580685"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Konfigurowanie Azure Monitor dzienników i zbieranie danych diagnostycznych dla Azure Logic Apps

Aby uzyskać bogatsze informacje o debugowaniu aplikacji logiki w czasie wykonywania, można skonfigurować i użyć [dzienników Azure monitor](../azure-monitor/logs/data-platform-logs.md) do rejestrowania i przechowywania informacji dotyczących danych i zdarzeń środowiska uruchomieniowego, takich jak zdarzenia wyzwalania, zdarzenia uruchamiania i zdarzenia akcji w [obszarze roboczym log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). [Azure monitor](../azure-monitor/overview.md) ułatwia monitorowanie środowisk w chmurze i lokalnych, dzięki czemu można łatwiej zachować swoją dostępność i wydajność. Korzystając z dzienników Azure Monitor, można tworzyć [zapytania dziennika](../azure-monitor/logs/log-query-overview.md) , które pomagają zbierać i przeglądać te informacje. Możesz również [używać tych danych diagnostycznych z innymi usługami platformy Azure](#extend-data), takimi jak Azure Storage i Azure Event Hubs.

Aby skonfigurować rejestrowanie dla aplikacji logiki, możesz [włączyć log Analytics podczas tworzenia aplikacji logiki](#logging-for-new-logic-apps)lub [zainstalować rozwiązanie do zarządzania Logic Apps](#install-management-solution) w obszarze roboczym log Analytics dla istniejących aplikacji logiki. To rozwiązanie zawiera zagregowane informacje o uruchomieniach aplikacji logiki i zawiera szczegółowe informacje, takie jak stan, czas wykonywania, stan ponownego przesłania i identyfikatory korelacji. Następnie, aby włączyć rejestrowanie i utworzyć zapytania dotyczące tych informacji, [Skonfiguruj dzienniki Azure monitor](#set-up-resource-logs).

W tym artykule pokazano, jak włączyć Log Analytics w nowych aplikacjach logiki i istniejących aplikacjach logiki, jak zainstalować i skonfigurować rozwiązanie do zarządzania Logic Apps oraz jak skonfigurować i utworzyć zapytania dla dzienników Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem potrzebny jest [obszar roboczy log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Jeśli nie masz obszaru roboczego, Dowiedz się, [jak utworzyć obszar roboczy log Analytics](../azure-monitor/logs/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Włącz Log Analytics dla nowych aplikacji logiki

Log Analytics można włączyć podczas tworzenia aplikacji logiki.

1. W [Azure Portal](https://portal.azure.com), w okienku, w którym są podane informacje służące do utworzenia aplikacji logiki, wykonaj następujące kroki:

   1. W obszarze **log Analytics** wybierz pozycję **włączone**.

   1. Z listy **obszarów roboczych log Analytics** wybierz obszar roboczy, w którym chcesz wysłać dane z aplikacji logiki.

      ![Podawanie informacji na temat aplikacji logiki](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Po zakończeniu tego kroku platforma Azure utworzy aplikację logiki, która jest teraz skojarzona z obszarem roboczym Log Analytics. Ten krok powoduje również automatyczne zainstalowanie rozwiązania do zarządzania Logic Apps w obszarze roboczym.

1. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

1. Po uruchomieniu aplikacji logiki, aby wyświetlić uruchomienia aplikacji logiki, [wykonaj te kroki](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Zainstaluj Logic Apps rozwiązanie do zarządzania

Jeśli włączono Log Analytics podczas tworzenia aplikacji logiki, Pomiń ten krok. Masz już zainstalowane rozwiązanie do zarządzania Logic Apps w obszarze roboczym Log Analytics.

1. W polu wyszukiwania [Azure Portal](https://portal.azure.com)wprowadź wartość `log analytics workspaces` , a następnie wybierz pozycję **log Analytics obszary robocze**.

   ![Wybierz pozycję "Log Analytics obszary robocze"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. W obszarze **obszary robocze log Analytics** wybierz swój obszar roboczy.

   ![Wybierz obszar roboczy Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. W okienku **Przegląd** w obszarze **wprowadzenie do log Analytics**  >  **konfigurowania rozwiązań monitorowania** wybierz pozycję **Wyświetl rozwiązania**.

   ![W okienku Przegląd wybierz pozycję "Wyświetl rozwiązania"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. W obszarze **Przegląd** wybierz pozycję **Dodaj**.

   ![W okienku Przegląd Dodaj nowe rozwiązanie](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Po otwarciu **portalu Marketplace** w polu wyszukiwania wpisz `logic apps management` , a następnie wybierz pozycję **Zarządzanie Logic Apps**.

   ![W witrynie Marketplace wybierz pozycję "Logic Apps Management".](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. W okienku opis rozwiązania wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Utwórz", aby dodać rozwiązanie "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Przejrzyj i Potwierdź obszar roboczy Log Analytics, w którym chcesz zainstalować rozwiązanie, a następnie wybierz pozycję **Utwórz** ponownie.

   ![Wybierz pozycję "Utwórz", aby utworzyć "Logic Apps zarządzanie"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Gdy platforma Azure wdroży rozwiązanie w grupie zasobów platformy Azure, która zawiera obszar roboczy Log Analytics, rozwiązanie zostanie wyświetlone w okienku podsumowania obszaru roboczego.

   ![Okienko podsumowania obszaru roboczego](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurowanie dzienników Azure Monitor

Podczas przechowywania informacji o zdarzeniach i danych środowiska uruchomieniowego w [dziennikach Azure monitor](../azure-monitor/logs/data-platform-logs.md)można utworzyć [zapytania dziennika](../azure-monitor/logs/log-query-overview.md) , które ułatwiają znajdowanie i przeglądanie tych informacji.

> [!NOTE]
> Po włączeniu ustawień diagnostycznych dane diagnostyczne mogą nie przepływać przez maksymalnie 30 minut do dzienników w określonym miejscu docelowym, na przykład Log Analytics, centrum zdarzeń lub koncie magazynu. To opóźnienie oznacza, że dane diagnostyczne z tego okresu mogą nie być dostępne do przeglądu. Ukończone zdarzenia i [śledzone właściwości](#extend-data) mogą nie być wyświetlane w obszarze roboczym Log Analytics przez 10-15 minut.

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz aplikację logiki.

1. W menu aplikacji logiki w obszarze **monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**  >  **Dodaj ustawienie diagnostyczne**.

   ![W obszarze "monitorowanie" Wybierz pozycję "Ustawienia diagnostyczne" > "Dodaj ustawienie diagnostyczne"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Aby utworzyć ustawienie, wykonaj następujące kroki:

   1. Podaj nazwę ustawienia.

   1. Wybierz pozycję **Wyślij do log Analytics**.

   1. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure, która jest skojarzona z obszarem roboczym log Analytics.

   1. W **obszarze obszar roboczy log Analytics** wybierz obszar roboczy, którego chcesz użyć.

   1. W obszarze **Dziennik** wybierz kategorię element **WorkflowRuntime** , która określa kategorię zdarzenia, która ma zostać zarejestrowana.

   1. Aby zaznaczyć wszystkie metryki, w obszarze **Metryka** wybierz pozycję **AllMetrics**.

   1. Po zakończeniu wybierz pozycję **Zapisz**.

   Na przykład:

   ![Wybierz Log Analytics obszar roboczy i dane do rejestrowania](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Wyświetl stan uruchomień aplikacji logiki

Po uruchomieniu aplikacji logiki można wyświetlić dane dotyczące tych przebiegów w obszarze roboczym Log Analytics.

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz obszar roboczy log Analytics.

1. W menu obszaru roboczego wybierz pozycję **Podsumowanie obszaru roboczego**  >  **Logic Apps zarządzanie**.

   ![Stan i liczba uruchomień aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Jeśli kafelek zarządzanie Logic Apps nie będzie natychmiast wyświetlał wyników po uruchomieniu, spróbuj wybrać pozycję **Odśwież** lub poczekaj przez krótki czas przed ponowieniem próby.

   W tym miejscu są uruchamiane aplikacje logiki pogrupowane według nazwy lub stanu wykonania. Ta strona zawiera także szczegółowe informacje o błędach w akcjach lub wyzwalaczach dla uruchomienia aplikacji logiki.

   ![Podsumowanie stanu dla uruchomionych aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Aby wyświetlić wszystkie uruchomienia dla określonej aplikacji logiki lub stanu, wybierz wiersz dla tej aplikacji logiki lub stanu.

   Oto przykład, w którym są wyświetlane wszystkie uruchomienia dla określonej aplikacji logiki:

   ![Wyświetlanie uruchomień i stanu aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   > [!NOTE]
   > Możliwość ponownego **przesłania** na tej stronie jest obecnie niedostępna.

   W przypadku akcji, w których można [skonfigurować śledzone właściwości](#extend-data), można także wyświetlić te właściwości, wybierając pozycję **Widok** w kolumnie **śledzone właściwości** . Aby wyszukać śledzone właściwości, Użyj filtru kolumn.

   ![Wyświetlanie śledzonych właściwości aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Aby filtrować wyniki, można wykonać filtrowanie po stronie klienta i po stronie serwera.

   * **Filtr po stronie klienta**: dla każdej kolumny wybierz odpowiednie filtry, na przykład:

     ![Przykładowe filtry kolumn](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtr po stronie serwera**: aby wybrać określony przedział czasu lub ograniczyć liczbę uruchomionych uruchomień, użyj kontrolki zakres w górnej części strony. Domyślnie w danym momencie są wyświetlane tylko 1 000 rekordów.

     ![Zmień przedział czasu](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Aby wyświetlić wszystkie akcje i ich szczegóły dotyczące określonego uruchomienia, wybierz wiersz dla uruchomienia aplikacji logiki.

   Oto przykład pokazujący wszystkie akcje i wyzwalacze dla określonego uruchomienia aplikacji logiki:

   ![Wyświetl akcje dla uruchomienia aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Wysyłanie danych diagnostycznych do usługi Azure Storage i platformy Azure Event Hubs

Wraz z Azure Monitorymi dziennikami możesz dołożyć, jak używać danych diagnostycznych aplikacji logiki z innymi usługami platformy Azure, na przykład:

* [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Przesyłanie strumieniowe dzienników platformy Azure do usługi Azure Event Hubs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Następnie można uzyskać monitorowanie w czasie rzeczywistym, korzystając z danych telemetrycznych i analiz z innych usług, takich jak [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [Power BI](../azure-monitor/visualize/powerbi.md). Na przykład:

* [Przesyłanie strumieniowe danych z Event Hubs do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizowanie danych przesyłanych strumieniowo za pomocą Stream Analytics i tworzenie pulpitu nawigacyjnego analizy w czasie rzeczywistym w programie Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na podstawie lokalizacji, w których chcesz wysyłać dane diagnostyczne, należy najpierw [utworzyć konto usługi Azure Storage](../storage/common/storage-account-create.md) lub [utworzyć centrum zdarzeń platformy Azure](../event-hubs/event-hubs-create.md). Następnie możesz wybrać miejsca docelowe, w których chcesz wysłać te dane. Okresy przechowywania są stosowane tylko w przypadku korzystania z konta magazynu.

![Wysyłanie danych do konta usługi Azure Storage lub centrum zdarzeń](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Monitor zdarzenia diagnostyki

Każde zdarzenie diagnostyczne zawiera szczegółowe informacje o aplikacji logiki oraz o tym zdarzeniu, na przykład o stanie, czasie rozpoczęcia, czasie zakończenia itd. Aby programowo skonfigurować monitorowanie, śledzenie i rejestrowanie, można użyć tych informacji z [interfejsem API REST dla Azure Logic Apps](/rest/api/logic) i [interfejsu api REST dla Azure monitor](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows). Można również użyć `clientTrackingId` `trackedProperties` właściwości i, które pojawiają się w 

* `clientTrackingId`: Jeśli nie zostanie podany, platforma Azure automatycznie generuje ten identyfikator i skorelowanie zdarzeń w ramach przebiegu aplikacji logiki, w tym wszystkich zagnieżdżonych przepływów pracy, które są wywoływane z aplikacji logiki. Można ręcznie określić ten identyfikator w wyzwalaczu, przekazując `x-ms-client-tracking-id` Nagłówek z wartością NIESTANDARDOWEGO identyfikatora w żądaniu wyzwalacza. Można użyć wyzwalacza żądania, wyzwalacza HTTP lub wyzwalacza elementu webhook.

* `trackedProperties`: Aby śledzić dane wejściowe lub wyjściowe w danych diagnostycznych, można dodać `trackedProperties` sekcję do akcji przy użyciu projektanta aplikacji logiki lub bezpośrednio w definicji JSON aplikacji logiki. Śledzone właściwości mogą śledzić tylko dane wejściowe i wyjściowe pojedynczej akcji, ale można użyć `correlation` właściwości zdarzeń do skorelowania między akcjami w przebiegu. Aby śledzić więcej niż jedną właściwość, jedną lub więcej właściwości, Dodaj `trackedProperties` sekcję i właściwości, które mają być używane w definicji akcji.

  Oto przykład, który pokazuje, jak definicja akcji **inicjowania zmiennej** obejmuje śledzone właściwości z danych wejściowych akcji, w których dane wejściowe są tablicami, a nie rekordem.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Ten przykład pokazuje wiele śledzonych właściwości:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Ten przykład pokazuje, jak `ActionCompleted` zdarzenie zawiera `clientTrackingId` atrybuty i `trackedProperties` :

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zapytań monitorowania i śledzenia](../logic-apps/create-monitoring-tracking-queries.md)
* [Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
