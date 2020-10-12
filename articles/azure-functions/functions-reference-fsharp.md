---
title: 'Dokumentacja dla deweloperów Azure Functions F #'
description: 'Dowiedz się, jak opracowywać Azure Functions przy użyciu skryptu F #.'
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088023"
---
# <a name="azure-functions-f-developer-reference"></a>Dokumentacja dla deweloperów Azure Functions F #

Język F # dla Azure Functions to rozwiązanie umożliwiające łatwe uruchamianie małych fragmentów kodu lub "funkcji" w chmurze. Dane są przesyłane do funkcji języka F # za pośrednictwem argumentów funkcji. Nazwy argumentów są określone w `function.json` , a istnieją wstępnie zdefiniowane nazwy do uzyskiwania dostępu do takich elementów jak Rejestrator funkcji i tokeny anulowania. 

>[!IMPORTANT]
>Skrypt języka F # (. FSX) jest obsługiwany tylko w [wersji 1. x](functions-versions.md#creating-1x-apps) środowiska uruchomieniowego Azure Functions. Jeśli chcesz użyć języka F # z wersją 2. x i nowszymi wersjami środowiska uruchomieniowego, musisz użyć prekompilowanego projektu biblioteki klas F # (. FS). Tworzenie projektu biblioteki klas F # i zarządzanie nim przy użyciu programu Visual Studio, tak jak w przypadku [projektu biblioteki klas języka C#](functions-dotnet-class-library.md). Więcej informacji o wersjach funkcji znajduje się w temacie [Azure Functions wersje środowiska uruchomieniowego — Omówienie](functions-versions.md).

