---
title: Monitorowanie fabryk danych przy użyciu usługi Azure Monitor
description: Dowiedz się, jak używać usługi Azure Monitor do monitorowania potoków usługi /Azure Data Factory, włączając dzienniki diagnostyczne z informacjami z fabryki danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419463"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Alerty i monitorowanie fabryk danych przy użyciu usługi Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Aplikacje w chmurze są złożone i mają wiele ruchomych części. Monitory dostarczają danych, aby zapewnić, że aplikacje pozostają w stanie dobrej kondycji. Monitory pomagają również uniknąć potencjalnych problemów i rozwiązywać problemy z przeszłościami.

Za pomocą danych monitorowania można uzyskać szczegółowe informacje o aplikacjach. Ta wiedza pomaga poprawić wydajność aplikacji i łatwość konserwacji. Pomaga również zautomatyzować akcje, które w przeciwnym razie wymagają ręcznej interwencji.

Usługa Azure Monitor udostępnia metryki infrastruktury na poziomie podstawowym i dzienniki dla większości usług platformy Azure. Dzienniki diagnostyczne platformy Azure są emitowane przez zasób i zapewniają bogate, częste dane dotyczące działania tego zasobu. Usługa Azure Data Factory zapisuje dzienniki diagnostyczne w monitorze.

