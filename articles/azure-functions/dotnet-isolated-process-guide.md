---
title: Przewodnik dotyczący procesu izolowanego platformy .NET dla programu .NET 5,0 w Azure Functions
description: Dowiedz się, jak używać procesu izolowanego od platformy .NET do uruchamiania funkcji języka C# na platformie .NET 5,0 w systemie Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: ab89c012c985afa8d7375ff94d0f55b0ea6941cc
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449462"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Przewodnik dotyczący uruchamiania funkcji na platformie .NET 5,0 na platformie Azure

_Obsługa platformy .NET 5,0 jest obecnie dostępna w wersji zapoznawczej._

Ten artykuł zawiera wprowadzenie do korzystania z języka C# do tworzenia izolowanych funkcji procesów .NET, które są uruchamiane w Azure Functions. Uruchamianie pozaprocesowe pozwala oddzielić kod funkcji z Azure Functions środowiska uruchomieniowego. Zapewnia również sposób tworzenia i uruchamiania funkcji przeznaczonych dla bieżącej wersji programu .NET 5,0. 

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
+ Plik Program.cs, który jest punktem wejścia dla aplikacji.

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

W przypadku korzystania z funkcji izolowanych przez platformę .NET masz dostęp do uruchamiania aplikacji funkcji, która zwykle znajduje się w Program.cs. Użytkownik jest odpowiedzialny za tworzenie i uruchamianie własnego wystąpienia hosta. W związku z tym masz również bezpośredni dostęp do potoku konfiguracji aplikacji. Można znacznie łatwiej wstrzyknąć zależności i uruchamiać oprogramowanie pośredniczące podczas uruchamiania poza procesem. 

Poniższy kod przedstawia przykład `HostBuilder` potoku:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

A służy `HostBuilder` do kompilowania i zwracania w pełni zainicjowanego `IHost` wystąpienia, które można uruchomić asynchronicznie, aby uruchomić aplikację funkcji. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfigurowanie

Mając dostęp do potoku konstruktora hosta, można ustawić każdą konfigurację specyficzną dla aplikacji podczas inicjacji. Te konfiguracje mają zastosowanie do aplikacji funkcji działającej w osobnym procesie. Aby wprowadzić zmiany do hosta funkcji lub wyzwalacza i konfiguracji powiązania, nadal trzeba będzie używać [host.jsw pliku](functions-host-json.md).      

Poniższy przykład pokazuje, jak dodać konfigurację `args` , które są odczytywane jako argumenty wiersza polecenia: 
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_app" :::

