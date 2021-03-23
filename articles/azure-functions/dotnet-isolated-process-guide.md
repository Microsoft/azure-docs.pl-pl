---
title: Przewodnik dotyczący procesu izolowanego platformy .NET dla programu .NET 5,0 w Azure Functions
description: Dowiedz się, jak używać procesu izolowanego od platformy .NET do uruchamiania funkcji języka C# na platformie .NET 5,0 w systemie Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: be11c32cf06b9873e10247d7ccc4a84133a6c688
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774936"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Przewodnik dotyczący uruchamiania funkcji na platformie .NET 5,0 na platformie Azure

Ten artykuł zawiera wprowadzenie do korzystania z języka C# do tworzenia izolowanych funkcji procesów .NET, które są uruchamiane w Azure Functions. Uruchamianie pozaprocesowe pozwala oddzielić kod funkcji z Azure Functions środowiska uruchomieniowego. Zapewnia również sposób tworzenia i uruchamiania funkcji przeznaczonych dla bieżącej wersji programu .NET 5,0. 

| Wprowadzenie | Pojęcia| Samples |
|--|--|--| 
| <ul><li>[Korzystanie z narzędzia Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Korzystanie z narzędzi wiersza polecenia](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Korzystanie z programu Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Opcje hostingu](functions-scale.md)</li><li>[Monitorowanie](functions-monitoring.md)</li> | <ul><li>[Przykłady referencyjne](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Jeśli nie musisz obsługiwać programu .NET 5,0 ani uruchamiać funkcji poza procesem, warto zamiast tego [utworzyć funkcje biblioteki klas języka C#](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Dlaczego proces izolowany od platformy .NET?

Wcześniej Azure Functions obsługuje tylko ściśle zintegrowane tryb dla usługi .NET Functions, które są uruchamiane [jako Biblioteka klas](functions-dotnet-class-library.md) w tym samym procesie co host. Ten tryb zapewnia ścisłą integrację między procesem hosta i funkcjami. Na przykład funkcje biblioteki klas .NET mogą udostępniać powiązania interfejsów API i typów. Ta integracja wymaga jednak ściślejszego sprzęgania między procesem hosta i funkcją .NET. Na przykład funkcje programu .NET działające w procesie są wymagane do uruchamiania w tej samej wersji platformy .NET, co środowisko uruchomieniowe funkcji. Aby umożliwić uruchamianie poza tymi ograniczeniami, można teraz wybrać uruchamianie w procesie izolowanym. Ta izolacja procesu umożliwia również opracowywanie funkcji korzystających z bieżących wersji platformy .NET (takich jak .NET 5,0), które nie są natywnie obsługiwane przez środowisko uruchomieniowe funkcji.

Ponieważ te funkcje są uruchamiane w oddzielnym procesie, istnieją pewne [różnice funkcji i funkcjonalności](#differences-with-net-class-library-functions) między izolowanymi aplikacjami funkcji i aplikacjami platformy .NET.

### <a name="benefits-of-running-out-of-process"></a>Zalety uruchamiania poza procesem

W przypadku uruchamiania poza procesem funkcje platformy .NET mogą korzystać z następujących korzyści: 

+ Mniej konfliktów: ponieważ funkcje są uruchamiane w osobnym procesie, zestawy używane w aplikacji nie będą powodować konfliktu z różnymi wersjami tych samych zestawów, które są używane przez proces hosta.  
+ Pełna kontrola procesu: kontrolujesz początkową aplikację i można kontrolować używane konfiguracje oraz uruchamiać oprogramowanie pośredniczące.
+ Wstrzykiwanie zależności: ponieważ masz pełną kontrolę nad procesem, możesz użyć bieżących zachowań platformy .NET do iniekcji zależności i dołączania oprogramowania pośredniczącego do aplikacji funkcji. 

## <a name="supported-versions"></a>Obsługiwane wersje

Jedyną wersją platformy .NET, która jest obecnie obsługiwana do uruchamiania poza procesem, jest .NET 5,0.

## <a name="net-isolated-project"></a>Projekt izolowany .NET

Projekt funkcji izolowanej .NET jest zasadniczo projektem aplikacji konsolowej .NET, który jest przeznaczony dla programu .NET 5,0. Poniżej przedstawiono podstawowe pliki wymagane w dowolnym projekcie izolowanym .NET:

+ [host.js](functions-host-json.md) pliku.
+ [local.settings.js](functions-run-local.md#local-settings-file) pliku.
+ Plik projektu C# (. csproj), który definiuje projekt i zależności.
+ Plik programu program. cs jest punktem wejścia dla aplikacji.

## <a name="package-references"></a>Odwołania do pakietu

Podczas uruchamiania poza procesem projekt platformy .NET używa unikatowego zestawu pakietów, który implementuje podstawowe funkcje i rozszerzenia powiązań. 

### <a name="core-packages"></a>Pakiety podstawowe 

Następujące pakiety są wymagane do uruchomienia funkcji .NET w procesie izolowanym:

+ [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft. Azure. Functions. Worker. Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Pakiety rozszerzeń

Ponieważ funkcje uruchamiane w procesie izolowanym .NET używają różnych typów powiązań, wymagają unikatowego zestawu pakietów rozszerzenia powiązania. 

Te pakiety rozszerzeń znajdują się w sekcji [Microsoft. Azure. Functions. Worker. Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Rozpoczęcie i konfiguracja 

W przypadku korzystania z funkcji izolowanych przez platformę .NET masz dostęp do uruchamiania aplikacji funkcji, która zwykle znajduje się w programie. cs. Użytkownik jest odpowiedzialny za tworzenie i uruchamianie własnego wystąpienia hosta. W związku z tym masz również bezpośredni dostęp do potoku konfiguracji aplikacji. Podczas uruchamiania poza procesem można znacznie łatwiej dodawać konfiguracje, wprowadzać zależności i uruchamiać własne oprogramowanie pośredniczące. 

Poniższy kod przedstawia przykład potoku [HostBuilder] :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

[HostBuilder] jest używany do kompilowania i zwracania w pełni zainicjowanego wystąpienia [IHost] , które można uruchomić asynchronicznie, aby uruchomić aplikację funkcji. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfigurowanie

Metoda [ConfigureFunctionsWorkerDefaults] służy do dodawania ustawień wymaganych przez aplikację funkcji do uruchamiania poza procesem, co obejmuje następujące funkcje:

+ Domyślny zestaw konwerterów.
+ Ustaw domyślną [JsonSerializerOptions] na ignorowanie wielkości liter w nazwach właściwości.
+ Integruj z rejestrowaniem Azure Functions.
+ Wyjściowe powiązania i funkcje oprogramowania.
+ Oprogramowanie pośredniczące wykonywania funkcji.
+ Domyślna obsługa gRPC. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Posiadanie dostępu do potoku konstruktora hosta oznacza, że można również ustawić każdą konfigurację specyficzną dla aplikacji podczas inicjacji. Możesz wywołać metodę [ConfigureAppConfiguration] na [HostBuilder] jeden lub więcej razy, aby dodać konfiguracje wymagane przez aplikację funkcji. Aby dowiedzieć się więcej o konfigurowaniu aplikacji, zobacz [Konfiguracja w ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

Te konfiguracje mają zastosowanie do aplikacji funkcji działającej w osobnym procesie. Aby wprowadzić zmiany do hosta funkcji lub wyzwalacza i konfiguracji powiązania, nadal trzeba będzie używać [host.jsw pliku](functions-host-json.md).   

### <a name="dependency-injection"></a>Wstrzykiwanie zależności

Wstrzykiwanie zależności jest uproszczone w porównaniu z bibliotekami klas .NET. Zamiast tworzyć klasy uruchamiania do rejestracji usług, wystarczy wywołać [ConfigureServices] na konstruktorze hosta i użyć metod rozszerzających [IServiceCollection] , aby wstrzyknąć określone usługi. 

Poniższy przykład wprowadza zależność usługi pojedynczej:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Aby dowiedzieć się więcej, zobacz [iniekcja zależności w ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Oprogramowanie pośredniczące

Platforma .NET izolowana również obsługuje rejestrację oprogramowania pośredniczącego, z wykorzystaniem modelu podobnego do tego, co istnieje w ASP.NET. Ten model daje możliwość iniekcji logiki do potoku wywołania oraz przed i po wykonaniu funkcji.

Metoda rozszerzenia [ConfigureFunctionsWorkerDefaults] ma Przeciążenie, które pozwala zarejestrować własne oprogramowanie pośredniczące, jak widać w poniższym przykładzie.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Aby zapoznać się z bardziej kompletnym przykładem użycia niestandardowych programów pośredniczących w aplikacji funkcji, zobacz [przykład odwołania do niestandardowego oprogramowania pośredniczącego](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Kontekst wykonywania

Platforma .NET izolowana przekazuje obiekt [kontekst funkcji] do metod funkcji. Ten obiekt umożliwia uzyskanie wystąpienia [ILogger] do zapisu w dziennikach przez wywołanie metody [getrejestratora] i dostarczenie `categoryName` ciągu. Aby dowiedzieć się więcej, zobacz [Rejestrowanie](#logging). 

## <a name="bindings"></a>Powiązania 

Powiązania są definiowane przy użyciu atrybutów metod, parametrów i zwracanych typów. Metoda funkcji to metoda z `Function` atrybutem i atrybut wyzwalacza zastosowany do parametru wejściowego, jak pokazano w następującym przykładzie:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Atrybut wyzwalacza określa typ wyzwalacza i wiąże dane wejściowe z parametrem metody. Poprzednia Przykładowa funkcja jest wyzwalana przez komunikat w kolejce, a komunikat kolejki jest przesyłany do metody w `myQueueItem` parametrze.

Ten `Function` atrybut oznacza metodę jako punkt wejścia funkcji. Nazwa musi być unikatowa w obrębie projektu, zaczynać się od litery i zawierać tylko litery, cyfry, `_` i `-` , maksymalnie 127 znaków. Szablony projektów często tworzą metodę o nazwie `Run` , ale nazwa metody może być dowolną prawidłową nazwą metody języka C#.

Ponieważ projekty izolowane .NET działają w osobnym procesie roboczym, powiązania nie mogą korzystać z zaawansowanych klas powiązań, takich jak `ICollector<T>` , `IAsyncCollector<T>` , i `CloudBlockBlob` . Nie istnieje również bezpośrednia pomoc techniczna dla typów dziedziczonych z bazowych zestawów SDK usług, takich jak [DocumentClient] i [BrokeredMessage]. Zamiast tego powiązania polegają na ciągach, tablicach i typach możliwych do serializacji, takich jak zwykłe stare obiekty klasy (POCOs). 

W przypadku wyzwalaczy HTTP należy użyć [HttpRequestData] i [HttpResponseData] , aby uzyskać dostęp do danych żądania i odpowiedzi. Dzieje się tak, ponieważ nie masz dostępu do oryginalnych obiektów żądania HTTP i odpowiedzi podczas uruchamiania poza procesem.

Aby zapoznać się z kompletnym zestawem przykładów referencyjnych na potrzeby używania wyzwalaczy i powiązań podczas uruchamiania pozaprocesowych, zobacz [przykład odwołania do rozszerzeń powiązań](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Powiązania wejściowe

Funkcja może mieć zero lub więcej powiązań wejściowych, które mogą przekazywać dane do funkcji. Takie jak wyzwalacze, powiązania wejściowe są definiowane przez zastosowanie atrybutu powiązania do parametru wejściowego. Gdy funkcja zostanie wykonana, środowisko uruchomieniowe próbuje pobrać dane określone w powiązaniu. Żądane dane często są zależne od informacji dostarczonych przez wyzwalacze przy użyciu parametrów powiązań.  

### <a name="output-bindings"></a>Powiązania wyjściowe

Aby zapisać do powiązania danych wyjściowych, należy zastosować atrybut powiązania danych wyjściowych do metody funkcji, która definiuje sposób zapisu w powiązanej usłudze. Wartość zwracana przez metodę jest zapisywana w powiązaniu danych wyjściowych. Na przykład poniższy przykład zapisuje wartość ciągu do kolejki komunikatów o nazwie przy `functiontesting2` użyciu powiązania danych wyjściowych:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Wiele powiązań wyjściowych

Dane zapisywane w powiązaniu wyjściowym są zawsze wartością zwracaną funkcji. Jeśli zachodzi potrzeba zapisu w więcej niż jednym powiązaniu wyjściowym, należy utworzyć niestandardowy typ zwracany. Ten typ zwracany musi mieć atrybut powiązania wyjściowego zastosowany do co najmniej jednej właściwości klasy. Poniższy przykład zapisuje dane do odpowiedzi HTTP i powiązania danych wyjściowych kolejki:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>HTTP trigger

Wyzwalacze protokołu HTTP tłumaczą przychodzące komunikaty żądania HTTP na obiekt [HttpRequestData] , który jest przesyłany do funkcji. Ten obiekt zawiera dane z żądania, w tym `Headers` ,,, `Cookies` `Identities` `URL` i opcjonalny komunikat `Body` . Ten obiekt jest reprezentacją obiektu żądania HTTP, a nie samego żądania. 

Podobnie funkcja zwraca obiekt [HttpReponseData], który zapewnia dane używane do tworzenia odpowiedzi HTTP, w tym komunikat `StatusCode` , `Headers` i opcjonalnie komunikat `Body` .  

Następujący kod jest wyzwalaczem HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Rejestrowanie

W odizolowanym środowisku .NET można zapisywać w dziennikach przy użyciu wystąpienia [ILogger] uzyskanego z obiektu [kontekst funkcji] przekazaną do funkcji. Wywołaj metodę [Getrejestratora] , przekazując wartość ciągu, która jest nazwą kategorii, w której zapisano dzienniki. Kategoria jest zwykle nazwą konkretnej funkcji, z której są zapisywane dzienniki. Aby dowiedzieć się więcej na temat kategorii, zobacz artykuł dotyczący [monitorowania](functions-monitoring.md#log-levels-and-categories). 

Poniższy przykład pokazuje, jak uzyskać [ILogger] i pisać dzienniki wewnątrz funkcji:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Różne metody [ILogger] umożliwiają pisanie różnych poziomów rejestrowania, takich jak `LogWarning` lub `LogError` . Aby dowiedzieć się więcej na temat poziomów dziennika, zobacz artykuł dotyczący [monitorowania](functions-monitoring.md#log-levels-and-categories).

[ILogger] jest również dostarczany podczas korzystania z [iniekcji zależności](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Różnice w funkcjach biblioteki klas .NET

W tej sekcji opisano bieżący stan funkcjonalności i różnice w działaniu na platformie .NET 5,0 w porównaniu z funkcjami biblioteki klas .NET uruchomionymi w procesie:

| Funkcja/zachowanie |  W procesie (.NET Core 3,1) | Pozaprocesowe (.NET 5,0) |
| ---- | ---- | ---- |
| Wersje .NET | LTS (.NET Core 3,1) | Current (.NET 5,0) |
| Pakiety podstawowe | [Microsoft. NET. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. Functions. Worker. Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Pakiety rozszerzeń powiązań | [Microsoft. Azure. WebJobs. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | W obszarze [Microsoft. Azure. Functions. Worker. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Rejestrowanie | [ILogger] przeszedł do funkcji | [ILogger] uzyskane z [kontekst funkcji] |
| Tokeny anulowania | [Obsługiwane](functions-dotnet-class-library.md#cancellation-tokens) | Nieobsługiwane |
| Powiązania wyjściowe | Parametry out | Wartości zwracane |
| Typy powiązań wyjściowych |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage]i inne typy specyficzne dla klienta | Typy proste, typy z możliwością serializacji JSON i tablice. |
| Wiele powiązań wyjściowych | Obsługiwane | [Obsługiwane](#multiple-output-bindings) |
| HTTP trigger | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Trwałe funkcje | [Obsługiwane](durable/durable-functions-overview.md) | Nieobsługiwane | 
| Bezwzględne powiązania | [Obsługiwane](functions-dotnet-class-library.md#binding-at-runtime) | Nieobsługiwane |
| function.jsw artefaktie | Wytwarza | Nie Wygenerowano |
| Konfigurowanie | [host.jsna](functions-host-json.md) | [host.js](functions-host-json.md) i inicjalizacji niestandardowej |
| Wstrzykiwanie zależności | [Obsługiwane](functions-dotnet-dependency-injection.md)  | [Obsługiwane](#dependency-injection) |
| Oprogramowanie pośredniczące | Nieobsługiwane | Obsługiwane |
| Czasy zimnego startu | Normalne | Dłużej, z powodu uruchomienia just-in-Time. Uruchamiaj system Linux zamiast systemu Windows, aby zmniejszyć potencjalne opóźnienia. |
| ReadyToRun | [Obsługiwane](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Znane problemy

Aby uzyskać informacje na temat obejścia problemów z uruchamianiem funkcji izolowanych procesów .NET, zobacz [stronę o znanych problemach](https://aka.ms/AAbh18e). Aby zgłosić problemy, [Utwórz problem w tym repozytorium GitHub](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose).  

## <a name="next-steps"></a>Następne kroki

+ [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)
+ [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Functions](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[Kontekst funkcji]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[Getrejestrator]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