Aby uzyskać szczegółowe informacje, zobacz [omówienie usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Przechowywanie danych usługi Azure Data Factory

Data Factory przechowuje dane przebiegu potoku tylko przez 45 dni. Użyj monitora, jeśli chcesz przechowywać te dane przez dłuższy czas. Za pomocą monitora można kierować dzienniki diagnostyczne do analizy. Można również przechowywać je na koncie magazynu, aby mieć informacje fabryczne dla wybranego czasu trwania.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

* Zapisz dzienniki diagnostyczne na koncie magazynu w celu inspekcji lub ręcznej inspekcji. Za pomocą ustawień diagnostycznych można określić czas przechowywania w dniach.
* Przesyłaj strumieniowo dzienniki do usługi Azure Event Hubs. Dzienniki stają się wprowadzane do usługi partnera lub do niestandardowego rozwiązania analitycznego, takiego jak usługa Power BI.
* Analizowanie dzienników za pomocą usługi Log Analytics.

Można użyć konta magazynu lub obszaru nazw centrum zdarzeń, który nie znajduje się w subskrypcji zasobu, który emituje dzienniki. Użytkownik, który konfiguruje to ustawienie, musi mieć odpowiedni dostęp do obu subskrypcji (RBAC) opartej na rolach.

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

### <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Ustawienia diagnostyczne służą do konfigurowania dzienników diagnostycznych dla zasobów niezgodnych z kompeteracją. Ustawienia formantu zasobów mają następujące funkcje:

* Określają one, gdzie dzienniki diagnostyczne są wysyłane. Przykłady obejmują konto magazynu platformy Azure, centrum zdarzeń platformy Azure lub dzienniki monitora.
* Określają one, które kategorie dzienników są wysyłane.
* Określają one, jak długo każda kategoria dziennika powinna być przechowywana na koncie magazynu.
* Przechowywanie przez zero dni oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni od 1 do 2,147,483,647.
* Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone, zasady przechowywania nie mają wpływu. Na przykład ten warunek może się zdarzyć, gdy zaznaczone są tylko opcje usługi Event Hubs lub Monitor dzienników.
* Zasady przechowywania są stosowane dziennie. Granica między dniami występuje o północy skoordynowanego czasu uniwersalnego (UTC). Na koniec dnia dzienniki z dni, które są poza zasadami przechowywania są usuwane. Na przykład jeśli masz zasady przechowywania jednego dnia, na początku dnia dzienniki sprzed wczoraj są usuwane.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Włączanie dzienników diagnostycznych za pomocą interfejsu API REST monitora azure

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Tworzenie lub aktualizowanie ustawienia diagnostyki w interfejsie API monitora REST

##### <a name="request"></a>Żądanie

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Nagłówki

* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` identyfikator zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pomocą grup zasobów.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Ustaw `Content-Type` nagłówek `application/json`na .
* Ustaw nagłówek autoryzacji na token internetowy JSON, który został łączona z usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie żądań](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Treść

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| **dyskKontokonto** |Ciąg | Identyfikator zasobu konta magazynu, do którego chcesz wysłać dzienniki diagnostyczne. |
| **usługaBusRuleId** |Ciąg | Identyfikator reguły magistrali usługowej obszaru nazw magistrali usług, w którym mają być utworzone centra zdarzeń dla dzienników diagnostycznych przesyłania strumieniowego. Identyfikator reguły ma `{service bus resource ID}/authorizationrules/{key name}`format .|
| **workspaceId** | Typ złożony | Tablica ziaren czasu metryki i ich zasad przechowywania. Wartość tej właściwości jest pusta. |
|**Metryki**| Wartości parametrów przebiegu potoku, który ma zostać przekazany do wywoływanego potoku| Obiekt JSON, który mapuje nazwy parametrów do wartości argumentów. |
| **Dzienniki**| Typ złożony| Nazwa kategorii dziennika diagnostycznego dla typu zasobu. Aby uzyskać listę kategorii dziennika diagnostycznego dla zasobu, wykonaj operację get diagnostyki ustawień. |
| **Kategorii**| Ciąg| Tablica kategorii dziennika i ich zasad przechowywania. |
| **timeGrain** | Ciąg | Ziarnistość metryk, które są przechwytywane w formacie czasu trwania ISO 8601. Wartość właściwości musi `PT1M`być , która określa jedną minutę. |
| **Włączone**| Wartość logiczna | Określa, czy dla tego zasobu jest włączona kolekcja kategorii metryki lub dziennika. |
| **retencjaPolityka**| Typ złożony| W tym artykule opisano zasady przechowywania dla kategorii metryki lub dziennika. Ta właściwość jest używana tylko dla kont magazynu. |
|**Dni**| int| Liczba dni do zachowania metryk lub dzienników. Jeśli wartość właściwości wynosi 0, dzienniki są przechowywane na zawsze. Ta właściwość jest używana tylko dla kont magazynu. |

##### <a name="response"></a>Odpowiedź

200 OK.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Uzyskaj informacje o ustawieniach diagnostyki w interfejsie API Monitor REST

##### <a name="request"></a>Żądanie

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Nagłówki

* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` identyfikator zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pomocą grup zasobów.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Ustaw `Content-Type` nagłówek `application/json`na .
* Ustaw nagłówek autoryzacji na token internetowy JSON, który został do odebrany z usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie żądań](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Odpowiedź

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
Aby uzyskać więcej informacji, zobacz [Ustawienia diagnostyczne](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schemat dzienników i zdarzeń

### <a name="monitor-schema"></a>Schemat monitora

#### <a name="activity-run-log-attributes"></a>Atrybuty dziennika przebiegu działania

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| **Poziom** |Ciąg | Poziom dzienników diagnostycznych. W przypadku dzienników przebiegu działania ustaw wartość właściwości na 4. | `4` |
| **Correlationid** |Ciąg | Unikatowy identyfikator do śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Czas** | Ciąg | Czas zdarzenia w formacie `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Ciąg| Identyfikator uruchomienia działania. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Ciąg| Identyfikator potoku uruchomić. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| Ciąg | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategorii**| Ciąg | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości `ActivityRuns`na . | `ActivityRuns` |
|**Poziom**| Ciąg | Poziom dzienników diagnostycznych. Ustaw wartość właściwości `Informational`na . | `Informational` |
|**Operationname**| Ciąg | Nazwa działania z jego stanem. Jeśli działanie jest pulsem początkowym, `MyActivity -`wartością właściwości jest . Jeśli działanie jest końcowym pulsem, wartością właściwości jest `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**nazwa potoku**| Ciąg | Nazwa potoku. | `MyPipeline` |
|**activityName**| Ciąg | Nazwa działania. | `MyActivity` |
|**Uruchomić**| Ciąg | Godzina rozpoczęcia działania jest uruchamiana w formacie TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`|
|**Końcu**| Ciąg | Godzina zakończenia działania jest uruchamiana w formacie TIMEPAN UTC. Jeśli dziennik diagnostyczny pokazuje, że działanie zostało rozpoczęte, `1601-01-01T00:00:00Z`ale jeszcze nie zakończone, wartość właściwości jest . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Atrybuty dziennika przebiegu potoku

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| **Poziom** |Ciąg | Poziom dzienników diagnostycznych. W przypadku dzienników przebiegu działania ustaw wartość właściwości na 4. | `4` |
| **Correlationid** |Ciąg | Unikatowy identyfikator do śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Czas** | Ciąg | Czas zdarzenia w formacie `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**runId (ida)**| Ciąg| Identyfikator potoku uruchomić. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| Ciąg | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategorii**| Ciąg | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości `PipelineRuns`na . | `PipelineRuns` |
|**Poziom**| Ciąg | Poziom dzienników diagnostycznych. Ustaw wartość właściwości `Informational`na . | `Informational` |
|**Operationname**| Ciąg | Nazwa potoku wraz z jego stanem. Po zakończeniu uruchomienia potoku wartość `Pipeline - Succeeded`właściwości jest . | `MyPipeline - Succeeded`. |
|**nazwa potoku**| Ciąg | Nazwa potoku. | `MyPipeline` |
|**Uruchomić**| Ciąg | Godzina rozpoczęcia działania jest uruchamiana w formacie TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**Końcu**| Ciąg | Godzina zakończenia działania jest uruchamiana w formacie TIMEPAN UTC. Jeśli dziennik diagnostyczny pokazuje, że działanie zostało rozpoczęte, ale jeszcze nie zakończone, wartość właściwości jest `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**Stan**| Ciąg | Ostateczny stan uruchomienia potoku. Możliwe wartości `Succeeded` właściwości `Failed`są i . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Atrybuty dziennika uruchamiane przez wyzwalacze

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| **Poziom** |Ciąg | Poziom dzienników diagnostycznych. W przypadku dzienników przebiegu działania ustaw wartość właściwości na 4. | `4` |
| **Correlationid** |Ciąg | Unikatowy identyfikator do śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Czas** | Ciąg | Czas zdarzenia w formacie `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Ciąg| Identyfikator uruchomienia wyzwalacza. | `08587023010602533858661257311` |
|**Resourceid**| Ciąg | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategorii**| Ciąg | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości `PipelineRuns`na . | `PipelineRuns` |
|**Poziom**| Ciąg | Poziom dzienników diagnostycznych. Ustaw wartość właściwości `Informational`na . | `Informational` |
|**Operationname**| Ciąg | Nazwa wyzwalacza z jego stan końcowy, który wskazuje, czy wyzwalacz pomyślnie uruchomiony. Jeśli bicie serca zakończyło się `MyTrigger - Succeeded`pomyślnie, wartością właściwości jest . | `MyTrigger - Succeeded` |
|**Triggername**| Ciąg | Nazwa wyzwalacza. | `MyTrigger` |
|**triggerType**| Ciąg | Typ wyzwalacza. Możliwe wartości `Manual Trigger` właściwości `Schedule Trigger`są i . | `ScheduleTrigger` |
|**triggerEvent (triggerEvent)**| Ciąg | Zdarzenie wyzwalacza. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Uruchomić**| Ciąg | Czas rozpoczęcia wyzwalacza wypalania w formacie TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`|
|**Stan**| Ciąg | Ostateczny stan pokazujący, czy wyzwalacz został pomyślnie uruchomiony. Możliwe wartości `Succeeded` właściwości `Failed`są i . | `Succeeded`|

### <a name="log-analytics-schema"></a>Schemat analizy dzienników

Usługa Log Analytics dziedziczy schemat z monitora z następującymi wyjątkami:

* Pierwsza litera w każdej nazwie kolumny jest pisana na wielkie litery. Na przykład nazwa kolumny "correlationId" w monitorze jest "CorrelationId" w usłudze Log Analytics.
* Nie ma kolumny "Poziom".
* Dynamiczna kolumna "właściwości" jest zachowywana jako następujący dynamiczny typ obiektu blob JSON.

    | Kolumna Monitor platformy Azure | Kolumna Usługa Log Analytics | Typ |
    | --- | --- | --- |
    | $.properties. Właściwości użytkowników | Właściwości użytkowników | Dynamiczny |
    | $.properties. Adnotacje | Adnotacje | Dynamiczny |
    | $.properties. Wejście | Dane wejściowe | Dynamiczny |
    | $.properties. Wyjście | Dane wyjściowe | Dynamiczny |
    | $.properties. Kod error.errorCode | ErrorCode | int |
    | $.properties. Komunikat error.message | Errormessage | ciąg |
    | $.properties. Błąd | Błąd | Dynamiczny |
    | $.properties. Poprzedników | Poprzedników | Dynamiczny |
    | $.properties. Parametry | Parametry | Dynamiczny |
    | $.properties. SystemParametry | SystemParametry | Dynamiczny |
    | $.properties. Tagi | Tagi | Dynamiczny |
    
## <a name="metrics"></a>Metryki

Za pomocą monitora można uzyskać wgląd w wydajność i kondycję obciążeń platformy Azure. Najważniejszym typem monitora danych jest metryka, która jest również nazywana licznikiem wydajności. Metryki są emitowane przez większość zasobów platformy Azure. Monitor udostępnia kilka sposobów konfigurowania i korzystania z tych metryk do monitorowania i rozwiązywania problemów.

Usługa Azure Data Factory w wersji 2 emituje następujące metryki.

| **Metryka**           | **Metryczna nazwa wyświetlana**         | **Jednostki** | **Typ agregacji** | **Opis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceedRuns | Powiodło się, że potok uruchamia metryki | Liczba    | Łącznie                | Całkowita liczba uruchomień potoku, które powiodły się w ciągu minuty. |
| PipelineFailedRuns (Nieupowłożone runy potoku)   | Nieudane przebiegi potoku    | Liczba    | Łącznie                | Całkowita liczba uruchomień potoku, które nie powiodło się w ciągu minuty okna.    |
| DziałalnośćSucceedRuns | Działanie, które powiodło się, uruchamia metryki | Liczba    | Łącznie                | Całkowita liczba uruchomień działania, które powiodły się w ciągu minuty.  |
| DziałalnośćFailedRuns   | Działanie nie powiodło się uruchamia metryki    | Liczba    | Łącznie                | Całkowita liczba uruchomień działania, które nie powiodło się w ciągu minuty okna.     |
| WyzwalaczeSucceedRuns | Pomyślne uruchomienie wyzwalacza powoduje  | Liczba    | Łącznie                | Całkowita liczba uruchomień wyzwalacza, które powiodły się w ciągu minuty.   |
| TriggerFailedRuns (Nieugiętyrun)    | Nieudany wyzwalacz uruchamia metryki     | Liczba    | Łącznie                | Całkowita liczba uruchomień wyzwalacza, które nie powiodły się w ciągu minuty.      |

Aby uzyskać dostęp do metryk, wykonaj instrukcje na [platformie danych Usługi Azure Monitor.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)

> [!NOTE]
> Emitowane są tylko ukończone, wyzwalane działania i zdarzenia przebiegów potoku. W toku i przebiegi piaskownicy/debugowania **nie** są emitowane. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorowanie metryk fabryki danych za pomocą usługi Azure Monitor

Za pomocą integracji usługi Data Factory z monitorem można kierować dane do monitora. Ta integracja jest przydatna w następujących scenariuszach:

* Chcesz napisać złożone kwerendy na bogatym zestawie metryk, który jest publikowany przez fabrykę danych do monitorowania. Można tworzyć niestandardowe alerty dla tych zapytań za pośrednictwem monitora.

* Chcesz monitorować w fabrykach danych. Dane można rozkieszywać z wielu fabryk danych do jednego obszaru roboczego monitora.

Aby zapoznać się z siedmiominutowym wprowadzeniem i prezentacją tej funkcji, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurowanie ustawień diagnostycznych i obszaru roboczego

Tworzenie lub dodawanie ustawień diagnostycznych dla fabryki danych.

1. W portalu przejdź do monitora. Wybierz**ustawienia diagnostyki** **ustawień** > .

1. Wybierz fabrykę danych, dla której chcesz ustawić ustawienie diagnostyczne.

1. Jeśli w wybranej fabryce danych nie istnieją żadne ustawienia, zostanie wyświetlony monit o utworzenie ustawienia. Wybierz **włącz diagnostykę**.

   ![Tworzenie ustawienia diagnostycznego, jeśli nie istnieją żadne ustawienia](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Jeśli w fabryce danych istnieją ustawienia, zostanie wyświetlona lista ustawień już skonfigurowanych w fabryce danych. Wybierz **pozycję Dodaj ustawienie diagnostyczne**.

   ![Dodawanie ustawienia diagnostycznego, jeśli istnieją ustawienia](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Nadaj ustawienie nazwy, wybierz **pozycję Wyślij do usługi Log Analytics**, a następnie wybierz obszar roboczy z obszaru **roboczego usługi Log Analytics**.

    ![Nadawanie nazw ustawieniom i wybieranie obszaru roboczego analizy dzienników](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Wybierz pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie pojawi się na liście ustawień tej fabryki danych. Dzienniki diagnostyczne są przesyłane strumieniowo do tego obszaru roboczego, gdy tylko zostaną wygenerowane nowe dane zdarzeń. Może ugięć się do 15 minut między emitowaniem zdarzenia a jego wyświetleniem w usłudze Log Analytics.

* W trybie _specyficznym dla zasobu_ dzienniki diagnostyczne z usługi Azure Data Factory przepływają do tabel _ADFPipelineRun,_ _ADFTriggerRun_i _ADFActivityRun_
* W trybie _diagnostyki platformy Azure_ dzienniki diagnostyczne są przesyłane do tabeli _AzureDiagnostics_

> [!NOTE]
> Ponieważ tabela dziennika platformy Azure nie może mieć więcej niż 500 kolumn, zdecydowanie zalecamy wybranie trybu specyficznego dla zasobów. Aby uzyskać więcej informacji, zobacz [Znane ograniczenia analizy dzienników](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalowanie usługi Azure Data Factory Analytics z witryny Azure Marketplace

![Przejdź do "Azure Marketplace", wprowadź "Filtr analizy" i wybierz "Usługa Azure Data Factory Analytics (wersja zapoznawcza")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Szczegółowe informacje o "Usłudze Azure Data Factory Analytics (Preview)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Wybierz **pozycję Utwórz,** a następnie wybierz pozycję **Ustawienia obszaru roboczego systemu OMS** i obszaru **roboczego systemu OMS**.

![Tworzenie nowego rozwiązania](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorowanie danych fabrycznych

Zainstalowanie usługi Azure Data Factory Analytics tworzy domyślny zestaw widoków, dzięki czemu następujące metryki stają się włączone:

- Przebiegi podajnika ADF — 1) przebiegi potoku według fabryki danych
 
- Przebiegi ADF- 2) Działanie uruchamiane przez fabrykę danych

- Przebiegi podajnika ADF — 3) Wyzwalacze uruchamiane przez fabrykę danych

- Błędy podajnika ADF - 1) 10 najważniejszych błędów potoku według fabryki danych

- Błędy podajnika ADF - 2) 10 najważniejszych przebiegów działań według fabryki danych

- Błędy ADF - 3) 10 najważniejszych błędów wyzwalaczy według fabryki danych

- Statystyki ADF - 1) Działanie przebiega według typu

- Statystyki podajnika ADF — 2) Wyzwalacz uruchamia według typu

- Statystyki ADF - 3) Maksymalny czas trwania przebiegów potoku

![Okno z wyróżnionymi "Skoroszytami (wersja zapoznawcza)" i "AzureDataFactoryAnalytics"](media/data-factory-monitor-oms/monitor-oms-image6.png)

Możesz wizualizować poprzednie metryki, przeglądać zapytania za tymi metrykami, edytować zapytania, tworzyć alerty i podejmować inne akcje.

![Graficzna reprezentacja przebiegów rurociągu przez fabrykę danych"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Usługa Azure Data Factory Analytics (Preview) wysyła dzienniki diagnostyczne do tabel docelowych _specyficznych dla zasobów._ Kwerendy można zapisywać w następujących tabelach: _ADFPipelineRun_, _ADFTriggerRun_i _ADFActivityRun_.

## <a name="alerts"></a>Alerty

Zaloguj się do witryny Azure portal i wybierz **alerty** > **monitora,** aby utworzyć alerty.

![Alerty w menu portalu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Tworzenie alertów

1. Wybierz **+ Nowa reguła alertu,** aby utworzyć nowy alert.

    ![Nowa reguła alertu](media/monitor-using-azure-monitor/alerts_image4.png)

1. Zdefiniuj warunek alertu.

    > [!NOTE]
    > Upewnij się, że na liście rozwijanej **Filtruj według typu zasobu** wybierz pozycję **Wszystko.**

    !["Definiuj warunek alertu" > "Wybierz obiekt docelowy", które otwiera okienko "Wybierz zasób" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definiuj warunek alertu" >" Dodaj kryteria", które otwiera okienko "Konfigurowanie logiki sygnału"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Okienko "Konfigurowanie typu sygnału"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Zdefiniuj szczegóły alertu.

    ![Szczegóły alertu](media/monitor-using-azure-monitor/alerts_image8.png)

1. Zdefiniuj grupę akcji.

    ![Tworzenie reguły z wyróżnioną wyróżnioną "Nową grupą akcji"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Tworzenie nowej grupy akcji](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurowanie poczty e-mail, wiadomości SMS, wypychania i głosu](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definiowanie grupy akcji](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Następne kroki
[Programowo monitoruj potoki i zarządzaj nimi](monitor-programmatically.md)
