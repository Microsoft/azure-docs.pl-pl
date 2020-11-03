---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284385"
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
- [Zaimplementuj zasady ponawiania](#retry-policies) (w razie potrzeby)

### <a name="use-structured-error-handling"></a>Użyj strukturalnej obsługi błędów

Błędy przechwytywania i rejestrowania mają kluczowe znaczenie dla monitorowania kondycji aplikacji. Najwyższy poziom dowolnego kodu funkcji powinien zawierać blok try/catch. W bloku catch można przechwytywać i rejestrować błędy.

## <a name="retry-policies"></a>Zasady ponawiania

Zasady ponawiania można definiować w dowolnej funkcji dla dowolnego typu wyzwalacza w aplikacji funkcji.  Zasady ponawiania ponownie wykonują funkcję do momentu pomyślnego wykonania lub do momentu, aż zostanie osiągnięta maksymalna liczba ponownych prób.  Zasady ponawiania można zdefiniować dla wszystkich funkcji w aplikacji lub dla poszczególnych funkcji.  Domyślnie aplikacja funkcji nie będzie ponawiać prób komunikatów (poza [określonymi wyzwalaczami, które mają zasady ponawiania w źródle wyzwalacza](#trigger-specific-retry-support)).  Zasady ponawiania są oceniane za każdym razem, gdy wykonanie powoduje nieprzechwycony wyjątek.  Najlepszym rozwiązaniem jest przechwycenie wszystkich wyjątków w kodzie i ponowne wygenerowanie wszelkich błędów, które powinny spowodować ponowienie próby.  Punkty kontrolne Event Hubs i Azure Cosmos DB nie będą zapisywane do momentu ukończenia zasad ponawiania dla wykonania, co oznacza, że postęp na tej partycji zostanie wstrzymany, dopóki bieżąca partia nie zostanie ukończona.

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

## <a name="trigger-specific-retry-support"></a>Obsługa ponawiania prób specyficznych dla wyzwalacza

Niektóre wyzwalacze zapewniają ponowną próbę w źródle wyzwalacza.  Te ponowne próby wyzwalacza mogą być używane oprócz lub jako zamiennika dla zasad ponawiania hosta aplikacji funkcji.  Jeśli pożądane jest poprawna liczba ponownych prób, należy użyć zasad ponawiania specyficznych dla wyzwalacza za pośrednictwem zasad ponownych prób hosta ogólnego.  Następujące wyzwalacze obsługują ponowną próbę w źródle wyzwalacza:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Usługa Azure queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Kolejka/temat)](../articles/azure-functions/functions-bindings-service-bus.md)

Domyślnie te wyzwalacze ponawiają żądania do pięciu razy. Po piątej ponowieniu próby zarówno usługa Azure queue storage, jak i wyzwalacz Azure Service Bus zapiszą komunikat do [kolejki trującej](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).
