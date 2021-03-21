---
title: Częściowe warunki, wzorce i znaki specjalne
titleSuffix: Azure Cognitive Search
description: Wykonaj zapytania o symbole wieloznaczne, wyrażenia regularne i prefiksy, aby dopasować całe lub częściowe warunki w żądaniu zapytania Wyszukiwanie poznawcze platformy Azure. Wzorce twarde do dopasowania, które zawierają znaki specjalne, można rozpoznać przy użyciu pełnej składni zapytań i analizatorów niestandardowych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 2e2625fff802e71f797bf6970e763f2bf11c393e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584180"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>Częściowe wyszukiwanie warunków i wzorce z znakami specjalnymi (łączniki, symbole wieloznaczne, wyrażenia regularne, wzorce)

*Częściowe wyszukiwanie warunków* odwołuje się do zapytań składających się z fragmentów terminów, gdzie zamiast całego terminu może być tylko początek, środek lub koniec okresu (czasami określany jako prefiksy, wrostkowe lub kwerendy sufiksów). Częściowe wyszukiwanie warunków może zawierać kombinację fragmentów, często z znakami specjalnymi, takimi jak łączniki, kreski lub ukośniki, które są częścią ciągu zapytania. Typowe przypadki użycia obejmują części numeru telefonu, adresu URL, kodów lub wyrazów złożonych z dzielenia wyrazów.

