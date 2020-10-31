---
title: Testowanie wydajności i skalowania przy użyciu Azure Cosmos DB
description: Dowiedz się, jak przeprowadzać testowanie skalowalności i wydajności przy użyciu Azure Cosmos DB. Następnie można oszacować funkcjonalność Azure Cosmos DB w scenariuszach aplikacji o wysokiej wydajności.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: b89c48b5040c198cb1b4a6ce383baed51dfac19e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097417"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Testowanie wydajności i skalowania przy użyciu Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Testowanie wydajności i skalowania to kluczowy krok podczas opracowywania aplikacji. W przypadku wielu aplikacji warstwa bazy danych ma znaczny wpływ na ogólną wydajność i skalowalność. W związku z tym jest to krytyczny składnik testowania wydajności. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) jest tworzona specjalnie dla elastycznego skalowania i przewidywalnej wydajności. Te funkcje sprawiają, że jest to doskonałe rozwiązanie dla aplikacji, które wymagają warstwy bazy danych o wysokiej wydajności. 

Ten artykuł zawiera informacje dotyczące deweloperów implementujących zestawy testów wydajnościowych dla ich obciążeń Azure Cosmos DB. Można go również użyć do oceny Azure Cosmos DB w scenariuszach aplikacji o wysokiej wydajności. Skupia się przede wszystkim na izolowanych testach wydajności bazy danych, ale również zawiera najlepsze rozwiązania dotyczące aplikacji produkcyjnych.

Po przeczytaniu tego artykułu będzie można odpowiedzieć na następujące pytania: 

* Gdzie można znaleźć przykładową aplikację kliencką .NET do testowania wydajności Azure Cosmos DB? 
* Jak mogę uzyskać wysoką przepływność dzięki Azure Cosmos DB z mojej aplikacji klienckiej?

Aby rozpocząć pracę z kodem, Pobierz projekt z [Azure Cosmos DB przykładowego testowania wydajnościowego](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Celem tej aplikacji jest zaprezentowanie, jak uzyskać najlepszą wydajność Azure Cosmos DB z niewielką liczbą komputerów klienckich. Celem przykładu nie jest osiągnięcie wydajności szczytowej przepływności Azure Cosmos DB (które można skalować bez ograniczeń).

Jeśli szukasz opcji konfiguracji po stronie klienta, aby zwiększyć wydajność Azure Cosmos DB, zobacz [Azure Cosmos DB porady dotyczące wydajności](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Uruchamianie aplikacji do testowania wydajnościowego
Najszybszym sposobem na rozpoczęcie pracy jest skompilowanie i uruchomienie przykładu programu .NET, zgodnie z opisem w poniższych krokach. Możesz również przejrzeć kod źródłowy i zaimplementować podobne konfiguracje na własnych aplikacjach klienckich.

**Krok 1:** Pobierz projekt z [przykładu Azure Cosmos DB test wydajnościowy](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)lub rozwidlenie repozytorium GitHub.

**Krok 2:** Zmodyfikuj ustawienia dla EndpointUrl, AuthorizationKey, CollectionThroughput i DocumentTemplate (opcjonalnie) w App.config.

> [!NOTE]
> Przed udostępnieniem kolekcji o wysokiej przepływności zapoznaj się ze [stroną cennika](https://azure.microsoft.com/pricing/details/cosmos-db/) , aby oszacować koszty na kolekcję. Azure Cosmos DB rozrachunkowe magazyny i przepływność niezależnie od siebie co godzinę. Możesz zaoszczędzić koszty, usuwając lub obniżając przepływność kontenerów usługi Azure Cosmos po przetestowaniu.
> 
> 

**Krok 3.** Kompiluj i uruchom aplikację konsolową z wiersza polecenia. Powinny pojawić się dane wyjściowe podobne do następujących:

```bash
C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
Summary:
---------------------------------------------------------------------
Endpoint: https://arramacquerymetrics.documents.azure.com:443/
Collection : db.data at 100000 request units per second
Document Template*: Player.json
Degree of parallelism*: -1
---------------------------------------------------------------------
DocumentDBBenchmark starting...
Found collection data with 100000 RU/s
Starting Inserts with 100 tasks
Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

Summary:
---------------------------------------------------------------------
Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
---------------------------------------------------------------------
DocumentDBBenchmark completed successfully.
Press any key to exit...
```

**Krok 4 (w razie potrzeby):** Raportowana przepływność (RU/s) z narzędzia powinna być taka sama lub wyższa od alokowanej przepływności kolekcji lub zestawu kolekcji. Jeśli tak nie jest, zwiększenie DegreeOfParallelism w małych przyrostach może ułatwić osiągnięcie limitu. Jeśli przepływność ze Plateau aplikacji klienckiej, należy uruchomić wiele wystąpień aplikacji na dodatkowych maszynach klienckich. Jeśli potrzebujesz pomocy w tym kroku, Zastąp bilet pomocy technicznej z [Azure Portal](https://portal.azure.com).

Po uruchomieniu aplikacji można wypróbować różne [zasady indeksowania](index-policy.md) i [poziomy spójności](consistency-levels.md) , aby zrozumieć ich wpływ na przepływność i opóźnienia. Możesz również przejrzeć kod źródłowy i zaimplementować podobne konfiguracje do własnych zestawów testów lub aplikacji produkcyjnych.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano, jak można przeprowadzić testowanie wydajności i skalowania za pomocą Azure Cosmos DB przy użyciu aplikacji konsolowej platformy .NET. Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Przykład testowania wydajności Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opcje konfiguracji klienta w celu poprawy wydajności Azure Cosmos DB](performance-tips.md)
* [Partycjonowanie po stronie serwera w Azure Cosmos DB](partitioning-overview.md)


