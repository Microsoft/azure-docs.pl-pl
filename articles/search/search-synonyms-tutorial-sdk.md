---
title: Przykładowe synonimy języka C#
titleSuffix: Azure Cognitive Search
description: W tym przykładzie w języku C# dowiesz się, jak dodać funkcję synonimów do indeksu w usłudze Azure Wyszukiwanie poznawcze. Mapa synonimów jest listą równoważnych terminów. Pola z obsługą synonimów rozwijają zapytania, aby uwzględnić termin podanego przez użytkownika i wszystkie powiązane synonimy.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704660"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Przykład: Dodawanie synonimów dla usługi Azure Wyszukiwanie poznawcze w języku C #

Synonimy rozszerzają zapytanie, dopasowując wyrażenia uznane za semantycznie równoważne z wyrażeniem wejściowym. Przykładowo można sprawić, aby wyraz „samochód” pasował do dokumentów zawierających wyrażenia „auto” lub „pojazd”. 

Na platformie Azure Wyszukiwanie poznawcze synonimy są zdefiniowane w formie *synonimu*, za pomocą *reguł mapowania* , które kojarzą równoważne warunki. Ten przykład obejmuje podstawowe kroki umożliwiające dodawanie i używanie synonimów z istniejącym indeksem.

W tym przykładzie dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz mapę synonimów przy użyciu [klasy SynonymMap](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Ustaw [Właściwość SynonymMapsName](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) dla pól, które powinny obsługiwać rozszerzanie zapytań za pomocą synonimów.

Można wysyłać zapytania do pola z włączonym synonimem, jak zwykle. Brak dodatkowej składni zapytania wymaganej do uzyskania dostępu do synonimów.

Możesz utworzyć wiele map synonimów, opublikować je jako zasób obejmujący usługę dostępny dla dowolnych indeksów, a następnie określić, której z nich należy używać na poziomie pola. W czasie zapytania, poza wyszukiwaniem indeksu, usługa Azure Wyszukiwanie poznawcze wykonuje wyszukiwanie w formie synonimu, jeśli jest określona dla pól używanych w zapytaniu.

> [!NOTE]
> Synonimy mogą być tworzone programowo, ale nie w portalu.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania samouczka obejmują poniższe elementy:

* [Program Visual Studio](https://www.visualstudio.com/downloads/)
* [Usługa Wyszukiwanie poznawcze platformy Azure](search-create-service-portal.md)
* [Azure.Search.Docpakiet uments](https://www.nuget.org/packages/Azure.Search.Documents/)

Jeśli nie znasz biblioteki klienta .NET, zobacz [jak korzystać z usługi Azure wyszukiwanie poznawcze w programie .NET](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Przykładowy kod

Pełny kod źródłowy przykładowej aplikacji użytej w tym przykładzie można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Omówienie

Zapytania "Before" i "After" są używane do zademonstrowania wartości synonimów. W tym przykładzie Przykładowa aplikacja wykonuje zapytania i zwraca wyniki na przykładowym indeksie "Hotele" wypełnionym dwoma dokumentami. Najpierw aplikacja wykonuje zapytania wyszukiwania przy użyciu terminów i fraz, które nie są wyświetlane w indeksie. Po drugie, kod włącza funkcję synonimów, a następnie ponownie wystawia te same zapytania, ten czas zwraca wyniki na podstawie dopasowań na mapie synonimów. 

Poniższy kod demonstruje ogólny przepływ.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>Zapytania „przed”

Względem indeksu `RunQueriesWithNonExistentTermsInIndex` wykonaj zapytania wyszukiwania z użyciem wyrażeń „five star”, „internet” oraz „economy AND hotel”.

Zapytania zwrotne, takie jak "pięć gwiazdek", muszą być ujęte w cudzysłów i mogą również potrzebować znaków ucieczki w zależności od klienta.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Żaden z dwóch zindeksowanych dokumentów nie zawiera warunków, dlatego otrzymujemy następujące dane wyjściowe z pierwszego `RunQueriesWithNonExistentTermsInIndex` :  **nie pasujący dokument**.

## <a name="enable-synonyms"></a>Włączanie synonimów

Po uruchomieniu zapytań "Before" przykładowy kod włącza synonimy. Włączenie synonimów to dwuetapowy proces. Najpierw Zdefiniuj i przekaż reguły synonimów. Następnie skonfiguruj pola, aby z nich korzystać. Proces jest opisany w `UploadSynonyms` i `EnableSynonymsInHotelsIndex`.

1. Dodaj mapę synonimów do usługi wyszukiwania. W `UploadSynonyms` definiujemy cztery reguły w mapie synonimów „desc-synonymmap” i przekazujemy je do usługi.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Skonfiguruj pola z możliwością wyszukiwania, aby używać mapy synonimów w definicji indeksu. W `AddSynonymMapsToFields` włączamy synonimy w dwóch polach, `category` i `tags`, ustawiając właściwość `SynonymMapNames` na nazwę nowo przekazanej mapy synonimów.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Podczas dodawania mapy synonimów ponowne kompilowanie indeksu nie jest wymagane. Możesz dodać mapę synonimów do usługi, a następnie zmienić istniejące definicje pól w dowolnym indeksie, aby użyć nowej mapy synonimów. Dodawanie nowych atrybutów nie ma wpływu na dostępność indeksu. To samo dotyczy wyłączania synonimów dla pola. Możesz po prostu ustawić właściwość `SynonymMapNames` na pustą listę.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>Zapytania „po”

Po przekazaniu mapy synonimów i zaktualizowaniu indeksu do używania mapy synonimów drugie wywołanie `RunQueriesWithNonExistentTermsInIndex` zwraca następujące wyniki:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

Pierwsze zapytanie znajduje dokument na podstawie reguły `five star=>luxury`. Drugie zapytanie rozszerza wyszukiwanie przy użyciu `internet,wifi`, a trzecie przy użyciu `hotel, motel` i `economy,inexpensive=>budget` w zakresie wyszukiwania dopasowanych dokumentów.

Dodanie synonimów całkowicie zmienia funkcję wyszukiwania. W tym przykładzie oryginalne zapytania nie zwróciły znaczących wyników, mimo że dokumenty w naszym indeksie były istotne. Włączając synonimy, możemy rozszerzyć indeks, aby objął popularne wyrażenia, nie zmieniając danych źródłowych w indeksie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Najszybszym sposobem oczyszczenia po przykładzie jest usunięcie grupy zasobów zawierającej usługę Wyszukiwanie poznawcze platformy Azure. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. Nazwa grupy zasobów w portalu znajduje się na stronie Przegląd usługi Azure Wyszukiwanie poznawcze.

## <a name="next-steps"></a>Następne kroki

Ten przykład przedstawia funkcję synonimów w kodzie C# do tworzenia i publikowania reguł mapowania, a następnie wywoływania mapy synonimów w zapytaniu. Dodatkowe informacje można znaleźć w dokumentacji referencyjnej [zestawu .NET SDK](/dotnet/api/overview/azure/search.documents-readme) i [interfejsu API REST](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Jak używać synonimów w usłudze Azure Wyszukiwanie poznawcze](search-synonyms.md)