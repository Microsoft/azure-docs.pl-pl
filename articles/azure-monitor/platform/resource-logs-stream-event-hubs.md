---
title: Przesyłanie strumieniowe dzienników platformy Azure do centrum zdarzeń
description: Informacje na temat przesyłania strumieniowego dzienników zasobów platformy Azure do centrum zdarzeń w celu wysyłania danych do systemów zewnętrznych, takich jak rozwiązań Siem innych firm i inne rozwiązania do analizy dzienników.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658918"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Przesyłanie strumieniowe dzienników platformy Azure do usługi Azure Event Hubs
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą.  W tym artykule opisano przesyłanie strumieniowe dzienników platformy do centrów zdarzeń w celu wysyłania danych do systemów zewnętrznych, takich jak rozwiązań Siem innych firm i inne rozwiązania do analizy dzienników.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Co możesz zrobić z dziennikami platformy wysyłanymi do centrum zdarzeń
Przesyłaj strumienie dzienników platformy Azure do centrów zdarzeń, aby zapewnić następujące funkcje:

* **Przesyłaj strumieniowo dzienniki do systemów rejestrowania i telemetrii innej firmy** — przesyłanie strumieniowe wszystkich dzienników platformy do jednego centrum zdarzeń w celu przełączenia danych dziennika do narzędzia Siem lub log Analytics innej firmy.
  
* **Utwórz niestandardową platformę telemetrii i rejestrowania** — wysoce skalowalny charakter publikowania/subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwanie dzienników platformy na niestandardową platformę teletry. Aby uzyskać szczegółowe informacje [, zobacz Projektowanie i ustalanie rozmiaru globalnej platformy telemetrii w usłudze Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

* **Wyświetlanie informacji o kondycji usługi przez przesyłanie strumieniowe danych do Power BI** — używanie Event Hubs, Stream Analytics i Power BI do przekształcania danych diagnostycznych na szczegółowe informacje w czasie rzeczywistym w ramach usług platformy Azure. Zobacz [Stream Analytics i Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym na potrzeby przesyłania strumieniowego danych](../../stream-analytics/stream-analytics-power-bi-dashboard.md) w celu uzyskania szczegółowych informacji dotyczących tego rozwiązania.

    Poniższy kod SQL to przykładowe zapytanie Stream Analytics, za pomocą którego można analizować wszystkie dane dzienników w tabeli Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze tego nie zrobiono, należy [utworzyć centrum zdarzeń](../../event-hubs/event-hubs-create.md) . Jeśli masz już ustawienie diagnostyczne używające tej Event Hubs przestrzeni nazw, to centrum zdarzeń zostanie ponownie użyte.

Zasady dostępu współdzielonego dla przestrzeni nazw określają uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do Event Hubs wymaga uprawnień do zarządzania, wysyłania i nasłuchiwania. Zasady dostępu współdzielonego można utworzyć lub zmodyfikować w Azure Portal na karcie Konfiguracja dla Event Hubs przestrzeni nazw.

Aby zaktualizować ustawienie diagnostyczne w celu uwzględnienia przesyłania strumieniowego, musisz mieć uprawnienie ListKey dla tej reguły autoryzacji Event Hubs. Przestrzeń nazw Event Hubs nie musi znajdować się w tej samej subskrypcji co subskrypcja, która emituje dzienniki, pod warunkiem, że użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji i obie subskrypcje są w tej samej dzierżawie usługi AAD.

## <a name="create-a-diagnostic-setting"></a>Utwórz ustawienie diagnostyczne
Wyślij dzienniki platformy do centrum zdarzeń i innych miejsc docelowych, tworząc ustawienia diagnostyczne dla zasobu platformy Azure. Aby uzyskać szczegółowe informacje [, zobacz Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md) .

## <a name="collect-data-from-compute-resources"></a>Zbieranie danych z zasobów obliczeniowych
Ustawienia diagnostyczne będą zbierać dzienniki zasobów dla zasobów obliczeniowych platformy Azure, takich jak każdy inny zasób, ale nie system operacyjny gościa ani ich obciążenia. Aby zebrać te dane, zainstaluj [agenta log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Zużywanie danych dziennika z centrów zdarzeń
Dzienniki platformy z centrów zdarzeń są używane w formacie JSON z elementami w poniższej tabeli.

| Nazwa elementu | Opis |
| --- | --- |
| ewidencj |Tablica wszystkich zdarzeń dziennika w tym ładunku. |
| time |Godzina wystąpienia zdarzenia. |
| category |Kategoria dziennika dla tego zdarzenia. |
| resourceId |Identyfikator zasobu zasobu, który spowodowało wygenerowanie tego zdarzenia. |
| operationName |Nazwa operacji. |
| poziom |Opcjonalny. Wskazuje poziom zdarzenia dziennika. |
| properties |Właściwości zdarzenia. Są one różne dla każdej usługi platformy Azure, zgodnie [ ]()z opisem w temacie. |


Poniżej przedstawiono przykładowe dane wyjściowe z Event Hubs dla dziennika zasobów:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Następne kroki

* [Przeczytaj więcej na temat dzienników zasobów](platform-logs-overview.md).
* [Utwórz ustawienie diagnostyczne, aby zbierać dzienniki i metryki na platformie Azure](diagnostic-settings.md).
* [Przesyłanie strumieniowe dzienników Azure Active Directory z Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Rozpocznij pracę z Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

