---
title: Tworzenie sugestora
titleSuffix: Azure Cognitive Search
description: Włącz akcje zapytania z wyprzedzeniem typu w usłudze Azure Cognitive Search, tworząc sugesty i formułując żądania, które wywołują terminy zapytania autouzupełniania lub automatycznego sugerowania.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 7eb2988628d60fa72c7d83b81a58a1e0fae5de33
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770098"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Tworzenie sugestatora w celu umożliwienia autouzupełnienia i sugerowanych wyników w kwerendzie

W usłudze Azure Cognitive Search opcja "wyszukiwanie zgodnie z typem" jest włączona za pomocą konstruktora **sugestywnyego** dodanego do [indeksu wyszukiwania.](search-what-is-an-index.md) Sugestywny obsługuje dwa środowiska: *autouzupełnianie*, które kończy częściowe wprowadzanie danych dla kwerendy całego okresu i *sugestie,* które zapraszają do kliknięcia do określonego dopasowania. Autouzupełnienie tworzy kwerendę. Sugestie tworzą pasujący dokument.

Poniższy zrzut ekranu z [Tworzenie pierwszej aplikacji w języku C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje zarówno. Autouzupełnienie przewiduje potencjalny termin, kończąc "tw" z "in". Sugestie to mini wyniki wyszukiwania, w których pole takie jak nazwa hotelu reprezentuje pasujący dokument wyszukiwania w hotelu z indeksu. W przypadku sugestii można wystawić dowolne pole, które zawiera informacje opisowe.

![Wizualne porównanie autouzupełniania i sugerowanych zapytań](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Wizualne porównanie autouzupełniania i sugerowanych zapytań")

Te funkcje można używać oddzielnie lub razem. Aby zaimplementować te zachowania w usłudze Azure Cognitive Search, istnieje składnik indeksu i kwerendy. 

+ W indeksie dodaj sugestywny do indeksu. Można użyć portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)lub [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Dalsza część artykułu koncentruje się na tworzeniu sugesty.

+ W żądaniu kwerendy należy wywołać jeden z [interfejsów API wymienionych poniżej](#how-to-use-a-suggester).

Obsługa wyszukiwania zgodnie z typem jest włączona dla pól ciągów dla pól ciągów według typu "wyszukiwanie". Można zaimplementować zarówno zachowania typuhead w ramach tego samego rozwiązania wyszukiwania, jeśli chcesz doświadczenie podobne do wskazanego na zrzucie ekranu. Oba żądania są przeznaczone dla *kolekcji dokumentów* określonego indeksu i odpowiedzi są zwracane po użytkownik dostarczył co najmniej trzy znakowy ciąg wejściowy.

## <a name="what-is-a-suggester"></a>Co to jest sugest?

Sugestywny jest wewnętrzna struktura danych, która obsługuje zachowania typu wyszukiwania jako typ przez przechowywanie prefiksów do dopasowania w kwerendach częściowych. Podobnie jak w przypadku terminów tokenizowanych, prefiksy są przechowywane w odwróconych indeksach, po jednym dla każdego pola określonego w kolekcji pól sugestywnych.

## <a name="define-a-suggester"></a>Definiowanie sugestatora

Aby utworzyć sugestator, dodaj go do [schematu indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) i [ustaw każdą właściwość](#property-reference). Najlepszy czas, aby utworzyć sugest jest, gdy są również definiowania pola, które będzie go używać.

+ Używaj tylko pól ciągów

+ Użyj domyślnego standardowego analizatora Lucene (`"analyzer": null`) lub [analizatora języka](index-add-language-analyzers.md) (na przykład `"analyzer": "en.Microsoft"`) w polu

### <a name="choose-fields"></a>Wybieranie pól

Chociaż sugestator ma kilka właściwości, jest to przede wszystkim zbiór pól ciągów, dla których są włączanie wyszukiwania jako typ środowiska. Istnieje jeden sugestywny dla każdego indeksu, więc lista sugestywny musi zawierać wszystkie pola, które przyczyniają się do zawartości zarówno sugestie i autouzupełnianie.

Autouzupełnianie korzysta z większej puli pól do wyciągnięcia, ponieważ dodatkowa zawartość ma większy potencjał ukończenia okresu.

Z drugiej strony sugestie dają lepsze wyniki, gdy wybór pola jest selektywny. Należy pamiętać, że sugestia jest serwerem proxy dla dokumentu wyszukiwania, więc będziesz chciał pola, które najlepiej reprezentują pojedynczy wynik. Nazwy, tytuły lub inne unikatowe pola, które rozróżniają wiele dopasowań, działają najlepiej. Jeśli pola składają się z powtarzających się wartości, sugestie składają się z identycznych wyników, a użytkownik nie będzie wiedział, który z nich kliknie.

Aby spełnić zarówno środowisko wyszukiwania zgodnie z typem, dodaj wszystkie pola potrzebne do autouzupełniania, ale następnie użyj **$select**, **$top** **, $filter**i **searchFields,** aby kontrolować wyniki pod kątem sugestii.

### <a name="choose-analyzers"></a>Wybieranie analizatorów

Wybór analizatora określa sposób tokenizacji pól, a następnie poprecysowania. Na przykład dla ciągu dzielonego, takich jak "kontekstowe", przy użyciu analizatora języka spowoduje te kombinacje tokenów: "context", "sensitive", "context-sensitive". Gdyby użyto standardowego analizatora Lucene, ciąg dzielenia wyrazów nie istniałby. 

Podczas oceny analizatorów, należy rozważyć użycie [interfejsu API analizy tekstu,](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) aby uzyskać wgląd w sposób terminy są tokenizowane, a następnie poprzedzone. Po utworzeniu indeksu, można wypróbować różne analizatory na ciąg, aby wyświetlić dane wyjściowe tokenu.

Pola, które używają [analizatorów niestandardowych](index-add-custom-analyzers.md) lub [wstępnie zdefiniowanych analizatorów](index-add-custom-analyzers.md#predefined-analyzers-reference) (z wyjątkiem standardowego Lucene) są jawnie zabronione, aby zapobiec słabym wynikom.

> [!NOTE]
> Jeśli chcesz obejść ograniczenie analizatora, na przykład jeśli potrzebujesz analizatora słowa kluczowego lub ngram dla niektórych scenariuszy kwerend, należy użyć dwóch oddzielnych pól dla tej samej zawartości. Pozwoli to na jedno z pól, aby mieć sugest, podczas gdy inne mogą być skonfigurowane za pomocą konfiguracji analizatora niestandardowego.

### <a name="when-to-create-a-suggester"></a>Kiedy utworzyć sugest

Najlepszy czas, aby utworzyć sugest jest, gdy są również tworzenie definicji pola się.

Jeśli spróbujesz utworzyć sugest przy użyciu wcześniej istniejących pól, interfejs API nie zezwala na to. Prefiksy są generowane podczas indeksowania, gdy częściowe terminy w dwóch lub więcej kombinacji znaków są tokenizowane obok całych terminów. Biorąc pod uwagę, że istniejące pola są już tokenizowane, trzeba będzie odbudować indeks, jeśli chcesz dodać je do sugesty. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks usługi Azure Cognitive Search](search-howto-reindex.md).

## <a name="create-using-rest"></a>Tworzenie przy użyciu funkcji REST

W interfejsie API REST dodaj sugestywne za pomocą [programu Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) lub Update [Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

W języku C#zdefiniuj [obiekt sugestywny](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`jest kolekcją, ale może zająć tylko jeden element. 

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
|`name`        |Nazwa sugestatora.|
|`searchMode`  |Strategia używana do wyszukiwania zwrotów kandydatów. Jedynym obecnie obsługiwanym trybem jest `analyzingInfixMatching`, który obecnie pasuje na początku okresu.|
|`sourceFields`|Lista co najmniej jednego pola, które są źródłem zawartości sugestii. Pola muszą być `Edm.String` `Collection(Edm.String)`typu i . Jeśli analizator jest określony w polu, musi to być nazwany analizator z [tej listy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (nie analizatora niestandardowego).<p/> Najlepszym rozwiązaniem jest określenie tylko tych pól, które nadają się do oczekiwanej i odpowiedniej odpowiedzi, niezależnie od tego, czy jest to ukończony ciąg na pasku wyszukiwania, czy na liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma precyzję. Pełne pola, takie jak opisy i komentarze, są zbyt gęste. Podobnie powtarzające się pola, takie jak kategorie i tagi, są mniej skuteczne. W przykładach i tak uwzględniamy "kategorię", aby wykazać, że można dołączyć wiele pól. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Korzystanie z sugestatora

Sugestywny jest używany w kwerendzie. Po utworzeniu sugestatora należy wywołać jeden z następujących interfejsów API dla środowiska wyszukiwania zgodnie z typem:

+ [Sugestie REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Interfejs API REST autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Sugestia z metodąHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Autouzupełnienie z metodąHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

W aplikacji wyszukiwania kod klienta należy wykorzystać biblioteki, takich jak [jQuery UI Autouzupełniania](https://jqueryui.com/autocomplete/) do zbierania kwerendy częściowej i zapewnić dopasowanie. Aby uzyskać więcej informacji na temat tego zadania, zobacz [Dodawanie autouzupełniania lub sugerowanych wyników do kodu klienta](search-autocomplete-tutorial.md).

Użycie interfejsu API jest zilustrowane w poniższym wywołaniu do interfejsu API REST autouzupełnianie. Istnieją dwa wynos z tego przykładu. Po pierwsze, podobnie jak w przypadku wszystkich zapytań, operacja jest przeciwko kolekcji dokumentów indeksu i kwerendy zawiera parametr **wyszukiwania,** który w tym przypadku zapewnia kwerendę częściową. Po drugie należy dodać **suggesterName** do żądania. Jeśli sugest nie jest zdefiniowany w indeksie, wywołanie autouzupełnienia lub sugestie zakończy się niepowodzeniem.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Przykładowy kod

+ [Utwórz pierwszą aplikację w języku C# (lekcja 3 — dodaj wyszukiwanie zgodnie z typem)](tutorial-csharp-type-ahead-and-suggestions.md) przykład pokazuje konstrukcję sugestywny, sugerowane zapytania, autouzupełnianie i nawigacji aspektowej. Ten przykładowy kod jest uruchamiany w usłudze Azure Cognitive Search w piaskownicy i używa wstępnie załadowanego indeksu hoteli, więc wszystko, co musisz zrobić, to nacisnąć klawisz F5, aby uruchomić aplikację. Nie jest wymagana subskrypcja ani logowanie.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) jest starsza próbka zawierająca kod Języka C# i Java. Pokazuje również konstrukcję sugestywny, sugerowane zapytania, autouzupełnianie i nawigację fasetacją. Ten przykładowy kod używa hostowanych danych przykładowych [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 

## <a name="next-steps"></a>Następne kroki

Zalecamy poniższy artykuł, aby dowiedzieć się więcej o tym, jak formułowanie żądań.

> [!div class="nextstepaction"]
> [Dodawanie autouzupełniania i sugestii do kodu klienta](search-autocomplete-tutorial.md) 
