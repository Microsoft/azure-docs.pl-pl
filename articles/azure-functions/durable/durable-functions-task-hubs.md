---
title: Centra zadań w funkcjach trwałych — Azure
description: Dowiedz się, co centrum zadań znajduje się w rozszerzeniu Funkcje trwałe dla usługi Azure Functions. Dowiedz się, jak skonfigurować centra zadań.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604610"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centra zadań w funkcjach trwałych (usługi Azure)

*Centrum zadań* w [funkcji trwałych](durable-functions-overview.md) jest kontener logiczny dla zasobów usługi Azure Storage, które są używane do aranżacji. Funkcje koordynatora i działania mogą wchodzić ze sobą w interakcje tylko wtedy, gdy należą do tego samego centrum zadań.

Jeśli wiele aplikacji funkcji współużytkuje konto magazynu, każda aplikacja funkcji *musi* być skonfigurowana z osobną nazwą centrum zadań. Konto magazynu może zawierać wiele centrów zadań. Na poniższym diagramie przedstawiono jedno centrum zadań na aplikację funkcji na kontach magazynu udostępnionego i dedykowanego.

![Diagram przedstawiający udostępnione i dedykowane konta magazynu.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Zasoby usługi Azure Storage

Centrum zadań składa się z następujących zasobów magazynu:

* Co najmniej jedna kolejka kontrolna.
* Jedna kolejka elementów roboczych.
* Jeden stół historii.
* Tabela wystąpień.
* Jeden kontener magazynu zawierający jeden lub więcej obiektów blob dzierżawy.
* Kontener magazynu zawierający duże ładunki wiadomości, jeśli ma to zastosowanie.

Wszystkie te zasoby są tworzone automatycznie na domyślnym koncie usługi Azure Storage, gdy funkcje programu orchestrator, encji lub działania są uruchamiane lub są planowane do uruchomienia. W artykule [Wydajność i skalowanie](durable-functions-perf-and-scale.md) wyjaśniono, jak te zasoby są używane.

## <a name="task-hub-names"></a>Nazwy centrum zadań

Centra zadań są identyfikowane za pomocą nazwy zgodnej z tymi regułami:

* Zawiera tylko znaki alfanumeryczne
* Zaczyna się od litery
* Ma minimalną długość 3 znaków, maksymalną długość 45 znaków

Nazwa centrum zadań jest zadeklarowana w pliku *host.json,* jak pokazano w poniższym przykładzie:

### <a name="hostjson-functions-20"></a>host.json (Funkcje 2.0)

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

### <a name="hostjson-functions-1x"></a>host.json (Funkcje 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Koncentratory zadań można również skonfigurować przy użyciu ustawień `host.json` aplikacji, jak pokazano w poniższym przykładowym pliku:

### <a name="hostjson-functions-10"></a>host.json (Funkcje 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Funkcje 2.0)

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

Nazwa centrum zadań zostanie ustawiona na `MyTaskHub` wartość ustawienia aplikacji. Poniżej `local.settings.json` przedstawiono sposób `MyTaskHub` definiowania `samplehubname`ustawienia jako:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Poniższy kod pokazuje, jak napisać funkcję, która używa [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) do pracy z centrum zadań, który jest skonfigurowany jako ustawienie aplikacji:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Poprzedni przykład języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Właściwość centrum zadań `function.json` w pliku jest ustawiana za pomocą ustawienia aplikacji:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Nazwy centrum zadań muszą zaczynać się od litery i składać się tylko z liter i cyfr. Jeśli nie zostanie określona, domyślna nazwa centrum zadań będzie używana w sposób pokazany w poniższej tabeli:

| Trwała wersja przedłużająca | Domyślna nazwa centrum zadań |
| - | - |
| 2.x | Po wdrożeniu na platformie Azure nazwa centrum zadań pochodzi od nazwy _aplikacji funkcji_. Gdy działa poza platformą Azure, `TestHubName`domyślną nazwą centrum zadań jest . |
| 1.x | Domyślna nazwa centrum zadań dla `DurableFunctionsHub`wszystkich środowisk to . |

Aby uzyskać więcej informacji na temat różnic między wersjami rozszerzenia, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

> [!NOTE]
> Nazwa jest tym, co odróżnia jedno centrum zadań od drugiego, gdy na koncie udostępnionego magazynu znajduje się wiele centrów zadań. Jeśli masz wiele aplikacji funkcji współużytkuje udostępnione konto magazynu, należy jawnie skonfigurować różne nazwy dla każdego centrum zadań w plikach *host.json.* W przeciwnym razie wiele aplikacji funkcji będzie konkurować ze sobą dla wiadomości, co może spowodować niezdefiniowane zachowanie, w tym aranżacji coraz nieoczekiwanie "zatrzymany" w `Pending` stanie lub. `Running`

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak radzić sobie z przechowywaniem wersji aranżacji](durable-functions-versioning.md)
