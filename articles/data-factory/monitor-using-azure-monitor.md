---
title: Monitorowanie fabryk danych przy użyciu Azure Monitor
description: Dowiedz się, jak używać Azure Monitor do monitorowania potoków Data Factory/Azure przez włączenie dzienników diagnostycznych z informacjami z Data Factory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419463"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Generowanie alertów i monitorowanie fabryk danych przy użyciu Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Aplikacje w chmurze są złożone i mają wiele ruchomych części. Monitory zapewniają dane, aby zapewnić, że aplikacje będą działać w dobrej kondycji. Monitory pomagają również uniknąć potencjalnych problemów i rozwiązywać problemy z poprzednimi.

Dane monitorowania umożliwiają uzyskanie szczegółowych informacji o aplikacjach. Ta wiedza ułatwia zwiększenie wydajności aplikacji i łatwość utrzymania. Pomaga również zautomatyzować akcje, które w przeciwnym razie wymagają ręcznej interwencji.

Azure Monitor udostępnia metryki i dzienniki infrastruktury podstawowej dla większości usług platformy Azure. Dzienniki diagnostyczne platformy Azure są emitowane przez zasób i zapewniają rozbudowane, częste dane dotyczące operacji tego zasobu. I Azure Data Factory zapisuje dzienniki diagnostyczne w monitorze.

Aby uzyskać szczegółowe informacje, zobacz [Azure monitor przegląd](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Przechowywanie danych Azure Data Factory

Data Factory przechowuje dane przebiegu potoku tylko przez 45 dni. Użyj monitora, jeśli chcesz przechowywać dane przez dłuższy czas. Monitor umożliwia kierowanie dzienników diagnostycznych do analizy. Możesz również zachować je na koncie magazynu, aby uzyskać informacje o fabryce dla wybranego czasu trwania.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

* Zapisz dzienniki diagnostyczne na koncie magazynu, aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz użyć ustawień diagnostycznych, aby określić czas przechowywania w dniach.
* Przesyłanie strumieniowe dzienników do Event Hubs platformy Azure. Dzienniki stają się danymi wejściowymi do usługi partnerskiej lub do rozwiązania do analizy niestandardowej, takiego jak Power BI.
* Analizuj dzienniki przy użyciu Log Analytics.

Możesz użyć konta magazynu lub przestrzeni nazw centrum zdarzeń, które nie znajduje się w subskrypcji zasobu, który emituje dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć dostęp do obu subskrypcji przy użyciu odpowiedniej kontroli dostępu opartej na rolach (RBAC).

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

### <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Użyj ustawień diagnostycznych, aby skonfigurować dzienniki diagnostyczne dla zasobów nieobliczeniowych. Ustawienia kontrolki zasobu mają następujące funkcje:

* Określają miejsce wysyłania dzienników diagnostycznych. Przykłady obejmują konto usługi Azure Storage, centrum zdarzeń platformy Azure lub dzienniki monitora.
* Określają, które kategorie dzienników są wysyłane.
* Określają, jak długo każda kategoria dziennika ma być przechowywana na koncie magazynu.
* Przechowywanie przez zero dni oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbą dni od 1 do 2 147 483 647.
* Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone, zasady przechowywania nie mają żadnego efektu. Ten stan może wystąpić na przykład wtedy, gdy wybrano opcję tylko Event Hubs lub monitorowanie dzienników.
* Zasady przechowywania są stosowane dziennie. Granica między dniami występuje o północy czasu uniwersalnego skoordynowanego (UTC). Po upływie dnia dzienniki są usuwane z dni, które wykraczają poza zasady przechowywania. Na przykład jeśli masz zasady przechowywania o jeden dzień, na początku dzisiaj dzienniki przed wczoraj zostaną usunięte.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Włączanie dzienników diagnostycznych za pośrednictwem interfejsu API REST Azure Monitor

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Tworzenie lub aktualizowanie ustawień diagnostycznych w interfejsie API REST monitora

##### <a name="request"></a>Request

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Nagłówki

* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` na identyfikator zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji, zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ustaw `Content-Type` nagłówek na `application/json`.
* Ustaw nagłówek autoryzacji na token sieci Web JSON uzyskany z Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [uwierzytelnianie żądań](../active-directory/develop/authentication-scenarios.md).

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
| **storageAccountId** |String | Identyfikator zasobu konta magazynu, do którego chcesz wysłać dzienniki diagnostyczne. |
| **serviceBusRuleId** |String | Identyfikator reguły magistrali usług dla przestrzeni nazw usługi Service Bus, w której chcesz utworzyć Event Hubs na potrzeby przesyłania strumieniowego dzienników diagnostycznych. Identyfikator reguły ma format `{service bus resource ID}/authorizationrules/{key name}`.|
| **workspaceId** | Typ złożony | Tablica ziaren i ich zasad przechowywania. Wartość tej właściwości jest pusta. |
|**Pomiar**| Wartości parametrów przebiegu potoku do przekazanie do wywołanego potoku| Obiekt JSON, który mapuje nazwy parametrów na wartości argumentów. |
| **Dziennik**| Typ złożony| Nazwa kategorii dziennika diagnostycznego dla typu zasobu. Aby uzyskać listę kategorii dzienników diagnostycznych dla zasobu, wykonaj operację pobierania ustawień diagnostycznych. |
| **kategorii**| String| Tablica kategorii dzienników i ich zasad przechowywania. |
| **timeGrain** | String | Stopień szczegółowości metryk, które są przechwytywane w formacie czasu trwania ISO 8601. Wartość właściwości musi być `PT1M`równa jednej minucie. |
| **dostępny**| Boolean | Określa, czy dla tego zasobu jest włączona kolekcja kategorii metryki lub dziennika. |
| **retentionPolicy**| Typ złożony| Zawiera opis zasad przechowywania dla kategorii Metryka lub dziennik. Ta właściwość jest używana tylko w przypadku kont magazynu. |
|**dni**| int| Liczba dni przechowywania metryk lub dzienników. Jeśli wartość właściwości to 0, dzienniki są przechowywane w nieskończoność. Ta właściwość jest używana tylko w przypadku kont magazynu. |

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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Uzyskaj informacje na temat ustawień diagnostycznych w interfejsie API REST monitora

##### <a name="request"></a>Request

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Nagłówki

* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` na identyfikator zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji, zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ustaw `Content-Type` nagłówek na `application/json`.
* Ustaw nagłówek autoryzacji na token sieci Web JSON uzyskany z usługi Azure AD. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie żądań](../active-directory/develop/authentication-scenarios.md).

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

