---
title: Testowanie jednostek Durable Functions platformy Azure
description: Dowiedz się, jak jednostkowe Durable Functions testowe.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: fe5a25e0296eb183ef2426e12f7bdee35633ec78
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076636"
---
# <a name="durable-functions-unit-testing"></a>Testowanie jednostkowe Durable Functions

Testowanie jednostkowe jest ważną częścią nowoczesnych rozwiązań do tworzenia oprogramowania. Testy jednostkowe weryfikują zachowanie logiki biznesowej i chronią przed wprowadzaniem niezauważalnych zmian w przyszłości. Durable Functions można łatwo zwiększyć złożoność, aby zapewnić testy jednostkowe, aby uniknąć istotnych zmian. W poniższych sekcjach wyjaśniono, jak przeprowadzić test jednostkowy trzech typów funkcji — klienta aranżacji, programu Orchestrator i funkcji działania.

> [!NOTE]
> Ten artykuł zawiera wskazówki dotyczące testowania jednostkowego dla aplikacji Durable Functions przeznaczonych dla Durable Functions 2. x. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Przykłady w tym artykule wymagają znajomości następujących pojęć i struktur:

* Testowanie jednostek

* Trwałe funkcje

* [xUnit](https://github.com/xunit/xunit) — struktura testowania

* [MOQ](https://github.com/moq/moq4) — struktura

## <a name="base-classes-for-mocking"></a>Klasy bazowe do imitacji

Symulacja jest obsługiwana za pośrednictwem następującego interfejsu:

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentityclient) i [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentitycontext)

Te interfejsy mogą być używane z różnymi wyzwalaczami i powiązaniami obsługiwanymi przez Durable Functions. Podczas wykonywania Azure Functions środowisko uruchomieniowe Functions uruchomi kod funkcji z konkretną implementacją tych interfejsów. Do testowania jednostkowego można przekazać wbudowaną wersję tych interfejsów w celu przetestowania logiki biznesowej.

## <a name="unit-testing-trigger-functions"></a>Funkcje wyzwalacza testów jednostkowych

W tej sekcji test jednostkowy będzie sprawdzał logikę następującej funkcji wyzwalacza HTTP do uruchamiania nowych aranżacji.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Zadanie testów jednostkowych będzie służyć do weryfikowania wartości `Retry-After` nagłówka podanego w ładunku odpowiedzi. Dlatego test jednostkowy zanotuje niektóre `IDurableClient` metody, aby zapewnić przewidywalne zachowanie.

Najpierw używamy struktury imitacji ([MOQ](https://github.com/moq/moq4) w tym przypadku) do zamakietowania `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Chociaż można zasymulować interfejsy przez bezpośrednie zaimplementowanie interfejsu jako klasy, struktury wizualizacji upraszczają proces na różne sposoby. Na przykład, jeśli nowa metoda zostanie dodana do interfejsu w różnych wersjach pomocniczych, MOQ nie będzie wymagać żadnych zmian kodu w przeciwieństwie do konkretnych implementacji.

Następnie `StartNewAsync` Metoda jest wbudowana w celu zwrócenia dobrze znanego identyfikatora wystąpienia.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

Następnie `CreateCheckStatusResponse` jest makieta, aby zawsze zwracała pustą odpowiedź HTTP 200.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

`ILogger` jest również makietą:

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

Teraz `Run` Metoda jest wywoływana z testu jednostkowego:

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 Ostatnim krokiem jest porównanie danych wyjściowych o oczekiwanej wartości:

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po połączeniu wszystkich kroków test jednostkowy będzie miał następujący kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funkcje programu Orchestrator do testowania jednostek

Funkcje programu Orchestrator są jeszcze bardziej interesujące w przypadku testów jednostkowych, ponieważ zazwyczaj mają znacznie większą logikę biznesową.

W tej sekcji testy jednostkowe będą sprawdzać poprawność danych wyjściowych `E1_HelloSequence` funkcji programu Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kod testu jednostkowego rozpocznie się od utworzenia makiety:

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Następnie wywołania metody działania zostaną zamakietne:

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Następny test jednostkowy wywoła `HelloSequence.Run` metodę:

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

A wreszcie dane wyjściowe zostaną zweryfikowane:

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Po połączeniu wszystkich kroków test jednostkowy będzie miał następujący kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funkcje działania testowania jednostkowego

Funkcje działania mogą być testowane jednostkowo w taki sam sposób jak w przypadku funkcji nietrwałych.

W tej sekcji test jednostkowy sprawdzi zachowanie `E1_SayHello` funkcji działania:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

A testy jednostkowe sprawdzają format danych wyjściowych. Testy jednostkowe mogą używać typów parametrów bezpośrednio lub makiety `IDurableActivityContext` klasy:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Dowiedz się więcej o MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
