---
title: Przewodnik po izolowanym procesie programu .NET dla programu .NET 5.0 w Azure Functions
description: Dowiedz się, jak używać izolowanego procesu platformy .NET do uruchamiania funkcji języka C# na platformie .NET 5.0 poza procesem na platformie Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 53f3c79886d26b20a584d747759176ea842741cf
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739282"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Przewodnik dotyczący uruchamiania funkcji na platformie .NET 5.0 na platformie Azure

Ten artykuł stanowi wprowadzenie do używania języka C# do opracowywania funkcji izolowanych procesów .NET, które są poza procesem w Azure Functions. Uruchamianie poza procesem umożliwia oddzielenie kodu funkcji od Azure Functions uruchomieniowego. Umożliwia również tworzenie i uruchamianie funkcji, które są kierowane do bieżącej wersji programu .NET 5.0. 

| Wprowadzenie | Pojęcia| Samples |
|--|--|--| 
| <ul><li>[Korzystanie z narzędzia Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Korzystanie z narzędzi wiersza polecenia](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Korzystanie z programu Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Opcje hostingu](functions-scale.md)</li><li>[Monitorowanie](functions-monitoring.md)</li> | <ul><li>[Przykłady referencyjne](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Jeśli nie musisz obsługiwać programu .NET 5.0 lub uruchamiać funkcji poza procesem, możesz zamiast tego opracować funkcje biblioteki klas [języka C#.](functions-dotnet-class-library.md)

## <a name="why-net-isolated-process"></a>Dlaczego izolowany proces .NET?

Wcześniej Azure Functions obsługiwany był tylko ściśle zintegrowany tryb dla funkcji .NET, które są uruchamiane jako biblioteka klas w tym samym procesie co host. [](functions-dotnet-class-library.md) Ten tryb zapewnia głęboką integrację między procesem hosta i funkcjami. Na przykład funkcje biblioteki klas .NET mogą współdzielić powiązania interfejsów API i typów. Jednak ta integracja wymaga również ściślejszego sprzężenia między procesem hosta i funkcją .NET. Na przykład funkcje .NET uruchomione w procesie muszą być uruchamiane w tej samej wersji środowiska .NET co środowisko uruchomieniowe usługi Functions. Aby umożliwić uruchamianie poza tymi ograniczeniami, można teraz wybrać uruchamianie w izolowanym procesie. Ta izolacja procesu umożliwia również tworzenie funkcji, które używają bieżących wersji .NET (takich jak .NET 5.0), które nie są natywnie obsługiwane przez środowisko uruchomieniowe usługi Functions.

Ponieważ te funkcje działają w osobnym [](#differences-with-net-class-library-functions) procesie, istnieją pewne różnice między funkcjami i funkcjami aplikacji funkcji izolowanych .NET i aplikacjami funkcji biblioteki klas .NET.

### <a name="benefits-of-running-out-of-process"></a>Zalety poza procesem

Podczas uruchamiania poza procesem funkcje programu .NET mogą korzystać z następujących korzyści: 

+ Mniej konfliktów: ponieważ funkcje są uruchamiane w osobnym procesie, zestawy używane w aplikacji nie będą w konflikcie z inną wersją tych samych zestawów, które są używane przez proces hosta.  
+ Pełna kontrola nad procesem: możesz kontrolować uruchamianie aplikacji i kontrolować używane konfiguracje oraz uruchomione oprogramowanie pośredniczące.
+ Wstrzykiwanie zależności: ponieważ masz pełną kontrolę nad procesem, możesz użyć bieżących zachowań .NET do wstrzykiwania zależności i dołączania oprogramowania pośredniczącego do aplikacji funkcji. 

## <a name="supported-versions"></a>Obsługiwane wersje

Jedyną wersją programu .NET, która jest obecnie obsługiwana do uruchamiania poza procesem, jest .NET 5.0.

## <a name="net-isolated-project"></a>Projekt izolowany .NET

Projekt funkcji izolowanej .NET jest zasadniczo projektem aplikacji konsolowej .NET przeznaczonym dla programu .NET 5.0. Poniżej przedstawiono podstawowe pliki wymagane w każdym izolowanym projekcie .NET:

+ [host.jsw pliku.](functions-host-json.md)
+ [local.settings.jsw pliku.](functions-run-local.md#local-settings-file)
+ Plik projektu w języku C# (csproj), który definiuje projekt i zależności.
+ Plik Program.cs, który jest punktem wejścia dla aplikacji.

## <a name="package-references"></a>Odwołania do pakietu

W przypadku uruchamiania poza procesem projekt .NET używa unikatowego zestawu pakietów, które implementują zarówno podstawowe funkcje, jak i rozszerzenia powiązań. 

### <a name="core-packages"></a>Pakiety podstawowe 

Następujące pakiety są wymagane do uruchamiania funkcji .NET w izolowanym procesie:

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Pakiety rozszerzeń

Ponieważ funkcje uruchamiane w izolowanym procesie .NET używają różnych typów powiązań, wymagają unikatowego zestawu pakietów rozszerzeń powiązań. 

Te pakiety rozszerzeń znajdują się w obszarze [Microsoft.Azure.Functions.Worker.Extensions.](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)

## <a name="start-up-and-configuration"></a>Uruchamianie i konfiguracja 

W przypadku korzystania z funkcji izolowanych .NET masz dostęp do uruchamiania aplikacji funkcji, która zazwyczaj znajduje się w programie Program.cs. Odpowiadasz za tworzenie i uruchamianie własnego wystąpienia hosta. W związku z tym masz również bezpośredni dostęp do potoku konfiguracji dla aplikacji. W przypadku wycześniania procesu można znacznie łatwiej dodawać konfiguracje, wstrzykiwać zależności i uruchamiać własne oprogramowanie pośredniczące. 

Poniższy kod przedstawia przykład potoku [HostBuilder:]

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Ten kod `using Microsoft.Extensions.DependencyInjection;` wymaga . 

[HostBuilder służy do] kompilowania i zwracania w pełni zainicjowanych wystąpień hosta [IHost] uruchamianych asynchronicznie w celu uruchomienia aplikacji funkcji. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfigurowanie

Metoda [ConfigureFunctionsWorkerDefaults] służy do dodawania ustawień wymaganych do uruchomienia poza procesem przez aplikację funkcji, co obejmuje następujące funkcje:

+ Domyślny zestaw konwerterów.
+ Ustaw domyślną [wartość JsonSerializerOptions,] aby ignorować wielkością w nazwach właściwości.
+ Integracja z Azure Functions rejestrowaniem danych.
+ Wyjściowe oprogramowanie pośredniczące i funkcje powiązania.
+ Oprogramowanie pośredniczące wykonywania funkcji.
+ Domyślna obsługa gRPC. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Dostęp do potoku konstruktora hosta oznacza, że podczas inicjowania można również ustawić konfiguracje specyficzne dla aplikacji. Możesz wywołać metodę [ConfigureAppConfiguration] w [węźle HostBuilder co] najmniej raz, aby dodać konfiguracje wymagane przez aplikację funkcji. Aby dowiedzieć się więcej na temat konfiguracji aplikacji, zobacz Configuration in ASP.NET Core (Konfiguracja [w programie ASP.NET Core).](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true) 

Te konfiguracje dotyczą aplikacji funkcji uruchomionej w osobnym procesie. Aby wprowadzić zmiany w hoście funkcji lub w konfiguracji wyzwalacza i powiązania, nadal musisz użyćhost.js[ pliku](functions-host-json.md).   

### <a name="dependency-injection"></a>Wstrzykiwanie zależności

Wstrzykiwanie zależności jest uproszczone w porównaniu z bibliotekami klas .NET. Zamiast tworzyć klasę startową w celu zarejestrowania usług, wystarczy wywołać [usługę ConfigureServices] w konstruktorze hosta i użyć metod rozszerzenia w klasie [IServiceCollection,] aby wstrzyknąć określone usługi. 

Poniższy przykład wprowadza pojedynczą zależność usługi:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Ten kod `using Microsoft.Extensions.DependencyInjection;` wymaga . Aby dowiedzieć się więcej, zobacz [Wstrzykiwanie zależności w ASP.NET Core.](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)

### <a name="middleware"></a>Oprogramowanie pośredniczące

Program .NET Isolated obsługuje również rejestrację oprogramowania pośredniczącego, ponownie przy użyciu modelu podobnego do tego, który istnieje w ASP.NET. Ten model umożliwia wstrzykiwanie logiki do potoku wywołania oraz przed i po wykonaniu funkcji.

Metoda [rozszerzenia ConfigureFunctionsWorkerDefaults] ma przeciążenie, które umożliwia zarejestrowanie własnego oprogramowania pośredniczącego, jak widać w poniższym przykładzie.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Aby uzyskać bardziej kompletny przykład użycia niestandardowego oprogramowania pośredniczącego w aplikacji funkcji, zobacz przykład [niestandardowego oprogramowania pośredniczącego](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Kontekst wykonywania

Izolowany obiekt .NET przekazuje [obiekt FunctionContext] do metod funkcji. Ten obiekt umożliwia uzyskiwanie wystąpienia [ILogger] do zapisu w dziennikach przez wywołanie [metody GetLogger] i dostarczenie `categoryName` ciągu. Aby dowiedzieć się więcej, zobacz [Rejestrowanie](#logging). 

## <a name="bindings"></a>Powiązania 

Powiązania są definiowane przy użyciu atrybutów metod, parametrów i typów zwracanych. Metoda funkcji to metoda z atrybutem i atrybutem wyzwalacza zastosowanym do `Function` parametru wejściowego, jak pokazano w poniższym przykładzie:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Atrybut wyzwalacza określa typ wyzwalacza i wiąże dane wejściowe z parametrem metody. Poprzedni przykładowa funkcja jest wyzwalana przez komunikat w kolejce, a komunikat w kolejce jest przekazywany do metody w `myQueueItem` parametrze .

Atrybut `Function` oznacza metodę jako punkt wejścia funkcji. Nazwa musi być unikatowa w obrębie projektu, rozpoczynać się od litery i zawierać tylko litery, cyfry, i , o długości `_` `-` do 127 znaków. Szablony projektów często tworzą metodę o nazwie `Run` , ale nazwa metody może być dowolną prawidłową nazwą metody języka C#.

Ponieważ izolowane projekty .NET są uruchamiane w oddzielnym procesie procesu roboczego, powiązania nie mogą korzystać z rozbudowanych klas powiązań, takich jak `ICollector<T>` `IAsyncCollector<T>` , i `CloudBlockBlob` . Nie ma również bezpośredniej obsługi typów dziedziczonych z podstawowych zestawów SDK usług, takich jak [DocumentClient] i [BrokeredMessage.] Zamiast tego powiązania polegają na ciągach, tablicach i typach, które można serializuje, takich jak zwykłe stare obiekty klas (POCO). 

W przypadku wyzwalaczy HTTP należy użyć funkcji [HttpRequestData] i [HttpResponseData,] aby uzyskać dostęp do danych żądań i odpowiedzi. Wynika to z tego, że nie masz dostępu do oryginalnych obiektów żądań i odpowiedzi HTTP w przypadku uruchomienia poza procesem.

Aby uzyskać kompletny zestaw przykładów referencyjnych na temat używania wyzwalaczy i powiązań podczas uruchamiania poza procesem, zobacz przykład referencyjny rozszerzenia [powiązań](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Powiązania wejściowe

Funkcja może mieć zero lub więcej powiązań wejściowych, które mogą przekazywać dane do funkcji. Podobnie jak wyzwalacze powiązania wejściowe są definiowane przez zastosowanie atrybutu powiązania do parametru wejściowego. Gdy funkcja jest wykonywana, środowisko uruchomieniowe próbuje pobrać dane określone w powiązaniu. Żądane dane często zależą od informacji dostarczonych przez wyzwalacz przy użyciu parametrów powiązania.  

### <a name="output-bindings"></a>Powiązania wyjściowe

Aby zapisać w powiązaniu danych wyjściowych, należy zastosować atrybut powiązania danych wyjściowych do metody funkcji, która zdefiniuje sposób zapisu w powiązanej usłudze. Wartość zwrócona przez metodę jest zapisywana w powiązaniu danych wyjściowych. Na przykład poniższy przykład zapisuje wartość ciągu do kolejki komunikatów o nazwie `functiontesting2` przy użyciu powiązania wyjściowego:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Wiele powiązań wyjściowych

Dane zapisane w powiązaniu wyjściowym są zawsze wartością zwracaną przez funkcję. Jeśli musisz zapisać w więcej niż jednym powiązaniu danych wyjściowych, musisz utworzyć niestandardowy typ zwracany. Ten zwracany typ musi mieć atrybut powiązania danych wyjściowych zastosowany do co najmniej jednej właściwości klasy. Poniższy przykład z wyzwalacza HTTP zapisuje zarówno odpowiedź HTTP, jak i powiązanie wyjściowe kolejki:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

Odpowiedź z wyzwalacza HTTP jest zawsze uważana za dane wyjściowe, więc atrybut wartości zwracanej nie jest wymagany.

### <a name="http-trigger"></a>HTTP trigger

Wyzwalacze HTTP tłumaczą przychodzący komunikat żądania HTTP na obiekt [HttpRequestData,] który jest przekazywany do funkcji. Ten obiekt udostępnia dane z żądania, w tym `Headers` , , , i `Cookies` `Identities` `URL` opcjonalnie komunikat `Body` . Ten obiekt jest reprezentacją obiektu żądania HTTP, a nie samego żądania. 

Podobnie funkcja zwraca obiekt [HttpResponseData,] który udostępnia dane używane do tworzenia odpowiedzi HTTP, w tym komunikat , i opcjonalnie `StatusCode` komunikat `Headers` `Body` .  

Poniższy kod jest wyzwalaczem HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Rejestrowanie

W izolowanym środowisku .NET można zapisywać w dziennikach przy użyciu wystąpienia [ILogger] uzyskanego z obiektu [FunctionContext] przekazanego do funkcji. Wywołaj [metodę GetLogger,] przekazując wartość ciągu, która jest nazwą kategorii, w której są zapisywane dzienniki. Kategoria jest zazwyczaj nazwą określonej funkcji, z której są zapisywane dzienniki. Aby dowiedzieć się więcej na temat kategorii, zobacz [artykuł monitorowania](functions-monitoring.md#log-levels-and-categories). 

W poniższym przykładzie pokazano, jak uzyskać [ILogger] i zapisać dzienniki wewnątrz funkcji:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Użyj różnych metod [ILogger] do zapisu różnych poziomów dziennika, takich jak `LogWarning` lub `LogError` . Aby dowiedzieć się więcej na temat poziomów dzienników, zobacz [artykuł monitorowania](functions-monitoring.md#log-levels-and-categories).

W [przypadku używania] iniekcji zależności jest również dostępny rejestrator [.](#dependency-injection)

## <a name="differences-with-net-class-library-functions"></a>Różnice w funkcjach biblioteki klas .NET

W tej sekcji opisano bieżący stan różnic funkcjonalnych i behawioralnych działających na out-of-process w programie .NET 5.0 w porównaniu z funkcjami biblioteki klas .NET uruchomionymi w procesie:

| Funkcja/zachowanie |  W procesie (.NET Core 3.1) | Poza procesem (.NET 5.0) |
| ---- | ---- | ---- |
| Wersje programu .NET | LTS (.NET Core 3.1) | Current (.NET 5.0) |
| Pakiety podstawowe | [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Wiązanie pakietów rozszerzeń | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | W [obszarze Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Rejestrowanie | [ILogger] przekazany do funkcji | [ILogger] uzyskany z [funkcji FunctionContext] |
| Tokeny anulowania | [Obsługiwane](functions-dotnet-class-library.md#cancellation-tokens) | Nieobsługiwane |
| Powiązania wyjściowe | Parametry out | Wartości zwracane |
| Typy powiązań wyjściowych |  `IAsyncCollector`, [DocumentClient,] [BrokeredMessage]i inne typy specyficzne dla klienta | Typy proste, typy JSON, które można serializuje, i tablice. |
| Wiele powiązań wyjściowych | Obsługiwane | [Obsługiwane](#multiple-output-bindings) |
| HTTP trigger | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Trwałe funkcje | [Obsługiwane](durable/durable-functions-overview.md) | Nieobsługiwane | 
| Powiązania imperatywne | [Obsługiwane](functions-dotnet-class-library.md#binding-at-runtime) | Nieobsługiwane |
| function.jsartefaktu | Generowane | Nie wygenerowano |
| Konfigurowanie | [host.jswł.](functions-host-json.md) | [host.jsi](functions-host-json.md) inicjowanie niestandardowe |
| Wstrzykiwanie zależności | [Obsługiwane](functions-dotnet-dependency-injection.md)  | [Obsługiwane](#dependency-injection) |
| Oprogramowanie pośredniczące | Nieobsługiwane | Obsługiwane |
| Godziny zimnego rozpoczęcia | Typowe | Dłuższy z powodu uruchamiania just in time. Uruchom w systemie Linux zamiast w systemie Windows, aby zmniejszyć potencjalne opóźnienia. |
| ReadyToRun | [Obsługiwane](functions-dotnet-class-library.md#readytorun) | _Tbd_ |

## <a name="known-issues"></a>Znane problemy

Aby uzyskać informacje na temat obejść, aby poznać problemy z uruchamianiem funkcji procesu izolowanego .NET, zobacz [tę stronę znanych problemów](https://aka.ms/AAbh18e). Aby zgłosić problemy, [utwórz problem w tym repozytorium GitHub.](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)  

## <a name="next-steps"></a>Następne kroki

+ [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)
+ [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Functions](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[Konfigurowanie usług]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[Objectresult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
