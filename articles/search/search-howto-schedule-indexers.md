---
title: Zaplanuj wykonywanie indeksatora
titleSuffix: Azure Cognitive Search
description: Zaplanuj indeksatory usługi Azure Wyszukiwanie poznawcze, aby okresowo indeksować zawartość lub w określonych godzinach.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: dfd8526a035d4eef4d07539e541e37c88023b500
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063217"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Jak zaplanować indeksatory na platformie Azure Wyszukiwanie poznawcze

Indeksator jest zwykle uruchamiany jednokrotnie, natychmiast po jego utworzeniu. Później można uruchomić ją ponownie na żądanie przy użyciu Azure Portal, [Uruchom indeksatora (REST)](/rest/api/searchservice/run-indexer)lub zestawu Azure SDK. Alternatywnie można również skonfigurować indeksator do uruchamiania zgodnie z harmonogramem. Niektóre sytuacje, w których jest przydatne planowanie indeksatora, to m.in.:

* Dane źródłowe zostaną zmienione z upływem czasu i chcesz, aby indeksator wyszukiwania automatycznie przetworzył różnicę.
* Dane źródłowe są bardzo duże i chcesz rozłożyć przetwarzanie indeksatora w czasie. Aby uzyskać więcej informacji na temat indeksowania dużych ilości danych, zobacz [jak indeksować duże zestawy danych w usłudze Azure wyszukiwanie poznawcze](search-howto-large-index.md).
* Indeks wyszukiwania zostanie wypełniony z wielu źródeł danych, a indeksatory mają być uruchamiane w różnym czasie w celu zmniejszenia konfliktów.

Wizualnie harmonogram może wyglądać następująco: od 1 stycznia, co 50 minut.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> Harmonogram jest wbudowaną funkcją Wyszukiwanie poznawcze platformy Azure. Nie ma obsługi dla zewnętrznych harmonogramów.

## <a name="schedule-property"></a>Właściwość harmonogramu

Harmonogram jest częścią definicji indeksatora. Jeśli właściwość **Schedule** zostanie pominięta, indeksator zostanie uruchomiony tylko natychmiast po jego utworzeniu. W przypadku dodania właściwości **harmonogramu** należy określić dwie części.

| Właściwość | Opis |
|----------|-------------|
|**Interwał** | potrzeb Czas między rozpoczęciem dwóch kolejnych wykonań indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut, a wartość maksymalna to 1440 minut (24 godziny). Musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Wzorzec dla tego elementu to: `P(nD)(T(nH)(nM))` . <br/><br/>Przykłady: `PT15M` co 15 minut, `PT2H` przez co 2 godziny.|
| **Godzina rozpoczęcia (UTC)** | obowiązkowe Wskazuje, kiedy mają zostać rozpoczęte zaplanowane wykonania. W przypadku pominięcia jest używany bieżący czas UTC. Ten czas może znajdować się w przeszłości, w tym przypadku pierwsze wykonanie jest zaplanowane tak, jakby indeksator działał w sposób ciągły od pierwotnego czasu **rozpoczęcia**.<br/><br/>Przykłady: `2021-01-01T00:00:00Z` od północy 1 stycznia, `2021-01-05T22:28:00Z` zaczynające się o godz. 10:28 5 stycznia.|

## <a name="scheduling-behavior"></a>Planowanie zachowania

Tylko jedno wykonanie indeksatora może być uruchamiane w danym momencie. Jeśli indeksator jest już uruchomiony po zaplanowaniu następnego wykonania, to wykonanie jest odroczone do czasu następnego zaplanowanego czasu.

Rozważmy przykład, aby zwiększyć tę liczbę. Załóżmy, że skonfigurowano harmonogram indeksatora z **interwałem** co godzinę i godziną **rozpoczęcia** od 1 czerwca 2019 8:00:00 o godz. UTC. Oto, co się dzieje, gdy działanie indeksatora trwa dłużej niż godzinę:

* Pierwsze wykonanie indeksatora rozpocznie się o 1 czerwca 2019 o godzinie 8:00 czasu UTC. Załóżmy, że wykonanie tej operacji trwa 20 minut (lub w dowolnym czasie krótszym niż 1 godzina).
* Drugie wykonanie jest uruchamiane o lub około 1 czerwca 2019 9:00 czasu UTC. Załóżmy, że to wykonanie trwa 70 minut — więcej niż godzinę — i nie zostanie ukończone do 10:10 czasu UTC.
* Zaplanowano rozpoczęcie trzeciego wykonania o godzinie 10:00 czasu UTC, ale w tym momencie poprzednie wykonanie jest nadal uruchomione. To zaplanowane wykonanie jest następnie pomijane. Następne wykonanie indeksatora nie zostanie uruchomione do czasu 11:00 czasu UTC.

> [!NOTE]
> Jeśli indeksator jest ustawiony na określony harmonogram, ale wielokrotnie powtarza się w tym samym dokumencie, indeksator zacznie działać w krótszym czasie (maksymalnie co 24 godziny) do momentu pomyślnego przekroczenia postępu. Jeśli uważasz, że problem dotyczy danego problemu, możesz uruchomić indeksator ręcznie i jeśli indeksowanie powiedzie się, indeksator powróci do swojego regularnego harmonogramu.

## <a name="schedule-using-rest"></a>Planowanie przy użyciu REST

Określ właściwość **Schedule** podczas tworzenia lub aktualizowania indeksatora.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Planowanie przy użyciu platformy .NET

Poniższy przykład w języku C# tworzy indeksator usługi Azure SQL Database przy użyciu wstępnie zdefiniowanego źródła danych i indeksu i ustawia jego harmonogram do uruchomienia raz dziennie, rozpoczynając od teraz:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Harmonogram jest definiowany przy użyciu klasy [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) podczas tworzenia lub aktualizowania indeksatora przy użyciu [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Konstruktor **IndexingSchedule** wymaga parametru **interwału** określonego przy użyciu obiektu **TimeSpan** . Jak wspomniano wcześniej, najmniejsza dozwolona wartość interwału wynosi 5 minut, a największe to 24 godziny. Drugi parametr **StartTime** określony jako obiekt **DateTimeOffset** jest opcjonalny.

## <a name="next-steps"></a>Następne kroki

W przypadku indeksatorów, które są uruchamiane zgodnie z harmonogramem, można monitorować operacje przez pobranie stanu usługi wyszukiwania lub uzyskiwanie szczegółowych informacji przez włączenie rejestrowania diagnostycznego.

* [Monitorowanie stanu indeksatora wyszukiwania](search-howto-monitor-indexers.md)
* [Zbieranie i analizowanie danych dziennika](search-monitor-logs.md)