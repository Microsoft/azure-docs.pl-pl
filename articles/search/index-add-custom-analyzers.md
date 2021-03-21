---
title: Dodawanie niestandardowych analizatorów do pól ciągów
titleSuffix: Azure Cognitive Search
description: Skonfiguruj tokenizatory tekstowe i filtry znaków, aby przeprowadzać analizę tekstu na ciągach podczas indeksowania i zapytań.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 831e57a68c79c245b96baec0fc3d062c4c9112c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604444"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Dodawanie niestandardowych analizatorów do pól ciągów w indeksie Wyszukiwanie poznawcze platformy Azure

*Analizator niestandardowy* jest kombinacją tokenizatora, co najmniej jednego filtru tokenu i jednego lub więcej filtrów znaków zdefiniowanych w indeksie wyszukiwania, a następnie odwołuje się do definicji pól, które wymagają analizy niestandardowej. Tokenizatora jest odpowiedzialny za przerywanie tekstu do tokenów i filtry tokenu do modyfikowania tokenów emitowanych przez tokenizatora. Filtry znaków przygotowują tekst wejściowy przed przetworzeniem przez tokenizatora. 

Analizator niestandardowy zapewnia kontrolę nad procesem konwertowania tekstu na indeksowanie i wyszukiwanie przy użyciu tokenów umożliwiających wybranie typów analizy lub filtrowania do wywołania oraz kolejności ich występowania. Jeśli chcesz użyć wbudowanej analizatora z opcjami niestandardowymi, takimi jak zmiana maxTokenLength na standardowym, utworzysz Analizator niestandardowy z nazwą zdefiniowaną przez użytkownika, aby ustawić te opcje.

Sytuacje, w których mogą być przydatne analizatory niestandardowe:

- Używanie filtrów znaków do usuwania znaczników HTML przed użyciem tokenów tekstowych i zamiany niektórych znaków lub symboli.

- Wyszukiwanie fonetyczne. Dodaj filtr fonetyczny, aby umożliwić wyszukiwanie na podstawie tego, jak program Word brzmi, a nie jego pisownię.  

- Wyłącz analizę leksykalną. Użyj analizatora słów kluczowych, aby utworzyć pola do wyszukiwania, które nie są analizowane.  

- Szybkie wyszukiwanie prefiksów/sufiksów. Dodaj filtr token N-grama krawędzi, aby zindeksować prefiksy wyrazów w celu włączenia szybkiego dopasowania prefiksu. Połącz go z filtrem odwrotnego tokenu, aby wykonać dopasowanie sufiksu.  

- Niestandardowy tokenizacji. Na przykład użyj odstępu tokenizatora, aby przerwać zdania do tokenów, używając odstępu jako ogranicznika  

- Łamanie w formacie ASCII. Dodaj standardowy filtr składania ASCII, aby znormalizować znaki diakrytyczne, takie jak ö lub ê, w terminach wyszukiwania.  

Aby utworzyć Analizator niestandardowy, należy określić go w sekcji "Analizatory" w indeksie w czasie projektowania, a następnie odwołać się do niego w polach typu EDM. String przy użyciu właściwości "Analyzer" albo pary "indexAnalyzer" i "searchAnalyzer".

> [!NOTE]  
> Niestandardowe analizatory, które tworzysz, nie są ujawniane w Azure Portal. Jedynym sposobem dodania analizatora niestandardowego jest użycie kodu, który definiuje indeks. 

## <a name="create-a-custom-analyzer"></a>Tworzenie niestandardowego analizatora

Definicja analizatora zawiera nazwę, typ, jeden lub więcej filtrów znaków, maksymalnie jeden tokenizatora oraz co najmniej jeden filtr tokenu dla przetwarzania po tokenizacji. Filtry znaków są stosowane przed tokenizacji. Filtry tokenów i filtry znaków są stosowane od lewej do prawej.

- Nazwy w analizatorze niestandardowym muszą być unikatowe i nie mogą być takie same jak filtry wbudowanych, tokenizatory, tokenów lub znaków. Musi zawierać tylko litery, cyfry, spacje, kreski i znaki podkreślenia, może zaczynać się i kończyć tylko znakami alfanumerycznymi i mieć ograniczone do 128 znaków. 

- Typ musi być #Microsoft. Azure. Search. CustomAnalyzer.

