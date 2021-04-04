---
title: Jak uruchamiać Durable Functions jako zadania WebJob — Azure
description: Dowiedz się, jak kodować i konfigurować Durable Functions do uruchamiania w zadaniach WebJob przy użyciu zestawu SDK usługi WebJobs.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009504"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak uruchamiać Durable Functions jako zadania WebJob

Domyślnie Durable Functions używa środowiska uruchomieniowego Azure Functions do hostowania aranżacji. Jednak mogą istnieć pewne scenariusze, w których potrzebna jest większa kontrola nad kodem, który nasłuchuje zdarzeń. W tym artykule opisano sposób implementacji aranżacji przy użyciu zestawu SDK usługi WebJobs. Aby wyświetlić bardziej szczegółowe porównanie między funkcjami i zadaniami WebJob, zobacz [porównywanie funkcji i zadań WebJob](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) i rozszerzenie [Durable Functions](durable-functions-overview.md) są oparte na [zestawie SDK zadań WebJob](../../app-service/webjobs-sdk-how-to.md). Hostem zadania w zestawie SDK zadań WebJob jest środowisko uruchomieniowe w Azure Functions. Jeśli konieczne jest kontrolowanie zachowania w sposób niemożliwy w Azure Functions, można opracowywać i uruchamiać Durable Functions za pomocą zestawu SDK usługi WebJobs samodzielnie.

W wersji 3. x zestawu SDK zadań WebJob host jest implementacją programu `IHost` , a w wersji 2. x jest używany `JobHost` obiekt.

