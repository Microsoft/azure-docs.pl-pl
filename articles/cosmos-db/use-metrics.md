---
title: Monitoruj i Debuguj przy użyciu metryk w Azure Cosmos DB
description: Użyj metryk w Azure Cosmos DB, aby debugować typowe problemy i monitorować bazę danych.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0711d764514e45d3c28e26cf99b45dc711ef201c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868260"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitoruj i Debuguj przy użyciu metryk w Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Usługa Azure Cosmos DB udostępnia metryki dotyczące przepływności, magazynu, spójności, dostępności i opóźnienia. Witryna Azure Portal oferuje zagregowany widok tych metryk. Możesz również wyświetlić metryki usługi Azure Cosmos DB z poziomu interfejsu API usługi Azure Monitor. W wartościach wymiarów dla metryk, takich jak nazwa kontenera, nie jest rozróżniana wielkość liter. Dlatego należy użyć porównania bez uwzględniania wielkości liter podczas porównywania ciągów dla tych wartości wymiarów. Aby dowiedzieć się, jak wyświetlać metryki z usługi Azure monitor, zobacz artykuł [Uzyskiwanie metryk z Azure monitor](./monitor-cosmos-db.md) .

W tym artykule przedstawiono typowe przypadki użycia oraz sposób, w jaki metryki usługi Azure Cosmos DB mogą być używane do analizowania i debugowania tych problemów. Metryki są zbierane co pięć minut i są przechowywane przez siedem dni.

## <a name="view-metrics-from-azure-portal"></a>Wyświetl metryki z Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

1. Otwórz okienko **metryki** . Domyślnie w okienku metryki są wyświetlane metryki magazynu, indeksu, jednostki żądań dla wszystkich baz danych na koncie usługi Azure Cosmos. Można filtrować te metryki na bazę danych, kontener lub region. Można również filtrować metryki z określoną szczegółowością czasu. Więcej informacji na temat przepływności, magazynu, dostępności, opóźnień i metryk spójności znajdują się na oddzielnych kartach. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB metryki wydajności w Azure Portal":::

Następujące metryki są dostępne w okienku **metryki** : 

* **Metryki przepływności** — ta Metryka przedstawia liczbę żądań zużytych lub zakończonych niepowodzeniem (429 kod odpowiedzi), ponieważ przekroczono pojemność przepływności lub magazynu dla kontenera.

* **Metryki magazynu** — ta Metryka przedstawia rozmiar danych i użycie indeksów.

* **Metryki dostępności** — ta Metryka przedstawia wartość procentową liczby pomyślnych żądań przez łączną liczbę żądań na godzinę. Częstotliwość powodzeń jest definiowana przez Azure Cosmos DB umowy SLA.

* **Metryki opóźnienia** — ta Metryka przedstawia opóźnienie odczytu i zapisu zaobserwowane przez Azure Cosmos DB w regionie, w którym działa Twoje konto. Możesz wizualizować opóźnienia w różnych regionach dla konta zreplikowanego geograficznie. Ta Metryka nie reprezentuje opóźnienia żądania end-to-end.

* **Metryki spójności** — ta Metryka przedstawia, jak ostateczna jest spójność wybranego modelu spójności. W przypadku kont wieloregionowych ta Metryka przedstawia również opóźnienie replikacji między wybranymi regionami.

* **Metryki systemu** — ta Metryka przedstawia liczbę żądań metadanych obsługiwanych przez partycję podstawową. Pomaga również identyfikować żądania ograniczające.

W poniższych sekcjach objaśniono typowe scenariusze, w których można używać metryk Azure Cosmos DB. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Informacje o liczbie żądań zakończonych powodzeniem lub błędach

