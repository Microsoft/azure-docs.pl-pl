---
title: Tworzenie sugestora
titleSuffix: Azure Cognitive Search
description: Włącz akcje kwerendy typu "w Wyszukiwanie poznawcze na platformie Azure", tworząc sugerowane i formułowane żądania, które wywołują Autouzupełnianie lub proponowane automatycznie terminy zapytań.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e439f7d2b0232a2e1c36517f24723e4e16f7e6bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537603"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Utwórz sugestię umożliwiającą włączenie autouzupełniania i sugerowanych wyników w zapytaniu

Na platformie Azure Wyszukiwanie poznawcze wartość "wyszukiwanie w trakcie pisania" jest włączana za pomocą konstrukcji **sugerującej** dodanej do [indeksu wyszukiwania](search-what-is-an-index.md). Program sugerujący obsługuje dwa środowiska: *Autouzupełnianie*, które uzupełnia częściowe dane wejściowe dla zapytania obejmującego całą kadencję, oraz *sugestie* , które zapraszają klikanie do określonego dopasowania. Funkcja autouzupełniania tworzy zapytanie. Sugestie tworzą pasujący dokument.

Poniższy zrzut ekranu przedstawiający [Tworzenie pierwszej aplikacji w języku C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje oba te elementy. Funkcja Autouzupełnianie przewiduje potencjalną kadencję, kończąc "TW" z "in". Sugestie to wyniki wyszukiwania mini, gdzie pole takie jak nazwa hotelu reprezentuje pasujący dokument wyszukiwania hotelowego z indeksu. W przypadku sugestii można wyświetlić dowolne pole, które zawiera opisowe informacje.

![Wizualne porównanie autouzupełniania i sugerowanych zapytań](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Wizualne porównanie autouzupełniania i sugerowanych zapytań")

Tych funkcji można używać osobno lub razem. Aby zaimplementować te zachowania w usłudze Azure Wyszukiwanie poznawcze, istnieje indeks i składnik zapytania. 

+ W indeksie Dodaj sugestię do indeksu. Możesz użyć portalu, [interfejsu API REST](/rest/api/searchservice/create-index)lub [zestawu .NET SDK](/dotnet/api/microsoft.azure.search.models.suggester). Pozostała część tego artykułu koncentruje się na tworzeniu sugestii.

+ W żądaniu zapytania Zadzwoń do jednego z [interfejsów API wymienionych poniżej](#how-to-use-a-suggester).

Obsługa wyszukiwania jako typu jest włączana dla poszczególnych pól dla pól ciągów. Można zaimplementować oba zachowania typeahead w ramach tego samego rozwiązania wyszukiwania, jeśli chcesz, aby środowisko było podobne do określonego na zrzucie ekranu. Oba żądania są kierowane do kolekcji *dokumenty* określonego indeksu i odpowiedzi są zwracane po podaniu przez użytkownika co najmniej trzech ciągów wejściowych znaku.

## <a name="what-is-a-suggester"></a>Co to jest sugerował?

Program sugerowany jest wewnętrzną strukturą danych, która obsługuje zachowania typu "Wyszukaj jako" przez przechowywanie prefiksów do dopasowania w przypadku zapytań częściowych. Podobnie jak w przypadku terminów z tokenami, prefiksy są przechowywane w odwróconych indeksach, po jednym dla każdego pola określonego w kolekcji pól sugerujących.

## <a name="define-a-suggester"></a>Definiowanie sugestii

Aby utworzyć sugestię, należy dodać jeden do [schematu indeksu](/rest/api/searchservice/create-index) i [ustawić każdą właściwość](#property-reference). Najlepszym terminem tworzenia sugestii jest określenie pola, które będzie z niego korzystać.

+ Używaj tylko pól ciągów

+ Użyj domyślnego standardowego analizatora Lucene ( `"analyzer": null` ) lub [analizatora języka](index-add-language-analyzers.md) (na przykład `"analyzer": "en.Microsoft"` ) w polu

### <a name="choose-fields"></a>Wybierz pola

Chociaż funkcja sugerował ma kilka właściwości, to przede wszystkim kolekcja pól ciągów, dla których jest włączone środowisko wyszukiwania jako typ. Dla każdego indeksu istnieje jeden program sugerujący, dlatego lista Sugerowana musi zawierać wszystkie pola, które tworzą zawartość zarówno dla sugestii, jak i autouzupełniania.

Wykorzystaj automatycznie korzyści z większej puli pól do narysowania ze względu na to, że dodatkowa zawartość ma większy potencjał uzupełniania.

Sugestie z drugiej strony dają lepsze wyniki, gdy wybór pola jest selektywny. Należy pamiętać, że sugestia jest serwerem proxy dla dokumentu wyszukiwania, dzięki czemu można chcieć, aby pola, które najlepiej reprezentują pojedynczy wynik. Nazwy, tytuły lub inne unikatowe pola, które odróżniają wiele pasujących wyników działają najlepiej. Jeśli pola zawierają powtarzające się wartości, sugestie składają się z identycznych wyników i użytkownik nie wie, który z nich należy kliknąć.

Aby zaspokoić zarówno środowiska typu "wyszukiwanie jako dane", Dodaj wszystkie pola, które są potrzebne do autouzupełniania, a następnie użyj **$SELECT**, **$Top**, **$Filter**i **searchFields** , aby kontrolować wyniki dla sugestii.

### <a name="choose-analyzers"></a>Wybieranie analizatorów

Wybór analizatora decyduje o tym, w jaki sposób pola są tokenami, a następnie prefiksem. Na przykład w przypadku ciągu z łącznikiem, takiego jak "kontekstowe", użycie analizatora języka spowoduje, że te kombinacje tokenu: "kontekst", "poufne", "kontekstowe". Czy użyto standardowego analizatora Lucene, ciąg podzielony na nie istnieje. 

Podczas oceniania analizatorów należy rozważyć użycie [interfejsu API analizy tekstu](/rest/api/searchservice/test-analyzer) do wglądu w sposób, w jaki warunki są tokenami, a następnie prefiksem. Po skompilowaniu indeksu można wypróbować różne analizatory w ciągu, aby wyświetlić dane wyjściowe tokenu.

Pola używające [analizatorów niestandardowych](index-add-custom-analyzers.md) lub [wstępnie zdefiniowanych analizatorów](index-add-custom-analyzers.md#predefined-analyzers-reference) (z wyjątkiem standardowych Lucene) są jawnie niedozwolone, aby zapobiec słabym rezultatom.

> [!NOTE]
> Jeśli musisz obejść ograniczenie analizatora, na przykład jeśli potrzebujesz słowa kluczowego lub analizatora ngram dla niektórych scenariuszy zapytań, należy użyć dwóch oddzielnych pól dla tej samej zawartości. Pozwoli to jednemu z pól na posiadanie sugestii, podczas gdy drugi można skonfigurować przy użyciu konfiguracji analizatora niestandardowego.

### <a name="when-to-create-a-suggester"></a>Kiedy należy utworzyć sugestię

Najlepszym terminem tworzenia sugestii jest również utworzenie samej definicji pola.

Jeśli spróbujesz utworzyć sugestię przy użyciu istniejących pól, interfejs API nie będzie go zezwalać. Prefiksy są generowane podczas indeksowania, gdy częściowe warunki w dwóch lub większej liczbie znaków są rozliczanie tokenów obok całych warunków. Mając na względzie, że istniejące pola są już tokenami, należy ponownie skompilować indeks, jeśli chcesz dodać je do sugestii. Aby uzyskać więcej informacji, zobacz [jak ponownie skompilować indeks wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md).

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

W języku C# Zdefiniuj [obiekt sugerujący](/dotnet/api/microsoft.azure.search.models.suggester). `Suggesters` jest kolekcją, ale może przyjmować tylko jeden element. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Odwołanie do właściwości

|Właściwość      |Opis      |
|--------------|-----------------|
|`name`        |Nazwa sugestii.|
|`searchMode`  |Strategia używana do wyszukiwania fraz kandydatów. Jedynym obsługiwanym trybem jest `analyzingInfixMatching` , który jest obecnie zgodny na początku okresu.|
|`sourceFields`|Lista co najmniej jednego pola, które jest źródłem zawartości dla sugestii. Pola muszą być typu `Edm.String` i `Collection(Edm.String)` . Jeśli analizator jest określony w polu, musi to być nazwany Analizator z [tej listy](/dotnet/api/microsoft.azure.search.models.analyzername) (nie Analizator niestandardowy).<p/> Najlepszym rozwiązaniem jest określenie tylko tych pól, które nadają się do oczekiwanej i odpowiedniej odpowiedzi, niezależnie od tego, czy jest to kompletny ciąg na pasku wyszukiwania, czy na liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma dokładnooć. Pełne pola, takie jak opisy i komentarze, są zbyt gęste. Podobnie powtarzające się pola, takie jak kategorie i Tagi, są mniej efektywne. W przykładach zawieramy "kategorię" Mimo to, aby pokazać, że można uwzględnić wiele pól. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Korzystanie z sugestii

W zapytaniu jest używany element sugerujący. Po utworzeniu sugestii Wywołaj jeden z następujących interfejsów API dla środowiska wyszukiwania w trakcie wpisywania:

+ [Sugestie interfejsu API REST](/rest/api/searchservice/suggestions) 
+ [Autouzupełnianie interfejsu API REST](/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestWithHttpMessagesAsync] (/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?
+ [AutocompleteWithHttpMessagesAsync, Metoda](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync)

W aplikacji wyszukiwania kod klienta powinien korzystać z biblioteki, takiej jak [Funkcja autouzupełniania interfejsu użytkownika jQuery](https://jqueryui.com/autocomplete/) , aby zebrać częściowe zapytanie i zapewnić dopasowanie. Aby uzyskać więcej informacji na temat tego zadania, zobacz [Dodawanie funkcji Autouzupełnianie lub sugerowanych wyników do kodu klienta](search-autocomplete-tutorial.md).

Użycie interfejsu API jest zilustrowane w następującym wywołaniu interfejsu API REST autouzupełniania. W tym przykładzie istnieją dwa wnioski. Po pierwsze, podobnie jak w przypadku wszystkich zapytań, operacja jest odnosząca się do kolekcji dokumentów indeksu, a zapytanie zawiera parametr **wyszukiwania** , który w tym przypadku zawiera częściowe zapytanie. Po drugie należy dodać **suggesterName** do żądania. Jeśli nie zdefiniowano sugestii w indeksie, wywołanie funkcji Autouzupełnianie lub sugestii zakończy się niepowodzeniem.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Przykładowy kod

+ [Tworzenie pierwszej aplikacji w języku C# (Lekcja 3 — Dodawanie wyszukiwania w trakcie pisania)](tutorial-csharp-type-ahead-and-suggestions.md) pokazuje, że zasugerowana jest konstrukcja, sugerowane zapytania, Autouzupełnianie i nawigacja aspektowa. Ten przykładowy kod jest uruchamiany w usłudze Azure Wyszukiwanie poznawcze piaskownicy i używa wstępnie załadowanego indeksu hoteli, więc wystarczy nacisnąć klawisz F5, aby uruchomić aplikację. Żadna subskrypcja ani logowanie nie są wymagane.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) jest starszym przykładem zawierającym kod C# i Java. Przedstawiono w nim także zasugerowaną konstrukcję, sugerowane zapytania, Autouzupełnianie i nawigację aspektową. Ten przykładowy kod używa hostowanych danych [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) . 

## <a name="next-steps"></a>Następne kroki

Zalecamy, aby dowiedzieć się więcej o tym, jak formułowane są żądania.

> [!div class="nextstepaction"]
> [Dodawanie funkcji Autouzupełnianie i sugestii do kodu klienta](search-autocomplete-tutorial.md)