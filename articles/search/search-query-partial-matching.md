---
title: Częściowe warunki, wzorce i znaki specjalne
titleSuffix: Azure Cognitive Search
description: Wykonaj zapytania o symbole wieloznaczne, wyrażenia regularne i prefiksy, aby dopasować całe lub częściowe warunki w żądaniu zapytania Wyszukiwanie poznawcze platformy Azure. Wzorce twarde do dopasowania, które zawierają znaki specjalne, można rozpoznać przy użyciu pełnej składni zapytań i analizatorów niestandardowych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 5a05f2973ac17460250fb3e80eb7bc0da9849940
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262880"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Częściowe wyszukiwanie warunków i wzorce ze znakami specjalnymi (symbol wieloznaczny, wyrażenie regularne, wzorce)

*Częściowe wyszukiwanie warunków* odwołuje się do zapytań składających się z fragmentów terminów, gdzie zamiast całego terminu może być tylko początek, środek lub koniec okresu (czasami określany jako prefiksy, wrostkowe lub kwerendy sufiksów). *Wzorzec* może być kombinacją fragmentów, często z znakami specjalnymi, takimi jak kreski lub ukośniki, które są częścią ciągu zapytania. Typowe przypadki użycia obejmują zapytania dotyczące części numeru telefonu, adresu URL, osób lub kodów produktów lub wyrazów złożonych.

