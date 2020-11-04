---
title: Użyj kanału informacyjnego zmiany szacowania Azure Cosmos DB
description: Dowiedz się, jak za pomocą szacowaniania zmian przeanalizować postęp procesora źródła zmian
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a44557d15f437317c2b5fa659ab8d4ca3c208edf
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339843"
---
# <a name="use-the-change-feed-estimator"></a>Korzystanie ze źródła zmian szacowania
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano, jak można monitorować postęp wystąpień [procesora źródła zmian](./change-feed-processor.md) podczas odczytywania źródła zmian.

## <a name="why-is-monitoring-progress-important"></a>Dlaczego monitorowanie postępu jest ważne?

Procesor kanału informacyjnego zmian działa jako wskaźnik, który przechodzi do przodu w [kanale informacyjnym zmiany](./change-feed.md) i dostarcza zmiany w implementacji delegata. 

Wdrożenie procesora kanału informacyjnego zmian może przetwarzać zmiany z określoną szybkością na podstawie dostępnych zasobów, takich jak procesor CPU, pamięć, Sieć i tak dalej.

Jeśli ta częstotliwość jest mniejsza niż szybkość, z jaką zmiany są wykonywane w kontenerze usługi Azure Cosmos, procesor rozpocznie się w stosunku do opóźnienia.

Określenie tego scenariusza pomaga zrozumieć, czy konieczne jest skalowanie wdrożenia procesora kanału informacyjnego zmian.

## <a name="implement-the-change-feed-estimator"></a>Zaimplementuj szacowania źródła zmian

Podobnie jak w przypadku [procesora kanału informacyjnego zmiany](./change-feed-processor.md), szacowania kanału informacyjnego działa jako model wypychania. Szacowania będzie mierzyć różnicę między ostatnim przetworzonym elementem (zdefiniowanym przez stan kontenera dzierżaw) i ostatnią zmianą w kontenerze i wypchnięciem tej wartości do delegata. Interwał, w którym jest wykonywana pomiar, można również dostosować przy użyciu wartości domyślnej wynoszącej 5 sekund.

Przykładowo, jeśli procesor źródła zmian został zdefiniowany w następujący sposób:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Prawidłowy sposób zainicjowania szacowania do mierzenia tego procesora mógłby być używany w `GetChangeFeedEstimatorBuilder` następujący sposób:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Miejsce, w którym zarówno procesor, jak i szacowania mają tę samą `leaseContainer` nazwę.

Pozostałe dwa parametry są delegatem, który będzie zawierać numer, który reprezentuje liczbę **oczekujących zmian, które mają zostać odczytane** przez procesor, oraz przedział czasu, w którym ma zostać wykonana ta miara.

Przykładem delegata, który otrzymuje oszacowanie, jest:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Można wysłać to oszacowanie do rozwiązania monitorowania i użyć go do zrozumienia, jak postęp zachowuje się wraz z upływem czasu.

> [!NOTE]
> Nie trzeba wdrażać szacowania źródła zmian w ramach procesora kanału informacyjnego zmian ani nie jest częścią tego samego projektu. Może być niezależna i działać w zupełnie innym wystąpieniu. Wystarczy użyć tej samej konfiguracji nazwy i dzierżawy.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zestaw SDK Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Przykłady użycia w witrynie GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w witrynie GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz można dowiedzieć się więcej o procesorze źródła zmian w następujących artykułach:

* [Omówienie procesora kanału informacyjnego zmiany](change-feed-processor.md)
* [Czas rozpoczęcia procesora zestawienia zmian](./change-feed-processor.md#starting-time)