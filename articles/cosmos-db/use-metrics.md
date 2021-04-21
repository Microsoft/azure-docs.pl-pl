---
title: Monitorowanie i debugowanie za pomocą metryk w Azure Cosmos DB
description: Użyj metryk w Azure Cosmos DB, aby debugować typowe problemy i monitorować bazę danych.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: cf92d9e1a1f92c2dc3294b71e3e620166fd90680
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818707"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitorowanie i debugowanie za pomocą metryk w Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Usługa Azure Cosmos DB udostępnia metryki dotyczące przepływności, magazynu, spójności, dostępności i opóźnienia. Witryna Azure Portal oferuje zagregowany widok tych metryk. Możesz również wyświetlić metryki usługi Azure Cosmos DB z poziomu interfejsu API usługi Azure Monitor. W wartościach wymiarów metryk, takich jak nazwa kontenera, nie jest uwzględniania liter. Dlatego podczas porównywania ciągów na tych wartościach wymiarów należy używać porównania bez uwzględniania liter. Aby dowiedzieć się, jak wyświetlać metryki z usługi Azure Monitor, zobacz artykuł [Get metrics from Azure Monitor](./monitor-cosmos-db.md) (Uzyskiwanie metryk z usługi Azure Monitor).

W tym artykule przedstawiono typowe przypadki użycia oraz sposób, w jaki metryki usługi Azure Cosmos DB mogą być używane do analizowania i debugowania tych problemów. Metryki są zbierane co pięć minut i przechowywane przez siedem dni.

## <a name="view-metrics-from-azure-portal"></a>Wyświetlanie metryk z Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

1. Otwórz **okienko Metryki.** Domyślnie okienko metryk zawiera metryki magazynu, indeksu i jednostek żądań dla wszystkich baz danych na koncie usługi Azure Cosmos. Te metryki można filtrować według bazy danych, kontenera lub regionu. Możesz również filtrować metryki z określonym poziomem szczegółowości czasu. Więcej szczegółów na temat metryk przepływności, magazynu, dostępności, opóźnienia i spójności można znaleźć na oddzielnych kartach. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB metryk wydajności w programie Azure Portal":::

W okienku Metryki są dostępne **następujące** metryki:

* **Metryki przepływności** — ta metryka przedstawia liczbę żądań, które zostały zużyte lub zakończyły się niepowodzeniem (kod odpowiedzi 429), ponieważ przekroczono przepływność lub pojemność magazynu aprowizowana dla kontenera.

* **Metryki magazynu —** ta metryka przedstawia rozmiar danych i użycie indeksu.

* **Metryki dostępności —** ta metryka przedstawia procent żądań pomyślnych w ciągu łącznej liczby żądań na godzinę. Wskaźnik powodzenia jest definiowany przez Azure Cosmos DB SLA.

* **Metryki opóźnienia —** ta metryka przedstawia opóźnienie odczytu i zapisu zaobserwowane przez Azure Cosmos DB w regionie, w którym działa Twoje konto. Możesz wizualizować opóźnienia między regionami dla konta z replikacją geograficzną. Ta metryka nie reprezentuje opóźnienia żądania typu end-to-end.

* **Metryki spójności —** ta metryka pokazuje, jak ostateczna jest spójność dla wybieranego modelu spójności. W przypadku kont w wielu regionach ta metryka pokazuje również opóźnienie replikacji między wybranymi regionami.

* **Metryki systemu —** ta metryka pokazuje, ile żądań metadanych jest obsługiwanych przez partycję podstawową. Ułatwia to również identyfikowanie żądań z ograniczeniami.

W poniższych sekcjach wyjaśniono typowe scenariusze, w których można używać Azure Cosmos DB metryk. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Informacje o tym, ile żądań zakończyło się powodzeniem lub błędami