Wyszukiwanie częściowe i wzorców może być problematyczne, jeśli indeks nie ma warunków w oczekiwanym formacie. Podczas [fazy analizy leksykalnej](search-lucene-query-architecture.md#stage-2-lexical-analysis) indeksowania (przy założeniu domyślnego analizatora standardowego) znaki specjalne są odrzucane, złożone i złożone ciągi są dzielone, a odstępy są usuwane. wszystko, co może spowodować niepowodzenie kwerend wzorca, gdy nie zostanie znalezione dopasowanie. Na przykład numer telefonu, taki jak `+1 (425) 703-6214` (tokeny AS `"1"`, `"425"`, `"703"`, `"6214"`) nie będzie wyświetlany w `"3-62"` zapytaniu, ponieważ ta zawartość nie istnieje w indeksie. 

Rozwiązaniem jest wywołanie analizatora, który zachowuje kompletny ciąg, w tym spacje i znaki specjalne, w razie potrzeby, aby można było dopasować częściowe terminy i wzorce. Tworzenie dodatkowego pola dla nieuszkodzonego ciągu, przy użyciu analizatora obsługującego zawartość, jest podstawą rozwiązania.

> [!TIP]
> Znasz interfejsy API Poster i REST? [Pobierz kolekcję przykładów zapytania](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/full-syntax-examples) , aby wykonać zapytanie dotyczące częściowych terminów i znaków specjalnych opisanych w tym artykule.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Co to jest częściowe wyszukiwanie w usłudze Azure Wyszukiwanie poznawcze

W przypadku usługi Azure Wyszukiwanie poznawcze w następujących formularzach jest dostępne częściowe wyszukiwanie i wzorzec:

+ [Wyszukiwanie prefiksów](query-simple-syntax.md#prefix-search), takie `search=cap*`jak, dopasowanie na "Cap'n Jack Waterfront Inn" lub "Gacc Wielka". Możesz użyć prostej składni zapytania lub pełnej składni zapytań Lucene dla wyszukiwania prefiksów.

+ [Wyszukiwanie przy użyciu symboli wieloznacznych](query-lucene-syntax.md#bkmk_wildcard) i [wyrażeń regularnych](query-lucene-syntax.md#bkmk_regex) , które wyszukują wzorzec lub fragmenty ciągu osadzonego. Wyrażenia z symbolami wieloznacznymi i regularnymi wymagają pełnej składni Lucene. Zapytania dotyczące sufiksów i indeksów są formułowane jako wyrażenie regularne.

  Poniżej przedstawiono kilka przykładów wyszukiwania częściowego terminu. W przypadku zapytania dotyczącego sufiksu, uwzględniając termin "alfanumeryczne", należy użyć wyszukiwania wieloznacznego (`search=/.*numeric.*/`) w celu znalezienia dopasowania. W przypadku częściowego terminu zawierającego znaki wewnętrzne, takie jak fragment adresu URL, może być konieczne dodanie znaków ucieczki. W formacie JSON ukośnik `/` jest wyprowadzany przy użyciu ukośnika `\`odwrotnego. W związku z `search=/.*microsoft.com\/azure\/.*/` tym, jest składnią dla FRAGMENTU adresu URL "Microsoft.com/Azure/".

Jak wspomniano, wszystkie powyższe wymagania wymagają, aby indeks zawierał ciągi w formacie obsługującym dopasowanie do wzorca, którego Analizator standardowy nie zapewnia. Wykonując kroki opisane w tym artykule, można upewnić się, że istnieje wymagana zawartość do obsługi tych scenariuszy.

## <a name="solving-partialpattern-search-problems"></a>Rozwiązywanie problemów z wyszukiwaniem części/wzorców

Jeśli chcesz wyszukać fragmenty lub wzorce lub znaki specjalne, możesz zastąpić domyślną Analizator analizatorem niestandardowym, który działa w ramach prostszych reguł tokenizacji, zachowując cały ciąg. Po przełączeniu do tyłu podejście wygląda następująco:

+ Zdefiniuj pole, aby zachować nienaruszoną wersję ciągu (przy założeniu, że chcesz przeanalizować i nieanalizowany tekst)
+ Wybierz wstępnie zdefiniowany Analizator lub Zdefiniuj Analizator niestandardowy, aby wyprowadził nieanalizowany ciąg nienaruszony
+ Przypisz Analizator niestandardowy do pola
+ Kompiluj i Testuj indeks

> [!TIP]
> Ocenianie analizatorów jest procesem iteracyjnym wymagającym częstych rekompilacji indeksów. Ten krok można ułatwić przy użyciu programu Poster, interfejsów API REST do [tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index), [usuwania indeksu](https://docs.microsoft.com/rest/api/searchservice/delete-index),[ładowania dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)i [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents). W przypadku dokumentów załadunkowych treść żądania powinna zawierać mały reprezentatywny zestaw danych, który ma zostać przetestowany (na przykład pole z numerami telefonów lub kodami produktów). Za pomocą tych interfejsów API w tej samej kolekcji ogłoszeń można szybko wykonać te kroki.

## <a name="duplicate-fields-for-different-scenarios"></a>Duplikowanie pól dla różnych scenariuszy

Analizatory są przypisywane według poszczególnych pól, co oznacza, że można tworzyć pola w indeksie w celu optymalizacji pod kątem różnych scenariuszy. W zależności od tego można zdefiniować "featureCode" i "featureCodeRegex" do obsługi regularnego wyszukiwania pełnotekstowego w pierwszej i zaawansowane dopasowywanie do wzorca w drugim.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Wybieranie analizatora

W przypadku wybrania analizatora, który tworzy tokeny całodzienne, typowe są następujące analizatory:

| Analizator | Zachowania |
|----------|-----------|
| [analizatory języka](index-add-language-analyzers.md) | Zachowuje łączniki w wyrazach złożonych lub ciągach, mutacjach samogłosek i formularzach zleceń. Jeśli wzorce zapytań zawierają łączniki, korzystanie z analizatora języka może być wystarczające. |
| [kodu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Zawartość całego pola jest tokenem pojedynczym terminem. |
| [odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Oddziela tylko odstępy. Warunki, które zawierają łączniki lub inne znaki, są traktowane jako pojedynczy token. |
| [Analizator niestandardowy](index-add-custom-analyzers.md) | Rekomendowane Utworzenie analizatora niestandardowego pozwala określić zarówno tokenizatora, jak i filtr tokenu. Poprzednie analizatory muszą być używane jako-is. Analizator niestandardowy umożliwia wybranie filtrów tokenizatory i tokenów, które mają być używane. <br><br>Zalecaną kombinacją jest [słowo kluczowe tokenizatora](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) z [filtrem tokenu o niższym przypadku](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). We wstępnie zdefiniowanym [analizatorze słów kluczowych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nie jest rozróżniana wielkość liter, co może spowodować niepowodzenie zapytań. Analizator niestandardowy zapewnia mechanizm dodawania do filtru tokenów małych liter. |

Jeśli używasz narzędzia testowego interfejsu API sieci Web, takiego jak Poster, możesz dodać [wywołanie REST analizatora testu](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) , aby przeprowadzić inspekcję danych wyjściowych z tokenami.

Musisz mieć istniejący indeks do pracy z programem. Uwzględniając istniejący indeks i pole zawierające łączniki lub częściowe warunki, możesz wypróbować różne analizatory, aby zobaczyć, jakie tokeny są emitowane.  

1. Sprawdź Analizator standardowej, aby zobaczyć, jak są domyślnie opisane tokeny.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Oceń odpowiedź, aby zobaczyć, w jaki sposób tekst jest tokenem w indeksie. Zwróć uwagę na to, jak każdy termin jest niższy i podzielony na siebie.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Zmodyfikuj żądanie, aby użyć `whitespace` analizatora `keyword` lub:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Teraz odpowiedź składa się z pojedynczego tokenu, wielkie litery, za pomocą łączników zachowywanych jako część ciągu. Jeśli chcesz wyszukać wzorzec lub częściowy termin, aparat zapytań ma teraz podstawę do znalezienia dopasowania.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Podczas kompilowania drzewa zapytań należy pamiętać, że analizatory zapytań często mają małe litery w wyrażeniu wyszukiwania. Jeśli używasz analizatora, który nie uwzględnia małych liter tekstowych, i nie otrzymujesz oczekiwanych wyników, może to być przyczyną. W rozwiązaniu należy dodać filtr tokenu o niższej wielkości liter, zgodnie z opisem w poniższej sekcji "Użyj analizatorów niestandardowych" poniżej.

## <a name="configure-an-analyzer"></a>Konfigurowanie analizatora
 
Bez względu na to, czy oceniasz analizatory, czy przenosisz się do przodu przy użyciu określonej konfiguracji, musisz określić analizatorze w definicji pola i ewentualnie skonfigurować Analizator, jeśli nie używasz wbudowanej analizatora. Podczas wymiany analizatorów należy zwykle ponownie skompilować indeks (Porzuć, Odtwórz ponownie i Załaduj ponownie). 

### <a name="use-built-in-analyzers"></a>Używanie wbudowanych analizatorów

Wbudowane lub wstępnie zdefiniowane analizatory mogą być określone przez nazwę we `analyzer` właściwości definicji pola i nie jest wymagana żadna dodatkowa konfiguracja w indeksie. W poniższym przykładzie pokazano, jak ustawić `whitespace` Analizator dla pola. 

Aby uzyskać więcej scenariuszy i dowiedzieć się więcej na temat innych wbudowanych analizatorów, zobacz [Lista wstępnie zdefiniowanych analizatorów](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Korzystanie z analizatorów niestandardowych

Jeśli używasz [analizatora niestandardowego](index-add-custom-analyzers.md), zdefiniuj go w indeksie przy użyciu zdefiniowanej przez użytkownika kombinacji tokenizatora, filtr tokenu z możliwymi ustawieniami konfiguracji. Następnie odwołują się do niego w definicji pola tak samo jak w przypadku wbudowanej analizatora.

Gdy celem jest tokenizacji w całości, zaleca się użycie analizatora niestandardowego, który składa się z **słowa kluczowego tokenizatora** i **niskiego poziomu** .

+ Słowo kluczowe tokenizatora tworzy pojedynczy token dla całej zawartości pola.
+ Filtr tokenów małymi literami przekształca wielkie litery w tekst małymi literami. Analizatory zapytań zwykle małymi literami danych tekstowych. Mniejsza wielkość liter jest zgodna z danymi wejściowymi z tokenami.

Poniższy przykład ilustruje niestandardowy Analizator, który udostępnia tokenizatora słów kluczowych i filtr tokenu z małymi literami.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2` Tokenizatora i `lowercase` filtr tokenu są znane systemowi i używają ich domyślnych konfiguracji, dlatego można odwoływać się do nich według nazwy bez konieczności definiowania ich jako pierwszej.

## <a name="build-and-test"></a>Skompiluj i testuj

Po zdefiniowaniu indeksu z analizatorami i definicjami pól, które obsługują twój scenariusz, Załaduj dokumenty, które mają reprezentatywne ciągi, aby można było przetestować częściowe zapytania ciągu. 

W poprzednich sekcjach objaśniono logikę. W tej części przedstawiono kroki poszczególnych interfejsów API, które należy wywołać podczas testowania rozwiązania. Jak wspomniano wcześniej, jeśli używasz interaktywnego narzędzia do testowania sieci Web, takiego jak program Poster, możesz szybko wykonać te zadania.

+ [Usuń indeks](https://docs.microsoft.com/rest/api/searchservice/delete-index) usuwa istniejący indeks o tej samej nazwie, aby można go było utworzyć ponownie.

+ [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) tworzy strukturę indeksu w usłudze wyszukiwania, w tym definicje analizatora i pola ze specyfikacją analizatora.

+ [Załaduj dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importuje dokumenty mające taką samą strukturę jak indeks, a także zawartość do przeszukania. Po wykonaniu tego kroku indeks jest gotowy do zbadania lub przetestowania.

+ [Analizator testów](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) został wprowadzony w [Wybierz Analizator](#choose-an-analyzer). Przetestuj niektóre ciągi w indeksie przy użyciu różnych analizatorów, aby zrozumieć, w jaki sposób są tokeny.

+ W obszarze [Wyszukaj dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents) objaśniono, jak utworzyć żądanie zapytania przy użyciu [prostej składni](query-simple-syntax.md) lub [pełnej składni](query-lucene-syntax.md) wyrażeń w przypadku symboli wieloznacznych i regularnych.

  W przypadku zapytań częściowych warunkowych, takich jak zapytanie "3-6214" aby znaleźć dopasowanie w "+ 1 (425) 703-6214", można użyć prostej składni: `search=3-6214&queryType=simple`.

  W przypadku zapytań wrostkowe i sufiksów, takich jak zapytania "num" lub "numeric", aby znaleźć dopasowanie dla "alfanumeryczne", użyj pełnej składni "Lucene" i wyrażenia regularnego:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="tune-query-performance"></a>Dostosowywanie wydajności zapytań

W przypadku zaimplementowania zalecanej konfiguracji obejmującej filtr keyword_v2 tokenizatora i niższych wielkości liter można zauważyć spadek wydajności zapytania ze względu na dodatkowe przetwarzanie filtru tokenów dla istniejących tokenów w indeksie. 

Poniższy przykład dodaje [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) , aby przyspieszyć dopasowanie prefiksu. Dodatkowe tokeny są generowane dla kombinacji znaków 2-25, które zawierają znaki: (nie tylko MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/kW, MSFT/SQL). Jak można wyobrazić, dodatkowe tokenizacji wyniki w większym indeksie.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Używanie różnych analizatorów do indeksowania i przetwarzania zapytań

Analizatory są wywoływane podczas indeksowania i podczas wykonywania zapytania. Jest to typowy sposób użycia tego samego analizatora dla obu, ale dla każdego obciążenia można skonfigurować niestandardowe analizy. Zastąpienia analizatora są określone w [definicji indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) w `analyzers` sekcji, a następnie przywoływane dla konkretnych pól. 

Gdy analiza niestandardowa jest wymagana tylko podczas indeksowania, można zastosować Analizator niestandardowy do samego indeksowania i nadal używać standardowego analizatora Lucene (lub innego analizatora) dla zapytań.

Aby określić analizę specyficzną dla roli, można ustawić właściwości pola dla każdego z nich, ustawienie `indexAnalyzer` i `searchAnalyzer` zamiast właściwości domyślnej. `analyzer`

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak analizatory przyczyniają się do problemów z kwerendą i rozwiązują problemy z kwerendami. Następnym krokiem jest bliższe przybliżenie wpływu analizatora na indeksowanie i przetwarzanie zapytań. W szczególności należy rozważyć użycie interfejsu API analizy tekstu do zwracania tokenów wyjściowych, aby zobaczyć dokładnie, co Analizator tworzy dla indeksu.

+ [Analizatory języków](search-language-support.md)
+ [Analizatory do przetwarzania tekstu na platformie Azure Wyszukiwanie poznawcze](search-analyzers.md)
+ [Analizowanie interfejsu API tekstu (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak działa wyszukiwanie pełnotekstowe (architektura zapytania)](search-lucene-query-architecture.md)