### <a name="monitor-schema"></a>Monitoruj schemat

#### <a name="activity-run-log-attributes"></a>Działanie — atrybuty dziennika uruchamiania

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
| **Poziom** |String | Poziom dzienników diagnostycznych. W przypadku dzienników uruchomienia działania ustaw wartość właściwości na 4. | `4` |
| **korelacj** |String | Unikatowy identyfikator śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **pierwszym** | String | Godzina zdarzenia w formacie `YYYY-MM-DDTHH:MM:SS.00000Z`czasu UTC. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| Identyfikator uruchomienia działania. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| Identyfikator uruchomienia potoku. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Identyfikator**| String | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorii**| String | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości na `ActivityRuns`. | `ActivityRuns` |
|**poziomie**| String | Poziom dzienników diagnostycznych. Ustaw wartość właściwości na `Informational`. | `Informational` |
|**operationName**| String | Nazwa działania z jego stanem. Jeśli działanie jest pulsem startowym, wartość właściwości to `MyActivity -`. Jeśli działanie jest pulsem końcowym, wartość właściwości to `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**potokname**| String | Nazwa potoku. | `MyPipeline` |
|**activityName**| String | Nazwa działania. | `MyActivity` |
|**Start**| String | Godzina rozpoczęcia działania jest uruchamiana w formacie czasu UTC. | `2017-06-26T20:55:29.5007959Z`|
|**punktów**| String | Czas zakończenia działania jest uruchamiany w formacie czasu UTC. Jeśli dziennik diagnostyczny pokazuje, że działanie zostało uruchomione, ale jeszcze nie zostało zakończone, wartość właściwości `1601-01-01T00:00:00Z`to. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Potoku — atrybuty dziennika uruchamiania

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
| **Poziom** |String | Poziom dzienników diagnostycznych. W przypadku dzienników uruchomienia działania ustaw wartość właściwości na 4. | `4` |
| **korelacj** |String | Unikatowy identyfikator śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **pierwszym** | String | Godzina zdarzenia w formacie `YYYY-MM-DDTHH:MM:SS.00000Z`czasu UTC. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| Identyfikator uruchomienia potoku. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Identyfikator**| String | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorii**| String | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości na `PipelineRuns`. | `PipelineRuns` |
|**poziomie**| String | Poziom dzienników diagnostycznych. Ustaw wartość właściwości na `Informational`. | `Informational` |
|**operationName**| String | Nazwa potoku wraz z jego stanem. Po zakończeniu uruchomienia potoku wartość właściwości jest `Pipeline - Succeeded`równa. | `MyPipeline - Succeeded`. |
|**potokname**| String | Nazwa potoku. | `MyPipeline` |
|**Start**| String | Godzina rozpoczęcia działania jest uruchamiana w formacie czasu UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**punktów**| String | Czas zakończenia działania jest uruchamiany w formacie czasu UTC. Jeśli dziennik diagnostyczny pokazuje działanie, które zostało uruchomione, ale jeszcze nie zostało zakończone, wartość `1601-01-01T00:00:00Z`właściwości to.  | `2017-06-26T20:55:29.5007959Z` |
|**Stany**| String | Końcowy stan uruchomienia potoku. Możliwe wartości właściwości to `Succeeded` i `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Wyzwalacz uruchomienia — atrybuty dziennika

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
| **Poziom** |String | Poziom dzienników diagnostycznych. W przypadku dzienników uruchomienia działania ustaw wartość właściwości na 4. | `4` |
| **korelacj** |String | Unikatowy identyfikator śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **pierwszym** | String | Godzina zdarzenia w formacie `YYYY-MM-DDTHH:MM:SS.00000Z`czasu UTC. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| Identyfikator uruchomienia wyzwalacza. | `08587023010602533858661257311` |
|**Identyfikator**| String | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorii**| String | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości na `PipelineRuns`. | `PipelineRuns` |
|**poziomie**| String | Poziom dzienników diagnostycznych. Ustaw wartość właściwości na `Informational`. | `Informational` |
|**operationName**| String | Nazwa wyzwalacza ze stanem końcowym, który wskazuje, czy wyzwalacz został pomyślnie wywołany. Jeśli puls zakończył się pomyślnie, wartość właściwości `MyTrigger - Succeeded`to. | `MyTrigger - Succeeded` |
|**triggerName**| String | Nazwa wyzwalacza. | `MyTrigger` |
|**triggerType**| String | Typ wyzwalacza. Możliwe wartości właściwości to `Manual Trigger` i `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| String | Zdarzenie wyzwalacza. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Start**| String | Godzina rozpoczęcia wyzwalacza wyzwalana w formacie czasu UTC. | `2017-06-26T20:55:29.5007959Z`|
|**Stany**| String | Końcowy stan pokazujący, czy wyzwalacz został pomyślnie wywołany. Możliwe wartości właściwości to `Succeeded` i `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Schemat Log Analytics

