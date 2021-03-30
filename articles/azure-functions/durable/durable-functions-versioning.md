---
title: Przechowywanie wersji w Durable Functions — Azure
description: Dowiedz się, jak zaimplementować obsługę wersji w rozszerzeniu Durable Functions Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74232757"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Przechowywanie wersji w Durable Functions (Azure Functions)

Jest to nieuniknione, że funkcje zostaną dodane, usunięte i zmienione w okresie istnienia aplikacji. [Durable Functions](durable-functions-overview.md) umożliwia łączenie się z funkcjami łańcucha w sposób, który nie był wcześniej możliwy i ten łańcuch ma wpływ na sposób obsługi wersji.

## <a name="how-to-handle-breaking-changes"></a>Jak obsłużyć istotne zmiany

Istnieje kilka przykładów istotnych zmian, o których należy wiedzieć. W tym artykule omówiono najczęstsze. Głównym motywem za wszystkie z nich jest to, że w przypadku nowych i istniejących aranżacji funkcji wpływają zmiany kodu funkcji.

### <a name="changing-activity-or-entity-function-signatures"></a>Zmiana sygnatury funkcji działania lub jednostki

Zmiana podpisu odwołuje się do zmiany w nazwie, danych wejściowych lub wyjściowych funkcji. Jeśli tego rodzaju zmiany są wprowadzane do działania lub funkcji jednostki, może to spowodować przerwanie każdej funkcji programu Orchestrator, która jest od niej zależna. Jeśli zaktualizujesz funkcję programu Orchestrator w celu uwzględnienia tej zmiany, możesz przerwać istniejące wystąpienia w locie.

Załóżmy na przykład, że mamy następującą funkcję programu Orchestrator.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta funkcja uproszczony pobiera wyniki **foo** i przekazuje je do **paska**. Załóżmy, że musimy zmienić wartość zwracaną **foo** z na, `bool` Aby `int` obsługiwać szersze różne wartości wynikowe. Wynik wygląda następująco:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> W poprzednich przykładach języka C# Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

Ta zmiana działa prawidłowo dla wszystkich nowych wystąpień funkcji programu Orchestrator, ale przerywa wszystkie wystąpienia w locie. Rozważmy na przykład przypadek, w którym wystąpienie aranżacji wywołuje funkcję o nazwie `Foo` , pobiera wartość logiczną, a następnie punkty kontrolne. Jeśli w tym momencie zostanie wdrożona zmiana podpisu, wystąpienie z punktem kontrolnym zakończy się niepowodzeniem natychmiast po jego wznowieniu i odtwarza wywołanie `context.CallActivityAsync<int>("Foo")` . Ten błąd występuje, ponieważ wynik w tabeli historii jest, `bool` ale nowy kod próbuje zdeserializować go do `int` .

Ten przykład jest tylko jednym z wielu różnych sposobów, w których zmiana podpisu może przerwać istniejące wystąpienia. Ogólnie rzecz biorąc, jeśli program Orchestrator musi zmienić sposób wywoływania funkcji, zmiana będzie prawdopodobnie powodować problemy.

### <a name="changing-orchestrator-logic"></a>Zmiana logiki programu Orchestrator

Druga klasa problemów z obsługą wersji polega na zmianie kodu funkcji programu Orchestrator w taki sposób, który odmówi logiki powtarzania dla wystąpień w locie.

Weź pod uwagę następujące funkcje programu Orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Teraz Załóżmy, że chcemy pozornie nieszkodliwe zmienić, aby dodać kolejne wywołanie funkcji.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> W poprzednich przykładach języka C# Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

Ta zmiana powoduje dodanie nowego wywołania funkcji do **SendNotification** między **foo** a **paskiem**. Brak zmian sygnatur. Problem występuje, gdy istniejące wystąpienie zostanie wznowione z wywołania do **paska**. Podczas powtarzania, jeśli oryginalne wywołanie metody **foo** zwróci `true` wartość, ponowne uruchomienie programu Orchestrator wywoła wywołanie do **SendNotification**, które nie znajduje się w jego historii wykonywania. W związku z tym trwała struktura zadań kończy się niepowodzeniem z `NonDeterministicOrchestrationException` powodu wystąpienia **SendNotification** , gdy oczekiwano na wyświetlenie wywołania do **paska**. Ten sam typ problemu może wystąpić podczas dodawania wszelkich wywołań do "trwałych" interfejsów API, w tym `CreateTimer` , `WaitForExternalEvent` itp.

## <a name="mitigation-strategies"></a>Strategie ograniczenia