Przykład Durable Functions łańcucha jest dostępny w wersji zestawu WebJobs SDK 2. x: pobieranie lub klonowanie [repozytorium Durable Functions](https://github.com/azure/azure-functions-durable-extension/)i wyewidencjonowywanie gałęzi *V1* i przejdź do folderu *Samples \\ webjobssdk \\ łańcucha* .

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz podstawowe informacje o zestawie SDK zadań WebJob, opracowaniu biblioteki klas C# dla Azure Functions i Durable Functions. Jeśli potrzebujesz wprowadzenia do tych tematów, zobacz następujące zasoby:

* [Wprowadzenie do zestawu SDK zadań WebJob](../../app-service/webjobs-sdk-get-started.md)
* [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Aby wykonać kroki opisane w tym artykule:

* [Zainstaluj program Visual Studio 2019](/visualstudio/install/) przy użyciu obciążeń **programistycznych platformy Azure** .

  Jeśli masz już program Visual Studio, ale nie masz tego obciążenia, Dodaj obciążenie, wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.

  (Zamiast tego można użyć [Visual Studio Code](https://code.visualstudio.com/) , ale niektóre instrukcje są specyficzne dla programu Visual Studio).

* Zainstaluj i uruchom [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md) w wersji 5,2 lub nowszej. Alternatywą jest zaktualizowanie pliku *App.config* przy użyciu parametrów połączenia usługi Azure Storage.

## <a name="webjobs-sdk-versions"></a>Wersje zestawu SDK zadań WebJob

W tym artykule wyjaśniono, jak utworzyć projekt zestawu WebJobs SDK 2. x (odpowiednik Azure Functions wersji 1. x). Aby uzyskać informacje o wersji 3. x, zobacz temat [WebJobs SDK 3. x](#webjobs-sdk-3x) w dalszej części tego artykułu.

## <a name="create-a-console-app"></a>tworzenie aplikacji konsoli

Aby uruchamiać Durable Functions jako zadania WebJob, należy najpierw utworzyć aplikację konsolową. Projekt zestawu SDK zadań WebJob jest tylko projektem aplikacji konsolowej z zainstalowanymi odpowiednimi pakietami NuGet.

W oknie dialogowym **Nowy projekt** programu Visual Studio wybierz kolejno pozycje **Windows klasyczna**  >  **Aplikacja konsolowa (.NET Framework)**. W pliku projektu `TargetFrameworkVersion` powinno być `v4.6.1` .

Program Visual Studio ma również szablon projektu zadań WebJob, którego można użyć, wybierając pozycję zadanie WebJob w **chmurze**  >  **Azure (.NET Framework)**. Ten szablon służy do instalowania wielu pakietów, a niektóre z nich mogą nie być potrzebne.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

Potrzebne są pakiety NuGet dla zestawu SDK usługi WebJobs, podstawowe powiązania, struktura rejestrowania i rozszerzenie zadania trwałego. Poniżej przedstawiono polecenia **konsoli Menedżera pakietów** dla tych pakietów z najnowszymi stałymi numerami wersji w dniu zapisania tego artykułu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Wymagane są również dostawcy rejestrowania. Następujące polecenia instalują dostawcę usługi Azure Application Insights i `ConfigurationManager` . `ConfigurationManager`Umożliwia uzyskanie klucza instrumentacji Application Insights z ustawień aplikacji.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Następujące polecenie instaluje dostawcę konsoli:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Kod JobHost

Po utworzeniu aplikacji konsolowej i zainstalowaniu wymaganych pakietów NuGet możesz użyć Durable Functions. Można to zrobić za pomocą kodu JobHost.

Aby użyć rozszerzenia Durable Functions, wywołaj `UseDurableTask` dla `JobHostConfiguration` obiektu w `Main` metodzie:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Aby uzyskać listę właściwości, które można ustawić w `DurableTaskExtension` obiekcie, zobacz [host.json](../functions-host-json.md#durabletask).

Ta `Main` Metoda jest również miejscem, w którym można skonfigurować dostawców rejestrowania. Poniższy przykład konfiguruje dostawców konsoli i Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funkcje

Durable Functions w kontekście zadań WebJob różni się nieco od Durable Functions w kontekście Azure Functions. Ważne jest, aby pamiętać o różnicach podczas pisania kodu.

Zestaw SDK zadań WebJob nie obsługuje następujących funkcji Azure Functions:

* [FunctionName — atrybut](#functionname-attribute)
* [Wyzwalacz HTTP](#http-trigger)
* [Durable Functions API zarządzania HTTP](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName — atrybut

W projekcie zestawu SDK zadań WebJob, nazwą metody funkcji jest nazwa funkcji. Ten `FunctionName` atrybut jest używany tylko w Azure Functions.

### <a name="http-trigger"></a>HTTP trigger

Zestaw SDK zadań WebJob nie ma wyzwalacza HTTP. Klient aranżacji przykładowego projektu używa wyzwalacza czasomierza:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>Interfejs API zarządzania HTTP

Ponieważ nie ma wyzwalacza HTTP, zestaw SDK usługi WebJobs nie ma [interfejsu API zarządzania http](durable-functions-http-api.md).

W projekcie zestawu SDK zadań WebJob można wywołać metody w obiekcie klienta aranżacji, zamiast wysyłać żądania HTTP. Poniższe metody odnoszą się do trzech zadań, które można wykonać za pomocą interfejsu API zarządzania HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkcja klienta aranżacji w przykładowym projekcie uruchamia funkcję programu Orchestrator, a następnie przechodzi do pętli, która wywołuje `GetStatusAsync` co 2 sekundy:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Masz Durable Functions skonfigurowany do uruchamiania jako zadanie WebJob i masz już świadomość, w jaki sposób może się to różnić w zależności od uruchamiania Durable Functions jako autonomicznych Azure Functions. W tym momencie widzisz, że może to być przydatne w przypadku przykładu.

Ta sekcja zawiera omówienie sposobu uruchamiania [przykładowego projektu](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining). Aby uzyskać szczegółowe instrukcje, w których wyjaśniono, jak uruchomić projekt zestawu SDK zadań WebJob lokalnie i wdrożyć go w usłudze Zadania WebJob platformy Azure, zobacz Wprowadzenie do [zestawu SDK usługi WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Uruchamianie lokalnie

1. Upewnij się, że emulator magazynu jest uruchomiony (zobacz [wymagania wstępne](#prerequisites)).

1. Jeśli chcesz zobaczyć dzienniki w Application Insights podczas lokalnego uruchamiania projektu:

    a. Utwórz zasób Application Insights i użyj dla niego **ogólnego** typu aplikacji.

    b. Zapisz klucz Instrumentacji w pliku *App.config* .

1. Uruchom projekt.

### <a name="run-in-azure"></a>Uruchom na platformie Azure

1. Utwórz aplikację internetową i konto magazynu.

1. W aplikacji sieci Web Zapisz parametry połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .

1. Utwórz zasób Application Insights i użyj dla niego **ogólnego** typu aplikacji.

1. Zapisz klucz Instrumentacji w ustawieniu aplikacji o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY` .

1. Wdróż jako zadanie WebJob.

## <a name="webjobs-sdk-3x"></a>Zestaw WebJobs SDK 3. x

W tym artykule wyjaśniono, jak utworzyć projekt zestawu WebJobs SDK 2. x. Jeśli opracowujesz projekt [zestawu WebJobs SDK 3. x](../../app-service/webjobs-sdk-get-started.md) , ta sekcja pomoże zrozumieć różnice.

Główną wprowadzaną zmianą jest użycie platformy .NET Core zamiast .NET Framework. Aby utworzyć projekt zestawu SDK 3. x zadań WebJob, instrukcje są takie same, z następującymi wyjątkami:

1. Tworzenie aplikacji konsolowej platformy .NET Core. W oknie dialogowym **Nowy projekt** programu Visual Studio wybierz pozycję Aplikacja konsoli **.NET Core**  >  **(.NET Core)**. Plik projektu określa, że `TargetFramework` jest `netcoreapp2.x` .

1. Wybierz zestaw SDK programu WebJobs w wersji 3. x następujących pakietów:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Ustaw parametry połączenia magazynu i klucz Instrumentacji Application Insights w *appsettings.js* pliku przy użyciu platformy .NET Core Configuration Framework. Oto przykład:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Zmień `Main` Kod metody, aby to zrobić. Oto przykład:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zestawie SDK zadań WebJob, zobacz [jak używać zestawu SDK WebJobs](../../app-service/webjobs-sdk-how-to.md).