Ta `ConfigureAppConfiguration` Metoda służy do konfigurowania pozostałej części procesu kompilacji i aplikacji. Ten przykład używa również [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true), co ułatwia dodawanie wielu elementów konfiguracji. Ponieważ `ConfigureAppConfiguration` zwraca to samo wystąpienie [`IConfiguration`](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true) , można również wywołać je wiele razy, aby dodać wiele elementów konfiguracji. Dostęp do pełnego zestawu konfiguracji można uzyskać z obu programów [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) i [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

Aby dowiedzieć się więcej o konfiguracji, zobacz [Konfiguracja w ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

### <a name="dependency-injection"></a>Wstrzykiwanie zależności

Wstrzykiwanie zależności jest uproszczone w porównaniu z bibliotekami klas .NET. Zamiast konieczności tworzenia klasy uruchamiania do rejestracji usług, wystarczy wywołać `ConfigureServices` konstruktora hosta i użyć metod rozszerzenia w [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) celu dodania określonych usług. 

Poniższy przykład wprowadza zależność usługi pojedynczej:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Aby dowiedzieć się więcej, zobacz [iniekcja zależności w ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Oprogramowanie pośredniczące

Platforma .NET izolowana również obsługuje rejestrację oprogramowania pośredniczącego, z wykorzystaniem modelu podobnego do tego, co istnieje w ASP.NET. Ten model daje możliwość iniekcji logiki do potoku wywołania oraz przed i po wykonaniu funkcji.

Chociaż pełny zestaw rejestracji oprogramowania pośredniczącego interfejsów API nie jest jeszcze uwidoczniony, jest obsługiwana Rejestracja oprogramowania pośredniczącego i dodaliśmy przykład do aplikacji przykładowej w folderze oprogramowania pośredniczącego.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_middleware" :::

## <a name="execution-context"></a>Kontekst wykonywania

Platforma .NET izolowana przekazuje `FunctionContext` obiekt do metod funkcji. Ten obiekt umożliwia uzyskanie [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) wystąpienia do zapisu w dziennikach przez wywołanie `GetLogger` metody i dostarczenie `categoryName` ciągu. Aby dowiedzieć się więcej, zobacz [Rejestrowanie](#logging). 

## <a name="bindings"></a>Powiązania 

Powiązania są definiowane przy użyciu atrybutów metod, parametrów i zwracanych typów. Metoda funkcji to metoda z `Function` atrybutem wyzwalacza zastosowana do parametru wejściowego, jak pokazano w następującym przykładzie:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Atrybut wyzwalacza określa typ wyzwalacza i wiąże dane wejściowe z parametrem metody. Poprzednia Przykładowa funkcja jest wyzwalana przez komunikat w kolejce, a komunikat kolejki jest przesyłany do metody w `myQueueItem` parametrze.

Ten `Function` atrybut oznacza metodę jako punkt wejścia funkcji. Nazwa musi być unikatowa w obrębie projektu, zaczynać się od litery i zawierać tylko litery, cyfry, `_` i `-` , maksymalnie 127 znaków. Szablony projektów często tworzą metodę o nazwie `Run` , ale nazwa metody może być dowolną prawidłową nazwą metody języka C#.

Ponieważ projekty izolowane .NET działają w osobnym procesie roboczym, powiązania nie mogą korzystać z zaawansowanych klas powiązań, takich jak `ICollector<T>` , `IAsyncCollector<T>` , i `CloudBlockBlob` . Nie istnieje również bezpośrednia pomoc techniczna dla typów dziedziczonych z bazowych zestawów SDK usług, takich jak [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) i [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Zamiast tego powiązania polegają na ciągach, tablicach i typach możliwych do serializacji, takich jak zwykłe stare obiekty klasy (POCOs). 

W przypadku wyzwalaczy HTTP należy użyć `HttpRequestData` i `HttpResponseData` uzyskać dostęp do danych żądania i odpowiedzi. Dzieje się tak, ponieważ nie masz dostępu do oryginalnych obiektów żądania HTTP i odpowiedzi podczas uruchamiania poza procesem. 

### <a name="input-bindings"></a>Powiązania wejściowe

Funkcja może mieć zero lub więcej powiązań wejściowych, które mogą przekazywać dane do funkcji. Takie jak wyzwalacze, powiązania wejściowe są definiowane przez zastosowanie atrybutu powiązania do parametru wejściowego. Gdy funkcja zostanie wykonana, środowisko uruchomieniowe próbuje pobrać dane określone w powiązaniu. Żądane dane często są zależne od informacji dostarczonych przez wyzwalacze przy użyciu parametrów powiązań.  

### <a name="output-bindings"></a>Powiązania wyjściowe

Aby zapisać do powiązania danych wyjściowych, należy zastosować atrybut powiązania danych wyjściowych do metody funkcji, która definiuje sposób zapisu w powiązanej usłudze. Wartość zwracana przez metodę jest zapisywana w powiązaniu danych wyjściowych. Na przykład poniższy przykład zapisuje wartość ciągu do kolejki komunikatów o nazwie przy `functiontesting2` użyciu powiązania danych wyjściowych:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Wiele powiązań wyjściowych

Dane zapisywane w powiązaniu wyjściowym są zawsze wartością zwracaną funkcji. Jeśli zachodzi potrzeba zapisu w więcej niż jednym powiązaniu wyjściowym, należy utworzyć niestandardowy typ zwracany. Ten typ zwracany musi mieć atrybut powiązania wyjściowego zastosowany do co najmniej jednej właściwości klasy. Poniższy przykład zapisuje dane do odpowiedzi HTTP i powiązania danych wyjściowych kolejki:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>HTTP trigger

Wyzwalacze protokołu HTTP tłumaczą przychodzące komunikaty żądania HTTP na `HttpRequestData` obiekt, który jest przesyłany do funkcji. Ten obiekt zawiera dane z żądania, w tym `Headers` ,,, `Cookies` `Identities` `URL` i opcjonalny komunikat `Body` . Ten obiekt jest reprezentacją obiektu żądania HTTP, a nie samego żądania. 

Podobnie funkcja zwraca `HttpReponseData` obiekt, który dostarcza dane używane do tworzenia odpowiedzi HTTP, w tym komunikat `StatusCode` , `Headers` i opcjonalnie komunikat `Body` .  

Następujący kod jest wyzwalaczem HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Rejestrowanie

W środowisku .NET izolowanym można zapisywać w dziennikach przy użyciu [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) wystąpienia uzyskanego z `FunctionContext` obiektu przekazaną do funkcji. Wywołaj `GetLogger` metodę, przekazując wartość ciągu, która jest nazwą kategorii, w której zapisano dzienniki. Kategoria jest zwykle nazwą konkretnej funkcji, z której są zapisywane dzienniki. Aby dowiedzieć się więcej na temat kategorii, zobacz artykuł dotyczący [monitorowania](functions-monitoring.md#log-levels-and-categories). 

Poniższy przykład pokazuje, jak uzyskać `ILogger` i napisać dzienniki w funkcji:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Użyj różnych metod, `ILogger` Aby napisać różne poziomy dziennika, takie jak `LogWarning` lub `LogError` . Aby dowiedzieć się więcej na temat poziomów dziennika, zobacz artykuł dotyczący [monitorowania](functions-monitoring.md#log-levels-and-categories).

[`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true)Jest również dostarczany przy korzystaniu z [iniekcji zależności](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Różnice w funkcjach biblioteki klas .NET

W tej sekcji opisano bieżący stan funkcjonalności i różnice w działaniu na platformie .NET 5,0 w porównaniu z funkcjami biblioteki klas .NET uruchomionymi w procesie:

| Funkcja/zachowanie |  W procesie (.NET Core 3,1) | Pozaprocesowe (.NET 5,0) |
| ---- | ---- | ---- |
| Wersje .NET | LTS (.NET Core 3,1) | Current (.NET 5,0) |
| Pakiety podstawowe | [Microsoft. NET. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Pakiety rozszerzeń powiązań | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Systemie [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Rejestrowanie | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) przeszedł do funkcji | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) uzyskane z `FunctionContext` |
| Tokeny anulowania | [Obsługiwane](functions-dotnet-class-library.md#cancellation-tokens) | Nieobsługiwane |
| Powiązania wyjściowe | Parametry out | Wartości zwracane |
| Typy powiązań wyjściowych |  `IAsyncCollector`, [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true), [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)i inne typy specyficzne dla klienta | Typy proste, typy z możliwością serializacji JSON i tablice. |
| Wiele powiązań wyjściowych | Obsługiwane | [Obsługiwane](#multiple-output-bindings) |
| HTTP trigger | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Trwałe funkcje | [Obsługiwane](durable/durable-functions-overview.md) | Nieobsługiwane | 
| Bezwzględne powiązania | [Obsługiwane](functions-dotnet-class-library.md#binding-at-runtime) | Nieobsługiwane |
| function.jsw artefaktie | Wytwarza | Nie Wygenerowano |
| Konfigurowanie | [host.jsna](functions-host-json.md) | [host.js](functions-host-json.md) i [inicjalizacji niestandardowej](#configuration) |
| Wstrzykiwanie zależności | [Obsługiwane](functions-dotnet-dependency-injection.md)  | [Obsługiwane](#dependency-injection) |
| Oprogramowanie pośredniczące | Nieobsługiwane | [Obsługiwane](#middleware) |
| Czasy zimnego startu | Normalne | Dłużej, z powodu uruchomienia just-in-Time. Uruchamiaj system Linux zamiast systemu Windows, aby zmniejszyć potencjalne opóźnienia. |
| ReadyToRun | [Obsługiwane](functions-dotnet-class-library.md#readytorun) | _TBD_ |


## <a name="next-steps"></a>Następne kroki

+ [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)
+ [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Functions](functions-best-practices.md)
