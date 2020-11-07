---
title: Zaplanuj wykonywanie indeksatora
titleSuffix: Azure Cognitive Search
description: Zaplanuj indeksatory usługi Azure Wyszukiwanie poznawcze, aby okresowo indeksować zawartość lub w określonych godzinach.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 80c3f9aa02680097276f966ce6aea02acf1e40fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358800"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Jak zaplanować indeksatory na platformie Azure Wyszukiwanie poznawcze

Indeksator jest zwykle uruchamiany jednokrotnie, natychmiast po jego utworzeniu. Można uruchomić je ponownie na żądanie przy użyciu portalu, interfejsu API REST lub zestawu .NET SDK. Można również skonfigurować indeksator do uruchamiania okresowo zgodnie z harmonogramem.

Niektóre sytuacje, w których planowanie indeksatora jest przydatne:

* Dane źródłowe ulegną zmianie z upływem czasu i chcesz, aby indeksatory platformy Azure Wyszukiwanie poznawcze automatycznie przetwarzać zmienione dane.
* Indeks zostanie wypełniony z wielu źródeł danych i chcesz się upewnić, że Indeksatory są uruchamiane w różnych porach, aby zmniejszyć liczbę konfliktów.
* Dane źródłowe są bardzo duże i chcesz rozłożyć przetwarzanie indeksatora w czasie. Aby uzyskać więcej informacji na temat indeksowania dużych ilości danych, zobacz [jak indeksować duże zestawy danych w usłudze Azure wyszukiwanie poznawcze](search-howto-large-index.md).

Harmonogram jest wbudowaną funkcją Wyszukiwanie poznawcze platformy Azure. Nie można użyć zewnętrznego harmonogramu do kontrolowania indeksatorów wyszukiwania.

## <a name="define-schedule-properties"></a>Definiowanie właściwości harmonogramu

Harmonogram indeksatora ma dwie właściwości:
* **Interwał** , który określa ilość czasu między wykonaniami zaplanowanego indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut, a największe to 24 godziny.
* **Godzina rozpoczęcia (UTC)** , która wskazuje czas pierwszego uruchomienia indeksatora.

