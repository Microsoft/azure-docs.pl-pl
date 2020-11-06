---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 39c0556350482e171234a3ff9dce0c16ed88d110
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93406785"
---
Błędy wywoływane w Azure Functions mogą pochodzić z jednego z następujących źródeł:

- Korzystanie z wbudowanych [wyzwalaczy Azure Functions i powiązań](..\articles\azure-functions\functions-triggers-bindings.md)
- Wywołania interfejsów API podstawowych usług platformy Azure
- Wywołania punktów końcowych REST
- Wywołania bibliotek klienckich, pakietów lub interfejsów API innych firm

Poniższe praktyki dotyczące obsługi błędów są ważne, aby uniknąć utraty danych lub brakujących komunikatów. Zalecane praktyki obsługi błędów obejmują następujące akcje:

- [Włącz usługę Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Użyj strukturalnej obsługi błędów](#use-structured-error-handling)
- [Projektuj dla idempotentności](../articles/azure-functions/functions-idempotent.md)
- [Zaimplementuj zasady ponawiania](#retry-policies-preview) (w razie potrzeby)

### <a name="use-structured-error-handling"></a>Użyj strukturalnej obsługi błędów

Błędy przechwytywania i rejestrowania mają kluczowe znaczenie dla monitorowania kondycji aplikacji. Najwyższy poziom dowolnego kodu funkcji powinien zawierać blok try/catch. W bloku catch można przechwytywać i rejestrować błędy.

## <a name="retry-policies-preview"></a>Zasady ponawiania (wersja zapoznawcza)

Zasady ponawiania można definiować w dowolnej funkcji dla dowolnego typu wyzwalacza w aplikacji funkcji.  Zasady ponawiania ponownie wykonują funkcję do momentu pomyślnego wykonania lub do momentu, aż zostanie osiągnięta maksymalna liczba ponownych prób.  Zasady ponawiania można zdefiniować dla wszystkich funkcji w aplikacji lub dla poszczególnych funkcji.  Domyślnie aplikacja funkcji nie będzie ponawiać prób komunikatów (poza [określonymi wyzwalaczami, które mają zasady ponawiania w źródle wyzwalacza](#using-retry-support-on-top-of-trigger-resilience)).  Zasady ponawiania są oceniane za każdym razem, gdy wykonanie powoduje nieprzechwycony wyjątek.  Najlepszym rozwiązaniem jest przechwycenie wszystkich wyjątków w kodzie i ponowne zgłoszenie błędów, które powinny skutkować ponowną próbą.  Punkty kontrolne Event Hubs i Azure Cosmos DB nie będą zapisywane do momentu ukończenia zasad ponawiania dla wykonania, co oznacza, że postęp na tej partycji zostanie wstrzymany, dopóki bieżąca partia nie zostanie ukończona.

### <a name="retry-policy-options"></a>Opcje zasad ponawiania

Poniższe opcje są dostępne w celu zdefiniowania zasad ponawiania.

Maksymalna liczba **ponowień** to maksymalna liczba ponownych prób wykonania przed zakończeniem. Wartość oznacza, `-1` Aby ponowić próbę w nieskończoność. Bieżąca liczba ponownych prób jest przechowywana w pamięci wystąpienia. Istnieje możliwość, że wystąpienie zawiera błąd między ponownymi próbami.  Gdy wystąpienie zakończy się niepowodzeniem w ramach zasad ponawiania prób, liczba ponownych prób zostanie utracona.  Jeśli występują błędy wystąpienia, wyzwalacze, takie jak Event Hubs, Azure Cosmos DB i magazyn kolejek, mogą wznowić przetwarzanie i ponowić próbę wykonania partii w nowym wystąpieniu, a liczba ponownych prób zostanie zresetowana do wartości zero.  Inne wyzwalacze, takie jak HTTP i Timer, nie są wznawiane na nowym wystąpieniu.  Oznacza to, że maksymalna liczba ponownych prób jest najlepszym nakładem pracy, a w niektórych rzadkich przypadkach można ponowić próbę wykonania więcej niż maksimum lub dla wyzwalaczy, takich jak HTTP i Timer, jest mniejsza niż wartość maksymalna.

**Strategia ponawiania** decyduje o sposobie zachowania ponownych prób.  Poniżej przedstawiono dwie obsługiwane opcje ponawiania prób:

| Opcja | Opis|
|---|---|
|**`fixedDelay`**| Czas między ponownymi próbami może upłynąć określoną ilość czasu|
| **`exponentialBackoff`**| Pierwsza ponowna próba czeka na minimalne opóźnienie. W przypadku kolejnych ponownych prób godzina jest dodawana wykładniczo do początkowego czasu trwania dla każdej ponowienia próby, aż do osiągnięcia maksymalnego opóźnienia.  Wycofanie wykładnicze powoduje dodanie niewielkich losowych wartości do opóźnień w celu rozłożenia ponownych prób w scenariuszach o wysokiej przepływności.|

#### <a name="app-level-configuration"></a>Konfiguracja poziomu aplikacji

Zasady ponawiania można zdefiniować dla wszystkich funkcji w aplikacji [przy użyciu `host.json` pliku](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Konfiguracja poziomu funkcji

Zasady ponawiania można zdefiniować dla określonej funkcji.  Konfiguracja specyficzna dla funkcji ma priorytet w porównaniu z konfiguracją na poziomie aplikacji.

#### <a name="fixed-delay-retry"></a>Naprawiono ponawianie opóźnień

# <a name="c"></a>[C#](#tab/csharp)

Ponowne próby wymagają pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Oto zasady ponawiania w *function.jsw* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Oto zasady ponawiania w *function.jsw* pliku:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Oto zasady ponawiania w *function.jsw* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Oto zasady ponawiania w *function.jsw* pliku:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Wycofywania wykładniczy

# <a name="c"></a>[C#](#tab/csharp)

Ponowne próby wymagają pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Oto zasady ponawiania w *function.jsw* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Oto zasady ponawiania w *function.jsw* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Oto zasady ponawiania w *function.jsw* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Oto zasady ponawiania w *function.jsw* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.jswłaściwości  |Właściwość atrybutu | Opis |
|---------|---------|---------| 
|strategii|n/d|Wymagane. Strategia ponawiania, która ma zostać użyta. Prawidłowe wartości to `fixedDelay` lub `exponentialBackoff` .|
|Wartość MaxRetryCount|n/d|Wymagane. Maksymalna dozwolona liczba ponownych prób na wykonanie funkcji. `-1` oznacza, aby ponowić próbę w nieskończoność.|
|delayInterval|n/d|Opóźnienie, które zostanie użyte między ponownymi próbami przy korzystaniu z `fixedDelay` strategii.|
|minimumInterval|n/d|Minimalne opóźnienie ponowienia próby w przypadku korzystania z `exponentialBackoff` strategii.|
|maximumInterval|n/d|Maksymalne opóźnienie ponowienia próby w przypadku korzystania z `exponentialBackoff` strategii.| 

### <a name="retry-limitations-during-preview"></a>Ograniczenia ponowień w trakcie okresu zapoznawczego

- W przypadku projektów platformy .NET może być konieczne ręczne ściągnięcie w wersji [programu Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23.
- W planie zużycia aplikacja może być skalowana w dół do zera podczas ponawiania próby końcowych komunikatów w kolejce.
- W planie zużycia aplikacja może być skalowana w dół podczas wykonywania ponownych prób.  Aby uzyskać najlepsze wyniki, wybierz interwał ponawiania prób <= 00:01:00 i <= 5 ponownych prób.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Używanie obsługi ponowień na poziomie odporności wyzwalacza

Zasady ponawiania działania aplikacji funkcji są niezależne od wszelkich ponownych prób lub odporności, które zapewnia wyzwalacz.  Zasady ponawiania funkcji będą tylko warstwa na wierzchu wyzwalacza.  Na przykład w przypadku używania Azure Service Bus kolejki domyślnie mają liczbę dostaw komunikatów 10.  Domyślna liczba dostaw oznacza po 10 próbach dostarczenia komunikatu w kolejce, Service Bus komunikat zostanie utracony.  Można zdefiniować zasady ponawiania dla funkcji, która ma wyzwalacz Service Bus, ale ponowna próba będzie warstwą na podstawie Service Bus prób dostarczenia.  

Na przykład jeśli użyto domyślnej liczby Service Bus dostarczania 10 i zdefiniowano zasady ponawiania funkcji 5.  Komunikat zostałby najpierw usunięty z kolejki, zwiększając konto dostawy usługi Service Bus do 1.  Jeśli każde wykonanie nie powiodło się, po pięciu próbach uruchomienia tego samego komunikatu ten komunikat zostałby oznaczony jako porzucony.  Service Bus będzie natychmiast ponownie kolejkować komunikat, wyzwoli funkcję i zwiększy liczbę dostaw do 2.  Na koniec po 50 prób (10 dostaw usługi Service Bus * pięć funkcji na dostarczenie) komunikat zostałby porzucony i wyzwolony utraconą wiadomość w usłudze Service Bus.

> [!WARNING]
> Nie zaleca się ustawiania liczby dostaw dla wyzwalacza, takiego jak Service Bus Queues na 1, co oznacza, że komunikat zostałby utracony natychmiast po ponowieniu próby pojedynczego działania.  Wynika to z faktu, że wyzwalacze zapewniają odporność z ponownymi próbami, podczas gdy zasady ponawiania działania są najlepszym nakładem pracy i mogą wynikać z mniejszą liczbą ponownych prób.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Wyzwalacze z dodatkową odpornością lub ponownymi próbami

Następujące wyzwalacze obsługują ponowną próbę w źródle wyzwalacza:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Usługa Azure queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Kolejka/temat)](../articles/azure-functions/functions-bindings-service-bus.md)

Domyślnie większość wyzwalaczy ponawia żądania do pięciu razy. Po piątej ponowieniu próby w usłudze Azure queue storage zostanie zapisany komunikat do [kolejki trującej](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).  Domyślna kolejka Service Bus i zasady tematu będą zapisywać komunikat do [kolejki utraconych](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) wiadomości po 10 próbach.
