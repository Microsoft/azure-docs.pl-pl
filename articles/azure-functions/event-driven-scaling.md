---
title: Skalowanie oparte na zdarzeniach w Azure Functions
description: Wyjaśnia zachowania skalowania planu zużycia i aplikacji funkcji planu Premium.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937750"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Skalowanie oparte na zdarzeniach w Azure Functions

W planach zużycia i Premium Azure Functions skaluje zasoby procesora i pamięci, dodając dodatkowe wystąpienia hosta funkcji. Liczba wystąpień jest określana na podstawie liczby zdarzeń wyzwalających funkcję. 

Każde wystąpienie hosta funkcji w planie zużycia jest ograniczone do 1,5 GB pamięci i jednego procesora CPU.  Wystąpieniem hosta jest cała aplikacja funkcji, co oznacza, że wszystkie funkcje w ramach zasobu funkcji udział aplikacji w ramach wystąpienia i skalowania w tym samym czasie. Aplikacje funkcji, które dzielą tę samą skalę planu zużycia niezależnie od siebie.  W planie Premium rozmiar planu określa dostępną pamięć i procesor dla wszystkich aplikacji w tym wystąpieniu.  

Pliki kodu funkcji są przechowywane w udziałach Azure Files na głównym koncie magazynu funkcji. Po usunięciu głównego konta magazynu aplikacji funkcji pliki kodu funkcji zostaną usunięte i nie będzie można go odzyskać.

## <a name="runtime-scaling"></a>Skalowanie środowiska uruchomieniowego

Azure Functions używa składnika zwanego *kontrolerem skalowania* , aby monitorować częstotliwość zdarzeń i określać, czy skalować w poziomie, czy skalować w poziomie. Kontroler skalowania używa algorytmów heurystycznych dla każdego typu wyzwalacza. Na przykład podczas korzystania z wyzwalacza usługi Azure queue storage skaluje się w zależności od długości kolejki i wieku najstarszej wiadomości w kolejce.

Jednostką skalowania Azure Functions jest aplikacja funkcji. Gdy aplikacja funkcji jest skalowana w poziomie, dodatkowe zasoby są przydzieleni do uruchamiania wielu wystąpień hosta Azure Functions. Z drugiej strony, ponieważ zapotrzebowanie obliczeniowe jest ograniczone, kontroler skalowania usuwa wystąpienia hosta funkcji. Liczba wystąpień jest ostatecznie "skalowana w" do zera, gdy żadne funkcje nie działają w ramach aplikacji funkcji.

