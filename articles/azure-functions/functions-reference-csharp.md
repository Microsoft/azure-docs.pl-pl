---
title: Azure Functions informacje dla deweloperów skryptów C#
description: Dowiedz się, jak opracowywać Azure Functions przy użyciu skryptu C#.
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 48614640660da6d85face5ea416d267fa9f59515
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164843"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Dokumentacja dewelopera Azure Functions skryptu C# (. CSX)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu skryptu C# (*. CSX*).

Azure Functions obsługuje języki programowania skryptów C# i C#. Jeśli szukasz wskazówek dotyczących [używania języka C# w projekcie biblioteki klas programu Visual Studio](functions-develop-vs.md), zobacz [Dokumentacja dla deweloperów języka C#](functions-dotnet-class-library.md).

W tym artykule przyjęto założenie, że został już odczytany [Przewodnik deweloperów Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Jak działa CSX

Środowisko skryptu C# dla Azure Functions jest oparte na [Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Dane są przesyłane do funkcji języka C# za pośrednictwem argumentów metody. Nazwy argumentów są określone w `function.json` pliku, a istnieją wstępnie zdefiniowane nazwy do uzyskiwania dostępu do takich elementów jak Rejestrator funkcji i tokeny anulowania.

Format *. CSX* umożliwia pisanie mniej "standardowych" i skoncentrowanie się na pisaniu tylko funkcji języka C#. Zamiast zawijać wszystko w przestrzeni nazw i klasie, wystarczy zdefiniować `Run` metodę. Dołącz wszystkie odwołania do zestawów i przestrzenie nazw na początku pliku w zwykły sposób.

Pliki *. CSX* aplikacji funkcji są kompilowane po zainicjowaniu wystąpienia. Ten krok kompilacji oznacza, jak zimne uruchomienie może trwać dłużej dla funkcji skryptu C# w porównaniu z bibliotekami klas języka C#. Ten krok kompilacji jest również przykładem, że funkcje skryptu C# są edytowalne w Azure Portal, natomiast biblioteki klas języka C# nie są.

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu skryptu C# wygląda następująco:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Istnieje [host.jsudostępnione w](functions-host-json.md) pliku, którego można użyć do skonfigurowania aplikacji funkcji. Każda funkcja ma własny plik kodu (. CSX) i plik konfiguracji powiązania (function.json).

Rozszerzenia powiązań wymagane w [wersji 2. x i nowszych wersjach](functions-versions.md) środowiska uruchomieniowego Functions są zdefiniowane w `extensions.csproj` pliku z rzeczywistymi plikami biblioteki w `bin` folderze. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami

Dane wejściowe lub wyjściowe są powiązane z parametrem funkcji skryptu C# za pośrednictwem `name` właściwości w *function.js* w pliku konfiguracyjnym. Poniższy przykład ilustruje *function.js* plik i plik *Run. CSX* dla funkcji wyzwalanej przez kolejkę. Parametr, który odbiera dane z komunikatu kolejki, jest nazwany, `myQueueItem` ponieważ jest to wartość `name` właściwości.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

`#r`Instrukcja została omówiona [w dalszej części tego artykułu](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Obsługiwane typy powiązań

Każde powiązanie ma własne obsługiwane typy; na przykład wyzwalacz obiektu BLOB może być używany z parametrem String, parametrem POCO, `CloudBlockBlob` parametrem lub dowolnym innym obsługiwanym typem. [Artykuł dotyczący powiązań powiązań obiektów BLOB](functions-bindings-storage-blob-trigger.md#usage) zawiera listę wszystkich obsługiwanych typów parametrów dla wyzwalaczy obiektów BLOB. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md) oraz [dokumenty referencyjne powiązań dla każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odwoływanie klas niestandardowych

Jeśli musisz użyć niestandardowej klasy POCO (Custom CLR Object), możesz dołączyć definicję klasy wewnątrz tego samego pliku lub umieścić ją w osobnym pliku.

W poniższym przykładzie przedstawiono przykład *Run. CSX* , który zawiera definicję klasy poco.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Klasa POCO musi mieć zdefiniowaną metodę pobierającą i setter dla każdej właściwości.

## <a name="reusing-csx-code"></a>Używanie kodu. CSX

Można użyć klas i metod zdefiniowanych w innych plikach *. CSX* w pliku *Run. CSX* . W tym celu należy użyć `#load` dyrektyw w pliku *Run. CSX* . W poniższym przykładzie procedura rejestrowania o nazwie `MyLogger` jest udostępniana w elemencie *webrejestratoru. CSX* i załadowana do polecenia *Run. CSX* przy użyciu `#load` dyrektywy:

Przykład *Run. CSX*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Przykładowy kod *CSX*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Użycie pliku Shared *. CSX* jest typowym wzorcem, gdy chcesz silnie wpisywać dane przesyłane między funkcjami przy użyciu obiektu poco. W poniższym uproszczonym przykładzie wyzwalacz HTTP i wyzwalacz kolejki współdzielą obiekt POCO o nazwie, `Order` aby silnie wpisać dane zamówienia:

Przykład *Run. CSX* dla wyzwalacza http:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Przykład *Run. CSX* dla wyzwalacza kolejki:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Przykład *Order. CSX*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

Możesz użyć ścieżki względnej z `#load` dyrektywą:

* `#load "mylogger.csx"` ładuje plik znajdujący się w folderze funkcji.
* `#load "loadedfiles\mylogger.csx"` ładuje plik znajdujący się w folderze w folderze funkcji.
* `#load "..\shared\mylogger.csx"` ładuje plik znajdujący się w folderze na tym samym poziomie co folder funkcji, czyli bezpośrednio w katalogu *wwwroot*.

`#load`Dyrektywa działa tylko z plikami *. CSX* , a nie z plikami *. cs* .

## <a name="binding-to-method-return-value"></a>Powiązanie z wartością zwracaną metody

Można użyć wartości zwracanej metody dla powiązania danych wyjściowych przy użyciu nazwy `$return` w *function.jsna*. Aby zapoznać się z przykładami, zobacz [wyzwalacze i powiązania](./functions-bindings-return-value.md).

Użyj wartości zwracanej tylko wtedy, gdy pomyślne wykonanie funkcji zawsze powoduje, że wartość zwracana zostanie przekazana do powiązania danych wyjściowych. W przeciwnym razie użyj `ICollector` lub `IAsyncCollector` , jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości wyjściowych

Aby zapisać wiele wartości do powiązania danych wyjściowych lub jeśli pomyślne wywołanie funkcji może nie spowodować, że wszystko jest przekazywane do powiązania danych wyjściowych, użyj [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) typów lub [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) . Te typy są kolekcjami tylko do zapisu, które są zapisywane do powiązania danych wyjściowych, gdy metoda zostanie zakończona.

Ten przykład zapisuje wiele komunikatów w kolejce w tej samej kolejce przy użyciu `ICollector` :

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Rejestrowanie

Aby zalogować dane wyjściowe do dzienników przesyłania strumieniowego w języku C#, Dołącz argument typu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Zalecamy, aby ją nazwać `log` . Unikaj używania `Console.Write` w Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Aby uzyskać informacje o nowszej platformie rejestrowania, których można użyć zamiast programu `TraceWriter` , zobacz dokumentację [ILogger](functions-dotnet-class-library.md#ilogger) w przewodniku dewelopera biblioteki klas .NET.

### <a name="custom-metrics-logging"></a>Rejestrowanie metryk niestandardowych

Przy użyciu `LogMetric` metody rozszerzania można `ILogger` tworzyć metryki niestandardowe w Application Insights. Oto przykładowe wywołanie metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ten kod jest alternatywą dla wywołania przy `TrackMetric` użyciu interfejsu API Application Insights dla platformy .NET.

## <a name="async"></a>Async

Aby wykonać funkcję [asynchroniczną](/dotnet/csharp/programming-guide/concepts/async/), należy użyć `async` słowa kluczowego i zwrócić `Task` obiekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Nie można używać `out` parametrów w funkcjach asynchronicznych. W przypadku powiązań wyjściowych Użyj zamiast niego [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiektu modułu zbierającego](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Tokeny anulowania

Funkcja może akceptować parametr [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , który umożliwia systemowi operacyjnemu powiadomienie Twojego kodu, gdy funkcja zostanie zakończona. Możesz użyć tego powiadomienia, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który opuszcza dane w stanie niespójnym.

Poniższy przykład pokazuje, jak sprawdzić nieoczekiwane zakończenie działania funkcji.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
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
```

## <a name="importing-namespaces"></a>Importowanie przestrzeni nazw

Jeśli zachodzi konieczność zaimportowania przestrzeni nazw, można to zrobić w zwykły sposób z `using` klauzulą.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące przestrzenie nazw są automatycznie importowane i dlatego są opcjonalne:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odwołujące się do zestawów zewnętrznych

W przypadku zestawów struktury Dodaj odwołania za pomocą `#r "AssemblyName"` dyrektywy.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingu Azure Functions:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Następujące zestawy mogą być przywoływane przez prostą nazwę (na przykład `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odwołujące się do zestawów niestandardowych

Aby odwołać się do zestawu niestandardowego, można użyć zestawu *udostępnionego* lub zestawu *prywatnego* :

* Zestawy udostępnione są współużytkowane przez wszystkie funkcje w aplikacji funkcji. Aby odwołać się do zestawu niestandardowego, Przekaż zestaw do folderu o nazwie `bin` w [folderze głównym aplikacji funkcji](functions-reference.md#folder-structure) (wwwroot).

* Zestawy prywatne są częścią danego kontekstu funkcji i obsługują ładowanie bezpośredniego różnych wersji. Zestawy prywatne należy przekazać do `bin` folderu w katalogu funkcji. Odwołuje się do zestawów przy użyciu nazwy pliku, takiej jak `#r "MyAssembly.dll"` .

Informacje o sposobach przekazywania plików do folderu funkcji znajdują się w sekcji [Zarządzanie pakietami](#using-nuget-packages).

### <a name="watched-directories"></a>Monitorowane katalogi

Katalog zawierający plik skryptu funkcji jest automatycznie oglądany pod kątem zmian w zestawach. Aby obejrzeć zmiany zestawu w innych katalogach, Dodaj je do `watchDirectories` listy w [host.jsna](functions-host-json.md).

## <a name="using-nuget-packages"></a>Korzystanie z pakietów NuGet
Aby używać pakietów NuGet w 2. x i nowszych funkcjach języka C#, Przekaż plik *. proj* do folderu funkcji w systemie plików aplikacji funkcji. Poniżej znajduje się przykładowy plik *Function. proj* , który dodaje odwołanie do *Microsoft. ProjectOxford.* *1.1.0*w wersji:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Aby użyć niestandardowego źródła danych NuGet, Określ źródło w pliku *Nuget.Config* w aplikacja funkcji głównym. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zachowania NuGet](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> W funkcjach 1. x języka C# do pakietów NuGet są przywoływane *project.jsw* pliku, a nie w pliku *Function. proj* .

W przypadku funkcji 1. x należy zamiast tego użyć *project.jsw* pliku. Oto przykład *project.jsw* pliku:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Korzystanie z pliku Function. proj

1. Otwórz funkcję w Azure Portal. Na karcie Dzienniki są wyświetlane dane wyjściowe instalacji pakietu.
2. Aby przekazać plik *Function. proj* , użyj jednej z metod opisanych w artykule [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate) w temacie Informacje dotyczące deweloperów Azure Functions.
3. Po przekazaniu pliku *Function. proj* zobaczysz dane wyjściowe podobne do następującego przykładu w dzienniku przesyłania strumieniowego funkcji:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Zmienne środowiskowe

Aby uzyskać zmienną środowiskową lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable` , jak pokazano w poniższym przykładzie kodu:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
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
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W języku C# i innych językach .NET można użyć wzorca powiązania [bezwzględnego](https://en.wikipedia.org/wiki/Imperative_programming) , w przeciwieństwie do [*deklaratywnych*](https://en.wikipedia.org/wiki/Declarative_programming) powiązań w *function.jsna*. Bezwzględne powiązanie jest przydatne, gdy parametry powiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Za pomocą tego wzorca można powiązać z obsługiwanymi powiązaniami wejściowymi i wyjściowymi na bieżąco w kodzie funkcji.

Zdefiniuj bezwzględne powiązanie w następujący sposób:

- **Nie** dołączaj wpisu w *function.jsna* potrzeby żądanych, bezwzględnych powiązań.
- Przekaż parametr wejściowy [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) lub [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Użyj następującego wzorca języka C#, aby wykonać powiązanie danych.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` jest atrybutem platformy .NET, który definiuje powiązanie i `T` jest typem wejściowym lub wyjściowym obsługiwanym przez ten typ powiązania. `T` nie może być `out` typem parametru (na przykład `out JObject` ). Na przykład powiązanie danych wyjściowych tabeli Mobile Apps obsługuje [sześć typów wyjściowych](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale można używać tylko [ICollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) dla `T` .

### <a name="single-attribute-example"></a>Przykład pojedynczego atrybutu

Poniższy przykładowy kod tworzy [powiązanie danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob-output.md) z ścieżką obiektu BLOB, która jest zdefiniowana w czasie wykonywania, a następnie zapisuje ciąg do obiektu BLOB.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[Obiekt blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definiuje powiązanie danych wejściowych lub wyjściowych [magazynu obiektów BLOB](functions-bindings-storage-blob.md) , a parametr [TextWriter](/dotnet/api/system.io.textwriter) jest obsługiwanym typem powiązania.

### <a name="multiple-attribute-example"></a>Przykład wielu atrybutów

W poprzednim przykładzie jest pobierane ustawienie aplikacji dla głównych parametrów połączenia konta magazynu aplikacji funkcji (co to jest `AzureWebJobsStorage` ). Możesz określić niestandardowe ustawienie aplikacji do użycia dla konta magazynu, dodając [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()` . Użyj `Binder` parametru, a nie `IBinder` .  Na przykład:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

Poniższa tabela zawiera listę atrybutów .NET dla każdego typu powiązania i pakietów, w których zostały zdefiniowane.

> [!div class="mx-codeBreakAll"]
> | Wiązanie | Atrybut | Dodawanie odwołania |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAttributeTests.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Kolejka magazynu | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Magazyn obiektów blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabela magazynu | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Functions](functions-best-practices.md)