Log Analytics dziedziczy schemat z monitora z następującymi wyjątkami:

* Pierwsze litery w każdej kolumnie są pisane wielkimi literami. Na przykład nazwa kolumny "identyfikator korelacji" w monitorze to "identyfikator korelacji" w Log Analytics.
* Brak kolumny "Level".
* Dynamiczna kolumna "Properties" jest zachowywana jako następujący dynamiczny typ obiektu BLOB JSON.

    | Kolumna Azure Monitor | Kolumna Log Analytics | Typ |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamiczny |
    | $. Properties. Adnotacj | Adnotacje | Dynamiczny |
    | $. Properties. Klawiatur | Dane wejściowe | Dynamiczny |
    | $. Properties. Rozdzielczości | Dane wyjściowe | Dynamiczny |
    | $. Properties. Błąd. errorCode | ErrorCode | int |
    | $. Properties. Błąd. komunikat | ErrorMessage | ciąg |
    | $. Properties. Porn | Error | Dynamiczny |
    | $. Properties. Poprzednikami hostowanymi | Poprzednikami hostowanymi | Dynamiczny |
    | $. Properties. Wejściowe | Parametry | Dynamiczny |
    | $. Properties. SystemParameters | SystemParameters | Dynamiczny |
    | $. Properties. Tabliczk | Tagi | Dynamiczny |
    
## <a name="metrics"></a>Metryki

