---
title: Przykładowe synonimy języka C#
titleSuffix: Azure Cognitive Search
description: W tym przykładzie w języku C# dowiesz się, jak dodać funkcję synonimów do indeksu w usłudze Azure Wyszukiwanie poznawcze. Mapa synonimów jest listą równoważnych terminów. Pola z obsługą synonimów rozwijają zapytania, aby uwzględnić termin podanego przez użytkownika i wszystkie powiązane synonimy.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 842d43c82875a1a8e5e45ba14f47ceb6eac26727
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538810"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Przykład: Dodawanie synonimów dla usługi Azure Wyszukiwanie poznawcze w języku C #

Synonimy rozszerzają zapytanie, dopasowując wyrażenia uznane za semantycznie równoważne z wyrażeniem wejściowym. Przykładowo można sprawić, aby wyraz „samochód” pasował do dokumentów zawierających wyrażenia „auto” lub „pojazd”. 

Na platformie Azure Wyszukiwanie poznawcze synonimy są zdefiniowane w formie *synonimu*, za pomocą *reguł mapowania* , które kojarzą równoważne warunki. Ten przykład obejmuje podstawowe kroki umożliwiające dodawanie i używanie synonimów z istniejącym indeksem. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz mapę synonimów przy użyciu klasy  [SynonymMap](/dotnet/api/microsoft.azure.search.models.synonymmap) . 
> * Ustaw właściwość [SynonymMaps](/dotnet/api/microsoft.azure.search.models.field.synonymmaps) dla pól, które powinny obsługiwać rozszerzanie zapytań za pomocą synonimów.

Można wysyłać zapytania do pola z włączonym synonimem, jak zwykle. Brak dodatkowej składni zapytania wymaganej do uzyskania dostępu do synonimów.

Możesz utworzyć wiele map synonimów, opublikować je jako zasób obejmujący usługę dostępny dla dowolnych indeksów, a następnie określić, której z nich należy używać na poziomie pola. W czasie zapytania, poza wyszukiwaniem indeksu, usługa Azure Wyszukiwanie poznawcze wykonuje wyszukiwanie w formie synonimu, jeśli jest określona dla pól używanych w zapytaniu.

