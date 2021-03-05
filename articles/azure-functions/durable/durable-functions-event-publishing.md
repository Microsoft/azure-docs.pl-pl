---
title: Durable Functions publikowania do Azure Event Grid (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować automatyczne publikowanie Azure Event Grid dla Durable Functions.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175961"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikowania do Azure Event Grid (wersja zapoznawcza)

W tym artykule przedstawiono sposób konfigurowania Durable Functions publikowania zdarzeń cyklu życia aranżacji (takich jak utworzone, ukończone i niepowodzenie) w temacie niestandardowym [Azure Event Grid](../../event-grid/overview.md).

Poniżej przedstawiono kilka scenariuszy, w których ta funkcja jest przydatna:

* **DevOps scenariusze, takie jak wdrożenia Blue/Green**: możesz chcieć wiedzieć, czy jakieś zadania są uruchomione przed implementacją [strategii wdrażania Side-by-Side](durable-functions-versioning.md#side-by-side-deployments).

* **Zaawansowana obsługa monitorowania i diagnostyki**: można śledzić informacje o stanie aranżacji w zewnętrznym magazynie zoptymalizowanym pod kątem zapytań, takich jak Azure SQL Database lub Azure Cosmos DB.

* **Długotrwałe działanie w tle**: jeśli używasz Durable Functions do długotrwałego działania w tle, ta funkcja pomaga znać bieżący stan.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) w projekcie Durable Functions.
* Zainstaluj [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md) (tylko system Windows) lub Użyj istniejącego konta usługi Azure Storage.
* Instalowanie [interfejsu wiersza polecenia platformy Azure](/cli/azure/) lub korzystanie z [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Tworzenie niestandardowego tematu Event Grid

Utwórz temat Event Grid do wysyłania zdarzeń z Durable Functions. Poniższe instrukcje przedstawiają sposób tworzenia tematu za pomocą interfejsu wiersza polecenia platformy Azure. Możesz również utworzyć temat za [pomocą programu PowerShell](../../event-grid/custom-event-quickstart-powershell.md) lub przy [użyciu Azure Portal](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia `az group create`. Obecnie Azure Event Grid nie obsługuje wszystkich regionów. Aby uzyskać informacje o obsługiwanych regionach, zobacz [omówienie Azure Event Grid](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat Event Grid zawiera zdefiniowany przez użytkownika punkt końcowy, w którym jest ogłaszane wydarzenie. Zamień `<topic_name>` na unikatową nazwę tematu. Nazwa tematu musi być unikatowa, ponieważ jest wpisem DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Pobieranie punktu końcowego i klucza

Pobierz punkt końcowy tematu. Zamień na `<topic_name>` wybraną nazwę.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Pobierz klucz tematu. Zamień na `<topic_name>` wybraną nazwę.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teraz możesz wysyłać zdarzenia do tematu.

## <a name="configure-event-grid-publishing"></a>Konfigurowanie publikowania Event Grid

W projekcie Durable Functions Znajdź `host.json` plik.

### <a name="durable-functions-1x"></a>Durable Functions 1. x

Dodaj `eventGridTopicEndpoint` i `eventGridKeySettingName` we `durableTask` właściwości.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2. x

Dodaj `notifications` sekcję do `durableTask` właściwości pliku, zastępując `<topic_name>` ją wybraną nazwą. Jeśli `durableTask` właściwości lub `extensions` nie istnieją, utwórz je jak w tym przykładzie:

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

Możliwe Azure Event Grid właściwości konfiguracji można znaleźć w [host.jsw dokumentacji](../functions-host-json.md#durabletask). Po skonfigurowaniu `host.json` pliku aplikacja funkcji wysyła zdarzenia cyklu życia do tematu Event Grid. Ta akcja jest uruchamiana, gdy uruchamiasz aplikację funkcji lokalnie i na platformie Azure.

Ustaw ustawienie aplikacji dla klucza tematu w aplikacja funkcji i `local.settings.json` . Poniższy kod JSON jest przykładem `local.settings.json` dla debugowania lokalnego. Zamień `<topic_key>` na klucz tematu.  

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

Jeśli używasz [emulatora magazynu](../../storage/common/storage-use-emulator.md) (tylko system Windows), upewnij się, że działa. Dobrym pomysłem jest uruchomienie `AzureStorageEmulator.exe clear all` polecenia przed wykonaniem.

Jeśli korzystasz z istniejącego konta usługi Azure Storage, Zastąp `UseDevelopmentStorage=true` `local.settings.json` ciąg parametrem połączenia.

## <a name="create-functions-that-listen-for-events"></a>Tworzenie funkcji nasłuchujących zdarzeń

Korzystając z Azure Portal, Utwórz kolejną aplikację funkcji, aby nasłuchiwać zdarzeń opublikowanych przez aplikację Durable Functions. Najlepiej znaleźć ją w tym samym regionie co Event Grid tematu.

### <a name="create-an-event-grid-trigger-function"></a>Tworzenie funkcji wyzwalacza Event Grid

1. W aplikacji funkcji wybierz pozycję **funkcje**, a następnie wybierz pozycję **+ Dodaj** . 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Dodaj funkcję w Azure Portal." border="true":::

1. Wyszukaj **Event Grid**, a następnie wybierz szablon **wyzwalacza Azure Event Grid** . 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Wybierz szablon wyzwalacza Event Grid w Azure Portal." border="true":::

1. Nazwij nowy wyzwalacz, a następnie wybierz pozycję **Utwórz funkcję**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Nazwij wyzwalacz Event Grid w Azure Portal." border="true":::


    Zostanie utworzona funkcja o następującym kodzie:

    # <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

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

### <a name="add-an-event-grid-subscription"></a>Dodawanie subskrypcji Event Grid

Teraz możesz dodać subskrypcję Event Grid dla utworzonego tematu Event Grid. Aby uzyskać więcej informacji, zobacz [pojęcia w Azure Event Grid](../../event-grid/concepts.md).

1. W nowej funkcji wybierz pozycję **integracja** , a następnie wybierz pozycję **Event Grid wyzwalacz (eventGridEvent)**. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Wybierz łącze wyzwalacz Event Grid." border="true":::

1. Wybierz pozycję **utwórz Event Grid opis**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Utwórz subskrypcję Event Grid." border="true":::

1. Nazwij swoją subskrypcję zdarzenia i wybierz typ tematu **tematy Event Grid** . 

1. Wybierz subskrypcję. Następnie wybierz grupę zasobów i zasób utworzony dla Event Grid tematu. 

1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Tworzy subskrypcję usługi Event Grid." border="true":::

Teraz możesz przystąpić do odbierania zdarzeń cyklu życia.

## <a name="run-durable-functions-app-to-send-the-events"></a>Uruchom aplikację Durable Functions, aby wysłać zdarzenia

W projekcie Durable Functions, który został wcześniej skonfigurowany, Rozpocznij debugowanie na maszynie lokalnej i Rozpocznij aranżację. Aplikacja publikuje Durable Functions zdarzenia cyklu życia, aby Event Grid. Sprawdź, czy Event Grid wyzwala funkcję odbiornika utworzoną przez sprawdzanie jej dzienników w Azure Portal.

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

* **`id`**: Unikatowy identyfikator dla zdarzenia Event Grid.
* **`subject`**: Ścieżka do tematu zdarzenia. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` to `Running` ,, `Completed` `Failed` , i `Terminated` .  
* **`data`**: Durable Functions określone parametry.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) .
  * **`functionName`**: Nazwa funkcji programu Orchestrator.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: Dodatkowe dane skojarzone ze zdarzeniem śledzenia. Aby uzyskać więcej informacji, zobacz [Diagnostyka w Durable Functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Stan środowiska uruchomieniowego aranżacji. Uruchomione, zakończone, zakończone niepowodzeniem, anulowane.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Czas zdarzenia (UTC).
* **`dataVersion`**: Wersja schematu zdarzenia cyklu życia.
* **`metadataVersion`**: Wersja metadanych.
* **`topic`**: Zasób tematu z siatką zdarzeń.

## <a name="how-to-test-locally"></a>Jak testować lokalnie

Aby przetestować lokalnie, Przeczytaj [funkcję Azure Function Event Grid Wyzwól lokalne debugowanie](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj Zarządzanie wystąpieniami w Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informacje o wersji w Durable Functions](durable-functions-versioning.md)