Dzięki monitorowi możesz uzyskać wgląd w wydajność i kondycję obciążeń platformy Azure. Najważniejszym typem danych monitorowania jest Metryka, która jest również nazywana licznikiem wydajności. Metryki są emitowane przez większość zasobów platformy Azure. Monitor oferuje kilka sposobów konfigurowania i używania tych metryk do monitorowania i rozwiązywania problemów.

Azure Data Factory wersja 2 emituje następujące metryki.

| **Metryka**           | **Nazwa wyświetlana metryki**         | **Jednostka** | **Typ agregacji** | **Opis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Metryki uruchamiania potoków zakończonych powodzeniem | Liczba    | Łącznie                | Całkowita liczba uruchomień potoków zakończonych powodzeniem w oknie minuty. |
| PipelineFailedRuns   | Metryki uruchomionych potoków zakończonych niepowodzeniem    | Liczba    | Łącznie                | Całkowita liczba uruchomień potoków zakończonych niepowodzeniem w oknie minuty.    |
| ActivitySucceededRuns | Metryki uruchamiania działań zakończonych powodzeniem | Liczba    | Łącznie                | Całkowita liczba uruchomień działania zakończonych powodzeniem w oknie minuty.  |
| ActivityFailedRuns   | Metryki uruchamiania działań zakończonych niepowodzeniem    | Liczba    | Łącznie                | Całkowita liczba uruchomień działania, które zakończyły się niepowodzeniem w oknie minuty.     |
| TriggerSucceededRuns | Wyzwalacze uruchomienia wyzwalają metryki  | Liczba    | Łącznie                | Całkowita liczba uruchomień wyzwalacza zakończonych powodzeniem w oknie minuty.   |
| TriggerFailedRuns    | Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem     | Liczba    | Łącznie                | Całkowita liczba uruchomień wyzwalacza zakończonych niepowodzeniem w oknie minuty.      |

Aby uzyskać dostęp do metryk, wykonaj instrukcje podane w temacie [Azure monitor Data Platform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Emitowane są tylko zdarzenia zakończone, wyzwolone działania i uruchomienia potoku. W toku, a uruchomienia piaskownicy/debugowania **nie** są emitowane. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitoruj metryki Data Factory przy użyciu Azure Monitor

Za pomocą programu Data Factory Integration z monitorem można kierować dane do monitorowania. Integracja ta jest przydatna w następujących scenariuszach:

* Chcesz pisać złożone zapytania na rozbudowanym zestawie metryk, który jest publikowany przez Data Factory do monitorowania. Możesz tworzyć niestandardowe alerty dla tych zapytań za pośrednictwem monitora.

* Chcesz monitorować w ramach fabryk danych. Dane można kierować z wielu fabryk danych do jednego obszaru roboczego monitora.

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurowanie ustawień diagnostycznych i obszaru roboczego

Utwórz lub Dodaj ustawienia diagnostyczne dla fabryki danych.

1. W portalu przejdź do pozycji monitorowanie. Wybierz kolejno pozycje **Ustawienia** > **Ustawienia diagnostyczne**.

1. Wybierz fabrykę danych, dla której chcesz ustawić ustawienie diagnostyczne.

1. Jeśli w wybranej fabryce danych nie istnieją żadne ustawienia, zostanie wyświetlony monit o utworzenie ustawienia. Wybierz pozycję **Włącz diagnostykę**.

   ![Utwórz ustawienie diagnostyczne, jeśli nie istnieją żadne ustawienia](media/data-factory-monitor-oms/monitor-oms-image1.png)

   W przypadku istniejących ustawień fabryki danych zostanie wyświetlona lista ustawień, które zostały już skonfigurowane w fabryce danych. Wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   ![Dodaj ustawienie diagnostyczne, jeśli istnieją ustawienia](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia, wybierz pozycję **Wyślij do log Analytics**, a następnie wybierz obszar roboczy z **log Analytics obszarze roboczym**.

    ![Nadaj nazwę ustawieniom i wybierz obszar roboczy usługi log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Wybierz pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tej fabryki danych. Dzienniki diagnostyczne są przesyłane strumieniowo do tego obszaru roboczego, gdy tylko nowe dane zdarzenia zostaną wygenerowane. Gdy zdarzenie jest emitowane i pojawia się w Log Analytics, może upłynąć do 15 minut.

* W trybie _specyficznym dla zasobów_ dzienniki diagnostyczne z Azure Data Factory Flow do tabel _ADFPipelineRun_, _ADFTriggerRun_i _ADFActivityRun_
* W trybie _diagnostyki platformy Azure_ dzienniki diagnostyczne są przesyłane do tabeli _AzureDiagnostics_

> [!NOTE]
> Ponieważ tabela dzienników platformy Azure nie może mieć więcej niż 500 kolumn, zdecydowanie zalecamy wybranie trybu określonego dla zasobu. Aby uzyskać więcej informacji, zobacz [log Analytics znane ograniczenia](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalowanie Azure Data Factory Analytics w witrynie Azure Marketplace

![Przejdź do witryny "Azure Marketplace", wprowadź "filtr analityczny" i wybierz pozycję "Azure Data Factory Analytics (wersja zapoznawcza")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Szczegóły dotyczące "Azure Data Factory Analytics (wersja zapoznawcza)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Wybierz pozycję **Utwórz** , a następnie wybierz pozycję **obszar roboczy pakietu OMS** i **Ustawienia obszaru roboczego OMS**.

![Tworzenie nowego rozwiązania](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitoruj metryki Data Factory

Zainstalowanie Azure Data Factory Analytics powoduje utworzenie domyślnego zestawu widoków, aby włączyć następujące metryki:

- Uruchomienia APD-1) uruchomienia potoków przez Data Factory
 
- Uruchomienia ADF — 2) uruchomienia działań według Data Factory

- Uruchomienia ADF-3) uruchomienia wyzwalacza przez Data Factory

- Błędy ADF — 1) 10 najważniejszych błędów potoku według Data Factory

- Błędy funkcji ADF — 2) pierwsze 10 uruchomienia działań według Data Factory

