---
title: Analizatory dla przetwarzania lingwistycznego i tekstu
titleSuffix: Azure Cognitive Search
description: Przypisz analizatory do pól tekstowych z możliwością wyszukiwania w indeksie, aby zastąpić domyślne standardowe Lucene z niestandardowym, wstępnie zdefiniowanym lub specyficznym dla języka alternatywą.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 01b57526b15a806271d58b250f06a4372fe56b72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532265"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analizatory do przetwarzania tekstu na platformie Azure Wyszukiwanie poznawcze

*Analizator* jest składnikiem [aparatu wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) odpowiedzialnego za przetwarzanie tekstu w ciągach zapytań i indeksowanych dokumentach. Przetwarzanie tekstu (nazywane również analizą leksykalną) to analizie przekształceń, modyfikowanie ciągu za pomocą akcji takich jak:

+ Usuwanie nieistotnych wyrazów (Stop-słowa) i interpunkcji
+ Podział fraz i słów wyrazów na części składników
+ Małe litery, wielkie litery
+ Zmniejsz liczbę słów w postaci pierwotnych formularzy głównych w celu zapewnienia wydajności magazynu, aby można było znaleźć dopasowania bez względu na intensywność

Analiza dotyczy `Edm.String` pól, które są oznaczone jako "z możliwością wyszukiwania", co oznacza wyszukiwanie pełnotekstowe. W przypadku pól o tej konfiguracji Analiza odbywa się podczas indeksowania podczas tworzenia tokenów, a następnie ponownie podczas wykonywania zapytania, gdy zapytania są analizowane i aparat skanuje w poszukiwaniu zgodnych tokenów. Dopasowanie może wystąpić, gdy ten sam analizator jest używany zarówno dla indeksowania, jak i dla zapytań, ale dla każdego obciążenia niezależnie od wymagań można ustawić dla nich Analizator.

Typy zapytań, które nie są wyszukiwaniem pełnotekstowym, takie jak wyrażenie regularne lub Wyszukiwanie rozmyte, nie należy przechodzić do fazy analizy po stronie zapytania. Zamiast tego parser wysyła te ciągi bezpośrednio do aparatu wyszukiwania przy użyciu wzorca, który stanowi podstawę dla dopasowania. Zazwyczaj te formularze zapytań wymagają tokenów pełnych ciągów, aby zapewnić zgodność z wzorcem. Aby uzyskać tokeny z całym warunkiem podczas indeksowania, może być konieczne [przeanalizowanie niestandardowe](index-add-custom-analyzers.md). Aby uzyskać więcej informacji na temat tego, kiedy i dlaczego są analizowane warunki zapytania, zobacz [wyszukiwanie pełnotekstowe w usłudze Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md).

Aby uzyskać więcej informacji na temat analizy leksykalnej, Zasłuchaj poniższego klipu wideo, aby uzyskać krótkie wyjaśnienie.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Analizator domyślny  

W przypadku zapytań usługi Azure Wyszukiwanie poznawcze analizator jest automatycznie wywoływany dla wszystkich pól ciągów oznaczonych jako przeszukiwane. 

