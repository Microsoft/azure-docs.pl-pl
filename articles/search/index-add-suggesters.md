---
title: Tworzenie sugestora
titleSuffix: Azure Cognitive Search
description: Włącz akcje kwerendy typu "w Wyszukiwanie poznawcze na platformie Azure", tworząc sugerowane i formułowane żądania, które wywołują Autouzupełnianie lub proponowane automatycznie terminy zapytań.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a03ca4bcad9bb577db68e2728ff9dbebb5779a7a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626830"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Utwórz sugestię umożliwiającą włączenie autouzupełniania i sugerowanych wyników w zapytaniu

Na platformie Azure Wyszukiwanie poznawcze, typeahead lub "wyszukiwanie w trakcie pisania" jest włączane za pomocą narzędzia do *sugerowania*. Program sugerowany zawiera listę pól, które poddają się do dodatkowych tokenizacji, generując sekwencje prefiksów do obsługi dopasowań w przypadku częściowych warunków. Na przykład, sugestia, która zawiera pole miasto o wartości dla "Seattle", będzie mieć prefiks kombinacji "Sea", "siedzisko", "siedzisko" i "Seattl" do obsługi typeahead.

Dopasowania dotyczące częściowych terminów mogą być albo zakończono autoukończeniem zapytania lub sugestią dopasowania. Ten sam program sugerujący obsługuje oba środowiska.

## <a name="typeahead-experiences-in-cognitive-search"></a>Typeahead środowiska w Wyszukiwanie poznawcze

Typeahead może być *uzupełnieniem*, który kończy częściowe dane wejściowe dla całego zapytania warunkowego, lub *sugestie* , które zapraszają klikanie do określonego dopasowania. Funkcja autouzupełniania tworzy zapytanie. Sugestie tworzą pasujący dokument.