> [!NOTE]
> Synonimy mogą być tworzone programowo, ale nie w portalu. Jeśli obsługa funkcji synonimów w witrynie Azure Portal byłaby dla Ciebie przydatna, przekaż swoją opinię na platformie [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania samouczka obejmują poniższe elementy:

* [Program Visual Studio](https://www.visualstudio.com/downloads/)
* [Usługa Wyszukiwanie poznawcze platformy Azure](search-create-service-portal.md)
* [Biblioteka Microsoft.Azure.Search .NET](/dotnet/api/overview/azure/search)
* [Jak korzystać z usługi Azure Wyszukiwanie poznawcze z poziomu aplikacji platformy .NET](./search-howto-dotnet-sdk.md)

## <a name="overview"></a>Omówienie

Zapytania „przed” i „po” demonstrują wartość synonimów. W tym przykładzie Użyj przykładowej aplikacji, która wykonuje zapytania i zwraca wyniki na przykładowym indeksie. Przykładowa aplikacja tworzy mały indeks o nazwie „hotels” uzupełniony dwoma dokumentami. Aplikacja wykonuje zapytania wyszukiwania przy użyciu wyrażeń i fraz, które nie pojawiają się w indeksie, włącza funkcję synonimów, a następnie ponownie wykonuje te same wyszukiwania. Poniższy kod demonstruje ogólny przepływ.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Procedura tworzenia i wypełniania przykładowego indeksu została omówiona w temacie [jak korzystać z usługi Azure wyszukiwanie poznawcze z poziomu aplikacji .NET](./search-howto-dotnet-sdk.md).

## <a name="before-queries"></a>Zapytania „przed”

Względem indeksu `RunQueriesWithNonExistentTermsInIndex` wykonaj zapytania wyszukiwania z użyciem wyrażeń „five star”, „internet” oraz „economy AND hotel”.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Żaden ze zindeksowanych dokumentów nie zawiera tych wyrażeń, więc z pierwszego indeksu `RunQueriesWithNonExistentTermsInIndex` uzyskujemy następujące dane wyjściowe.
```
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
```

## <a name="enable-synonyms"></a>Włączanie synonimów

Włączenie synonimów to dwuetapowy proces. Najpierw definiujemy i przekazujemy reguły synonimów, a następnie konfigurujemy pola, aby ich używały. Proces jest opisany w `UploadSynonyms` i `EnableSynonymsInHotelsIndex`.

1. Dodaj mapę synonimów do usługi wyszukiwania. W `UploadSynonyms` definiujemy cztery reguły w mapie synonimów „desc-synonymmap” i przekazujemy je do usługi.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Mapa synonimów musi być zgodna z formatem `solr` w standardzie open source. Ten format jest objaśniony w [synonimach na platformie Azure wyszukiwanie poznawcze](search-synonyms.md) w sekcji `Apache Solr synonym format` .

2. Skonfiguruj pola z możliwością wyszukiwania, aby używać mapy synonimów w definicji indeksu. W `EnableSynonymsInHotelsIndex` włączamy synonimy w dwóch polach, `category` i `tags`, ustawiając właściwość `synonymMaps` na nazwę nowo przekazanej mapy synonimów.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Podczas dodawania mapy synonimów ponowne kompilowanie indeksu nie jest wymagane. Możesz dodać mapę synonimów do usługi, a następnie zmienić istniejące definicje pól w dowolnym indeksie, aby użyć nowej mapy synonimów. Dodawanie nowych atrybutów nie ma wpływu na dostępność indeksu. To samo dotyczy wyłączania synonimów dla pola. Możesz po prostu ustawić właściwość `synonymMaps` na pustą listę.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Zapytania „po”

Po przekazaniu mapy synonimów i zaktualizowaniu indeksu do używania mapy synonimów drugie wywołanie `RunQueriesWithNonExistentTermsInIndex` zwraca następujące wyniki:

```
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```
Pierwsze zapytanie znajduje dokument na podstawie reguły `five star=>luxury`. Drugie zapytanie rozszerza wyszukiwanie przy użyciu `internet,wifi`, a trzecie przy użyciu `hotel, motel` i `economy,inexpensive=>budget` w zakresie wyszukiwania dopasowanych dokumentów.

Dodanie synonimów całkowicie zmienia funkcję wyszukiwania. W tym przykładzie oryginalne zapytania nie zwróciły znaczących wyników, mimo że dokumenty w naszym indeksie były istotne. Włączając synonimy, możemy rozszerzyć indeks, aby objął popularne wyrażenia, nie zmieniając danych źródłowych w indeksie.

## <a name="sample-application-source-code"></a>Kod źródłowy przykładowej aplikacji
Możesz znaleźć pełny kod źródłowy przykładowej aplikacji używanej w tym poradniku w portalu [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Najszybszym sposobem oczyszczenia po przykładzie jest usunięcie grupy zasobów zawierającej usługę Wyszukiwanie poznawcze platformy Azure. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. Nazwa grupy zasobów w portalu znajduje się na stronie Przegląd usługi Azure Wyszukiwanie poznawcze.

## <a name="next-steps"></a>Następne kroki

Ten przykład przedstawia funkcję synonimów w kodzie C# do tworzenia i publikowania reguł mapowania, a następnie wywoływania mapy synonimów w zapytaniu. Dodatkowe informacje można znaleźć w dokumentacji referencyjnej [zestawu .NET SDK](/dotnet/api/microsoft.azure.search) i [interfejsu API REST](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Jak używać synonimów w usłudze Azure Wyszukiwanie poznawcze](search-synonyms.md)