Częściowe wyszukiwanie warunków i ciągi zapytań, które zawierają znaki specjalne mogą być problematyczne, jeśli indeks nie ma tokenów w oczekiwanym formacie. W [fazie analizy leksykalnej](search-lucene-query-architecture.md#stage-2-lexical-analysis) indeksowania (przy założeniu domyślnego analizatora standardowego) znaki specjalne są odrzucane, wyrazy złożone są dzielone, a odstępy są usuwane. wszystkie z nich mogą spowodować niepowodzenie zapytań, gdy nie zostanie znalezione dopasowanie. Na przykład numer telefonu, taki jak `+1 (425) 703-6214` (tokeny AS `"1"` , `"425"` , `"703"` , `"6214"` ) nie będzie wyświetlany w `"3-62"` zapytaniu, ponieważ ta zawartość nie istnieje w indeksie. 

Rozwiązaniem jest wywoływanie analizatora podczas indeksowania, które zachowuje pełny ciąg, w tym spacje i znaki specjalne, w razie potrzeby, aby można było uwzględnić spacje i znaki w ciągu zapytania. Podobnie, posiadanie kompletnego ciągu, który nie jest tokenem do mniejszych części, umożliwia dopasowanie wzorców dla "zaczyna się od" lub "kończy się na" zapytania, gdzie wzorzec, który jest udostępniany, można ocenić względem terminu, który nie jest przekształcony przez analizę leksykalną. Utworzenie dodatkowego pola dla nieuszkodzonego ciągu, a także użycie analizatora obsługującego zawartość, który emituje tokeny całodzienne, jest rozwiązaniem dla obu wzorców i dla dopasowania do ciągów zapytań, które zawierają znaki specjalne.

> [!TIP]
> Jeśli znasz interfejsy API Poster i REST, [Pobierz kolekcję przykładów zapytania](https://github.com/Azure-Samples/azure-search-postman-samples/) , aby wykonać zapytanie dotyczące częściowych terminów i znaków specjalnych opisanych w tym artykule.

## <a name="about-partial-term-search"></a>Wyszukiwanie częściowe warunków — informacje

Usługa Azure Wyszukiwanie poznawcze skanuje w poszukiwaniu całych terminów z tokenami w indeksie i nie znajdzie dopasowania w częściowym warunku, chyba że dołączysz symbole wieloznaczne symboli wieloznacznych ( `*` i `?` ), lub sformatuj zapytanie jako wyrażenie regularne. Częściowe warunki są określane przy użyciu następujących metod:

+ [Zapytania wyrażenia regularnego](query-lucene-syntax.md#bkmk_regex) mogą być dowolnym wyrażeniem regularnym, które jest prawidłowe w obszarze Apache Lucene. 

+ [Operatory symboli wieloznacznych z dopasowywaniem prefiksów](query-simple-syntax.md#prefix-search) odnoszą się do ogólnie rozpoznanego wzorca, który zawiera początek okresu, a następnie `*` `?` Operatory sufiksów, takie jak `search=cap*` dopasowanie do "Cap'n Jack Waterfront Inn" lub "Gacc stolic". Dopasowywanie prefiksów jest obsługiwane zarówno w prostej, jak i pełnej składni zapytań Lucene.

+ [Symbol wieloznaczny z wrostkowe i dopasowywanie sufiksów](query-lucene-syntax.md#bkmk_wildcard) umieszcza `*` `?` Operatory i wewnątrz lub na początku terminu i wymaga składni wyrażenia regularnego (gdzie wyrażenie jest ujęte w ukośniki). Na przykład ciąg zapytania ( `search=/.*numeric*./` ) zwraca wyniki dla "alfanumeryczne" i "alfanumeryczne" jako sufiks i wrostkowe dopasowania.

W przypadku częściowego wyszukiwania warunkowego lub wzorca i kilku innych formularzy zapytań, takich jak Wyszukiwanie rozmyte, analizatory nie są używane w czasie zapytania. Dla tych formularzy zapytań, których Analizator wykrywa obecność operatorów i ograniczników, ciąg zapytania jest przesyłany do aparatu bez analizy leksykalnej. Dla tych formularzy zapytań Analizator określony w polu jest ignorowany.

> [!NOTE]
> Gdy częściowy ciąg zapytania zawiera znaki, takie jak ukośniki w fragmentu adresu URL, może być konieczne dodanie znaków ucieczki. W formacie JSON ukośnik `/` jest wyprowadzany przy użyciu ukośnika odwrotnego `\` . W związku z tym, `search=/.*microsoft.com\/azure\/.*/` jest składnią dla fragmentu adresu URL "Microsoft.com/Azure/".

## <a name="solving-partialpattern-search-problems"></a>Rozwiązywanie problemów z wyszukiwaniem części/wzorców

Jeśli chcesz wyszukać fragmenty lub wzorce lub znaki specjalne, możesz zastąpić domyślną Analizator analizatorem niestandardowym, który działa w ramach prostszych reguł tokenizacji, zachowując cały ciąg w indeksie. Po przełączeniu do tyłu podejście wygląda następująco:

1. Zdefiniuj pole, aby przechowywać nienaruszoną wersję ciągu (przy założeniu, że chcesz przeanalizować i nieanalizowany tekst w czasie zapytania)
1. Oceń i wybieraj spośród różnych analizatorów, które emitują tokeny na właściwym poziomie szczegółowości
1. Przypisz Analizator do pola
1. Kompiluj i Testuj indeks

> [!TIP]
> Ocenianie analizatorów jest procesem iteracyjnym wymagającym częstych rekompilacji indeksów. Ten krok można ułatwić przy użyciu programu Poster, interfejsów API REST do [tworzenia indeksu](/rest/api/searchservice/create-index), [usuwania indeksu](/rest/api/searchservice/delete-index),[ładowania dokumentów](/rest/api/searchservice/addupdate-or-delete-documents)i [wyszukiwania dokumentów](/rest/api/searchservice/search-documents). W przypadku dokumentów załadunkowych treść żądania powinna zawierać mały reprezentatywny zestaw danych, który ma zostać przetestowany (na przykład pole z numerami telefonów lub kodami produktów). Za pomocą tych interfejsów API w tej samej kolekcji ogłoszeń można szybko wykonać te kroki.

## <a name="1---create-a-dedicated-field"></a>1 — Tworzenie dedykowanego pola

Analizatory określają, w jaki sposób są określane tokeny w indeksie. Ponieważ analizatory są przypisywane według poszczególnych pól, można utworzyć pola w indeksie, aby zoptymalizować je pod kątem różnych scenariuszy. Na przykład można zdefiniować "featureCode" i "featureCodeRegex" do obsługi regularnego wyszukiwania pełnotekstowego w pierwszej i zaawansowane dopasowywanie do wzorca w drugim. Analizatory przypisane do każdego pola określają sposób, w jaki zawartość każdego pola jest tokenami w indeksie.  

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

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2 — Ustawianie analizatora

W przypadku wybrania analizatora, który tworzy tokeny całodzienne, typowe są następujące analizatory:

| Analizator | Zachowania |
|----------|-----------|
| [analizatory języka](index-add-language-analyzers.md) | Zachowuje łączniki w wyrazach złożonych lub ciągach, mutacjach samogłosek i formularzach zleceń. Jeśli wzorce zapytań zawierają łączniki, korzystanie z analizatora języka może być wystarczające. |
| [kodu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Zawartość całego pola jest tokenem pojedynczym terminem. |
| [odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Oddziela tylko odstępy. Warunki, które zawierają łączniki lub inne znaki, są traktowane jako pojedynczy token. |
| [Analizator niestandardowy](index-add-custom-analyzers.md) | Rekomendowane Utworzenie analizatora niestandardowego pozwala określić zarówno tokenizatora, jak i filtr tokenu. Poprzednie analizatory muszą być używane jako-is. Analizator niestandardowy umożliwia wybranie filtrów tokenizatory i tokenów, które mają być używane. <br><br>Zalecaną kombinacją jest [słowo kluczowe tokenizatora](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) z [filtrem tokenu o niższym przypadku](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). W przypadku wbudowanego [analizatora słów kluczowych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nie są rozróżniane wielkie litery, co może spowodować niepowodzenie zapytań. Analizator niestandardowy zapewnia mechanizm dodawania do filtru tokenów małych liter. |

Jeśli używasz narzędzia testowego interfejsu API sieci Web, takiego jak Poster, możesz dodać [wywołanie REST analizatora testu](/rest/api/searchservice/test-analyzer) , aby przeprowadzić inspekcję danych wyjściowych z tokenami.

Do pracy z programem musi być wypełniony indeks. Uwzględniając istniejący indeks i pole zawierające łączniki lub częściowe warunki, możesz wypróbować różne analizatory, aby zobaczyć, jakie tokeny są emitowane.  

1. Najpierw sprawdź Analizator standardowej, aby zobaczyć, jak są domyślnie opisane tokeny.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Oceń odpowiedź, aby zobaczyć, w jaki sposób tekst jest tokenem w indeksie. Zwróć uwagę na sposób, w jaki każdy termin jest małymi literami, usunięte łączniki i podciągi podzielone na poszczególne tokeny. Tylko te zapytania, które pasują do tych tokenów zwróciją ten dokument w wynikach. Zapytanie zawierające wartość "10-lub" zakończy się niepowodzeniem.

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
1. Teraz zmodyfikuj żądanie, aby użyć `whitespace` `keyword` analizatora lub:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Teraz odpowiedź składa się z pojedynczego tokenu, wielkie litery, za pomocą łączników zachowywanych jako część ciągu. Jeśli zachodzi potrzeba przeszukania wzorca lub częściowego warunku takiego jak "10-lub", aparat zapytań ma teraz podstawę do znalezienia dopasowania.


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
> Podczas kompilowania drzewa zapytań należy pamiętać, że analizatory zapytań często mają małe litery w wyrażeniu wyszukiwania. Jeśli używasz analizatora, który nie uwzględnia małych wielkości liter podczas indeksowania i nie otrzymujesz oczekiwanych wyników, może to być przyczyną. W rozwiązaniu należy dodać filtr tokenu o niższej wielkości liter, zgodnie z opisem w poniższej sekcji "Użyj analizatorów niestandardowych" poniżej.

## <a name="3---configure-an-analyzer"></a>3 — Konfigurowanie analizatora
 
Bez względu na to, czy oceniasz analizatory, czy przenosisz się do przodu przy użyciu określonej konfiguracji, musisz określić analizatorze w definicji pola i ewentualnie skonfigurować Analizator, jeśli nie używasz wbudowanej analizatora. Podczas wymiany analizatorów należy zwykle ponownie skompilować indeks (Porzuć, Odtwórz ponownie i Załaduj ponownie). 

### <a name="use-built-in-analyzers"></a>Używanie wbudowanych analizatorów

Wbudowane analizatory mogą być określone przez nazwę we `analyzer` właściwości definicji pola i nie jest wymagana żadna dodatkowa konfiguracja w indeksie. W poniższym przykładzie pokazano, jak ustawić `whitespace` Analizator dla pola. 

W przypadku innych scenariuszy i Dowiedz się więcej o innych wbudowanych analizatorach, zobacz [wbudowane analizatory](./index-add-custom-analyzers.md#built-in-analyzers). 

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
> `keyword_v2`Tokenizatora i `lowercase` Filtr tokenu są znane systemowi i używają ich domyślnych konfiguracji, dlatego można odwoływać się do nich według nazwy bez konieczności definiowania ich jako pierwszej.

## <a name="4---build-and-test"></a>4 — Kompilowanie i testowanie

Po zdefiniowaniu indeksu z analizatorami i definicjami pól, które obsługują twój scenariusz, Załaduj dokumenty, które mają reprezentatywne ciągi, aby można było przetestować częściowe zapytania ciągu. 

W poprzednich sekcjach objaśniono logikę. W tej części przedstawiono kroki poszczególnych interfejsów API, które należy wywołać podczas testowania rozwiązania. Jak wspomniano wcześniej, jeśli używasz interaktywnego narzędzia do testowania sieci Web, takiego jak program Poster, możesz szybko wykonać te zadania.

+ [Usuń indeks](/rest/api/searchservice/delete-index) usuwa istniejący indeks o tej samej nazwie, aby można go było utworzyć ponownie.

+ [Create index](/rest/api/searchservice/create-index) tworzy strukturę indeksu w usłudze wyszukiwania, w tym definicje analizatora i pola ze specyfikacją analizatora.

+ [Załaduj dokumenty](/rest/api/searchservice/addupdate-or-delete-documents) importuje dokumenty mające taką samą strukturę jak indeks, a także zawartość do przeszukania. Po wykonaniu tego kroku indeks jest gotowy do zbadania lub przetestowania.

+ [Analizator testów](/rest/api/searchservice/test-analyzer) został wprowadzony w [ustawieniu analizatora](#set-an-analyzer). Przetestuj niektóre ciągi w indeksie przy użyciu różnych analizatorów, aby zrozumieć, w jaki sposób są tokeny.

+ W obszarze [Wyszukaj dokumenty](/rest/api/searchservice/search-documents) objaśniono, jak utworzyć żądanie zapytania przy użyciu [prostej składni](query-simple-syntax.md) lub [pełnej składni](query-lucene-syntax.md) wyrażeń w przypadku symboli wieloznacznych i regularnych.

  W przypadku zapytań częściowych warunkowych, takich jak zapytanie "3-6214" aby znaleźć dopasowanie w "+ 1 (425) 703-6214", można użyć prostej składni: `search=3-6214&queryType=simple` .

  W przypadku zapytań wrostkowe i sufiksów, takich jak zapytania "num" lub "numeric", aby znaleźć dopasowanie dla "alfanumeryczne", użyj pełnej składni "Lucene" i wyrażenia regularnego: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Dostosowywanie wydajności zapytań

W przypadku zaimplementowania zalecanej konfiguracji obejmującej filtr keyword_v2 tokenizatora i niższych wielkości liter można zauważyć spadek wydajności zapytania ze względu na dodatkowe przetwarzanie filtru tokenów dla istniejących tokenów w indeksie. 

Poniższy przykład dodaje [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) , aby przyspieszyć dopasowanie prefiksu. Dodatkowe tokeny są generowane dla kombinacji znaków 2-25, które zawierają znaki: (nie tylko MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/kW, MSFT/SQL). 

Jak można wyobrazić, dodatkowe tokenizacji wyniki w większym indeksie. Jeśli masz wystarczającą pojemność, aby pomieścić większy indeks, to podejście z szybszym czasem odpowiedzi może być lepszym rozwiązaniem.

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

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak analizatory przyczyniają się do problemów z kwerendą i rozwiązują problemy z kwerendami. Następnym krokiem jest bliższe przybliżenie wpływu analizatora na indeksowanie i przetwarzanie zapytań. W szczególności należy rozważyć użycie interfejsu API analizy tekstu do zwracania tokenów wyjściowych, aby zobaczyć dokładnie, co Analizator tworzy dla indeksu.

+ [Analizatory języków](search-language-support.md)
+ [Analizatory do przetwarzania tekstu na platformie Azure Wyszukiwanie poznawcze](search-analyzers.md)
+ [Analizowanie interfejsu API tekstu (REST)](/rest/api/searchservice/test-analyzer)
+ [Jak działa wyszukiwanie pełnotekstowe (architektura zapytania)](search-lucene-query-architecture.md)