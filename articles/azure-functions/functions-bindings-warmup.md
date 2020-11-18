---
title: Azure Functions wyzwalacz rozgrzewania
description: Dowiedz się, jak używać wyzwalacza rozgrzewania w Azure Functions.
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, Functions, przetwarzanie zdarzeń, rozgrzewania, zimny start, Premium, Dynamic COMPUTE, architektura bezserwerowa
ms.service: azure-functions
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/08/2019
ms.author: cshoe
ms.openlocfilehash: 6a909b246e7cb3695533a75294c523d7c9aeaeda
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832946"
---
# <a name="azure-functions-warm-up-trigger"></a>Wyzwalacz rozgrzewania Azure Functions

W tym artykule opisano sposób pracy z wyzwalaczem rozgrzewania w Azure Functions. Wyzwalacz rozgrzewania jest obsługiwany tylko w przypadku aplikacji funkcji działających w ramach [planu Premium](functions-premium-plan.md). Wyzwalacz rozgrzewania jest wywoływany po dodaniu wystąpienia w celu skalowania uruchomionej aplikacji funkcji. Wyzwalacza rozgrzewania można użyć do wstępnego ładowania zależności niestandardowych podczas [procesu wstępnego rozgrzewania](./functions-premium-plan.md#pre-warmed-instances) , dzięki czemu funkcje są gotowe do natychmiastowego uruchamiania przetwarzania żądań. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Wymagany jest pakiet NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) w wersji **3.0.5 lub nowszej** . Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz rozgrzewania umożliwia zdefiniowanie funkcji, która będzie uruchamiana w nowym wystąpieniu, gdy zostanie dodana do uruchomionej aplikacji. Możesz użyć funkcji rozgrzewania, aby otworzyć połączenia, Załaduj zależności lub uruchomić dowolną inną logikę niestandardową, zanim aplikacja zacznie odbierać ruch. 

Wyzwalacz rozgrzewania jest przeznaczony do tworzenia współużytkowanych zależności, które będą używane przez inne funkcje w aplikacji. [Zobacz przykłady zależności udostępnionych tutaj](./manage-connections.md#client-code-examples).

Należy zauważyć, że wyzwalacz rozgrzewania jest wywoływany tylko podczas operacji skalowania w poziomie, a nie w trakcie ponownego uruchamiania lub w innych uruchomieniach bez skalowania. Musisz się upewnić, że logika może ładować wszystkie wymagane zależności bez użycia wyzwalacza rozgrzewania. Ładowanie z opóźnieniem jest dobrym wzorcem do osiągnięcia tego celu.

## <a name="trigger---example"></a>Wyzwalacz — przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która będzie uruchamiana na każdym nowym wystąpieniu po dodaniu go do aplikacji. Atrybut wartości zwracanej nie jest wymagany.


* Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.
* Aby użyć rozgrzewania jako funkcji biblioteki klas .NET, upewnij się, że masz odwołanie do pakietu **Microsoft. Azure. WebJobs. Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Komentarze symboli zastępczych pokazują, gdzie w aplikacji należy zadeklarować i zainicjować współdzielone zależności. 
[Więcej informacji na temat udostępnionych zależności znajdziesz tutaj](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)


Poniższy przykład przedstawia wyzwalacz rozgrzewania w *function.js* w pliku i [funkcję skryptu języka C#](functions-reference-csharp.md) , która będzie uruchamiana na każdym nowym wystąpieniu po dodaniu go do aplikacji.

Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może mieć tylko jedną funkcję rozgrzewania na aplikację.

Oto *function.js* pliku:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

W sekcji [Konfiguracja](#trigger---configuration) objaśniono te właściwości.

```cs
public static void Run(WarmupContext warmupContext, ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia wyzwalacz rozgrzewania w *function.js* w pliku oraz [funkcja języka JavaScript](functions-reference-node.md)  , która będzie uruchamiana w każdym nowym wystąpieniu, gdy zostanie dodana do aplikacji.

Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto *function.js* pliku:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

W sekcji [Konfiguracja](#trigger---configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia wyzwalacz rozgrzewania w *function.js* w pliku i [funkcję języka Python](functions-reference-python.md) , która będzie uruchamiana na każdym nowym wystąpieniu po dodaniu go do aplikacji.

Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto *function.js* pliku:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

W sekcji [Konfiguracja](#trigger---configuration) objaśniono te właściwości.

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład przedstawia wyzwalacz rozgrzewania, który jest uruchamiany, gdy każde nowe wystąpienie zostanie dodane do aplikacji.

Funkcja musi mieć nazwę `warmup` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md) `WarmupTrigger` atrybut jest dostępny w celu skonfigurowania funkcji.

# <a name="c"></a>[C#](#tab/csharp)

W tym przykładzie pokazano, jak używać atrybutu [rozgrzewania](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Należy pamiętać, że funkcja musi być wywoływana ```Warmup``` i może istnieć tylko jedna funkcja rozgrzewania dla aplikacji.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Aby uzyskać pełny przykład, zobacz [przykład wyzwalacza](#trigger---example).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

Wyzwalacz rozgrzewania nie jest obsługiwany w języku Java jako atrybut.

---

## <a name="trigger---configuration"></a>Wyzwalacz — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `WarmupTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Wprowadź** | n/d| Wymagane — musi być ustawiony na wartość `warmupTrigger` . |
| **wskazywa** | n/d| Wymagane — musi być ustawiony na wartość `in` . |
| **Nazwij** | n/d| Wymagane — nazwa zmiennej używana w kodzie funkcji.|

## <a name="trigger---usage"></a>Wyzwalacz-użycie

Żadne dodatkowe informacje nie są dostarczane do funkcji wyzwalanej przez funkcję rozgrzewania, gdy jest wywoływana.

## <a name="trigger---limits"></a>Wyzwalacze — limity

* Wyzwalacz rozgrzewania jest dostępny tylko dla aplikacji uruchomionych w ramach [planu Premium](./functions-premium-plan.md).
* Wyzwalacz rozgrzewania jest wywoływany tylko podczas operacji skalowania w poziomie, a nie w trakcie ponownego uruchamiania lub w innych uruchomieniach bez skalowania. Musisz się upewnić, że logika może ładować wszystkie wymagane zależności bez użycia wyzwalacza rozgrzewania. Ładowanie z opóźnieniem jest dobrym wzorcem do osiągnięcia tego celu.
* Nie można wywołać wyzwalacza rozgrzewania, gdy wystąpienie jest już uruchomione.
* Dla każdej aplikacji funkcji może istnieć tylko jedna funkcja wyzwalacza rozgrzewania.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