Poniżej przedstawiono niektóre strategie postępowania z wyzwaniami dotyczącymi wersji:

* Nic nie rób
* Zatrzymaj wszystkie wystąpienia w locie
* Wdrożenia równoległe

### <a name="do-nothing"></a>Nic nie rób

Najprostszym sposobem obsługi istotnej zmiany jest umożliwienie awarii wystąpienia aranżacji w locie. Nowe wystąpienia pomyślnie uruchomiły zmieniony kod.

Czy ten rodzaj awarii jest problemem, zależy od ważności wystąpień w locie. Jeśli pracujesz w aktywnym programowaniu i nie Zadbaj o wystąpienia w locie, może to być wystarczające. Należy jednak zająć się wyjątkami i błędami w potoku diagnostyki. Aby uniknąć tych problemów, należy wziąć pod uwagę inne opcje przechowywania wersji.

### <a name="stop-all-in-flight-instances"></a>Zatrzymaj wszystkie wystąpienia w locie

Innym rozwiązaniem jest zatrzymanie wszystkich wystąpień w locie. Zatrzymanie wszystkich wystąpień może odbywać się przez wyczyszczenie zawartości kolejek wewnętrznej **kontroli** i kolejek elementów **roboczych** . Wystąpienia będą w nieskończoność zablokowane, gdzie się znajdują, ale nie będą zasłaniać dzienników i komunikatów o błędach. To podejście jest idealne do szybkiego tworzenia prototypów.

> [!WARNING]
> Szczegóły tych kolejek mogą ulec zmianie z upływem czasu, dlatego nie należy polegać na tej metodzie w przypadku obciążeń produkcyjnych.

### <a name="side-by-side-deployments"></a>Wdrożenia równoległe

Najbardziej nieudaną próbą zapewnienia, że krytyczne zmiany są wdrażane bezpiecznie, wdrażając je równolegle ze starszymi wersjami. Można to zrobić przy użyciu dowolnej z następujących technik:

* Wdróż wszystkie aktualizacje jako całkowicie nowe funkcje, pozostawiając istniejące funkcje jako-is. Może to być trudne, ponieważ obiekty wywołujące nowe wersje funkcji muszą zostać zaktualizowane, a także zgodnie z tymi samymi wskazówkami.
* Wdróż wszystkie aktualizacje jako nową aplikację funkcji przy użyciu innego konta magazynu.
* Wdróż nową kopię aplikacji funkcji przy użyciu tego samego konta magazynu, ale ze zaktualizowaną `taskHub` nazwą. Zalecaną techniką są wdrożenia równoległe.

### <a name="how-to-change-task-hub-name"></a>Jak zmienić nazwę centrum zadań

Centrum zadań można skonfigurować w *host.js* pliku w następujący sposób:

#### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funkcje 2,0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Wartość domyślna dla Durable Functions v1. x to `DurableFunctionsHub` . Począwszy od Durable Functions v 2.0, domyślna nazwa centrum zadań jest taka sama jak nazwa aplikacji funkcji na platformie Azure lub w `TestHubName` przypadku uruchamiania poza platformą Azure.

Wszystkie jednostki usługi Azure Storage są nazwane na podstawie `hubName` wartości konfiguracji. Nadając centrum zadań nową nazwę, upewnij się, że dla nowej wersji aplikacji zostanie utworzona osobna kolejka i tabela historii. Aplikacja funkcji, jednak zatrzyma przetwarzanie zdarzeń dla aranżacji lub jednostek utworzonych w ramach poprzedniej nazwy centrum zadań.

Zalecamy wdrożenie nowej wersji aplikacji funkcji w nowym [miejscu wdrożenia](../functions-deployment-slots.md). Miejsca wdrożenia umożliwiają uruchamianie wielu kopii aplikacji funkcji obok siebie tylko z jednym z nich jako aktywnym miejscem *produkcyjnym* . Gdy wszystko jest gotowe do uwidocznienia nowej logiki aranżacji do istniejącej infrastruktury, może to być proste, ponieważ zamienia nową wersję na miejsce produkcyjne.

> [!NOTE]
> Ta strategia działa najlepiej, gdy używane są wyzwalacze protokołu HTTP i elementu webhook dla funkcji programu Orchestrator. W przypadku wyzwalaczy innych niż HTTP, takich jak kolejki lub Event Hubs, definicja wyzwalacza powinna [pochodzić od ustawienia aplikacji](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , które jest aktualizowane w ramach operacji zamiany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać problemy dotyczące wydajności i skalowania](durable-functions-perf-and-scale.md)