Domyślnie usługa Azure Wyszukiwanie poznawcze korzysta z narzędzia [Apache Lucene Standard Analyzer (standardowa Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), które dzieli tekst na elementy po regułach ["segmentacji tekstu Unicode"](https://unicode.org/reports/tr29/) . Dodatkowo Analizator standardowy konwertuje wszystkie znaki na ich małą literę. Zarówno indeksowane dokumenty, jak i wyszukiwane terminy przechodzą przez analizę podczas indeksowania i przetwarzania zapytań.  

Można przesłonić wartość domyślną dla każdego pola. Alternatywni analizatory mogą być [analizatorem języka](index-add-language-analyzers.md) dla przetwarzania lingwistycznego, [analizatorem niestandardowym](index-add-custom-analyzers.md)lub wstępnie zdefiniowanym analizatorze z [listy dostępnych analiz](index-add-custom-analyzers.md#AnalyzerTable).

## <a name="types-of-analyzers"></a>Typy analizatorów

Na poniższej liście opisano, które analizatory są dostępne w usłudze Azure Wyszukiwanie poznawcze.

| Kategoria | Opis |
|----------|-------------|
| [Standardowy Analizator Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Domyślne. Nie jest wymagana Specyfikacja ani konfiguracja. Ten Analizator ogólnego przeznaczenia dobrze sprawdza się w wielu językach i scenariuszach.|
| Wstępnie zdefiniowane analizatory | Oferowany jako gotowy produkt przeznaczony do użycia w przypadku, gdy jest to możliwe. <br/>Istnieją dwa typy: wyspecjalizowane i językowe. Co sprawia, że "wstępnie zdefiniowane" polega na odwoływaniu się do nich według nazwy, bez konfiguracji ani dostosowywania. <br/><br/>[Analizatory wyspecjalizowane (Language-niezależny od)](index-add-custom-analyzers.md#AnalyzerTable) są używane, gdy dane wejściowe wymagają wyspecjalizowanego przetwarzania lub minimalnego przetwarzania. Analizatory wstępnie zdefiniowane w języku innym niż język to **Asciifolding**, **słowo kluczowe**, **wzorzec**, **prosty**, **stop**, **biały**znak.<br/><br/>[Analizatory języka](index-add-language-analyzers.md) są używane, gdy potrzebna jest zaawansowana obsługa języków w poszczególnych językach. Platforma Azure Wyszukiwanie poznawcze obsługuje analizatory języków Lucene 50 i 35 analizatory przetwarzania języka naturalnego firmy Microsoft. |
|[Analizatory niestandardowe](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odnosi się do zdefiniowanej przez użytkownika konfiguracji kombinacji istniejących elementów, która składa się z jednego tokenizatora (wymagane) i opcjonalnych filtrów (Char lub token).|

Kilka wstępnie zdefiniowanych analizatorów, takich jak **wzorzec** lub **Zatrzymywanie**, obsługują ograniczony zestaw opcji konfiguracji. Aby ustawić te opcje, można efektywnie utworzyć Analizator niestandardowy składający się z wstępnie zdefiniowanego analizatora i jedną z opcji alternatywnych udokumentowanych w [wstępnie zdefiniowanych odwołaniach analizatora](index-add-custom-analyzers.md#AnalyzerTable). Podobnie jak w przypadku dowolnej konfiguracji niestandardowej, należy podać nową konfigurację przy użyciu nazwy, takiej jak *myPatternAnalyzer* , aby odróżnić ją od analizatora wzorców Lucene.

## <a name="how-to-specify-analyzers"></a>Jak określić analizatory

Ustawienie analizatora jest opcjonalne. Jako ogólną regułę spróbuj najpierw użyć domyślnego standardowego analizatora luce, aby zobaczyć, jak to działa. Jeśli zapytania nie zwracają oczekiwanych wyników, przełączenie do innego analizatora jest często odpowiednim rozwiązaniem.

1. Podczas tworzenia definicji pola w [indeksie](/rest/api/searchservice/create-index)ustaw właściwość  **Analizator** na jedną z następujących opcji: [wstępnie zdefiniowany Analizator](index-add-custom-analyzers.md#AnalyzerTable) , taki jak `keyword` , [Analizator języka](index-add-language-analyzers.md) , taki jak `en.microsoft` , lub Analizator niestandardowy (zdefiniowany w tym samym schemacie indeksu).  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   Jeśli używasz [analizatora języka](index-add-language-analyzers.md), musisz użyć właściwości **analizatora** , aby ją określić. Właściwości **searchAnalyzer** i **indexAnalyzer** nie obsługują analizatorów języka.

1. Alternatywnie Ustaw **indexAnalyzer** i **searchAnalyzer** , aby różnić Analizator dla każdego obciążenia. Te właściwości są ustawiane razem i zastępują Właściwość **analizatora** , która musi mieć wartość null. Można użyć różnych analizatorów do przygotowania i pobrania danych, jeśli jedno z tych działań wymagało konkretnego przekształcenia, które nie jest potrzebne.

   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "whitespace"
    },
   ```

1. W przypadku tylko analizatorów niestandardowych Utwórz wpis w sekcji **[analizatory]** indeksu, a następnie przypisz Analizator niestandardowy do definicji pola na jeden z poprzednich dwóch kroków. Aby uzyskać więcej informacji, zobacz [Tworzenie indeksu](/rest/api/searchservice/create-index) i [Dodawanie niestandardowych analizatorów](index-add-custom-analyzers.md).

## <a name="when-to-add-analyzers"></a>Kiedy należy dodawać analizatory

Najlepszy czas dodawania i przypisywania analizatorów odbywa się podczas aktywnego programowania, gdy porzucanie i odtwarzanie indeksów jest rutynowe.

Ponieważ analizatory są używane do tokenize warunków, należy przypisać analizatorze podczas tworzenia pola. W rzeczywistości przypisanie **analizatora** lub **indexAnalyzer** do pola, które zostało już utworzone fizycznie jest niedozwolone (chociaż można zmienić właściwość **searchAnalyzer** w dowolnym momencie bez wpływu na indeks).

Aby zmienić Analizator istniejącego pola, należy [całkowicie ponownie skompilować indeks](search-howto-reindex.md) (nie można ponownie skompilować pojedynczych pól). W przypadku indeksów w środowisku produkcyjnym można odroczyć ponowną kompilację przez utworzenie nowego pola z nowym przypisaniem analizatora i rozpoczęcie korzystania z niego zamiast Starego. Użyj [indeksu aktualizacji](/rest/api/searchservice/update-index) , aby uwzględnić nowe pole i [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) w celu wypełnienia. Później w ramach planowanej obsługi indeksów można wyczyścić indeks w celu usunięcia przestarzałych pól.

Aby dodać nowe pole do istniejącego indeksu, wywołaj polecenie [Aktualizuj indeks](/rest/api/searchservice/update-index) , aby dodać pole i [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) je wypełnić.

Aby dodać Analizator niestandardowy do istniejącego indeksu, Przekaż flagę **allowIndexDowntime** w [indeksie aktualizacji](/rest/api/searchservice/update-index) , jeśli chcesz uniknąć tego błędu:

*"Aktualizacja indeksu jest niedozwolona, ponieważ spowodowałoby to przestoje. Aby dodać nowe analizatory, tokenizatory, filtry tokenów lub filtry znaków do istniejącego indeksu, ustaw parametr zapytania "allowIndexDowntime" na wartość "true" w żądaniu aktualizacji indeksu. Należy pamiętać, że ta operacja spowoduje przełączenie indeksu w tryb offline przez co najmniej kilka sekund, co spowoduje niepowodzenie żądania indeksowania i zapytań. Dostępność indeksu może być niesparowana przez kilka minut od momentu zaktualizowania indeksu lub dłuższe dla bardzo dużych indeksów.*

## <a name="recommendations-for-working-with-analyzers"></a>Zalecenia dotyczące pracy z analizatorami

Ta sekcja zawiera wskazówki dotyczące pracy z analizatorami.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden Analizator do odczytu i zapisu, o ile nie masz określonych wymagań

Usługa Azure Wyszukiwanie poznawcze pozwala określić różne analizatory do indeksowania i wyszukiwania za pomocą dodatkowych właściwości pól **indexAnalyzer** i **searchAnalyzer** . Jeśli nie zostanie określony, Analizator zestawu z właściwością **Analizator** jest używany do indeksowania i wyszukiwania. Jeśli **Analizator** nie zostanie określony, używany jest domyślny standardowy Analizator Lucene.

Ogólną zasadą jest użycie tego samego analizatora zarówno dla indeksowania, jak i wykonywania zapytań, chyba że określone wymagania nie wyróżnią się w inny sposób. Pamiętaj o dokładnym przetestowaniu. Gdy przetwarzanie tekstu różni się w czasie wyszukiwania i indeksowania, należy uruchomić ryzyko niezgodności między terminami zapytania a indeksowanymi terminami, kiedy konfiguracje analizatora wyszukiwania i indeksowania nie są wyrównane.

### <a name="test-during-active-development"></a>Testowanie podczas aktywnego programowania

Zastępowanie analizatora standardowego wymaga odbudowania indeksu. Jeśli to możliwe, zdecyduj, które analizatory mają być używane podczas aktywnego programowania, przed przetworzeniem indeksu do produkcji.

### <a name="inspect-tokenized-terms"></a>Sprawdzanie tokenów w terminologii

Jeśli wyszukiwanie nie zwróci oczekiwanych wyników, najbardziej prawdopodobną przyczyną jest niezgodności tokenów między danymi wejściowymi zapytania a terminami tokenów w indeksie. Jeśli tokeny nie są takie same, dopasowań nie powiedzie się zmaterializowania. Aby sprawdzić dane wyjściowe tokenizatora, zalecamy korzystanie z [interfejsu API analizy](/rest/api/searchservice/test-analyzer) jako narzędzia do badania. Odpowiedź składa się z tokenów, zgodnie z wygenerowanym przez określony Analizator.

<a name="examples"></a>

## <a name="rest-examples"></a>Przykłady REST

W poniższych przykładach przedstawiono definicje analizatora dla kilku kluczowych scenariuszy.

+ [Przykład analizatora niestandardowego](#Custom-analyzer-example)
+ [Przypisanie analizatorów do przykładu pola](#Per-field-analyzer-assignment-example)
+ [Mieszanie analizatorów do indeksowania i wyszukiwania](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Przykładowy Analizator języka](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Przykład analizatora niestandardowego

Ten przykład ilustruje definicję analizatora z opcjami niestandardowymi. Opcje niestandardowe filtrów char, tokenizatory i filtrów tokenów są określane oddzielnie jako konstrukcje nazwane, a następnie przywoływane w definicji analizatora. Wstępnie zdefiniowane elementy są używane jako-is i po prostu przywoływane przez nazwę.

Przechodzenie przez ten przykład:

* Analizatory są właściwością klasy Field dla pola z możliwością wyszukiwania.
* Analizator niestandardowy jest częścią definicji indeksu. Może być odpowiednio dostosowany (na przykład dostosowanie pojedynczej opcji w jednym filtrze) lub dostosowany w wielu miejscach.
* W takim przypadku Analizator niestandardowy jest "my_analyzer", który z kolei używa niestandardowego standardowego tokenizatora "my_standard_tokenizer" i dwóch filtrów tokenów: małe i dostosowane filtry asciifolding "my_asciifolding".
* Definiuje również 2 niestandardowe filtry znaków "map_dash" i "remove_whitespace". Pierwszy z nich zastępuje wszystkie kreski znakami podkreślenia, podczas gdy druga z nich usuwa wszystkie spacje. Spacje muszą być zakodowane w formacie UTF-8 w regułach mapowania. Filtry znaków są stosowane przed tokenizacji i będą miały wpływ na wynikowe tokeny (standardowy tokenizatora jest podzielony na kreski i spacje, ale nie w podkreśleniu).

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Przykład przypisywania analizatora poszczególnych pól

Standardowa analizator jest wartością domyślną. Załóżmy, że chcesz zastąpić wartość domyślną przy użyciu innego wstępnie zdefiniowanego analizatora, takiego jak Analizator wzorców. Jeśli nie ustawiasz opcji niestandardowych, wystarczy określić ją tylko według nazwy w definicji pola.

Element "Analyzer" przesłania analizatora standardowego w zależności od pola. Nie istnieje globalne zastąpienie. W tym przykładzie `text1` używa analizatora wzorców i `text2` , który nie określa analizatora, używa domyślnego.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mieszanie analizatorów dla operacji indeksowania i wyszukiwania

Interfejsy API zawierają dodatkowe atrybuty indeksu do określania różnych analizatorów do indeksowania i wyszukiwania. Atrybuty **searchAnalyzer** i **indexAnalyzer** muszą być określone jako para, zastępując atrybut pojedynczego **analizatora** .


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Przykładowy Analizator języka

Pola zawierające ciągi w różnych językach mogą używać analizatora języka, podczas gdy inne pola zachowują wartość domyślną (lub używają innego wstępnie zdefiniowanego lub niestandardowego analizatora). Jeśli używasz analizatora języka, musi on być używany do operacji indeksowania i wyszukiwania. Pola, które używają analizatora języka, nie mogą mieć różnych analizatorów do indeksowania i wyszukiwania.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>Przykłady w języku C#

Jeśli używasz przykładów kodu zestawu .NET SDK, możesz dołączyć te przykłady, aby użyć lub skonfigurować analizatory.

+ [Przypisywanie wbudowanej analizatora](#Assign-a-language-analyzer)
+ [Konfigurowanie analizatora](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Przypisywanie analizatora języka

Każdy Analizator, który jest używany jako-is, bez konfiguracji, jest określony w definicji pola. Nie jest wymagane, aby utworzyć wpis w sekcji **[analizatory]** indeksu. 

W tym przykładzie przypisujemy do pól opisu język angielski i analizatory francuskie firmy Microsoft. Jest to fragment pochodzący z większej definicji indeksu hoteli, który tworzy się przy użyciu klasy hotelu w pliku hotels.cs przykładu [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) .

[Analizator](/dotnet/api/microsoft.azure.search.models.analyzer)wywołań, określający typ [analizatora](/dotnet/api/microsoft.azure.search.models.analyzername) , który zapewnia analizator tekstu obsługiwany przez wyszukiwanie poznawcze platformy Azure.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definiowanie analizatora niestandardowego

Gdy wymagane jest dostosowanie lub konfiguracja, konieczne będzie dodanie konstrukcji analizatora do indeksu. Po jego zdefiniowaniu można dodać do niego definicję pola, jak pokazano w poprzednim przykładzie.

Utwórz obiekt [CustomAnalyzer](/dotnet/api/microsoft.azure.search.models.customanalyzer) . Aby uzyskać więcej przykładów, zobacz [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Następne kroki

+ Zapoznaj się z kompleksowym wyjaśnieniem [działania wyszukiwania pełnotekstowego w usłudze Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md). W tym artykule przedstawiono przykłady zachowań, które mogą wydawać się intuicyjne na powierzchni.

+ Wypróbuj dodatkową składnię zapytania z przykładowej sekcji [Wyszukaj dokumenty](/rest/api/searchservice/search-documents#bkmk_examples) lub z [prostej składni zapytania](query-simple-syntax.md) w Eksploratorze wyszukiwania w portalu.

+ Dowiedz się, jak stosować [analizatory leksykalne charakterystyczne dla języka](index-add-language-analyzers.md).

+ [Skonfiguruj Niestandardowe analizatory](index-add-custom-analyzers.md) dla minimalnego przetwarzania lub wyspecjalizowanego przetwarzania dla poszczególnych pól.

## <a name="see-also"></a>Zobacz też

 [Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/search-documents) 

 [Prosta składnia zapytań](query-simple-syntax.md) 

 [Pełna składnia zapytań Lucene](query-lucene-syntax.md) 
 
 [Obsługa wyników wyszukiwania](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png