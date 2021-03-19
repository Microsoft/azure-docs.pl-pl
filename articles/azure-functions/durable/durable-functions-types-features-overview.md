---
title: Typy funkcji w usłudze Azure Durable Functions
description: Dowiedz się więcej o typach funkcji i ról, które obsługują komunikację typu "funkcja-funkcja" w Durable Functions aranżacji w Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84697711"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions — typy i funkcje

Durable Functions jest rozszerzeniem [Azure Functions](../functions-overview.md). Durable Functions służy do organizowania stanowej aranżacji wykonywania funkcji. Trwała aplikacja funkcji to rozwiązanie, które składa się z różnych funkcji platformy Azure. Funkcje mogą odtwarzać różne role w trwałej aranżacji funkcji. 

Istnieją obecnie cztery trwałe typy funkcji w Azure Functions: aktywność, Orchestrator, Entity i Client. Pozostała część tej sekcji zawiera więcej szczegółowych informacji o typach funkcji związanych z aranżacją.

## <a name="orchestrator-functions"></a>Funkcje programu Orchestrator

Funkcje programu Orchestrator opisują sposób wykonywania akcji i kolejność wykonywania akcji. Funkcje programu Orchestrator opisują aranżację w kodzie (C# lub JavaScript), jak pokazano w [Durable Functions wzorców aplikacji](durable-functions-overview.md#application-patterns). Aranżacja może mieć wiele różnych typów akcji, w tym [funkcje działania](#activity-functions), [podwykonawcy](durable-functions-orchestrations.md#sub-orchestrations), [oczekujące na zewnętrzne zdarzenia](durable-functions-orchestrations.md#external-events), [http](durable-functions-http-features.md)i [czasomierze](durable-functions-orchestrations.md#durable-timers). Funkcje programu Orchestrator mogą również współdziałać z [funkcjami jednostek](#entity-functions).

> [!NOTE]
> Funkcje programu Orchestrator są zapisywane przy użyciu zwykłego kodu, ale istnieją ścisłe wymagania dotyczące pisania kodu. W każdym przypadku kod funkcji programu Orchestrator musi być *deterministyczny*. Niespełnienie wymagań dotyczących tego ustalenia może spowodować, że funkcje programu Orchestrator nie działają poprawnie. Szczegółowe informacje o tych wymaganiach i sposobach ich obejścia można znaleźć w temacie [ograniczenia kodu](durable-functions-code-constraints.md) .

Aby uzyskać bardziej szczegółowe informacje na temat funkcji programu Orchestrator i ich funkcji, zobacz artykuł dotyczący [nietrwałych aranżacji](durable-functions-orchestrations.md) .

## <a name="activity-functions"></a>Funkcje działania

Funkcje działania to podstawowa jednostka pracy w ramach aranżacji funkcji trwałych. Funkcje działania to funkcje i zadania, które są zorganizowane w procesie. Można na przykład utworzyć funkcję programu Orchestrator, która będzie przetwarzać zamówienie. Zadania obejmują sprawdzanie spisu, ładowanie klienta i tworzenie wysyłki. Każde zadanie będzie oddzielną funkcją działania. Te funkcje działania mogą być wykonywane szeregowo, równolegle lub z obu kombinacji.

W przeciwieństwie do funkcji programu Orchestrator, funkcje działania nie są ograniczone do typu pracy, którą można w nich wykonywać. Funkcje działania są często używane do wykonywania wywołań sieciowych lub wykonywania operacji intensywnie korzystających z procesora CPU. Funkcja działania może również zwracać dane z powrotem do funkcji programu Orchestrator. Trwała struktura zadań gwarantuje, że każda wywołana funkcja Activity zostanie wykonana *co najmniej raz* podczas wykonywania aranżacji.

> [!NOTE]
> Ponieważ funkcje działania gwarantują tylko *jednokrotne* wykonanie, zalecamy, aby logika funkcji działania *idempotentne* się zawsze wtedy, gdy jest to możliwe.

Użyj [wyzwalacza działania](durable-functions-bindings.md#activity-trigger) , aby zdefiniować funkcję działania. Funkcja .NET otrzymuje `DurableActivityContext` jako parametr. Można również powiązać wyzwalacz z dowolnym innym obiektem serializacji JSON, aby przekazać dane wejściowe do funkcji. W języku JavaScript można uzyskać dostęp do danych wejściowych za pośrednictwem `<activity trigger binding name>` właściwości [ `context.bindings` obiektu](../functions-reference-node.md#bindings). Do funkcji działania można przekazywać tylko jedną wartość. Aby przekazać wiele wartości, należy użyć krotek, tablic lub typów złożonych.

> [!NOTE]
> Funkcję działania można wyzwolić tylko przy użyciu funkcji programu Orchestrator.

## <a name="entity-functions"></a>Funkcje jednostki

Funkcje jednostki definiują operacje odczytu i aktualizowania małych fragmentów stanu. Często odwołują się do tych jednostek stanowych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. Mogą być również wywoływane z funkcji klienta lub funkcji programu Orchestrator. W przeciwieństwie do funkcji programu Orchestrator, funkcje jednostek nie mają żadnych ograniczeń związanych z kodem. Funkcje jednostek również zarządzają stanem jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.

> [!NOTE]
> Funkcje jednostki i powiązane funkcje są dostępne tylko w Durable Functions 2,0 i nowszych.

Aby uzyskać więcej informacji na temat funkcji jednostki, zobacz artykuł dotyczący [jednostek trwałych](durable-functions-entities.md) .

## <a name="client-functions"></a>Funkcje klienta

Funkcje programu Orchestrator są wywoływane przez [powiązanie wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger) , a funkcje jednostek są wyzwalane przez [powiązanie wyzwalacza jednostki](durable-functions-bindings.md#entity-trigger). Oba te wyzwalacze działają przez oddziałanie do komunikatów, które są w kolejce do [centrum zadań](durable-functions-task-hubs.md). Podstawowym sposobem na dostarczenie tych komunikatów jest użycie [powiązania klienta programu Orchestrator](durable-functions-bindings.md#orchestration-client) lub [powiązania klienta jednostki](durable-functions-bindings.md#entity-client) z poziomu *funkcji klienta*. Każda funkcja, która nie jest koordynatorem, może być *funkcją kliencką*. Na przykład można wyzwolić koordynatora z funkcji wyzwalanej przez protokół HTTP, funkcji wyzwalanej przez centrum zdarzeń platformy Azure itp. Co sprawia, że funkcja *klienta* korzysta z trwałego powiązania danych wyjściowych klienta.

> [!NOTE]
> W przeciwieństwie do innych typów funkcji, funkcje programu Orchestrator i Entity nie mogą być wyzwalane bezpośrednio przy użyciu przycisków w witrynie Azure Portal. Jeśli chcesz przetestować funkcję programu Orchestrator lub Entity w witrynie Azure Portal, należy zamiast tego uruchomić *funkcję klienta* , która uruchamia program Orchestrator lub funkcję Entity w ramach swojej implementacji. W najprostszym środowisku testowym zaleca się wykonanie funkcji *ręcznego wyzwalacza* .

Oprócz wyzwalania funkcji programu Orchestrator lub jednostek, trwałe powiązanie *klienta* może służyć do współdziałania z uruchomionymi aranżacjami i jednostkami. Na przykład można wykonywać zapytania o aranżacje, zakończyć pracę i mogą być do nich wywoływane zdarzenia. Aby uzyskać więcej informacji na temat zarządzania aranżacjami i jednostkami, zobacz artykuł dotyczący [zarządzania wystąpieniami](durable-functions-instance-management.md) .

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, Utwórz pierwszą trwałą funkcję w [języku C#](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Przeczytaj więcej na temat Durable Functions aranżacji](durable-functions-orchestrations.md)