Aby rozpocząć, należy przejść do [Azure Portal](https://portal.azure.com) i przejdź do bloku **metryki** . W bloku Znajdź * * liczba żądań, które przekroczyły pojemność na wykres 1-minutowy. Ten wykres pokazuje łączną liczbę żądań, które są segmentami przez minutę przez kod stanu. Aby uzyskać więcej informacji na temat kodów stanu HTTP, zobacz [kody stanu HTTP dla Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Najbardziej typowym kodem stanu błędu jest 429 (ograniczanie szybkości/ograniczanie przepustowości). Ten błąd oznacza, że żądania Azure Cosmos DB przekraczają przepływność zainicjowaną. Najbardziej typowym rozwiązaniem tego problemu jest [skalowanie w górę jednostek ru](./set-throughput.md) dla danej kolekcji.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Liczba żądań na minutę":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Określanie rozkładu przepływności między partycjami

Dobrą kardynalnością kluczy partycji jest istotna dla każdej skalowalnej aplikacji. Aby określić dystrybucję przepływności dowolnego kontenera partycjonowanego podzielonego na partycje, przejdź do **bloku metryki** w [Azure Portal](https://portal.azure.com). Na karcie **przepływność** , podział magazynu jest pokazywany w polu **Maksymalna liczba ZUŻYTych jednostek ru na sekundę według poszczególnych wykresów partycji fizycznej** . Na poniższej ilustracji przedstawiono przykład słabej dystrybucji danych, jak pokazano na partycji skośnej po lewej stronie.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Użycie pojedynczej partycji":::

Nierówna dystrybucja przepływności może spowodować powstanie partycji na *gorąco* , co może spowodować ograniczenie żądań i może wymagać ponownego partycjonowania. Aby uzyskać więcej informacji na temat partycjonowania w Azure Cosmos DB, zobacz [partycja i skalowanie w Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Określanie dystrybucji magazynu między partycjami

Posiadanie odpowiedniej kardynalności partycji jest niezbędne dla każdej skalowalnej aplikacji. Aby określić dystrybucję magazynu dla dowolnego kontenera partycjonowanego podzielonego na partycje, przejdź do bloku metryki w [Azure Portal](https://portal.azure.com). Na karcie Magazyn zostanie wyświetlona wartość podział magazynu w magazynie dane i indeks używane przez wykres najważniejszych kluczy partycji. Poniższa ilustracja przedstawia słabą dystrybucję magazynu danych, jak pokazano na partycji skośnej po lewej stronie.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Przykład słabej dystrybucji danych":::

Możesz przyczynić się, że klucz partycji pochyla dystrybucję, klikając partycję na wykresie.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Klucz partycji pochyla rozkład":::

Po zidentyfikowaniu, który klucz partycji powoduje pochylenie w dystrybucji, może być konieczne ponowne partycjonowanie kontenera przy użyciu bardziej rozproszonego klucza partycji. Aby uzyskać więcej informacji na temat partycjonowania w Azure Cosmos DB, zobacz [partycja i skalowanie w Azure Cosmos DB](./partitioning-overview.md).

## <a name="compare-data-size-against-index-size"></a>Porównaj rozmiar danych z rozmiarem indeksu

W Azure Cosmos DB łączny zużyty magazyn to kombinacja rozmiaru danych i rozmiaru indeksu. Zazwyczaj rozmiar indeksu jest częścią rozmiaru danych. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [rozmiaru indeksu](index-policy.md#index-size) . W bloku metryk w [Azure Portal](https://portal.azure.com)na karcie Magazyn jest prezentowany podział użycia magazynu na podstawie danych i indeksu.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Jeśli chcesz zaoszczędzić miejsce na indeksie, możesz dostosować [zasady indeksowania](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Debugowanie dlaczego zapytania działają wolno

W zestawach SDK interfejsu API SQL Azure Cosmos DB zawiera statystyki wykonywania zapytania.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* zawiera szczegółowe informacje o tym, jak długo każdy składnik zapytania wykonał wykonanie. Najbardziej powszechną główną przyczyną długotrwałych zapytań jest skanowanie, co oznacza, że zapytanie nie mogło użyć indeksów. Ten problem można rozwiązać, używając lepszego warunku filtru.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak monitorować i debugować problemy przy użyciu metryk podanych w Azure Portal. Warto dowiedzieć się więcej o ulepszaniu wydajności bazy danych, czytając następujące artykuły:

* Aby dowiedzieć się, jak wyświetlać metryki z usługi Azure monitor, zobacz artykuł [Uzyskiwanie metryk z Azure monitor](./monitor-cosmos-db.md) . 
* [Testowanie wydajności i skalowania przy użyciu Azure Cosmos DB](performance-testing.md)
* [Porady dotyczące wydajności usługi Azure Cosmos DB](performance-tips.md)