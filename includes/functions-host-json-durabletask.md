---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 24dc2cad8d299d150adddc03de5e9006fc831fc6
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061640"
---
Ustawienia konfiguracji dla [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

> [!NOTE]
> Wszystkie główne wersje Durable Functions są obsługiwane we wszystkich wersjach środowiska uruchomieniowego Azure Functions. Jednak schemat host.jsna konfiguracji jest nieco inny w zależności od wersji środowiska uruchomieniowego Azure Functions i używanej wersji rozszerzenia Durable Functions. Poniższe przykłady służą do korzystania z Azure Functions 2,0 i 3,0. W obu przykładach, jeśli używasz Azure Functions 1,0, dostępne ustawienia są takie same, ale w sekcji "durableTask" host.json powinien przejść do katalogu głównego host.jsw konfiguracji, a nie jako pola w obszarze "rozszerzenia".

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2. x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "controlQueueBatchSize": 32,
      "partitionCount": 4,
      "controlQueueVisibilityTimeout": "00:05:00",
      "workItemQueueVisibilityTimeout": "00:05:00",
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10,
      "maxQueuePollingInterval": "00:00:30",
      "azureStorageConnectionStringName": "AzureWebJobsStorage",
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "traceInputsAndOutputs": false,
      "logReplayEvents": false,
      "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
      "eventGridKeySettingName":  "EventGridKey",
      "eventGridPublishRetryCount": 3,
      "eventGridPublishRetryInterval": "00:00:30",
      "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
    }
  }
}
```

Nazwy centrów zadań muszą zaczynać się literą i składać się tylko z liter i cyfr. Jeśli nie zostanie określony, domyślną nazwą centrum zadań dla aplikacji funkcji jest **DurableFunctionsHub** . Aby uzyskać więcej informacji, zobacz [centra zadań](../articles/azure-functions/durable-functions-task-hubs.md).

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternatywne nazwy [centrów zadań](../articles/azure-functions/durable-functions-task-hubs.md) mogą służyć do izolowania wielu Durable Functions aplikacji od siebie, nawet jeśli używają tego samego zaplecza magazynu.|
|controlQueueBatchSize|32|Liczba komunikatów do ściągnięcia z kolejki kontroli w danym momencie.|
|controlQueueBufferThreshold|256|Liczba komunikatów w kolejce sterującej, które mogą być buforowane w pamięci w danym momencie, gdy Dyspozytor będzie oczekiwać przed dekolejkowanie wszelkich dodatkowych komunikatów.|
|partitionCount |4|Liczba partycji dla kolejki sterującej. Może to być dodatnia liczba całkowita z zakresu od 1 do 16.|
|controlQueueVisibilityTimeout |5 minut|Przekroczenie limitu czasu widoczności komunikatów w kolejce sterującej.|
|workItemQueueVisibilityTimeout |5 minut|Przekroczenie limitu czasu widoczności komunikatów w kolejce elementów roboczych.|
|maxConcurrentActivityFunctions |10X liczbę procesorów na bieżącym komputerze|Maksymalna liczba funkcji działania, które mogą być przetwarzane współbieżnie na jednym wystąpieniu hosta.|
|maxConcurrentOrchestratorFunctions |10X liczbę procesorów na bieżącym komputerze|Maksymalna liczba funkcji programu Orchestrator, które mogą być przetwarzane współbieżnie na jednym wystąpieniu hosta.|
|maxQueuePollingInterval|30 sekund|Maksymalny interwał sondowania kolejki elementów roboczych i kontrolki w formacie *gg: mm: SS* . Wyższe wartości mogą skutkować większymi opóźnieniami przetwarzania komunikatów. Niższe wartości mogą spowodować wyższe koszty magazynowania z powodu zwiększonych transakcji magazynu.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nazwa ustawienia aplikacji, które zawiera parametry połączenia usługi Azure Storage używane do zarządzania bazowymi zasobami usługi Azure Storage.|
|trackingStoreConnectionStringName||Nazwa parametrów połączenia do użycia w tabelach historia i wystąpienia. Jeśli nie zostanie określony, `connectionStringName` używane jest połączenie (trwałe 2. x) lub `azureStorageConnectionStringName` (trwałe 1. x).|
|trackingStoreNamePrefix||Prefiks, który ma być używany dla tabel historii i wystąpień, gdy `trackingStoreConnectionStringName` jest określony. Jeśli nie zostanie ustawiona, domyślną wartością prefiksu będzie `DurableTask` . Jeśli `trackingStoreConnectionStringName` nie zostanie określony, tabele historia i wystąpienia będą używać `hubName` wartości jako ich prefiksu, a wszystkie ustawienia dla `trackingStoreNamePrefix` zostaną zignorowane.|
|traceInputsAndOutputs |fałsz|Wartość wskazująca, czy należy śledzić dane wejściowe i wyjściowe wywołań funkcji. Zachowanie domyślne podczas śledzenia zdarzeń wykonania funkcji polega na uwzględnieniu liczby bajtów w serializowanych danych wejściowych i wyjściowych dla wywołań funkcji. Takie zachowanie zapewnia minimalną ilość informacji o tym, co się stało z danymi wejściowymi i wyjściowymi, bez przeładowania dzienników lub przypadkowo ujawnia informacje poufne. Ustawienie tej właściwości na wartość true powoduje, że funkcja domyślna rejestrowania w usłudze rejestruje całą zawartość danych wejściowych i wyjściowych funkcji.|
|logReplayEvents|fałsz|Wartość wskazująca, czy należy zapisywać zdarzenia powtarzania aranżacji do Application Insights.|
|eventGridTopicEndpoint ||Adres URL Azure Event Grid niestandardowego punktu końcowego tematu. Po ustawieniu tej właściwości zdarzenia powiadomień cyklu życia aranżacji są publikowane w tym punkcie końcowym. Ta właściwość obsługuje rozwiązanie ustawień aplikacji.|
|eventGridKeySettingName ||Nazwa ustawienia aplikacji zawierającego klucz używany do uwierzytelniania za pomocą Azure Event Grid niestandardowego tematu pod adresem `EventGridTopicEndpoint` .|
|eventGridPublishRetryCount|0|Liczba ponownych prób w przypadku, gdy publikowanie w temacie Event Grid nie powiedzie się.|
|eventGridPublishRetryInterval|5 minut|Event Grid publikuje interwał ponawiania prób w formacie *gg: mm: SS* .|
|eventGridPublishEventTypes||Lista typów zdarzeń do opublikowania w Event Grid. Jeśli nie zostanie określony, zostaną opublikowane wszystkie typy zdarzeń. Dozwolone wartości to `Started` , `Completed` , `Failed` , `Terminated` .|
|useAppLease|true|Po ustawieniu na `true` aplikacje będą wymagały uzyskania dzierżawy obiektu BLOB na poziomie aplikacji przed przetworzeniem komunikatów centrum zadań. Więcej informacji można znaleźć w dokumentacji dotyczącej [odzyskiwania po awarii i dystrybucji geograficznej](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md) . Dostępne od 2.3.0.
|useLegacyPartitionManagement|true|Po ustawieniu na `false` program używa algorytmu zarządzania partycjami, który zmniejsza możliwość duplikowania wykonywania funkcji podczas skalowania w górę.  Dostępne od 2.3.0. Wartość domyślna zostanie zmieniona na `false` w przyszłej wersji.|
|useGracefulShutdown|fałsz|Przeglądania Włącz bezpieczne zamykanie, aby zmniejszyć prawdopodobieństwo niepowodzenia zamykania funkcji w procesie.|

Wiele z tych ustawień umożliwia optymalizację wydajności. Aby uzyskać więcej informacji, zobacz [wydajność i skalowanie](../articles/azure-functions/durable-functions-perf-and-scale.md).