![Skalowanie zdarzeń monitorowania kontrolera i Tworzenie wystąpień](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Zimne uruchomienie

Gdy aplikacja funkcji jest bezczynna przez kilka minut, platforma może skalować liczbę wystąpień, na których aplikacja jest uruchamiana na zero. Następne żądanie ma dodanie opóźnienia skalowania od zera do jednego. To opóźnienie jest określane jako _zimne uruchomienie_. Liczba zależności wymaganych przez aplikację funkcji może mieć wpływ na zimny czas rozpoczęcia. Zimny start to więcej problemu w przypadku operacji synchronicznych, takich jak wyzwalacze HTTP, które muszą zwrócić odpowiedź. Jeśli zimne uruchomienie ma wpływ na funkcje, należy rozważyć uruchomienie w planie Premium lub w dedykowanym planie z włączonym ustawieniem **zawsze** włączone.   

## <a name="understanding-scaling-behaviors"></a>Zrozumienie zachowań skalowania

Skalowanie może się różnić w zależności od liczby czynników i skalować w różny sposób w zależności od wybranego wyzwalacza i języka. Istnieje kilka złożonego zachowań do skalowania:

* **Maksymalna liczba wystąpień:** Pojedyncza aplikacja funkcji jest skalowana tylko do maksymalnie 200 wystąpień. Pojedyncze wystąpienie może przetwarzać więcej niż jeden komunikat lub żądanie w tym samym czasie, więc nie ma ustawionego limitu liczby współbieżnych wykonań.  Możesz [określić niższą maksymalną](#limit-scale-out) przepustowość, aby ograniczyć skalę w miarę potrzeb.
* **Nowa częstotliwość wystąpień:** W przypadku wyzwalaczy HTTP nowe wystąpienia są przydzielono maksymalnie raz na sekundę. W przypadku wyzwalaczy nie korzystających z protokołu HTTP nowe wystąpienia są przydzielono co najwyżej, co 30 sekund. Skalowanie jest szybsze, gdy działa w [planie Premium](functions-premium-plan.md).
* **Wydajność skalowania:** W przypadku wyzwalaczy Service Bus Użyj _zarządzania_ prawami do zasobów, aby uzyskać najbardziej wydajne skalowanie. W przypadku praw _nasłuchu_ skalowanie nie jest tak dokładne, ponieważ długość kolejki nie może być używana do informowania o decyzjach o skalowaniu. Aby dowiedzieć się więcej o ustawianiu uprawnień Service Bus zasad dostępu, zobacz [zasady autoryzacji dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Aby uzyskać informacje na temat wyzwalaczy centrum zdarzeń, zobacz [wskazówki dotyczące skalowania](functions-bindings-event-hubs-trigger.md#scaling) w artykule referencyjnym. 

## <a name="limit-scale-out"></a>Ogranicz skalowanie w poziomie

Można ograniczyć maksymalną liczbę wystąpień aplikacji używanej do skalowania w poziomie.  Jest to najbardziej typowe w przypadku, gdy składnik podrzędny, taki jak baza danych, ma ograniczoną przepływność.  Domyślnie funkcje planu zużycia umożliwiają skalowanie do maksymalnie 200 wystąpień, a funkcje planu Premium będą skalowane do maksymalnie 100 wystąpień.  Możesz określić niższą wartość maksymalną dla konkretnej aplikacji, modyfikując ją `functionAppScaleLimit` .  `functionAppScaleLimit`Można ustawić na wartość `0` lub `null` w przypadku nieograniczonego lub prawidłowej wartości między `1` i maksimum aplikacji.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Najlepsze rozwiązania i wzorce dotyczące skalowalnych aplikacji

Istnieje wiele aspektów aplikacji funkcji, które mają wpływ na sposób skalowania, w tym Konfiguracja hosta, wydajność środowiska uruchomieniowego i efektywność zasobów.  Aby uzyskać więcej informacji, zobacz [sekcję skalowalność artykułu zagadnienia dotyczące wydajności](functions-best-practices.md#scalability-best-practices). Należy również wiedzieć, jak połączenia działają w miarę skalowania aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [jak zarządzać połączeniami w Azure Functions](manage-connections.md).

Aby uzyskać więcej informacji na temat skalowania w języku Python i Node.js, zobacz [Azure Functions Przewodnik dla deweloperów w języku Python — skalowanie i współbieżność](functions-reference-python.md#scaling-and-performance) oraz [Azure Functions Node.js Przewodnik dla deweloperów — skalowanie i współbieżność](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Model rozliczania

Rozliczenia dla różnych planów są szczegółowo opisane na [stronie cennika Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Użycie jest agregowane na poziomie aplikacji funkcji i zlicza tylko czas wykonywania kodu funkcji. Następujące jednostki dotyczą rozliczeń:

* **Użycie zasobów w gigabajtach sekund (GB-s)**. Obliczany jako kombinacja rozmiaru pamięci i czasu wykonywania dla wszystkich funkcji w aplikacji funkcji. 
* **Wykonania**. Zliczane za każdym razem, gdy funkcja jest wykonywana w odpowiedzi na wyzwalacz zdarzenia.

Przydatne zapytania i informacje dotyczące sposobu zrozumienia rachunku zużycia można znaleźć [na stronie często zadawanych pytań dotyczących rozliczeń](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Następne kroki

+ [Opcje hostingu Azure Functions](functions-scale.md)

