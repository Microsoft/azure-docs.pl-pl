---
title: Funkcje trwałe publikowania w usłudze Azure Event Grid (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować automatyczne publikowanie w usłudze Azure Event Grid dla funkcji trwałych.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535489"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Funkcje trwałe publikowania w usłudze Azure Event Grid (wersja zapoznawcza)

W tym artykule pokazano, jak skonfigurować funkcje trwałe do publikowania zdarzeń cyklu życia aranżacji (takich jak utworzone, ukończone i nie powiodło się) w niestandardowym [temacie siatki zdarzeń platformy Azure.](https://docs.microsoft.com/azure/event-grid/overview)

Oto kilka scenariuszy, w których ta funkcja jest przydatna:

* **Scenariusze DevOps, takie jak wdrożenia niebieskie/zielone:** Przed wdrożeniem [strategii wdrażania side-by-side](durable-functions-versioning.md#side-by-side-deployments)warto wiedzieć, czy są uruchomione jakieś zadania.

* **Zaawansowana obsługa monitorowania i diagnostyki:** Można śledzić informacje o stanie aranżacji w magazynie zewnętrznym zoptymalizowanym pod kątem zapytań, takich jak usługa Azure SQL Database lub usługa Azure Cosmos DB.

* **Długotrwała aktywność w tle:** Jeśli używasz funkcji trwałych do długotrwałego działania w tle, ta funkcja pomaga poznać bieżący stan.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) w projekcie funkcji trwałych.
* Zainstaluj [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md) (tylko system Windows) lub użyj istniejącego konta usługi Azure Storage.
* Instalowanie [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) lub korzystanie z usługi Azure Cloud [Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Tworzenie niestandardowego tematu siatki zdarzeń

Utwórz temat siatki zdarzeń do wysyłania zdarzeń z funkcji trwałych. Poniższe instrukcje pokazują, jak utworzyć temat przy użyciu interfejsu wiersza polecenia platformy Azure. Można to również zrobić [za pomocą programu PowerShell](../../event-grid/custom-event-quickstart-powershell.md) lub [przy użyciu witryny Azure Portal.](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia `az group create`. Obecnie usługa Azure Event Grid nie obsługuje wszystkich regionów. Aby uzyskać informacje o obsługiwanych regionach, zobacz [omówienie usługi Azure Event Grid](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat Siatka zdarzeń zawiera zdefiniowany przez użytkownika punkt końcowy, w którego można zaksięgować zdarzenie. Zamień `<topic_name>` na unikatową nazwę tematu. Nazwa tematu musi być unikatowa, ponieważ staje się wpisem DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Pobierz punkt końcowy i klucz

Pobierz punkt końcowy tematu. Zamień `<topic_name>` na wybraną nazwę.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Pobierz klucz tematu. Zamień `<topic_name>` na wybraną nazwę.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teraz możesz wysyłać wydarzenia do tematu.

## <a name="configure-event-grid-publishing"></a>Konfigurowanie publikowania w siatce zdarzeń

W projekcie funkcje trwałe `host.json` znajdź plik.

### <a name="durable-functions-1x"></a>Funkcje trwałe 1.x

Dodaj `eventGridTopicEndpoint` `eventGridKeySettingName` i `durableTask` we właściwości.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Funkcje trwałe 2.x

Dodaj `notifications` sekcję `durableTask` do właściwości pliku, `<topic_name>` zastępując wybraną nazwą. Jeśli `durableTask` właściwości `extensions` lub nie istnieją, utwórz je w tym przykładzie:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

Możliwe właściwości konfiguracji usługi Azure Event Grid można znaleźć w [dokumentacji host.json](../functions-host-json.md#durabletask). Po skonfigurowaniu `host.json` pliku aplikacja funkcji wysyła zdarzenia cyklu życia do tematu Siatka zdarzeń. Działa to po uruchomieniu aplikacji funkcji zarówno lokalnie, jak i na platformie Azure.

Ustaw ustawienie aplikacji dla klucza tematu `local.settings.json`w aplikacji funkcji i . Następujące JSON jest próbką `local.settings.json` do debugowania lokalnego. Zamień `<topic_key>` na klawisz tematu.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Jeśli używasz [emulatora magazynu](../../storage/common/storage-use-emulator.md) (tylko dla systemu Windows), upewnij się, że działa. Dobrym pomysłem jest uruchomienie `AzureStorageEmulator.exe clear all` polecenia przed wykonaniem.

Jeśli używasz istniejącego konta usługi `UseDevelopmentStorage=true` `local.settings.json` Azure Storage, zastąp jego parametry połączenia.

## <a name="create-functions-that-listen-for-events"></a>Tworzenie funkcji nasłuchiuj zdarzeń

Korzystając z witryny Azure portal, utwórz inną aplikację funkcji, aby nasłuchiwać zdarzeń opublikowanych przez aplikację Funkcje trwałe. Najlepiej jest zlokalizować go w tym samym regionie, co temat siatka zdarzeń.

### <a name="create-an-event-grid-trigger-function"></a>Tworzenie funkcji wyzwalania siatki zdarzeń

Utwórz funkcję, aby odbierać zdarzenia cyklu życia. Wybierz **pozycję Funkcja niestandardowa**.

![Wybierz pozycję Utwórz funkcję niestandardową.](./media/durable-functions-event-publishing/functions-portal.png)

Wybierz pozycję Wyzwalacz siatki zdarzeń i wybierz język.

![Wybierz wyzwalacz siatki zdarzeń.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Wprowadź nazwę funkcji, a następnie `Create`wybierz opcję .

![Utwórz wyzwalacz siatki zdarzeń.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Tworzona jest funkcja z następującym kodem:

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Wybierz pozycję `Add Event Grid Subscription`. Ta operacja dodaje subskrypcję usługi Event Grid dla utworzonego tematu siatki zdarzeń. Aby uzyskać więcej informacji, zobacz [Pojęcia w usłudze Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Wybierz łącze Wyzwalacz siatki zdarzeń.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Wybierz `Event Grid Topics` dla **typu tematu**. Wybierz grupę zasobów utworzoną dla tematu Siatka zdarzeń. Następnie wybierz wystąpienie tematu Siatka zdarzeń. Naciśnij `Create`przycisk .

![Tworzy subskrypcję usługi Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Teraz możesz przystąpić do odbierania zdarzeń cyklu życia.

## <a name="run-durable-functions-app-to-send-the-events"></a>Uruchom aplikację Trwałe funkcje, aby wysłać zdarzenia

W projekcie funkcje trwałe, który został skonfigurowany wcześniej, rozpocząć debugowanie na komputerze lokalnym i rozpocząć aranżację. Aplikacja publikuje zdarzenia cyklu życia funkcji trwałych do siatki zdarzeń. Sprawdź, czy usługa Event Grid wyzwala utworzoną funkcję odbiornika, sprawdzając jej dzienniki w witrynie Azure portal.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schemat zdarzeń

Na poniższej liście opisano schemat zdarzeń cyklu życia:

* **`id`**: Unikatowy identyfikator zdarzenia w siatce zdarzeń.
* **`subject`**: Ścieżka do tematu zdarzenia. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`będzie `Running`, `Completed` `Failed`, `Terminated`i .  
* **`data`**: Trwałe funkcje Specyficzne parametry.
  * **`hubName`**: Nazwa [TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: Nazwa funkcji koordynatora.
  * **`instanceId`**: Trwałe funkcje instanceId.
  * **`reason`**: Dodatkowe dane skojarzone ze zdarzeniem śledzenia. Aby uzyskać więcej informacji, zobacz [Diagnostyka w trwałych funkcjach (usługi Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Stan środowiska uruchomieniowego aranżacji. Uruchamianie, ukończone, nie powiodło się, anulowane.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Czas zdarzenia (UTC).
* **`dataVersion`**: Wersja schematu zdarzenia cyklu życia.
* **`metadataVersion`**: Wersja metadanych.
* **`topic`**: Zasób tematu siatki zdarzeń.

## <a name="how-to-test-locally"></a>Jak przetestować lokalnie

Aby przetestować lokalnie, przeczytaj [artykuł Azure Function Event Grid Trigger Local Debugging](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zarządzanie wystąpieniami w funkcji trwałych](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak wersjonować w funkcji trwałe](durable-functions-versioning.md)
