---
title: Podaranżacje dla Durable Functions — Azure
description: Jak wywoływać aranżacje z aranżacji w rozszerzeniu Durable Functions Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85340814"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Organizowanie podrzędne w Durable Functions (Azure Functions)

Oprócz wywoływania funkcji działania, funkcje programu Orchestrator mogą wywoływać inne funkcje programu Orchestrator. Można na przykład utworzyć większą organizację poza biblioteką mniejszych funkcji programu Orchestrator. Można też uruchomić wiele wystąpień funkcji programu Orchestrator równolegle.

Funkcja programu Orchestrator może wywoływać inną funkcję programu Orchestrator przy użyciu `CallSubOrchestratorAsync` `CallSubOrchestratorWithRetryAsync` metod w programie .NET lub `callSubOrchestrator` metod w `callSubOrchestratorWithRetry` języku JavaScript. W artykule dotyczącym [obsługi błędów & wynagrodzenie](durable-functions-error-handling.md#automatic-retry-on-failure) zawiera więcej informacji na temat automatycznego ponawiania próby.

Funkcje programu sub-Orchestrator zachowują się podobnie jak funkcje działania z perspektywy obiektu wywołującego. Mogą zwrócić wartość, zgłosić wyjątek i może oczekiwać przez nadrzędną funkcję programu Orchestrator. 

> [!NOTE]
> Podzbiory są obecnie obsługiwane w programie .NET i JavaScript.

## <a name="example"></a>Przykład

Poniższy przykład ilustruje scenariusz IoT ("Internet rzeczy"), w którym istnieje wiele urządzeń, które muszą być obsługiwane. Poniższa funkcja reprezentuje przepływ pracy aprowizacji, który należy wykonać dla każdego urządzenia:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Ta funkcja programu Orchestrator może być używana jako — dla jednorazowej aprowizacji urządzeń lub może być częścią większej aranżacji. W tym drugim przypadku nadrzędna funkcja programu Orchestrator może planować wystąpienia `DeviceProvisioningOrchestration` przy użyciu `CallSubOrchestratorAsync` interfejsu API (.NET) lub `callSubOrchestrator` (JavaScript).

Oto przykład, w którym pokazano, jak uruchomić wiele funkcji programu Orchestrator równolegle.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Poprzednie przykłady w języku C# są przeznaczone dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Podaranżacje muszą być zdefiniowane w tej samej aplikacji funkcji co w przypadku aranżacji nadrzędnej. Jeśli konieczne jest wywołanie i oczekiwanie na aranżację w innej aplikacji funkcji, należy rozważyć użycie wbudowanej obsługi interfejsów API protokołu HTTP i wzorca klienta sondowania HTTP 202. Aby uzyskać więcej informacji, zobacz temat [funkcje protokołu HTTP](durable-functions-http-features.md) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak ustawić niestandardowy stan aranżacji](durable-functions-custom-orchestration-status.md)
