---
title: Testowanie jednostek Durable Functions platformy Azure
description: Dowiedz się, jak jednostkowe Durable Functions testowe.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 7786a0a2e2d31086e1938b70e63fe2374e16fe7f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601360"
---
# <a name="durable-functions-unit-testing"></a>Testowanie jednostkowe Durable Functions

Testowanie jednostkowe jest ważną częścią nowoczesnych rozwiązań do tworzenia oprogramowania. Testy jednostkowe weryfikują zachowanie logiki biznesowej i chronią przed wprowadzaniem niezauważalnych zmian w przyszłości. Durable Functions można łatwo zwiększyć złożoność, aby zapewnić testy jednostkowe, aby uniknąć istotnych zmian. W poniższych sekcjach wyjaśniono, jak przeprowadzić test jednostkowy trzech typów funkcji — klienta aranżacji, programu Orchestrator i funkcji działania.

> [!NOTE]
> Ten artykuł zawiera wskazówki dotyczące testowania jednostkowego dla aplikacji Durable Functions przeznaczonych dla Durable Functions 1. x. Nie została jeszcze zaktualizowana w celu uwzględnienia zmian wprowadzonych w Durable Functions 2. x. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Przykłady w tym artykule wymagają znajomości następujących pojęć i struktur:

* Testowanie jednostek

* Trwałe funkcje

* [xUnit](https://github.com/xunit/xunit) — struktura testowania

* [MOQ](https://github.com/moq/moq4) — struktura

## <a name="base-classes-for-mocking"></a>Klasy bazowe do imitacji

Symulacja jest obsługiwana przez trzy klasy abstrakcyjne w Durable Functions 1. x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Te klasy są klasami podstawowymi dla `DurableOrchestrationClient` , `DurableOrchestrationContext` i, `DurableActivityContext` które definiują klienta aranżacji, program Orchestrator i metody działania. Makiety spowodują ustawienie oczekiwanego zachowania dla metod klasy bazowej, aby test jednostkowy mógł zweryfikować logikę biznesową. Istnieje dwuetapowy przepływ pracy służący do testowania jednostek logiki biznesowej w kliencie aranżacji i w programie Orchestrator:

1. Użyj klas bazowych zamiast konkretnej implementacji podczas definiowania sygnatury funkcji klienta aranżacji i programu Orchestrator.
2. W testach jednostkowych Zanotuj zachowanie klas podstawowych i sprawdź logikę biznesową.

Więcej szczegółów znajduje się w poniższych sekcjach dotyczących funkcji testowych, które korzystają z powiązania klienta aranżacji i powiązania wyzwalacza programu Orchestrator.

## <a name="unit-testing-trigger-functions"></a>Funkcje wyzwalacza testów jednostkowych

W tej sekcji test jednostkowy będzie sprawdzał logikę następującej funkcji wyzwalacza HTTP do uruchamiania nowych aranżacji.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Zadanie testów jednostkowych będzie służyć do weryfikowania wartości `Retry-After` nagłówka podanego w ładunku odpowiedzi. Dlatego test jednostkowy zanotuje niektóre `DurableOrchestrationClientBase` metody, aby zapewnić przewidywalne zachowanie.

Najpierw jest wymagana makieta klasy bazowej `DurableOrchestrationClientBase` . Makieta może być nową klasą, która implementuje `DurableOrchestrationClientBase` . Jednak użycie struktury imitacji, takiej jak [MOQ](https://github.com/moq/moq4) upraszcza proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Następnie `StartNewAsync` Metoda jest wbudowana w celu zwrócenia dobrze znanego identyfikatora wystąpienia.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Następnie `CreateCheckStatusResponse` jest makieta, aby zawsze zwracała pustą odpowiedź HTTP 200.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
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
        durableOrchestrationClientBaseMock.Object,
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
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
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

A testy jednostkowe sprawdzają format danych wyjściowych. Testy jednostkowe mogą używać typów parametrów bezpośrednio lub makiety `DurableActivityContextBase` klasy:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Dowiedz się więcej o MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