Możesz określić harmonogram podczas pierwszego tworzenia indeksatora lub przez aktualizację właściwości indeksatora później. Harmonogramy indeksatorów można ustawiać przy użyciu [portalu](#portal), [interfejsu API REST](#restApi)lub [zestawu .NET SDK](#dotNetSdk).

Tylko jedno wykonanie indeksatora może być uruchamiane w danym momencie. Jeśli indeksator jest już uruchomiony po zaplanowaniu następnego wykonania, to wykonanie jest odroczone do czasu następnego zaplanowanego czasu.

Rozważmy przykład, aby zwiększyć tę liczbę. Załóżmy, że skonfigurowano harmonogram indeksatora z **interwałem** co godzinę i godziną **rozpoczęcia** od 1 czerwca 2019 8:00:00 o godz. UTC. Oto, co się dzieje, gdy działanie indeksatora trwa dłużej niż godzinę:

* Pierwsze wykonanie indeksatora rozpocznie się o 1 czerwca 2019 o godzinie 8:00 czasu UTC. Załóżmy, że wykonanie tej operacji trwa 20 minut (lub w dowolnym czasie krótszym niż 1 godzina).
* Drugie wykonanie jest uruchamiane o lub około 1 czerwca 2019 9:00 czasu UTC. Załóżmy, że to wykonanie trwa 70 minut — więcej niż godzinę — i nie zostanie ukończone do 10:10 czasu UTC.
* Zaplanowano rozpoczęcie trzeciego wykonania o godzinie 10:00 czasu UTC, ale w tym momencie poprzednie wykonanie jest nadal uruchomione. To zaplanowane wykonanie jest następnie pomijane. Następne wykonanie indeksatora nie zostanie uruchomione do czasu 11:00 czasu UTC.

> [!NOTE]
> Jeśli indeksator jest ustawiony na określony harmonogram, ale wielokrotnie powtarza się w tym samym dokumencie za każdym razem, gdy zostanie on uruchomiony, indeksator zacznie działać w krótszym interwale (maksymalnie co 24 godziny) do momentu pomyślnego przekroczenia postępu.  Jeśli uważasz, że Rozwiązano problem, który spowodował zablokowanie indeksatora w określonym punkcie, można wykonać uruchomienie na żądanie indeksatora, a jeśli ten proces pomyślnie przejdzie, indeks ponownie powróci do jego ustawionego interwału harmonogramu.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Planowanie w portalu

Kreator importu danych w portalu umożliwia zdefiniowanie harmonogramu indeksatora podczas tworzenia. Domyślne ustawienie harmonogramu jest **co godzinę** , co oznacza, że indeksator jest uruchamiany po utworzeniu i ponownie uruchamiany co godzinę.

Jeśli nie chcesz, aby indeksator był uruchamiany automatycznie lub **codziennie** do uruchamiania raz dziennie, możesz zmienić ustawienie harmonogramu na **jeden raz** . Ustaw wartość na **Custom** , jeśli chcesz określić inny interwał lub określony przyszły czas rozpoczęcia.

Po ustawieniu harmonogramu na **niestandardowy** , pola są wyświetlane, aby umożliwić określenie **interwału** i **godziny rozpoczęcia (UTC)**. Najkrótszy dozwolony interwał wynosi 5 minut, a najdłuższy czas to 1440 minut (24 godziny).

   ![Ustawianie harmonogramu indeksatora w Kreatorze importu danych](media/search-howto-schedule-indexers/schedule-import-data.png "Ustawianie harmonogramu indeksatora w Kreatorze importu danych")

Po utworzeniu indeksatora można zmienić ustawienia harmonogramu za pomocą panelu edycji indeksatora. Pola harmonogramu są takie same jak w Kreatorze importu danych.

   ![Ustawianie harmonogramu w panelu edytowania indeksatora](media/search-howto-schedule-indexers/schedule-edit.png "Ustawianie harmonogramu w panelu edytowania indeksatora")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Planowanie przy użyciu interfejsów API REST

Można zdefiniować harmonogram dla indeksatora przy użyciu interfejsu API REST. W tym celu należy uwzględnić Właściwość **Schedule** podczas tworzenia lub aktualizowania indeksatora. Poniższy przykład przedstawia żądanie PUT, aby zaktualizować istniejący indeksator:

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Parametr **interwału** jest wymagany. Interwał odnosi się do czasu między rozpoczęciem dwóch kolejnych wykonań indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut; Najdłuższa wartość to jeden dzień. Musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Wzorzec dla tego elementu to: `P(nD)(T(nH)(nM))` . Przykłady: `PT15M` co 15 minut, `PT2H` przez co 2 godziny.

Opcjonalny parametr **StartTime** wskazuje, kiedy mają zostać rozpoczęte zaplanowane wykonania. W przypadku pominięcia jest używany bieżący czas UTC. Ten czas może znajdować się w przeszłości, w tym przypadku pierwsze wykonanie jest zaplanowane tak, jakby indeksator działał w sposób ciągły od pierwotnego czasu **rozpoczęcia**.

Indeksator można również uruchomić na żądanie w dowolnym momencie przy użyciu wywołania Uruchom indeksator. Aby uzyskać więcej informacji o uruchamianiu indeksatorów i ustawianiu harmonogramów indeksatora, zobacz [Uruchamianie indeksatora](/rest/api/searchservice/run-indexer), [pobieranie indeksatora](/rest/api/searchservice/get-indexer)i [Aktualizowanie indeksatora](/rest/api/searchservice/update-indexer) w dokumentacji interfejsu API REST.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Planowanie przy użyciu zestawu .NET SDK

Można zdefiniować harmonogram dla indeksatora przy użyciu zestawu Azure Wyszukiwanie poznawcze .NET SDK. W tym celu należy uwzględnić Właściwość **Schedule** podczas tworzenia lub aktualizowania indeksatora.

Poniższy przykład w języku C# tworzy indeksator usługi Azure SQL Database przy użyciu wstępnie zdefiniowanego źródła danych i indeksu, a następnie ustawia jego harmonogram uruchamiania raz dziennie:

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


Jeśli właściwość **Schedule** zostanie pominięta, indeksator zostanie uruchomiony tylko natychmiast po jego utworzeniu.

Parametr **StartTime** można ustawić na godzinę w przeszłości. W takim przypadku pierwsze wykonanie jest zaplanowane tak, jakby indeksator działał w sposób ciągły od danego momentu **rozpoczęcia**.

Harmonogram jest definiowany przy użyciu klasy [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) . Konstruktor **IndexingSchedule** wymaga parametru **interwału** określonego przy użyciu obiektu **TimeSpan** . Najmniejsza dozwolona wartość interwału wynosi 5 minut, a największe to 24 godziny. Drugi parametr **StartTime** określony jako obiekt **DateTimeOffset** jest opcjonalny.

Zestaw SDK platformy .NET umożliwia kontrolowanie operacji indeksatora przy użyciu [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). 

Indeksator można uruchomić na żądanie w dowolnym momencie przy użyciu jednej z metod [RunIndexer](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) lub [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync) .

Aby uzyskać więcej informacji na temat tworzenia, aktualizowania i uruchamiania indeksatorów, zobacz [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient).