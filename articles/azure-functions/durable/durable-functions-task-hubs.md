---
title: Centra zadań w Durable Functions — Azure
description: Dowiedz się, co to jest centrum zadań w Durable Functions rozszerzeniu Azure Functions. Dowiedz się, jak skonfigurować centra zadań.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009521"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centra zadań w Durable Functions (Azure Functions)

*Centrum zadań* w [Durable Functions](durable-functions-overview.md) jest kontenerem logicznym dla zasobów usługi Azure Storage, które są używane dla aranżacji. Funkcje programu Orchestrator i Activity mogą współdziałać ze sobą tylko wtedy, gdy należą do tego samego centrum zadań.

Jeśli wiele aplikacji funkcji udostępnia konto magazynu, każda aplikacja funkcji *musi* być skonfigurowana przy użyciu oddzielnej nazwy centrum zadań. Konto magazynu może zawierać wiele centrów zadań. Na poniższym diagramie przedstawiono jeden centrum zadań dla każdej aplikacji funkcji na udostępnionych i dedykowanych kontach magazynu.

![Diagram przedstawiający udostępnione i dedykowane konta magazynu.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Zasoby usługi Azure Storage

Centrum zadań składa się z następujących zasobów magazynu:

* Jedna lub więcej kolejek kontroli.
* Jedna kolejka elementów roboczych.
* Jedna tabela historii.
* Jedna tabela wystąpień.
* Jeden kontener magazynu zawierający co najmniej jeden obiekt BLOB dzierżawy.
* Kontener magazynu zawierający duże ładunki komunikatów, jeśli ma zastosowanie.

Wszystkie te zasoby są tworzone automatycznie w domyślnym koncie usługi Azure Storage, gdy funkcje programu Orchestrator, Entity lub Activity działają lub są zaplanowane do uruchomienia. W artykule [wydajność i skalowanie](durable-functions-perf-and-scale.md) wyjaśniono, jak te zasoby są używane.

## <a name="task-hub-names"></a>Nazwy centrów zadań

Centra zadań są identyfikowane za pomocą nazwy, która jest zgodna z następującymi regułami:

* Zawiera tylko znaki alfanumeryczne
* Rozpoczyna się od litery
* Ma minimalną długość wynoszącą 3 znaki, maksymalną długość 45 znaków

Nazwa centrum zadań jest zadeklarowana w *host.jsw* pliku, jak pokazano w następującym przykładzie:

### <a name="hostjson-functions-20"></a>host.json (Functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (funkcje 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Centra zadań można również skonfigurować za pomocą ustawień aplikacji, jak pokazano w następującym `host.json` przykładowym pliku:

### <a name="hostjson-functions-10"></a>host.json (Functions 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Nazwa centrum zadań zostanie ustawiona na wartość `MyTaskHub` Ustawienia aplikacji. Poniżej `local.settings.json` przedstawiono sposób definiowania `MyTaskHub` Ustawienia jako `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Poniższy kod ilustruje sposób pisania funkcji, która używa [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) do pracy z centrum zadań, które jest skonfigurowane jako ustawienie aplikacji:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Poprzedni przykład w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Właściwość centrum zadań w `function.json` pliku jest ustawiana za pośrednictwem ustawienia aplikacji:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

Właściwość centrum zadań w `function.json` pliku jest ustawiana za pośrednictwem ustawienia aplikacji:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Nazwy centrów zadań muszą zaczynać się literą i składać się tylko z liter i cyfr. Jeśli nie zostanie określony, zostanie użyta domyślna nazwa centrum zadań, jak pokazano w poniższej tabeli:

| Wersja rozszerzenia trwałego | Nazwa domyślnego centrum zadań |
| - | - |
| 2.x | Po wdrożeniu na platformie Azure nazwa centrum zadań jest tworzona na podstawie nazwy _aplikacji funkcji_. W przypadku uruchamiania poza platformą Azure domyślna nazwa centrum zadań to `TestHubName` . |
| 1.x | Domyślną nazwą centrum zadań dla wszystkich środowisk jest `DurableFunctionsHub` . |

Aby uzyskać więcej informacji o różnicach między wersjami rozszerzeń, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

> [!NOTE]
> Nazwa różni się od jednego centrum zadań, gdy istnieje wiele centrów zadań na koncie magazynu udostępnionego. Jeśli masz wiele aplikacji funkcji współużytkujących udostępnione konto magazynu, musisz jawnie skonfigurować różne nazwy dla każdego centrum zadań w *host.jsna* plikach. W przeciwnym razie wiele aplikacji funkcji będzie współdziałać ze sobą dla komunikatów, co może spowodować niezdefiniowane zachowanie, w tym aranżacje nieoczekiwanie "zablokowane" w `Pending` `Running` stanie lub.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać przechowywanie wersji aranżacji](durable-functions-versioning.md)
