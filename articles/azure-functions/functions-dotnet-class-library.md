---
title: Dokumentacja dla deweloperów Azure Functions C#
description: Dowiedz się, jak opracowywać Azure Functions przy użyciu języka C#.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: 23b0961c369c21f50d9a873678a1c910385e6a91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206209"
---
# <a name="azure-functions-c-developer-reference"></a>Dokumentacja dla deweloperów Azure Functions C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu języka C# w bibliotekach klas platformy .NET.

Azure Functions obsługuje języki programowania skryptów C# i C#. Jeśli szukasz wskazówek dotyczących [używania języka C# w Azure Portal](functions-create-function-app-portal.md), zobacz [informacje dotyczące deweloperów skryptów C# (CSX)](functions-reference-csharp.md).

W tym artykule przyjęto założenie, że zostały już przeczytane następujące artykuły:

* [Przewodnik dla deweloperów Azure Functions](functions-reference.md)
* [Azure Functions narzędzia programu Visual Studio 2019](functions-develop-vs.md)

## <a name="supported-versions"></a>Obsługiwane wersje

Wersje środowiska uruchomieniowego Functions działają z określonymi wersjami platformy .NET. W poniższej tabeli przedstawiono najwyższy poziom .NET Core i .NET Framework i .NET Core, których można używać z określoną wersją funkcji w projekcie. 

| Wersja środowiska uruchomieniowego funkcji | Maksymalna wersja platformy .NET |
| ---- | ---- |
| Funkcje 3. x | .NET Core 3,1 |
| Functions w wersji 2.x | .NET Core 2.2 |
| Functions w wersji 1.x |  .NET Framework 4.7 |

Aby dowiedzieć się więcej, zobacz [Omówienie wersji środowiska uruchomieniowego Azure Functions](functions-versions.md)

## <a name="functions-class-library-project"></a>Projekt biblioteki klas funkcji

W programie Visual Studio szablon projektu **Azure Functions** tworzy projekt biblioteki klas języka C#, który zawiera następujące pliki:

* [host.jsw](functions-host-json.md) przypadku przechowywania ustawień konfiguracji, które mają wpływ na wszystkie funkcje w projekcie podczas uruchamiania lokalnego lub na platformie Azure.
* [local.settings.js](functions-run-local.md#local-settings-file) — ustawienia aplikacji i parametry połączenia, które są używane podczas uruchamiania lokalnego. Ten plik zawiera wpisy tajne i nie jest publikowany w aplikacji funkcji na platformie Azure. Zamiast tego [Dodaj ustawienia aplikacji do aplikacji funkcji](functions-develop-vs.md#function-app-settings).

Podczas kompilowania projektu, struktura folderów, która wygląda podobnie do następującego przykładu, jest generowana w katalogu danych wyjściowych kompilacji:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Ten katalog jest wdrażany w aplikacji funkcji na platformie Azure. Rozszerzenia powiązań wymagane w [wersji 2. x](functions-versions.md) środowiska uruchomieniowego funkcji są [dodawane do projektu jako pakiety NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Proces kompilacji tworzy *function.jsw* pliku dla każdej funkcji. Ta *function.jsw* pliku nie jest przeznaczona do edycji bezpośrednio. Nie można zmienić konfiguracji powiązań ani wyłączyć funkcji, edytując ten plik. Aby dowiedzieć się, jak wyłączyć funkcję, zobacz [How to disable](disable-function.md)Functions.


## <a name="methods-recognized-as-functions"></a>Metody rozpoznawane jako funkcje

W bibliotece klas funkcja jest metodą statyczną z `FunctionName` atrybutem i wyzwalaczem, jak pokazano w następującym przykładzie:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Ten `FunctionName` atrybut oznacza metodę jako punkt wejścia funkcji. Nazwa musi być unikatowa w obrębie projektu, zaczynać się od litery i zawierać tylko litery, cyfry, `_` i `-` , maksymalnie 127 znaków. Szablony projektów często tworzą metodę o nazwie `Run` , ale nazwa metody może być dowolną prawidłową nazwą metody języka C#.

Atrybut wyzwalacza określa typ wyzwalacza i wiąże dane wejściowe z parametrem metody. Przykładowa funkcja jest wyzwalana przez komunikat w kolejce, a komunikat kolejki jest przesyłany do metody w `myQueueItem` parametrze.

## <a name="method-signature-parameters"></a>Parametry sygnatury metody

Sygnatura metody może zawierać parametry inne niż używane z atrybutem wyzwalacza. Poniżej przedstawiono niektóre dodatkowe parametry, które można uwzględnić:

* [Powiązania wejściowe i wyjściowe](functions-triggers-bindings.md) oznaczone jako takie przez dekorowania nazwy je z atrybutami.  
* `ILogger`Parametr lub `TraceWriter` ([wersja 1. x-Only](functions-versions.md#creating-1x-apps)) do [rejestrowania](#logging).
* `CancellationToken`Parametr służący do [bezpiecznego zamykania](#cancellation-tokens).
* Parametry [wyrażeń powiązań](./functions-bindings-expressions-patterns.md) w celu uzyskania metadanych wyzwalacza.

Kolejność parametrów w sygnaturze funkcji nie ma znaczenia. Na przykład można umieścić parametry wyzwalacza przed lub po innych powiązaniach i można umieścić parametr rejestratora przed lub po parametrach parametrów lub powiązania.

### <a name="output-binding-example"></a>Przykład powiązania danych wyjściowych

Poniższy przykład modyfikuje poprzednią wartość, dodając powiązanie kolejki wyjściowej. Funkcja zapisuje komunikat kolejki, który wyzwala funkcję do nowej kolejki komunikatu w innej kolejce.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Szczegółowe artykuły referencyjne (na przykład[kolejki magazynu](functions-bindings-storage-queue.md)) wyjaśniają, które typy parametrów mogą być używane z atrybutami wyzwalacza, danych wejściowych lub wyjściowych.

### <a name="binding-expressions-example"></a>Przykład wyrażeń powiązań

Poniższy kod pobiera nazwę kolejki do monitorowania z poziomu aplikacji i pobiera w parametrach czas utworzenia komunikatu w kolejce `insertionTime` .

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>function.jswygenerowany automatycznie

Proces kompilacji tworzy *function.js* w pliku w folderze funkcji w folderze Build. Jak wspomniano wcześniej, ten plik nie jest przeznaczony do edycji bezpośrednio. Nie można zmienić konfiguracji powiązań ani wyłączyć funkcji, edytując ten plik. 

Celem tego pliku jest udostępnienie informacji kontrolerowi skalowania do użycia w celu [skalowania w ramach planu zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work). Z tego powodu plik zawiera tylko informacje wyzwalacza, a nie dane wejściowe i wyjściowe.

Wygenerowany *function.jsw* pliku zawiera `configurationSource` Właściwość, która informuje środowisko uruchomieniowe, aby używało atrybutów .NET dla powiązań, a nie *function.jsw* konfiguracji. Oto przykład:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft. NET. Sdk. Functions

*function.jspodczas* generowania plików jest wykonywane przez pakiet NuGet [ \. funkcje Microsoft .NET \. SDK \. ](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Ten sam pakiet jest używany zarówno w wersji 1. x, jak i 2. x środowiska uruchomieniowego usługi Functions. Platforma docelowa to odróżnia projekt 1. x z projektu 2. x. Poniżej przedstawiono odpowiednie części plików *. csproj* , które pokazują różne platformy docelowe i ten sam `Sdk` pakiet:

**Functions w wersji 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions w wersji 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Między `Sdk` zależnościami pakietów są wyzwalacze i powiązania. Projekt 1. x odwołuje się do 1. x wyzwalaczy i powiązań, ponieważ te wyzwalacze i powiązania są przeznaczone dla .NET Framework, natomiast wyzwalacze 2. x i powiązania są przeznaczone dla platformy .NET Core.

`Sdk`Pakiet jest również zależny od [Newtonsoft.Jsna](https://www.nuget.org/packages/Newtonsoft.Json)i pośrednio w [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Te zależności zadbają o to, aby projekt używał wersji tych pakietów, które działają z wersją środowiska uruchomieniowego funkcji, której dotyczy projekt. Na przykład `Newtonsoft.Json` ma wersję 11 dla .NET Framework 4.6.1, ale środowisko uruchomieniowe Functions, które jest elementem docelowym .NET Framework 4.6.1 jest zgodne z `Newtonsoft.Json` 9.0.1. Dlatego kod funkcji w tym projekcie musi również używać `Newtonsoft.Json` 9.0.1.

Kod źródłowy `Microsoft.NET.Sdk.Functions` jest dostępny w repozytorium GitHub [usługi Azure \- Functions \- vs \- Build \- SDK](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Wersja środowiska uruchomieniowego

Program Visual Studio używa [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) do uruchamiania projektów funkcji. Podstawowe narzędzia to interfejs wiersza polecenia dla środowiska uruchomieniowego usługi Functions.

Jeśli instalujesz podstawowe narzędzia przy użyciu programu npm, które nie ma wpływu na podstawową wersję narzędzi używaną przez program Visual Studio. W przypadku środowiska uruchomieniowego usługi Functions w wersji 1. x program Visual Studio przechowuje podstawowe wersje narzędzi w programie *%USERPROFILE%\AppData\Local\Azure.Functions.CLI* i używa w tym miejscu najnowszej wersji. W przypadku funkcji 2. x podstawowe narzędzia są zawarte w rozszerzeniu **narzędzia Azure Functions i Web Jobs Tools** . Dla obu 1. x i 2. x można sprawdzić, jaka wersja jest używana w danych wyjściowych konsoli podczas uruchamiania projektu funkcji:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

Aplikację funkcji można skompilować jako plik [binarny ReadyToRun](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images). ReadyToRun jest formą kompilacji z wyprzedzeniem, która może poprawić wydajność uruchamiania, aby pomóc w zmniejszeniu wpływu [zimnego uruchomienia](functions-scale.md#cold-start) w ramach [planu zużycia](functions-scale.md#consumption-plan).

ReadyToRun jest dostępny w programie .NET 3,0 i wymaga [wersji 3,0 środowiska uruchomieniowego Azure Functions](functions-versions.md).

Aby skompilować projekt jako ReadyToRun, zaktualizuj plik projektu przez dodanie `<PublishReadyToRun>` `<RuntimeIdentifier>` elementów i. Poniżej przedstawiono konfigurację publikowania w aplikacji funkcji systemu Windows 32-bitowego.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun obecnie nie obsługuje kompilacji krzyżowej. Musisz skompilować aplikację na tej samej platformie, w której znajduje się cel wdrożenia. Należy również zwrócić uwagę na "bitową", która jest skonfigurowana w aplikacji funkcji. Na przykład jeśli aplikacja funkcji na platformie Azure jest w systemie Windows 64-bitowym, musisz skompilować aplikację w systemie Windows za pomocą `win-x64` [identyfikatora czasu wykonywania](/dotnet/core/rid-catalog).

Możesz również utworzyć aplikację za pomocą ReadyToRun z poziomu wiersza polecenia. Aby uzyskać więcej informacji, zobacz `-p:PublishReadyToRun=true` opcję w [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>Obsługiwane typy powiązań

Każde powiązanie ma własne obsługiwane typy; na przykład atrybut wyzwalacza obiektu BLOB może być stosowany do parametru ciągu, POCO parametru, `CloudBlockBlob` parametru lub dowolnego innego obsługiwanego typu. [Artykuł dotyczący powiązań powiązań obiektów BLOB](functions-bindings-storage-blob-trigger.md#usage) zawiera listę wszystkich obsługiwanych typów parametrów. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md) oraz [dokumenty referencyjne powiązań dla każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Powiązanie z wartością zwracaną metody

Można użyć wartości zwracanej metody dla powiązania danych wyjściowych, stosując atrybut do wartości zwracanej przez metodę. Aby zapoznać się z przykładami, zobacz [wyzwalacze i powiązania](./functions-bindings-return-value.md). 

Użyj wartości zwracanej tylko wtedy, gdy pomyślne wykonanie funkcji zawsze powoduje, że wartość zwracana zostanie przekazana do powiązania danych wyjściowych. W przeciwnym razie użyj `ICollector` lub `IAsyncCollector` , jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości wyjściowych

Aby zapisać wiele wartości do powiązania danych wyjściowych lub jeśli pomyślne wywołanie funkcji może nie spowodować, że wszystko jest przekazywane do powiązania danych wyjściowych, użyj [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) typów lub [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) . Te typy są kolekcjami tylko do zapisu, które są zapisywane do powiązania danych wyjściowych, gdy metoda zostanie zakończona.

Ten przykład zapisuje wiele komunikatów w kolejce w tej samej kolejce przy użyciu `ICollector` :

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Rejestrowanie

Aby zalogować dane wyjściowe do dzienników przesyłania strumieniowego w języku C#, Dołącz argument typu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Zalecamy, aby ją nazwać `log` , jak w poniższym przykładzie:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Unikaj używania `Console.Write` w Azure Functions. Aby uzyskać więcej informacji, zobacz [Zapisywanie dzienników w języku C#](functions-monitoring.md#write-logs-in-c-functions) w artykule **monitorowanie Azure Functions** .

## <a name="async"></a>Async

Aby wykonać funkcję [asynchroniczną](/dotnet/csharp/programming-guide/concepts/async/), należy użyć `async` słowa kluczowego i zwrócić `Task` obiekt.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Nie można używać `out` parametrów w funkcjach asynchronicznych. W przypadku powiązań wyjściowych Użyj zamiast niego [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiektu modułu zbierającego](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Tokeny anulowania

Funkcja może akceptować parametr [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , który umożliwia systemowi operacyjnemu powiadomienie Twojego kodu, gdy funkcja zostanie zakończona. Możesz użyć tego powiadomienia, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który opuszcza dane w stanie niespójnym.

Poniższy przykład pokazuje, jak sprawdzić nieoczekiwane zakończenie działania funkcji.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Zmienne środowiskowe

Aby uzyskać zmienną środowiskową lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable` , jak pokazano w poniższym przykładzie kodu:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Ustawienia aplikacji mogą być odczytywane ze zmiennych środowiskowych zarówno podczas tworzenia lokalnego, jak i uruchamiania na platformie Azure. Podczas programowania lokalnego ustawienia aplikacji pochodzą z `Values` kolekcji w *local.settings.js* pliku. W obu środowiskach — lokalnie i na platformie Azure, `GetEnvironmentVariable("<app setting name>")` Pobiera wartość nazwanego ustawienia aplikacji. Na przykład w przypadku uruchamiania lokalnego "Moja witryna Name" będzie zwracana, jeśli *local.settings.jsw* pliku zawiera `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` .

Właściwość [System.Configuration.ConfigurationManager. AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) jest ALTERNATYWnym interfejsem API do uzyskiwania wartości ustawień aplikacji, ale zalecamy użycie metody, `GetEnvironmentVariable` jak pokazano tutaj.

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W języku C# i innych językach .NET można użyć wzorca powiązania [bezwzględnego](https://en.wikipedia.org/wiki/Imperative_programming) , w przeciwieństwie do [*deklaratywnych*](https://en.wikipedia.org/wiki/Declarative_programming) powiązań w atrybutach. Bezwzględne powiązanie jest przydatne, gdy parametry powiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Za pomocą tego wzorca można powiązać z obsługiwanymi powiązaniami wejściowymi i wyjściowymi na bieżąco w kodzie funkcji.

Zdefiniuj bezwzględne powiązanie w następujący sposób:

- **Nie** dołączaj atrybutu do podpisu funkcji dla żądanych, bezwzględnych powiązań.
- Przekaż parametr wejściowy [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) lub [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Użyj następującego wzorca języka C#, aby wykonać powiązanie danych.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` jest atrybutem platformy .NET, który definiuje powiązanie, i `T` jest typem wejściowym lub wyjściowym obsługiwanym przez ten typ powiązania. `T` nie może być `out` typem parametru (na przykład `out JObject` ). Na przykład powiązanie danych wyjściowych tabeli Mobile Apps obsługuje [sześć typów wyjściowych](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale można używać tylko [ICollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [IAsyncCollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) z bezwzględnym wiązaniem.

### <a name="single-attribute-example"></a>Przykład pojedynczego atrybutu

Poniższy przykładowy kod tworzy [powiązanie danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob-output.md) z ścieżką obiektu BLOB, która jest zdefiniowana w czasie wykonywania, a następnie zapisuje ciąg do obiektu BLOB.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[Obiekt blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definiuje powiązanie danych wejściowych lub wyjściowych [magazynu obiektów BLOB](functions-bindings-storage-blob.md) , a parametr [TextWriter](/dotnet/api/system.io.textwriter) jest obsługiwanym typem powiązania.

### <a name="multiple-attribute-example"></a>Przykład wielu atrybutów

W poprzednim przykładzie jest pobierane ustawienie aplikacji dla głównych parametrów połączenia konta magazynu aplikacji funkcji (co to jest `AzureWebJobsStorage` ). Możesz określić niestandardowe ustawienie aplikacji do użycia dla konta magazynu, dodając [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()` . Użyj `Binder` parametru, a nie `IBinder` .  Na przykład:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Wyzwalacze i powiązania 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Functions](functions-best-practices.md)