Poniższy zrzut ekranu przedstawiający [Tworzenie pierwszej aplikacji w języku C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje oba te elementy. Funkcja Autouzupełnianie przewiduje potencjalną kadencję, kończąc "TW" z "in". Sugestie to wyniki wyszukiwania mini, gdzie pole takie jak nazwa hotelu reprezentuje pasujący dokument wyszukiwania hotelowego z indeksu. W przypadku sugestii można wyświetlić dowolne pole, które zawiera opisowe informacje.

![Wizualne porównanie autouzupełniania i sugerowanych zapytań](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Wizualne porównanie autouzupełniania i sugerowanych zapytań")

Tych funkcji można używać osobno lub razem. Aby zaimplementować te zachowania w usłudze Azure Wyszukiwanie poznawcze, istnieje indeks i składnik zapytania. 

+ Dodaj sugestię do definicji indeksu wyszukiwania. Pozostała część tego artykułu koncentruje się na tworzeniu sugestii.

+ Wywołaj zapytanie z włączoną sugestią, w formie żądania sugestii lub żądania autouzupełniania, używając jednego z [interfejsów API wymienionych poniżej](#how-to-use-a-suggester).

Obsługa wyszukiwania jako typu jest włączana dla poszczególnych pól dla pól ciągów. Można zaimplementować oba zachowania typeahead w ramach tego samego rozwiązania wyszukiwania, jeśli chcesz, aby środowisko było podobne do określonego na zrzucie ekranu. Oba żądania są kierowane do kolekcji *dokumenty* określonego indeksu i odpowiedzi są zwracane po podaniu przez użytkownika co najmniej trzech ciągów wejściowych znaku.

## <a name="how-to-create-a-suggester"></a>Jak utworzyć sugestię

Aby utworzyć program sugerujący, Dodaj go do [definicji indeksu](/rest/api/searchservice/create-index). Funkcja Sugerowana przyjmuje nazwę i kolekcję pól, w których włączono środowisko typeahead. Najlepszym terminem tworzenia sugestii jest określenie pola, które będzie z niego korzystać.

+ Używaj tylko pól ciągów.

+ Jeśli pole String jest częścią typu złożonego (na przykład pole miasto w adres), Uwzględnij element nadrzędny w ścieżce pola: `"Address/City"` (REST, C# i Python) lub `["Address"]["City"]` (JavaScript).

+ Użyj domyślnego standardowego analizatora Lucene ( `"analyzer": null` ) lub [analizatora języka](index-add-language-analyzers.md) (na przykład `"analyzer": "en.Microsoft"` ) dla tego pola.

Jeśli spróbujesz utworzyć sugestię przy użyciu istniejących pól, interfejs API nie będzie go zezwalać. Prefiksy są generowane podczas indeksowania, gdy częściowe warunki w dwóch lub większej liczbie znaków są rozliczanie tokenów obok całych warunków. Mając na względzie, że istniejące pola są już tokenami, należy ponownie skompilować indeks, jeśli chcesz dodać je do sugestii. Aby uzyskać więcej informacji, zobacz [jak ponownie skompilować indeks wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md).

### <a name="choose-fields"></a>Wybierz pola

Chociaż funkcja sugerował ma kilka właściwości, to przede wszystkim kolekcja pól ciągów, dla których jest włączone środowisko wyszukiwania jako typ. Dla każdego indeksu istnieje jeden program sugerujący, dlatego lista Sugerowana musi zawierać wszystkie pola, które tworzą zawartość zarówno dla sugestii, jak i autouzupełniania.

Wykorzystaj automatycznie korzyści z większej puli pól do narysowania ze względu na to, że dodatkowa zawartość ma większy potencjał uzupełniania.

Sugestie z drugiej strony dają lepsze wyniki, gdy wybór pola jest selektywny. Należy pamiętać, że sugestia jest serwerem proxy dla dokumentu wyszukiwania, dzięki czemu można chcieć, aby pola, które najlepiej reprezentują pojedynczy wynik. Nazwy, tytuły lub inne unikatowe pola, które odróżniają wiele pasujących wyników działają najlepiej. Jeśli pola zawierają powtarzające się wartości, sugestie składają się z identycznych wyników i użytkownik nie wie, który z nich należy kliknąć.

Aby zaspokoić zarówno środowisko wyszukiwania zgodnie z oczekiwaniami, Dodaj wszystkie pola, które są potrzebne do autouzupełniania, ale Użyj "$select", "$top", "$filter" i "searchFields", aby kontrolować wyniki dla sugestii.

### <a name="choose-analyzers"></a>Wybieranie analizatorów

Wybór analizatora decyduje o tym, w jaki sposób pola są tokenami, a następnie prefiksem. Na przykład w przypadku ciągu z łącznikiem, takiego jak "kontekstowe", użycie analizatora języka spowoduje, że te kombinacje tokenu: "kontekst", "poufne", "kontekstowe". Czy użyto standardowego analizatora Lucene, ciąg podzielony na nie istnieje. 

Podczas oceniania analizatorów należy rozważyć użycie [interfejsu API analizy tekstu](/rest/api/searchservice/test-analyzer) do wglądu w sposób przetwarzania warunków. Po skompilowaniu indeksu można wypróbować różne analizatory w ciągu, aby wyświetlić dane wyjściowe tokenu.

Pola używające [analizatorów niestandardowych](index-add-custom-analyzers.md) lub [wbudowanych analizatorów](index-add-custom-analyzers.md#built-in-analyzers) (z wyjątkiem standardowych Lucene) są jawnie niedozwolone, aby zapobiec słabym rezultatom.

> [!NOTE]
> Jeśli musisz obejść ograniczenie analizatora, na przykład jeśli potrzebujesz słowa kluczowego lub analizatora ngram dla niektórych scenariuszy zapytań, należy użyć dwóch oddzielnych pól dla tej samej zawartości. Pozwoli to jednemu z pól na posiadanie sugestii, podczas gdy drugi można skonfigurować przy użyciu konfiguracji analizatora niestandardowego.

## <a name="create-using-the-portal"></a>Tworzenie przy użyciu portalu

W przypadku tworzenia indeksu przy użyciu kreatora **dodawania indeksu** lub **importowania danych** istnieje możliwość włączenia narzędzia do sugerowania:

1. W definicji indeksu wprowadź nazwę dla sugestii.

1. W każdej definicji pola dla nowych pól zaznacz pole wyboru w kolumnie Sugeruj. Pole wyboru jest dostępne tylko w przypadku pól ciągów. 

Jak wspomniano wcześniej, wybór analizatora ma wpływ na tokenizacji i tworzenie prefiksów. Podczas włączania sugestii należy wziąć pod uwagę całą definicję pola. 

## <a name="create-using-rest"></a>Tworzenie przy użyciu REST

W interfejsie API REST Dodaj sugestie za pomocą pozycji [Utwórz indeks](/rest/api/searchservice/create-index) lub [Aktualizuj indeks](/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Tworzenie przy użyciu platformy .NET

W języku C#, zdefiniuj [obiekt SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` jest kolekcją obiektu SearchIndex, ale może przyjmować tylko jeden element. Dodaj sugestię do definicji indeksu.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Odwołanie do właściwości

|Właściwość      |Opis      |
|--------------|-----------------|
| name        | Określony w definicji sugerowania, ale również wywoływany na żądanie autouzupełniania lub sugestii. |
| sourceFields | Określony w definicji sugestii. Jest to lista co najmniej jednego pola w indeksie, które są źródłem zawartości dla sugestii. Pola muszą być typu `Edm.String` i `Collection(Edm.String)` . Jeśli analizator jest określony w polu, musi być nazwany Analizator leksykalny z [tej listy](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (a nie analizatora niestandardowego). </br></br>Najlepszym rozwiązaniem jest określenie tylko tych pól, które nadają się do oczekiwanej i odpowiedniej odpowiedzi, niezależnie od tego, czy jest to kompletny ciąg na pasku wyszukiwania, czy na liście rozwijanej. </br></br>Nazwa hotelu jest dobrym kandydatem, ponieważ ma dokładnooć. Pełne pola, takie jak opisy i komentarze, są zbyt gęste. Podobnie powtarzające się pola, takie jak kategorie i Tagi, są mniej efektywne. W przykładach zawieramy "kategorię" Mimo to, aby pokazać, że można uwzględnić wiele pól. |
| searchMode  | Tylko parametr REST, ale również widoczny w portalu. Ten parametr nie jest dostępny w zestawie .NET SDK. Wskazuje ona strategię używaną do wyszukiwania fraz kandydatów. Jedynym obsługiwanym trybem jest `analyzingInfixMatching` , który jest obecnie zgodny na początku okresu.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Korzystanie z sugestii

W zapytaniu jest używany element sugerujący. Po utworzeniu sugestii Wywołaj jeden z następujących interfejsów API dla środowiska wyszukiwania w trakcie wpisywania:

+ [Sugestie interfejsu API REST](/rest/api/searchservice/suggestions)
+ [Autouzupełnianie interfejsu API REST](/rest/api/searchservice/autocomplete)
+ [SuggestAsync, Metoda](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync, Metoda](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

W aplikacji wyszukiwania kod klienta powinien korzystać z biblioteki, takiej jak [Funkcja autouzupełniania interfejsu użytkownika jQuery](https://jqueryui.com/autocomplete/) , aby zebrać częściowe zapytanie i zapewnić dopasowanie. Aby uzyskać więcej informacji na temat tego zadania, zobacz [Dodawanie funkcji Autouzupełnianie lub sugerowanych wyników do kodu klienta](search-add-autocomplete-suggestions.md).

Użycie interfejsu API jest zilustrowane w następującym wywołaniu interfejsu API REST autouzupełniania. W tym przykładzie istnieją dwa wnioski. Po pierwsze, podobnie jak w przypadku wszystkich zapytań, operacja jest odnosząca się do kolekcji dokumentów indeksu, a zapytanie zawiera parametr "Search", który w tym przypadku zawiera zapytanie częściowe. Po drugie należy dodać do żądania "suggesterName". Jeśli nie zdefiniowano sugestii w indeksie, wywołanie funkcji Autouzupełnianie lub sugestii zakończy się niepowodzeniem.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Przykładowy kod

+ [Dodawanie wyszukiwania do witryny sieci Web (JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) używa pakietu sugestii typu open source do częściowego uzupełniania terminu w aplikacji klienckiej.

+ [Tworzenie pierwszej aplikacji w języku C# (Lekcja 3 — Dodawanie wyszukiwania w trakcie pisania)](tutorial-csharp-type-ahead-and-suggestions.md) — przykład przedstawia sugerowane zapytania, Autouzupełnianie i nawigację aspektową. Ten kod zapewnia natywną obsługę typeahead zamiast używania widżetu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat żądań \ formowania.

> [!div class="nextstepaction"]
> [Dodawanie funkcji Autouzupełnianie i sugestii do kodu klienta](search-add-autocomplete-suggestions.md)