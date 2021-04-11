---
title: Użyj kanału informacyjnego zmiany szacowania Azure Cosmos DB
description: Dowiedz się, jak za pomocą szacowaniania zmian przeanalizować postęp procesora źródła zmian
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/01/2021
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d4e461b25a25ecdf0d4d89ee7f1c82b9d4a0737
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220168"
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

### <a name="as-a-push-model-for-automatic-notifications"></a>Jako model wypychania dla powiadomień automatycznych

Podobnie jak w przypadku [procesora kanału informacyjnego zmiany](./change-feed-processor.md), szacowania źródła zmian może współpracować z modelem wypychania. Szacowania będzie mierzyć różnicę między ostatnim przetworzonym elementem (zdefiniowanym przez stan kontenera dzierżaw) i ostatnią zmianą w kontenerze i wypchnięciem tej wartości do delegata. Interwał, w którym jest wykonywana pomiar, można również dostosować przy użyciu wartości domyślnej wynoszącej 5 sekund.

Przykładowo, jeśli procesor źródła zmian został zdefiniowany w następujący sposób:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Prawidłowy sposób zainicjowania szacowania do mierzenia tego procesora mógłby być używany w `GetChangeFeedEstimatorBuilder` następujący sposób:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Miejsce, w którym zarówno procesor, jak i szacowania mają tę samą `leaseContainer` nazwę.

Pozostałe dwa parametry są delegatem, który będzie zawierać numer, który reprezentuje liczbę **oczekujących zmian, które mają zostać odczytane** przez procesor, oraz przedział czasu, w którym ma zostać wykonana ta miara.

Przykładem delegata, który otrzymuje oszacowanie, jest:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Można wysłać to oszacowanie do rozwiązania monitorowania i użyć go do zrozumienia, jak postęp zachowuje się wraz z upływem czasu.

### <a name="as-an-on-demand-detailed-estimation"></a>Zgodnie z szczegółowym oszacowaniem na żądanie

W przeciwieństwie do modelu wypychania istnieje alternatywa umożliwiająca uzyskanie oszacowania na żądanie. Ten model zawiera również bardziej szczegółowe informacje:

* Szacowane opóźnienie na dzierżawę.
* Wystąpienie, które jest właścicielem i przetwarza każdą dzierżawę, aby można było sprawdzić, czy występuje problem z wystąpieniem.

Jeśli procesor kanału informacyjnego zmian jest zdefiniowany w następujący sposób:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimatorDetailed)]

Można utworzyć szacowania z tą samą konfiguracją dzierżawy:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimatorDetailed)]

W dowolnym momencie, z wymaganą częstotliwością, można uzyskać szczegółowe oszacowanie:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=GetIteratorEstimatorDetailed)]

Każda `ChangeFeedProcessorState` z nich będzie zawierać informacje o dzierżawie i zwłoki, a także to, kto jest bieżącym wystąpieniem będącym właścicielem. 

> [!NOTE]
> Nie trzeba wdrażać szacowania źródła zmian w ramach procesora kanału informacyjnego zmian ani nie jest częścią tego samego projektu. Może być niezależna i działać w zupełnie innym wystąpieniu, które jest zalecane. Wystarczy użyć tej samej konfiguracji nazwy i dzierżawy.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zestaw SDK Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Przykłady użycia w witrynie GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w witrynie GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz można dowiedzieć się więcej o procesorze źródła zmian w następujących artykułach:

* [Omówienie procesora kanału informacyjnego zmiany](change-feed-processor.md)
* [Czas rozpoczęcia procesora zestawienia zmian](./change-feed-processor.md#starting-time)