- Błędy w usłudze ADF — 3) 10 najważniejszych błędów wyzwalaczy według Data Factory

- Statystyka ADF — 1) uruchomienia działań według typu

- Statystyka ADF — 2) uruchomienia wyzwalacza według typu

- Statystyka ADF — 3) maksymalny czas trwania przebiegu potoku

![Okno z wyróżnioną pozycją "skoroszyty (wersja zapoznawcza)" i "AzureDataFactoryAnalytics"](media/data-factory-monitor-oms/monitor-oms-image6.png)

Możesz wizualizować powyższe metryki, przyjrzeć się kwerendom związanym z tymi metrykami, edytować zapytania, tworzyć alerty i podejmować inne akcje.

![Graficzna reprezentacja przebiegów potoku przez fabrykę danych "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (wersja zapoznawcza) wysyła dzienniki diagnostyczne do tabel docelowych _specyficznych dla zasobów_ . Zapytania można pisać dla następujących tabel: _ADFPipelineRun_, _ADFTriggerRun_i _ADFActivityRun_.

## <a name="alerts"></a>Alerty

Zaloguj się do Azure Portal i wybierz pozycję **Monitoruj** > **alerty** , aby utworzyć alerty.

![Alerty w menu portalu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Tworzenie alertów

1. Wybierz pozycję **+ Nowa reguła alertu** , aby utworzyć nowy Alert.

    ![Nowa reguła alertu](media/monitor-using-azure-monitor/alerts_image4.png)

1. Zdefiniuj warunek alertu.

    > [!NOTE]
    > Upewnij się, że wybrano opcję **wszystkie** na liście rozwijanej **Filtruj według typu zasobu** .

    !["Zdefiniuj warunek alertu" > "Wybierz element docelowy", co spowoduje otwarcie okienka "Wybierz zasób" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Zdefiniuj warunek alertu" > "Dodaj kryteria", co spowoduje otwarcie okienka "Konfigurowanie logiki sygnałów"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Okienko "Konfigurowanie typu sygnału"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Zdefiniuj szczegóły alertu.

    ![Szczegóły alertu](media/monitor-using-azure-monitor/alerts_image8.png)

1. Zdefiniuj grupę akcji.

    ![Tworzenie reguły z wyróżnioną pozycją "Nowa grupa akcji"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Utwórz nową grupę akcji](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurowanie poczty e-mail, wiadomości SMS, wypychania i głosu](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Zdefiniuj grupę akcji](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Następne kroki
[Programistyczne monitorowanie potoków i zarządzanie nimi](monitor-programmatically.md)