W tym artykule przyjęto założenie, że już odczytano [informacje dotyczące deweloperów Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Jak działa FSX
`.fsx`Plik to skrypt języka F #. Można go traktować jako projekt F #, który znajduje się w pojedynczym pliku. Plik zawiera kod dla programu (w tym przypadku funkcję platformy Azure) i dyrektywy dotyczące zarządzania zależnościami.

Gdy używasz `.fsx` dla funkcji platformy Azure, często wymagane zestawy są automatycznie dołączane, co pozwala skupić się na funkcji, a nie w kodzie "standardowy".

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu skryptu języka F # wygląda następująco:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Istnieje [host.jsudostępnione w](functions-host-json.md) pliku, którego można użyć do skonfigurowania aplikacji funkcji. Każda funkcja ma własny plik kodu (. FSX) i plik konfiguracji powiązania (function.json).

Rozszerzenia powiązań wymagane w [wersji 2. x i nowszych wersjach](functions-versions.md) środowiska uruchomieniowego Functions są zdefiniowane w `extensions.csproj` pliku z rzeczywistymi plikami biblioteki w `bin` folderze. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami
Każde powiązanie obsługuje jakiś zestaw argumentów, zgodnie z opisem w temacie [Azure Functions wyzwalacze i powiązania deweloperów](functions-triggers-bindings.md). Na przykład jeden z powiązań argumentów obsługiwanych przez wyzwalacz obiektu BLOB to POCO, który można wyrazić przy użyciu rekordu języka F #. Na przykład:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Funkcja języka F # platformy Azure będzie mieć jeden lub więcej argumentów. Gdy będziemy mówić o Azure Functions argumentach, odwołująmy się do argumentów *wejściowych* i argumentów *danych wyjściowych* . Argument wejściowy jest dokładnie to, co brzmi: dane wejściowe do funkcji platformy Azure w języku F #. Argument *wyjściowy* jest modyfikowalnymi danymi lub `byref<>` argumentem, który służy jako sposób przekazywania danych z funkcji *out* .

W powyższym przykładzie `blob` jest argumentem wejściowym i `output` jest argumentem wyjściowym. Zwróć uwagę, że `byref<>` została użyta `output` (nie ma potrzeby dodawania `[<Out>]` adnotacji). Użycie `byref<>` typu umożliwia funkcji zmianę rekordu lub obiektu, do którego odwołuje się argument.

Gdy rekord języka F # jest używany jako typ danych wejściowych, definicja rekordu musi być oznaczona przy użyciu, `[<CLIMutable>]` Aby zezwolić Azure Functions Framework na ustawianie pól odpowiednio przed przekazaniem rekordu do funkcji. W obszarze okapu program `[<CLIMutable>]` generuje metody ustawiające właściwości rekordu. Na przykład:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Klasy F # można również użyć dla obu argumentów i. W przypadku klasy właściwości zazwyczaj będą wymagały metod pobierających i setter. Na przykład:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Rejestrowanie
Aby zalogować dane wyjściowe do [dzienników przesyłania strumieniowego](../app-service/troubleshoot-diagnostic-logs.md) w języku F #, funkcja powinna przyjmować argument typu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). W celu zapewnienia spójności zaleca się, aby ten argument miał nazwę `log` . Na przykład:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
`async`Można użyć przepływu pracy, ale wynik musi zwrócić `Task` . Można to zrobić za pomocą programu `Async.StartAsTask` , na przykład:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token anulowania
Jeśli funkcja musi bezpiecznie obsłużyć zamykanie, można nadać jej [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument. Można to połączyć za pomocą programu `async` , na przykład:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importowanie przestrzeni nazw
Przestrzenie nazw można otwierać w zwykły sposób:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące obszary nazw są automatycznie otwierane:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odwołujące się do zestawów zewnętrznych
Podobnie odwołania do zestawów struktury można dodać do `#r "AssemblyName"` dyrektywy.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingu Azure Functions:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Ponadto następujące zestawy są specjalne i mogą być przywoływane przez prostą literę (np. `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Jeśli musisz odwołać się do zestawu prywatnego, możesz przekazać plik zestawu do `bin` folderu względem funkcji i odwołać się do niego przy użyciu nazwy pliku (np.  `#r "MyAssembly.dll"`). Informacje o sposobach przekazywania plików do folderu funkcji znajdują się w poniższej sekcji dotyczącej zarządzania pakietami.

## <a name="editor-prelude"></a>Preludium edytora
Edytor obsługujący usługi kompilatora F # nie ma informacji o obszarach nazw i zestawach, które Azure Functions automatycznie dołączane. W związku z tym może być przydatne do uwzględnienia Preludium, który ułatwia edytorowi znalezienie zestawów, z których korzystasz, oraz do jawnie otwartych przestrzeni nazw. Na przykład:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Gdy Azure Functions wykonuje swój kod, przetwarza Źródło ze `COMPILED` zdefiniowaną, więc Edytor Preludium zostanie zignorowany.

<a name="package"></a>

## <a name="package-management"></a>Zarządzanie pakietami
Aby użyć pakietów NuGet w funkcji języka F #, Dodaj `project.json` plik do folderu funkcji w systemie plików aplikacji funkcji. Oto przykładowy `project.json` plik, który dodaje odwołanie do pakietu NuGet do `Microsoft.ProjectOxford.Face` wersji 1.1.0:

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

Obsługiwane są tylko .NET Framework 4,6, dlatego upewnij się, że `project.json` plik określa `net46` , jak pokazano tutaj.

Podczas przekazywania `project.json` pliku środowisko uruchomieniowe pobiera pakiety i automatycznie dodaje odwołania do zestawów pakietów. Nie musisz dodawać `#r "AssemblyName"` dyrektyw. Po prostu Dodaj wymagane `open` instrukcje do `.fsx` pliku.

Możesz chcieć umieścić automatycznie odwołujące się do zestawów w edytorze Preludium, aby ulepszyć interakcję edytora z usługami kompilacji języka F #.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Jak dodać `project.json` plik do funkcji platformy Azure
1. Zacznij od upewnienia się, że aplikacja funkcji działa, którą można wykonać, otwierając funkcję w Azure Portal. Zapewnia to również dostęp do dzienników przesyłania strumieniowego, w których będą wyświetlane dane wyjściowe instalacji pakietu.
2. Aby przekazać `project.json` plik, należy użyć jednej z metod opisanych w artykule [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). Jeśli używasz [ciągłego wdrażania dla Azure Functions](functions-continuous-deployment.md), możesz dodać `project.json` plik do gałęzi tymczasowej, aby eksperymentować z nim przed dodaniem go do rozgałęzienia wdrożenia.
3. Po `project.json` dodaniu pliku zostaną wyświetlone dane wyjściowe podobne do następującego przykładu w dzienniku przesyłania strumieniowego funkcji:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Zmienne środowiskowe
Aby uzyskać zmienną środowiskową lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable` na przykład:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Używanie kodu. FSX
Możesz użyć kodu z innych `.fsx` plików za pomocą `#load` dyrektywy. Na przykład:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Ścieżki do dyrektywy odnoszą się `#load` do lokalizacji `.fsx` pliku.

* `#load "logger.fsx"` ładuje plik znajdujący się w folderze funkcji.
* `#load "package\logger.fsx"` ładuje plik znajdujący się w `package` folderze w folderze funkcji.
* `#load "..\shared\mylogger.fsx"` ładuje plik znajdujący się w `shared` folderze na tym samym poziomie co folder funkcji, czyli bezpośrednio w `wwwroot` .

`#load`Dyrektywa działa tylko z `.fsx` plikami (F # Script), a nie z `.fs` plikami.

## <a name="next-steps"></a>Następne kroki
Więcej informacji można znaleźć w następujących zasobach:

* [Przewodnik języka F#](/dotnet/articles/fsharp/index)
* [Najlepsze rozwiązania dotyczące Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* [Testowanie Azure Functions](functions-test-a-function.md)
* [Skalowanie Azure Functions](functions-scale.md)