- "charFilters" może być jednym lub więcej filtrami z [filtrów znaków](#CharFilter)przetworzonych przed tokenizacji w podanej kolejności. Niektóre filtry znaków mają opcje, które można ustawić w sekcji "charFilter". Filtry znaków są opcjonalne.

- "tokenizatora" to dokładnie jeden [tokenizatora](#tokenizers). Wartość jest wymagana. Jeśli potrzebujesz więcej niż jednego tokenizatora, możesz utworzyć wiele analizatorów niestandardowych i przypisać je do poszczególnych pól w schemacie indeksu.

- "tokenFilters" może być co najmniej jednym filtrem z [filtrów tokenów](#TokenFilters)przetworzonym po tokenizacji w podanej kolejności. W przypadku filtrów tokenów z opcjami Dodaj sekcję "tokenFilter", aby określić konfigurację. Filtry tokenów są opcjonalne.

Analizatory nie mogą generować tokenów dłuższych niż 300 znaków lub indeksowanie zakończy się niepowodzeniem. Aby przyciąć długi token lub wykluczyć je, należy odpowiednio użyć **TruncateTokenFilter** i **LengthTokenFilter** . Zobacz [**filtry tokenu**](#TokenFilters) , aby uzyskać odwołanie.

```json
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

W ramach definicji indeksu można umieścić tę sekcję w dowolnym miejscu w treści żądania utworzenia indeksu, ale zazwyczaj jest to na końcu:  

```json
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Definicja analizatora jest częścią większego indeksu. Definicje filtrów char, tokenizatory i filtrów tokenów są dodawane do indeksu tylko w przypadku ustawiania opcji niestandardowych. Aby użyć istniejącego filtru lub tokenizatora jako-is, należy określić go według nazwy w definicji analizatora. Aby uzyskać więcej informacji, zobacz [create index (REST)](/rest/api/searchservice/create-index). Aby uzyskać więcej przykładów, zobacz [Dodawanie analizatorów na platformie Azure wyszukiwanie poznawcze](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Testowanie niestandardowych analizatorów

Można użyć [analizatora testowego (REST)](/rest/api/searchservice/test-analyzer) , aby zobaczyć, w jaki sposób analizator przerywa dany tekst do tokenów.

**Żądanie**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Odpowiedź**

```http
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Aktualizowanie analizatorów niestandardowych

Gdy jest zdefiniowany Analizator, tokenizatora, filtr tokenu lub filtr znaków, nie można go modyfikować. Nowe ustawienia można dodać do istniejącego indeksu tylko wtedy, gdy `allowIndexDowntime` flaga ma wartość true w żądaniu aktualizacji indeksu:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Ta operacja przyjmuje indeks w trybie offline przez co najmniej kilka sekund, co spowoduje niepowodzenie żądania indeksowania i zapytań. Dostępność indeksu może być niesparowana przez kilka minut od momentu, gdy indeks zostanie zaktualizowany lub dłuższy dla bardzo dużych indeksów, ale te skutki są tymczasowe i ostatecznie rozwiązane.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Wbudowane analizatory

Jeśli chcesz użyć wbudowanej analizatora z opcjami niestandardowymi, tworzenie analizatora niestandardowego jest mechanizmem, za pomocą którego określisz te opcje. Z drugiej strony, aby użyć wbudowanej analizatora tak samo, należy po prostu [odwołać się do niego według nazwy](search-analyzers.md#how-to-specify-analyzers) w definicji pola.

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Opis i opcje**|  
|-----------------|-------------------------------|---------------------------|  
|[kodu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typ ma zastosowanie tylko wtedy, gdy opcje są dostępne) |Traktuje całą zawartość pola jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kody ZIP, identyfikatory i nazwy produktów.|  
|[znaczne](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Elastycznie oddziela tekst do warunków za pośrednictwem wzorca wyrażenia regularnego. </br></br>**Opcje** </br></br>małe litery (Type: bool) — określa, czy warunki są małymi literami. Wartość domyślna to true. </br></br>[wzorzec](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: ciąg) — wzorzec wyrażenia regularnego w celu dopasowania do separatorów tokenów. Wartość domyślna to `\W+` , która dopasowuje znaki niebędące słowami. </br></br>[flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Type: String) — flagi wyrażenia regularnego. Wartość domyślna to pusty ciąg. Dozwolone wartości: CANON_EQ, CASE_INSENSITIVE, komentarze, DOTALL, LITERAŁ, WIELOWIERSZOWY, UNICODE_CASE, UNIX_LINES </br></br>Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Wartość domyślna to pusta lista.|  
|[ułatwia](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne) |Dzieli tekst bez liter i konwertuje je na małe litery. |  
|[Standardowa](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(Określane również jako standardowe. Lucene)|StandardAnalyzer|Standardowy Analizator Luceny, składający się z standardowego tokenizatora, małych i niepisanych filtrów. </br></br>**Opcje** </br></br>maxTokenLength (typ: int) — Maksymalna długość tokenu. Wartość domyślna to 255. Tokeny dłuższe niż maksymalna długość są dzielone. Maksymalna długość tokenu, która może być użyta, to 300 znaków. </br></br>Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Wartość domyślna to pusta lista.|  
|standardasciifolding. Lucene|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne) |Analizator standardowy z filtrem składania kodu ASCII. |  
|[komunikat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Dzieli tekst bez liter, stosuje filtry tokenów małych i odrzucany termin. </br></br>**Opcje** </br></br>Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Wartość domyślna to wstępnie zdefiniowana lista dla języka angielskiego. |  
|[odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne) |Analizator używający odstępu tokenizatora. Podział tokenów dłuższy niż 255 znaków.|  

 <sup>1</sup> analizatory mają zawsze prefiksy w kodzie z "#Microsoft. Azure. Search", tak że "PatternAnalyzer" byłby rzeczywiście określony jako "#Microsoft. Azure. Search. PatternAnalyzer". Usunięto prefiks dla elementu zwięzłości, ale prefiks jest wymagany w kodzie.

Analyzer_type jest dostępna tylko dla analizatorów, które można dostosować. Jeśli nie ma żadnych opcji, podobnie jak w przypadku analizatora słów kluczowych, nie ma skojarzonego #Microsoft. Azure. Search Type.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Filtry znaków

W poniższej tabeli filtry znaków zaimplementowane przy użyciu oprogramowania Apache Lucene są połączone z dokumentacją interfejsu API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Opis i opcje**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Filtr znaków, który próbuje rozdzielić konstrukcje HTML.|  
|[mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Filtr znaków, który stosuje mapowania zdefiniowane z opcją mapowania. Dopasowanie jest zachłanne (najdłuższym dopasowaniem do wzorca w danym punkcie WINS). Zastąpienie może być ciągiem pustym.  </br></br>**Opcje**  </br></br> mapowania (typ: tablica ciągów) — lista mapowań następującego formatu: "a =>b" (wszystkie wystąpienia znaku "a" są zastępowane znakiem "b"). Wymagane.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Filtr znaków, który zastępuje znaki w ciągu wejściowym. Używa wyrażenia regularnego, aby identyfikować sekwencje znaków do zachowania oraz wzorzec zamienny do identyfikowania znaków do zastąpienia. Przykład: input text = "AA BB AA BB", wzorzec = "(AA) \\ \s + (BB)" zastąpienie = "$ 1 # $2", wynik = "AA # BB AA # BB".  </br></br>**Opcje**  </br></br>wzorzec (typ: ciąg) — wymagany.  </br></br>zastąpienie (typ: ciąg) — wymagany.|  

 <sup>1</sup> typy filtrów znaków zawsze są poprzedzone prefiksem kodu z "#Microsoft. Azure. Search", tak że "MappingCharFilter" byłby rzeczywiście określony jako "#Microsoft. Azure. Search. MappingCharFilter. Usunęliśmy prefiks, aby zmniejszyć szerokość tabeli, ale pamiętaj, aby uwzględnić ją w kodzie. Należy zauważyć, że char_filter_type jest dostępna tylko dla filtrów, które można dostosować. Jeśli nie ma żadnych opcji, tak jak w przypadku html_strip, nie ma skojarzonego #Microsoft. Azure. Search Type.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Tokenizatory

Tokenizatora dzieli ciągły tekst na sekwencję tokenów, na przykład przerywanie zdania do wyrazów. W poniższej tabeli tokenizatory zaimplementowane przy użyciu oprogramowania Apache Lucene są połączone z dokumentacją interfejsu API Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Opis i opcje**|  
|------------------|-------------------------------|---------------------------|  
|[wdrożenie klasyczne](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Tokenizatora na podstawie gramatyki, które są odpowiednie do przetwarzania większości dokumentów w języku europejskim.  </br></br>**Opcje**  </br></br>maxTokenLength (typ: int) — Maksymalna długość tokenu. Wartość domyślna: 255, maksimum: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes dane wejściowe z krawędzi do n-gramów o podanej wielkości.  </br></br> **Opcje**  </br></br>minGram (typ: int) — wartość domyślna: 1, maksimum: 300.  </br></br>maxGram (typ: int) — wartość domyślna: 2, maksimum: 300. Musi być większa niż minGram.  </br></br>tokenChars (typ: tablica ciągów) — klasy znaków, które mają być przechowywane w tokenach. Dozwolone wartości: </br>"Letter", "cyfra", "Odstęp", "interpunkcja", "symbol". Domyślnie do pustej tablicy — wszystkie znaki są zachowywane. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Emituje cały dane wejściowe jako jeden token.  </br></br>**Opcje**  </br></br>maxTokenLength (typ: int) — Maksymalna długość tokenu. Wartość domyślna: 256, maksimum: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[cyrylicy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Dzieli tekst na nie literę. Podział tokenów dłuższy niż 255 znaków.|  
|[dużych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Dzieli tekst bez liter i konwertuje je na małe litery. Podział tokenów dłuższy niż 255 znaków.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Dzieli tekst przy użyciu reguł specyficznych dla języka.  </br></br>**Opcje**  </br></br>maxTokenLength (typ: int) — Maksymalna długość tokenu, domyślnie: 255, maksimum: 300. Tokeny dłuższe niż maksymalna długość są dzielone. Tokeny dłuższe niż 300 znaków są najpierw podzielone na tokeny o długości 300, a następnie każdy z tych tokenów jest podzielony na podstawie zestawu maxTokenLength.  </br></br>isSearchTokenizer (typ: bool) — ustaw wartość true, jeśli jest używana jako tokenizatora wyszukiwania, ustaw wartość false, jeśli zostanie użyta jako tokenizatora indeksowania. </br></br>Language (typ: String) — język, który ma być używany, domyślnie "angielski". Dozwolone wartości to: </br>"bengalski", "bułgarski", "kataloński", "chineseSimplified", "chineseTraditional", "chorwacki", "czeski", "duński", "holenderski", "angielski", "francuski", "niemiecki", "włoski", "gudżarati", "hindi", "islandzki", "język" Malajski "," malajalam "," marathi "," norwegianBokmaal "," Polski "," portugalski "," portugueseBrazilian "," pendżabski "," rumuński "," rosyjski "," serbianCyrillic "," serbianLatin "," słoweński "," hiszpański "," szwedzki "," tamilski "," telugu "," tajlandzki "," ukraiński "," urdu "," wietnamski " |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Dzieli tekst przy użyciu reguł specyficznych dla języka i zmniejsza liczbę słów do ich formularzy podstawowych. </br></br>**Opcje** </br></br>maxTokenLength (typ: int) — Maksymalna długość tokenu, domyślnie: 255, maksimum: 300. Tokeny dłuższe niż maksymalna długość są dzielone. Tokeny dłuższe niż 300 znaków są najpierw podzielone na tokeny o długości 300, a następnie każdy z tych tokenów jest podzielony na podstawie zestawu maxTokenLength. </br></br> isSearchTokenizer (typ: bool) — ustaw wartość true, jeśli jest używana jako tokenizatora wyszukiwania, ustaw wartość false, jeśli zostanie użyta jako tokenizatora indeksowania. </br></br>Language (typ: String) — język, który ma być używany, domyślnie "angielski". Dozwolone wartości to: </br>"arabski", "bengalski", "bułgarski", "kataloński", "chorwacki", "czeski", "duński", "holenderski", "angielski", "Estoński", "fiński", "francuski", "niemiecki", "grecki", "gudżarati", "hebrajski", "hindi", "węgierski", "islandzki", "English", "włoski", "kannada", "łotewski", "litewski", "Malajski", "malajalam", "marathi", "norwegianBokmaal", "Polski", "portugalski", "portugueseBrazilian", "pendżabski", "rumuński", "rosyjski", "serbianCyrillic", "serbianLatin", "słowacki", "słoweński", "hiszpański", "szwedzki", "tamilski", "telugu", "turecki", "ukraiński", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes dane wejściowe do n-gramów o podanej wielkości. </br></br>**Opcje** </br></br>minGram (typ: int) — wartość domyślna: 1, maksimum: 300. </br></br>maxGram (typ: int) — wartość domyślna: 2, maksimum: 300. Musi być większa niż minGram. </br></br>tokenChars (typ: tablica ciągów) — klasy znaków, które mają być przechowywane w tokenach. Dozwolone wartości: "litera", "cyfra", "Odstęp", "interpunkcja", "symbol". Domyślnie do pustej tablicy — wszystkie znaki są zachowywane. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizatora dla hierarchii podobnej do ścieżki. **Opcje** </br></br>ogranicznik (typ: ciąg) — wartość domyślna: "/. </br></br>zastąpienie (typ: ciąg) — Jeśli jest ustawiony, zastępuje znak ogranicznika. Domyślnie taka sama jak wartość ogranicznika. </br></br>maxTokenLength (typ: int) — Maksymalna długość tokenu. Wartość domyślna: 300, maksimum: 300. Ścieżki dłuższe niż maxTokenLength są ignorowane. </br></br>Odwróć (typ: bool) — Jeśli prawda, generuje token w odwrotnej kolejności. Wartość domyślna: false. </br></br>Skip (Type: bool) — początkowe tokeny do pominięcia. Wartość domyślna to 0.|  
|[znaczne](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Ten tokenizatora używa dopasowania wzorca wyrażenia regularnego do konstruowania odrębnych tokenów. </br></br>**Opcje** </br></br> [wzorzec](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (typ: ciąg) — wzorzec wyrażenia regularnego w celu dopasowania do separatorów tokenów. Wartość domyślna to `\W+` , która dopasowuje znaki niebędące słowami. </br></br>[flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Type: String) — flagi wyrażenia regularnego. Wartość domyślna to pusty ciąg. Dozwolone wartości: CANON_EQ, CASE_INSENSITIVE, komentarze, DOTALL, LITERAŁ, WIELOWIERSZOWY, UNICODE_CASE, UNIX_LINES </br></br>Group (typ: int) — Grupa, która ma zostać wyodrębniona do tokenów. Wartość domyślna to-1 (Split).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Dzieli tekst po [regułach segmentacji tekstu Unicode](https://unicode.org/reports/tr29/). </br></br>**Opcje** </br></br>maxTokenLength (typ: int) — Maksymalna długość tokenu. Wartość domyślna: 255, maksimum: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes adresy URL i wiadomości e-mail jako jeden token. </br></br>**Opcje** </br></br> maxTokenLength (typ: int) — Maksymalna długość tokenu. Wartość domyślna: 255, maksimum: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne) |Dzieli tekst na białe znaki. Podział tokenów dłuższy niż 255 znaków.|  

 <sup>1</sup> typy tokenizatora są zawsze poprzedzone znakiem "#Microsoft. Azure. Search", tak że "ClassicTokenizer" byłby rzeczywiście określony jako "#Microsoft. Azure. Search. ClassicTokenizer". Usunęliśmy prefiks, aby zmniejszyć szerokość tabeli, ale pamiętaj, aby uwzględnić ją w kodzie. Należy zauważyć, że tokenizer_type jest dostępna tylko dla tokenizatory, które można dostosować. Jeśli nie ma żadnych opcji, tak jak w przypadku liter tokenizatora, nie ma żadnych skojarzonych #Microsoft. Azure. Search Type.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Filtry tokenów

Filtr tokenu służy do filtrowania lub modyfikowania tokenów wygenerowanych przez tokenizatora. Na przykład można określić filtr małych liter, który konwertuje wszystkie znaki na małe litery. W analizatorze niestandardowym można korzystać z wielu filtrów tokenu. Filtry tokenów działają w kolejności, w jakiej są wyświetlane. 

W poniższej tabeli filtry tokenów zaimplementowane przy użyciu oprogramowania Apache Lucene są połączone z dokumentacją interfejsu API Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Opis i opcje**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Filtr tokenu, który stosuje normalizację arabski do normalizacji orthography.|  
|[apostrof](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Paski wszystkie znaki po znaku apostrofu (łącznie z apostrofem). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Konwertuje alfabetyczne, liczbowe i symboliczne znaki Unicode, które nie znajdują się w pierwszych 127 znaków ASCII (blok Unicode "podstawowy Latin") do ich odpowiedników ASCII (jeśli taki istnieje).<br /><br /> **Opcje**<br /><br /> preserveOriginal (typ: bool) — w przypadku wartości true jest przechowywany oryginalny token. Wartością domyślną jest false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Tworzy dwugramowe warunki CJK, które są generowane na podstawie StandardTokenizer.<br /><br /> **Opcje**<br /><br /> ignoreScripts (typ: tablica ciągów) — skrypty do zignorowania. Dozwolone wartości to: "Han", "Hiragana", "katakana", "Hangul". Wartość domyślna to pusta lista.<br /><br /> outputUnigrams (typ: bool) — ustaw wartość true, jeśli chcesz, aby dane wyjściowe były zawsze unigrams i dwugramowe. Wartością domyślną jest false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Normalizuje różnice szerokości CJK. Zagnij warianty ASCII o pełnej szerokości do równoważnej podstawowej odmiany katakana i połówkowej szerokości w równoważnej postaci kana. |  
|[wdrożenie klasyczne](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Usuwa angielską possessives oraz kropki z akronimów. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Utwórz dwugramy dla często zachodzących terminów podczas indeksowania. Pojedyncze terminy nadal są indeksowane, a dwugramowe.<br /><br /> **Opcje**<br /><br /> commonWords (typ: tablica ciągów) — zestaw typowych wyrazów. Wartość domyślna to pusta lista. Wymagane.<br /><br /> ignoreCase (typ: bool) — w przypadku wartości true dopasowanie uwzględnia wielkość liter. Wartością domyślną jest false.<br /><br /> querymode (typ: bool) — generuje dwugramy, a następnie usuwa typowe słowa i pojedyncze terminy, po których następuje wspólny wyraz. Wartością domyślną jest false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Deredagowanie słów złożonych znalezionych w wielu językach Germanic.<br /><br /> **Opcje**<br /><br /> Lista wyrazów (typ: tablica ciągów) — lista słów do dopasowania. Wartość domyślna to pusta lista. Wymagane.<br /><br /> minWordSize (typ: int) — tylko wyrazy dłuższe niż ten przetworzony. Wartość domyślna to 5.<br /><br /> minSubwordSize (typ: int) — wszystkie słowa podrzędne są dłuższe niż te są zwracane. Wartość domyślna to 2.<br /><br /> maxSubwordSize (typ: int) — wszystkie słowa podrzędne są krótsze niż te są zwracane. Wartość domyślna to 15.<br /><br /> onlyLongestMatch (typ: bool) — Dodaj tylko najdłuższy pasujący wyraz podrzędny do danych wyjściowych. Wartością domyślną jest false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Generuje n-gramy o podanej wielkości od początku lub z tyłu tokenu wejściowego.<br /><br /> **Opcje**<br /><br /> minGram (typ: int) — wartość domyślna: 1, maksimum: 300.<br /><br /> maxGram (typ: int) — wartość domyślna: 2, maksymalnie 300. Musi być większa niż minGram.<br /><br /> Side (typ: ciąg) — określa, z której strony wejściowej ma być generowany n-gram. Dozwolone wartości: "Front", "Wstecz" |  
|[dla koprocedury](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Usuwa elisions. Na przykład "L'Avion" (płaszczyzna) jest konwertowana na "Avion" (płaszczyzna).<br /><br /> **Opcje**<br /><br /> Artykuły (typ: tablica ciągów) — zestaw artykułów do usunięcia. Wartość domyślna to pusta lista. Jeśli nie ma żadnej listy artykułów, domyślnie wszystkie artykuły francuski są usuwane.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Normalizuje znaki niemieckie zgodnie z algorytmami heurystycznymi [algorytmu German2 Snowball](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Normalizuje tekst w języku hindi, aby usunąć różnice pisowni. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizuje reprezentację tekstu w formacie Unicode w językach indyjskich.
|[Zachowaj](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Filtr tokenu, który przechowuje tylko tokeny z tekstem zawartym na określonej liście wyrazów.<br /><br /> **Opcje**<br /><br /> keepWords (typ: tablica ciągów) — lista słów do zachowania. Wartość domyślna to pusta lista. Wymagane.<br /><br /> keepWordsCase (typ: bool) — w przypadku wartości true należy najpierw małe litery. Wartością domyślną jest false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Oznacza terminy jako słowa kluczowe.<br /><br /> **Opcje**<br /><br /> Słowa kluczowe (typ: tablica ciągów) — lista słów do oznaczenia jako słowa kluczowe. Wartość domyślna to pusta lista. Wymagane.<br /><br /> ignoreCase (typ: bool) — w przypadku wartości true należy najpierw małe litery. Wartością domyślną jest false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Emituje każdy token przychodzący dwa razy, jako słowo kluczowe i jeden raz jako niebędące słowami kluczowymi. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Filtr kstem o wysokiej wydajności dla języka angielskiego. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Usuwa wyrazy, które są zbyt długie lub zbyt krótkie.<br /><br /> **Opcje**<br /><br /> min (typ: int) — minimalna liczba. Wartość domyślna: 0, wartość maksymalna: 300.<br /><br /> Max (typ: int) — Maksymalna liczba. Wartość domyślna: 300, maksimum: 300.|  
|[limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft. Azure. Search. LimitTokenFilter|Ogranicza liczbę tokenów podczas indeksowania.<br /><br /> **Opcje**<br /><br /> maxTokenCount (typ: int) — Maksymalna liczba tokenów do wygenerowania. Wartość domyślna to 1.<br /><br /> consumeAllTokens (typ: bool) — czy wszystkie tokeny z danych wejściowych muszą być używane nawet po osiągnięciu maxTokenCount. Wartością domyślną jest false.|  
|[dużych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Normalizuje tekst tokenu małymi literami. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Generuje n-gramy o podanych rozmiarach.<br /><br /> **Opcje**<br /><br /> minGram (typ: int) — wartość domyślna: 1, maksimum: 300.<br /><br /> maxGram (typ: int) — wartość domyślna: 2, maksymalnie 300. Musi być większa niż minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Używa wyrażeń regularnych języka Java do emisji wielu tokenów, po jednej dla każdej grupy przechwytywania w jednym lub kilku wzorcach.<br /><br /> **Opcje**<br /><br /> wzorce (typ: tablica ciągów) — Lista wzorców do dopasowania do każdego tokenu. Wymagane.<br /><br /> preserveOriginal (typ: bool) — ustaw na true, aby zwracał oryginalny token nawet wtedy, gdy jeden z wzorców pasuje, wartość domyślna: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Filtr tokenu, który stosuje wzorzec do każdego tokenu w strumieniu, zastępując wystąpienia dopasowania z określonym ciągiem zastępczym.<br /><br /> **Opcje**<br /><br /> wzorzec (typ: ciąg) — wymagany.<br /><br /> zastąpienie (typ: ciąg) — wymagany.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne) |Stosuje normalizację dla perski. |  
|[postaci fonetycznej](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Utwórz tokeny dla dopasowań fonetycznych.<br /><br /> **Opcje**<br /><br /> koder (typ: ciąg) — koder fonetyczny do użycia. Dozwolone wartości to: "unphone", "doubleMetaphone", "SOUNDEX", "refinedSoundex", "caverphone1", "caverphone2", "Kolonia", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Wartość domyślna: "Identyfikator telefonu". Wartość domyślna to unphone.<br /><br /> Aby uzyskać więcej informacji, zobacz [koder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) .<br /><br /> Replace (Type: bool)-true Jeśli zakodowane tokeny powinny zastąpić oryginalne tokeny, wartość false, jeśli powinny być dodawane jako synonimy. Wartość domyślna to true.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Przekształca strumień tokenu zgodnie z [algorytmem Porter rdzeni](https://tartarus.org/~martin/PorterStemmer/). |  
|[cofnięci](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Odwraca ciąg tokenu. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Normalizuje użycie znaków Scandinavian, które są zamienne. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Składa znaki Scandinavian åÅäæÄÆ->a i öÖøØ->o. Odróżni się także od używania podwójnych samogłosek AA, AE, Ao, OE i oo, pozostawiając tylko pierwszy z nich. |  
|[shingle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Tworzy kombinację tokenów jako pojedynczy token.<br /><br /> **Opcje**<br /><br /> maxShingleSize (typ: int) — wartość domyślna to 2.<br /><br /> minShingleSize (typ: int) — wartość domyślna to 2.<br /><br /> outputUnigrams (typ: bool) — w przypadku wartości true strumień wyjściowy zawiera tokeny wejściowe (unigrams), a także Shingles. Wartość domyślna to true.<br /><br /> outputUnigramsIfNoShingles (typ: bool) — Jeśli prawda, Zastąp zachowanie outputUnigrams = = false dla tych czasów, gdy żaden Shingles nie jest dostępny. Wartością domyślną jest false.<br /><br /> tokenSeparator (typ: ciąg) — ciąg, który ma być używany podczas dołączania sąsiednich tokenów w celu utworzenia Shingle. Wartość domyślna to "".<br /><br /> filterToken (typ: ciąg) — ciąg do wstawienia dla każdej pozycji, w której nie ma tokenu. Wartość domyślna to "_".|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtr tokenów Snowball.<br /><br /> **Opcje**<br /><br /> Language (typ: String) — dozwolone wartości to: "armeński", "baskijski", "kataloński", "duński", "holenderski", "angielski", "fiński", "francuski", "niemiecki", "German2", "węgierski", "włoski", "KP", "Lovins", "norweski", "Porter", "portugalski", "rumuński", "rosyjski", "hiszpański", "szwedzki", "turecki"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizuje reprezentację tekstu sorani w formacie Unicode.<br /><br /> **Opcje**<br /><br /> Brak.|  
|programy szukające rdzeni|StemmerTokenFilter|Filtr z poszczególnymi językami.<br /><br /> **Opcje**<br /><br /> Language (typ: String) — dozwolone wartości to: <br /> -   [języku](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [Armenia](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [baskijski](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [Real brazylijski](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"bułgarski"<br />-   [Kataloński](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [czeski](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [egzemplarz](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [niderlandzkim](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [Anglia](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [języka](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   [Polski](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"Galicyjski"<br />- "minimalGalician"<br />-   [wersja](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   [Grecka](https://sais.se/mthprize/2007/ntais2007.pdf)<br />— "hindi"<br />-   [Węgiersk](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [Rupia](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   [Irlandii](https://snowballstem.org/otherapps/oregan/)<br />-   [włoski](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [języka](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [norweski](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [portugalski](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [rumuński](https://snowballstem.org/otherapps/romanian/)<br />-   [Federację](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [hiszpański](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [języka](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   [eksportujący](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Wszystkie zależne od słownika terminy są oznaczane jako słowa kluczowe, co uniemożliwia wyszukanie łańcucha. Musi być umieszczony przed filtrami rdzeni.<br /><br /> **Opcje**<br /><br /> reguły (typ: tablica ciągów) — reguły dotyczące rdzeni w następującym formacie "Word => trzon" na przykład "uruchomił => Run". Wartość domyślna to pusta lista.  Wymagane.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Usuwa zatrzymywanie słów ze strumienia tokenu. Domyślnie filtr używa wstępnie zdefiniowanej listy ograniczników zatrzymania dla języka angielskiego.<br /><br /> **Opcje**<br /><br /> Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Nie można określić, jeśli określono stopwordsList.<br /><br /> stopwordsList (typ: ciąg) — wstępnie zdefiniowana lista Stop-słowa. Nie można określić, jeśli określono Stop-słowa. Dozwolone wartości to: "arabski", "armeński", "baskijski", "Brazylia", "bułgarski", "kataloński", "czeski", "duński", "holenderski", "angielski", "fiński", "francuski", "Galicyjski", "niemiecki", "grecki", "hindi", "węgierski", "indonezyjski", "irlandzki", "włoski", "łotewski", "norweski", "Perski", "portugalski", "rumuński", "turecki", "sorani", "hiszpański" Nie można określić, jeśli określono Stop-słowa. <br /><br /> ignoreCase (typ: bool) — Jeśli prawda, wszystkie słowa są najpierw małymi literami. Wartością domyślną jest false.<br /><br /> removeTrailing (typ: bool) — Jeśli prawda, zignoruj ostatni termin wyszukiwania, jeśli jest to słowo Stop. Wartość domyślna to true.
|[synonim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Dopasowuje jeden lub wiele synonimów wyrazów w strumieniu tokenu.<br /><br /> **Opcje**<br /><br /> Synonimy (typ: tablica ciągów) — wymagane. Lista synonimów w jednym z następujących dwóch formatów:<br /><br /> -niejawne, nie, wspaniałe => — wszystkie warunki po lewej stronie znaku => są zastępowane wszystkimi terminami po prawej stronie.<br /><br /> -Super, nierówne, wspaniałe, niedobrze — rozdzielana przecinkami lista słów równoważnych. Ustaw opcję Rozwiń, aby zmienić sposób interpretacji tej listy.<br /><br /> ignoreCase (typ: bool) — uwzględnianie wielkości liter w celu dopasowania. Wartością domyślną jest false.<br /><br /> expand (Type: bool) — w przypadku wartości true wszystkie wyrazy znajdujące się na liście synonimów (jeśli => nie są używane) mapowane na siebie. <br />Poniższa lista: niezwykłe, nierówne, wspaniałe, super jest równoważne: niezwykłej, nierównej, wspaniałe, Super => niezwykłym, przytwierdzeniem, wspaniałe, Super<br /><br />-W przypadku wartości false następująca lista: niezwykłe, nierówne, wspaniałe, Super są równoważne: niezwykłym, przypuszczać, wspaniałe, Super => niesamowity.|  
|[Trim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Przycina wiodące i końcowe odstępy od tokenów. |  
|[obciąć](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Obcina warunki do określonej długości.<br /><br /> **Opcje**<br /><br /> Długość (typ: int) — wartość domyślna: 300, maksimum: 300. Wymagane.|  
|[unikatowy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtruje tokeny z tym samym tekstem co poprzedni token.<br /><br /> **Opcje**<br /><br /> onlyOnSamePosition (typ: bool) — Jeśli to ustawienie, Usuń duplikaty tylko w tym samym położeniu. Wartość domyślna to true.|  
|[znaki](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typ ma zastosowanie tylko wtedy, gdy opcje są dostępne)  |Normalizuje tekst tokenu do Wielkiej litery. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Dzieli słowa na słowa podrzędne i wykonuje opcjonalne przekształcenia w grupach podwyrazów.<br /><br /> **Opcje**<br /><br /> generateWordParts (typ: bool) — powoduje wygenerowanie części wyrazów, na przykład "AzureSearch" zmieni się na "wyszukiwanie" na platformie Azure. Wartość domyślna to true.<br /><br /> generateNumberParts (typ: bool) — powoduje wygenerowanie liczby podwyrazów. Wartość domyślna to true.<br /><br /> catenateWords (typ: bool) — powoduje, że maksymalne uruchomienia części wyrazów mają być catenated, na przykład "Azure-Search" zmieni się na "AzureSearch". Wartością domyślną jest false.<br /><br /> catenateNumbers (typ: bool) — powoduje, że maksymalne uruchomienia części liczby mają być catenated, na przykład "1-2" przyjmuje wartość "12". Wartością domyślną jest false.<br /><br /> catenateAll (typ: bool) — powoduje, że wszystkie części wyrazów, które mają być catenated, na przykład "Azure-Search-1" staną się "AzureSearch1". Wartością domyślną jest false.<br /><br /> splitOnCaseChange (typ: bool) — w przypadku wartości true dzieli wyrazy na caseChange, na przykład "AzureSearch" zmieni się na "wyszukiwanie" na platformie Azure. Wartość domyślna to true.<br /><br /> preserveOriginal — powoduje zachowywanie oryginalnych słów i dodawanie ich do listy wyrazów. Wartością domyślną jest false.<br /><br /> splitOnNumerics (typ: bool) — Jeśli prawda, dzieli na cyfry, na przykład "Azure1Search" zmieni się na "Azure" "1" "Wyszukaj". Wartość domyślna to true.<br /><br /> stemEnglishPossessive (typ: bool) — powoduje usunięcie końcowego elementu "" "dla każdego wyrazu podrzędnego. Wartość domyślna to true.<br /><br /> protectedWords (typ: tablica ciągów) — tokeny, które mają być chronione przed ograniczeniem. Wartość domyślna to pusta lista.|  

 <sup>1</sup> typy filtrów tokenu zawsze są poprzedzone znakiem "#Microsoft. Azure. Search", tak że "ArabicNormalizationTokenFilter" byłby rzeczywiście określony jako "#Microsoft. Azure. Search. ArabicNormalizationTokenFilter".  Usunęliśmy prefiks, aby zmniejszyć szerokość tabeli, ale pamiętaj, aby uwzględnić ją w kodzie.  

## <a name="see-also"></a>Zobacz też

- [Interfejsy API REST usługi Azure Wyszukiwanie poznawcze](/rest/api/searchservice/)
- [Analizatory na platformie Azure Wyszukiwanie poznawcze (przykłady)](search-analyzers.md#examples)
- [Tworzenie indeksu (REST)](/rest/api/searchservice/create-index)