Aby rozpocząć, przejdź do [Azure Portal](https://portal.azure.com) i przejdź do bloku **Metryki.** W bloku znajdź wykres **Liczba żądań przekroczonych pojemność na 1 minutę. Na tym wykresie przedstawiono minutę po minucie całkowitą liczbę żądań segmentowanych według kodu stanu. Aby uzyskać więcej informacji na temat kodów stanu [HTTP, zobacz Http status codes for Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Najbardziej powszechnym kodem stanu błędu jest 429 (ograniczanie/ograniczanie szybkości). Ten błąd oznacza, że żądania Azure Cosmos DB są więcej niż aprowizowana przepływność. Najbardziej powszechnym rozwiązaniem tego problemu jest [skalowanie w górę](./set-throughput.md) procesorów DLA danej kolekcji.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Liczba żądań na minutę":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Określanie dystrybucji przepływności między partycjami

Dobra kardynalność kluczy partycji jest niezbędna dla każdej skalowalnej aplikacji. Aby określić dystrybucję przepływności dowolnego partycjonowanych kontenerów podzielonych według partycji, przejdź do bloku **Metryki** w [Azure Portal](https://portal.azure.com). Na karcie **Przepływność** podział magazynu jest wyświetlany na wykresie Maksymalna liczba zużytych **ru/s przez poszczególne partycje** fizyczne. Na poniższej ilustracji przedstawiono przykład słabego rozkładu danych, jak pokazano w przypadku pochyłej partycji po lewej stronie.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Duże użycie pojedynczej partycji":::

Nierówna dystrybucja przepływności może powodować *gorące* partycje, co może powodować ograniczanie żądań i wymagać ponownego partycjonowania. Aby uzyskać więcej informacji na temat partycjonowania w Azure Cosmos DB, zobacz [Partycjonowanie](./partitioning-overview.md)i skalowanie w Azure Cosmos DB .

## <a name="determine-the-storage-distribution-across-partitions"></a>Określanie dystrybucji magazynu między partycjami

Dobra kardynalność partycji jest niezbędna dla każdej skalowalnej aplikacji. Aby określić dystrybucję magazynu dowolnego partycjonowanych kontenerów podzielonych według partycji, przejdź do bloku Metryki w [Azure Portal](https://portal.azure.com). Na karcie Magazyn podział magazynu jest wyświetlany na wykresie Magazyn danych i indeksu, który jest zużywany przez najważniejsze klucze partycji. Na poniższej ilustracji przedstawiono słabą dystrybucję magazynu danych, jak pokazano w przypadku pochyłej partycji po lewej stronie.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Przykład słabej dystrybucji danych":::

Aby określić główną przyczynę tego, który klucz partycji powoduje niesłabnący rozkład, kliknij partycję na wykresie.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Klucz partycji niesyłania dystrybucji":::

Po zidentyfikowaniu, który klucz partycji powoduje niesyć w dystrybucji, może być trzeba ponownie podzielić kontener na bardziej rozproszony klucz partycji. Aby uzyskać więcej informacji na temat partycjonowania w Azure Cosmos DB, zobacz [Partycjonowanie](./partitioning-overview.md)i skalowanie w Azure Cosmos DB .

## <a name="compare-data-size-against-index-size"></a>Porównanie rozmiaru danych z rozmiarem indeksu

W Azure Cosmos DB łączny zużyty magazyn jest kombinacją zarówno rozmiaru danych, jak i rozmiaru indeksu. Zazwyczaj rozmiar indeksu jest ułamkiem rozmiaru danych. Aby dowiedzieć się więcej, zobacz [artykuł Rozmiar](index-policy.md#index-size) indeksu. W bloku Metryki w Azure Portal [karta](https://portal.azure.com)Magazyn przedstawia podział użycia magazynu na podstawie danych i indeksu.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Jeśli chcesz zaoszczędzić miejsce na indeksie, możesz dostosować [zasady indeksowania](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Debugowanie przyczyn powolnego działania zapytań

W zestawach SDK interfejsu API SQL Azure Cosmos DB statystyki wykonywania zapytań.

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

*Element QueryMetrics* zawiera szczegółowe informacje o tym, jak długo trwało wykonywanie poszczególnych składników zapytania. Najczęstszą główną przyczyną długotrwałych zapytań są skanowania, co oznacza, że zapytanie nie może wykorzystać indeksów. Ten problem można rozwiązać za pomocą lepszego warunku filtru.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak monitorować i debugować problemy przy użyciu metryk dostępnych w Azure Portal. Aby dowiedzieć się więcej na temat poprawy wydajności bazy danych, zapoznaj się z następującymi artykułami:

* Aby dowiedzieć się, jak wyświetlać metryki z usługi Azure Monitor, zobacz artykuł Get [metrics from Azure Monitor](./monitor-cosmos-db.md) (Uzyskiwanie metryk z usługi Azure Monitor). 
* [Testowanie wydajności i skali za pomocą Azure Cosmos DB](performance-testing.md)
* [Porady dotyczące wydajności usługi Azure Cosmos DB](performance-tips.md)