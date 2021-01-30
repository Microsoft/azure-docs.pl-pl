---
title: 'Samouczek: Tworzenie analizatora niestandardowego'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć Analizator niestandardowy w celu poprawy jakości wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097660"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Samouczek: Tworzenie niestandardowego analizatora dla numerów telefonów

[Analizatory](search-analyzers.md) to kluczowy składnik w dowolnym rozwiązaniu wyszukiwania. Aby poprawić jakość wyników wyszukiwania, ważne jest, aby zrozumieć, w jaki sposób analizatory działają i wpływają na te wyniki.

W niektórych przypadkach, podobnie jak w przypadku pola tekstowego Free, po prostu wybranie odpowiedniego [analizatora języka](index-add-language-analyzers.md) poprawi wyniki wyszukiwania. Jednak niektóre scenariusze, takie jak dokładne wyszukiwanie numerów telefonów, adresów URL lub wiadomości e-mail, mogą wymagać użycia analizatorów niestandardowych.

W tym samouczku używane są następujące [interfejsy API REST](/rest/api/searchservice/) i wyszukiwanie poznawcze platformy Azure:

> [!div class="checklist"]
> * Wyjaśnij, jak działają analizatory
> * Definiowanie niestandardowego analizatora do wyszukiwania numerów telefonów
> * Testowanie tekstu tokenizes analizatora niestandardowego
> * Tworzenie oddzielnych analizatorów służących do indeksowania i wyszukiwania w celu dokładniejszego ulepszania wyników

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku są wymagane następujące usługi i narzędzia.

+ [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)
+ [Utwórz](search-create-service-portal.md) lub [Znajdź istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>Pobieranie plików

Kod źródłowy tego samouczka znajduje się w folderze [Custom-analizatorers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) w repozytorium [Azure-Samples/Azure-Search-Poster](https://github.com/Azure-Samples/azure-search-postman-samples) .

## <a name="1---create-azure-cognitive-search-service"></a>1 — Tworzenie usługi Azure Wyszukiwanie poznawcze

Do ukończenia tego samouczka potrzebna będzie usługa Wyszukiwanie poznawcze platformy Azure, którą można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten przewodnik, możesz skorzystać z warstwy Bezpłatna.

W następnym kroku należy znać nazwę usługi wyszukiwania i jej klucz interfejsu API. Jeśli nie wiesz, jak znaleźć te elementy, zapoznaj się z tym [przewodnikiem Szybki Start](search-create-service-portal.md#get-a-key-and-url-endpoint).


## <a name="2---set-up-postman"></a>2 — Konfigurowanie wpisu

Następnie uruchom program Poster i zaimportuj kolekcję pobraną z [platformy Azure — przykłady/Azure-Search-Samples](https://github.com/Azure-Samples/azure-search-postman-samples).

Aby zaimportować kolekcję, przejdź do obszaru   >  **Importowanie** plików, a następnie wybierz plik kolekcji, który chcesz zaimportować.

Dla każdego żądania należy:

1. Zastąp element `<YOUR-SEARCH-SERVICE>` nazwą usługi wyszukiwania.

1. Zamień na `<YOUR-ADMIN-API-KEY>` klucz podstawowy lub pomocniczy usługi wyszukiwania.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Adres URL i nagłówek żądania post" border="false":::

Jeśli nie znasz programu Poster, zobacz [Poznaj interfejsy API REST platformy Azure wyszukiwanie poznawcze](search-get-started-rest.md).

## <a name="3---create-an-initial-index"></a>3 — Tworzenie początkowego indeksu

W tym kroku utworzymy indeks początkowy, załadujesz dokumenty do indeksu, a następnie wyślesz zapytanie do dokumentów, aby zobaczyć, jak wykonywane są początkowe wyszukiwania.

### <a name="create-index"></a>tworzenie indeksu

Zaczniemy od utworzenia prostego indeksu o nazwie `tutorial-basic-index` z dwoma polami: `id` i `phone_number` . Analizator nie został jeszcze zdefiniowany, więc `standard.lucene` Analizator będzie używany domyślnie.

Aby utworzyć indeks, wysyłamy następujące żądanie:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Ładowanie danych

Następnie załadujemy dane do indeksu. W niektórych przypadkach użytkownik może nie mieć kontroli nad formatem pozyskanych numerów telefonów, aby przetestować różne rodzaje formatów. W idealnym przypadku rozwiązanie wyszukiwania zwróci wszystkie zgodne numery telefonów, niezależnie od ich formatu.

Dane są ładowane do indeksu przy użyciu następującego żądania: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Za pomocą danych w indeksie wszystko jest gotowe do rozpoczęcia wyszukiwania.

### <a name="search"></a>Wyszukaj

Aby wyszukiwanie było intuicyjne, najlepiej jest nie oczekiwać, że użytkownicy sformatują zapytania w określony sposób. Użytkownik może wyszukać `(425) 555-0100` w dowolnym formacie, który pokazano powyżej i nadal będzie oczekiwać na zwrócenie wyników. W tym kroku przetestujemy kilka przykładowych zapytań, aby zobaczyć, jak są one wykonywane.

Rozpoczynamy od wyszukiwania `(425) 555-0100` :

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

To zapytanie zwraca **trzy z czterech oczekiwanych wyników,** ale również zwraca **dwa nieoczekiwane wyniki**:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Następnie przeszukajmy numer bez żadnego formatowania `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

To zapytanie jest nawet gorsze i zwraca tylko **jeden z czterech prawidłowych dopasowań**.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Jeśli okaże się, że te wyniki są mylące, nie są same. W następnej sekcji digmy, dlaczego uzyskujemy te wyniki.

## <a name="4---debug-search-results"></a>4 — debugowanie wyników wyszukiwania

Aby zrozumieć te wyniki wyszukiwania, ważne jest, aby najpierw zrozumieć, jak działają analizatory. W tym miejscu możemy przetestować analizatora domyślnego przy użyciu [interfejsu API analizy tekstu](/rest/api/searchservice/test-analyzer) , a następnie utworzyć Analizator, który spełnia nasze potrzeby.

### <a name="how-analyzers-work"></a>Jak działają analizatory

[Analizator](search-analyzers.md) jest składnikiem [aparatu wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) odpowiedzialnego za przetwarzanie tekstu w ciągach zapytań i indeksowanych dokumentach. Różne analizatory manipulują tekstem na różne sposoby w zależności od scenariusza. W tym scenariuszu musimy skompilować Analizator dostosowany do numerów telefonów.

Analizatory składają się z trzech składników:

+ [**Filtry znaków**](#CharFilters) , które usuwają lub zastępują poszczególne znaki z tekstu wejściowego.
+ [**Tokenizatora**](#Tokenizers) , który dzieli tekst wejściowy na tokeny, które staną się kluczami w indeksie wyszukiwania.
+ [**Filtry tokenów**](#TokenFilters) , które manipulują tokenami utworzonymi przez tokenizatora.

Na poniższym diagramie można zobaczyć, jak te trzy składniki współpracują ze sobą, aby tokenize zdanie:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Diagram procesu analizatora służący do tokenize zdania":::

Tokeny te są następnie przechowywane w odwróconym indeksie, co umożliwia szybkie wyszukiwanie pełnotekstowe.  Odwrócony indeks umożliwia wyszukiwanie pełnotekstowe przez mapowanie wszystkich unikatowych terminów wyodrębnionych podczas analizy leksykalnej do dokumentów, w których występują. Na poniższym diagramie można zobaczyć przykład:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Przykładowy indeks odwrócony":::

Wszystkie wyszukiwania przechodzą do wyszukiwania warunków przechowywanych w odwróconym indeksie. Gdy użytkownik wystawia zapytanie:

1. Zapytanie jest analizowane i są analizowane terminy zapytania.
1. Odwrócony indeks jest następnie skanowany pod kątem dokumentów z pasującymi terminami.
1. Na koniec pobrane dokumenty są klasyfikowane według [algorytmu podobieństwa](index-ranking-similarity.md).

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Diagram podobieństwa klasyfikacji procesów analizatora":::

Jeśli terminy zapytania nie pasują do warunków w indeksie odwróconym, wyniki nie zostaną zwrócone. Aby dowiedzieć się więcej o działaniu zapytań, zapoznaj się z artykułem dotyczącym [wyszukiwania pełnotekstowego](search-lucene-query-architecture.md).

> [!Note]
> [Częściowe zapytania warunkowe](search-query-partial-matching.md) są ważnym wyjątkiem od tej reguły. Te zapytania (zapytanie o prefiks, zapytanie symboli wieloznacznych, zapytanie regularne) pomijają proces analizy leksykalnej w przeciwieństwie do zwykłych zapytań warunkowych. Częściowe warunki są tylko małe przed dopasowaniem do warunków w indeksie. Jeśli analizator nie jest skonfigurowany do obsługi tych typów zapytań, często otrzymujesz nieoczekiwane wyniki, ponieważ pasujące terminy nie istnieją w indeksie.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Test Analyzer przy użyciu interfejsu API analizy tekstu

Usługa Azure Wyszukiwanie poznawcze udostępnia [interfejs API analizy tekstu](/rest/api/searchservice/test-analyzer) , który umożliwia testowanie analizatorów w celu zrozumienia sposobu przetwarzania tekstu.

Interfejs API analizy tekstu jest wywoływany przy użyciu następującego żądania:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

Interfejs API następnie zwraca listę tokenów wyodrębnionych z tekstu. Można sprawdzić, czy standardowy Analizator Luceny dzieli numer telefonu na trzy osobne tokeny:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Z kolei numer telefonu `4255550100` sformatowany bez żadnych znaków interpunkcyjnych jest podzielony na jeden token.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Należy pamiętać, że analizowane są zarówno terminy zapytania, jak i indeksowane dokumenty. Zapoznaj się z wynikami wyszukiwania w poprzednim kroku, możemy zacząć zobaczyć, dlaczego te wyniki zostały zwrócone.

W pierwszej kwerendzie nieprawidłowe numery telefonów zostały zwrócone, ponieważ jeden z ich warunków pasuje do `555` jednego z poszukiwanych warunków. W drugim zapytaniu tylko ten numer został zwrócony, ponieważ był to jedyny rekord, który miał pasujący termin `4255550100` .

## <a name="5---build-a-custom-analyzer"></a>5 — Kompilowanie analizatora niestandardowego

Teraz wiemy, jak oglądamy wyniki, utworzymy Analizator niestandardowy w celu usprawnienia logiki tokenizacji.

Celem jest zapewnienie intuicyjnego wyszukiwania na podstawie numerów telefonów bez względu na format zapytania lub ciągu indeksowanego. Aby osiągnąć ten wynik, określimy [Filtr znaków](#CharFilters), [tokenizatora](#Tokenizers)i [Filtr tokenu](#TokenFilters).

<a name="CharFilters"></a>

### <a name="character-filters"></a>Filtry znaków

Filtry znaków są używane do przetwarzania tekstu przed wprowadzeniem ich do tokenizatora. Typowe zastosowania filtrów znaków obejmują Filtrowanie elementów HTML lub zastępowanie znaków specjalnych.

W przypadku numerów telefonów chcemy usunąć odstępy i znaki specjalne, ponieważ nie wszystkie formaty numeru telefonu zawierają te same znaki specjalne i spacje.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

Powyższy filtr usunie `-` `(` `)` `+` `.` i spacje z danych wejściowych.

|Dane wejściowe|Dane wyjściowe|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizatory

Tokenizatory dzielenie tekstu na tokeny i odrzucanie niektórych znaków, takich jak interpunkcja, w sposób. W wielu przypadkach celem tokenizacji jest dzielenie zdania na poszczególne słowa.

W tym scenariuszu użyjemy słowa kluczowego tokenizatora, `keyword_v2` ponieważ chcemy przechwycić numer telefonu jako pojedynczy termin. Należy zauważyć, że nie jest to jedyny sposób, aby rozwiązać ten problem. Zapoznaj się z sekcją [alternatywne podejścia](#Alternate) poniżej.

Słowo kluczowe tokenizatory zawsze wyprowadza ten sam tekst, który został podany jako pojedynczy termin.

|Dane wejściowe|Dane wyjściowe|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Filtry tokenów

Filtry tokenów będą odfiltrować lub modyfikować tokeny wygenerowane przez tokenizatora. Typowym zastosowaniem filtru tokenu jest użycie małych liter we wszystkich znakach przy użyciu filtru tokenów małych liter. Innym typowym zastosowaniem jest filtrowanie Stop-słowa, takich jak `the` , `and` , lub `is` .

Chociaż nie musimy używać żadnego z tych filtrów w tym scenariuszu, użyjemy filtru tokenów nGram, aby umożliwić częściowe wyszukiwanie numerów telefonów.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[Filtr token nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) dzieli tokeny na n-gramy o danym rozmiarze na podstawie `minGram` parametrów i `maxGram` .

Dla analizatora telefonu ustawiamy `minGram` jako `3` najkrótszy podciąg, który oczekujemy, że użytkownicy będą wyszukiwać. `maxGram` jest ustawiona na tak `20` , aby upewnić się, że wszystkie numery telefonów, nawet z rozszerzeniami, zostaną dopasowane do jednego n-gramu.

 Efektem ubocznym w przypadku n-gramów jest to, że niektóre fałszywe dodatnie zostaną zwrócone. Naprawimy to w kroku 7, tworząc oddzielny analizator wyszukiwania, który nie zawiera filtru tokenów n-gramowych.

|Dane wejściowe|Dane wyjściowe|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analizator

Dzięki naszym filtrom znaków, tokenizatora i filtrom tokenów jesteśmy gotowi do definiowania naszego analizatora.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Dane wejściowe|Dane wyjściowe|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Zauważ, że wszystkie tokeny w danych wyjściowych mogą teraz być przeszukiwane. Jeśli zapytanie zawiera dowolny z tych tokenów, zostanie zwrócony numer telefonu.

Po zdefiniowaniu niestandardowego analizatora Utwórz ponownie indeks, aby Analizator niestandardowy był dostępny do testowania w następnym kroku. Dla uproszczenia kolekcja Poster tworzy nowy indeks o nazwie `tutorial-first-analyzer` z analizatorem zdefiniowanym przez siebie.

## <a name="6---test-the-custom-analyzer"></a>6 — Testowanie analizatora niestandardowego

Po utworzeniu indeksu możesz teraz przetestować Analizator utworzony przy użyciu następującego żądania:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Następnie będzie można zobaczyć kolekcję tokenów uzyskanych na podstawie numeru telefonu:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 — Tworzenie analizatora niestandardowego dla zapytań

Po wprowadzeniu przykładowych zapytań względem indeksu przy użyciu analizatora niestandardowego zobaczysz, że odwołanie zostało ulepszone i wszystkie zgodne numery telefonów są teraz zwracane. Jednak filtr tokenu n-gramowego powoduje, że zwracane są tylko fałszywe dodatnie. Jest to typowy efekt uboczny filtru tokenów n-gramowych.

Aby uniknąć fałszywych wartości dodatnich, utworzymy osobny Analizator do wykonywania zapytań. Ten Analizator będzie taki sam jak utworzony przez nas Analizator, ale **bez** `custom_ngram_filter` .

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

W definicji indeksu należy określić zarówno `indexAnalyzer` , jak i `searchAnalyzer` .

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Dzięki tej zmianie wszystko jest gotowe. Utwórz ponownie indeks, Indeksuj dane i przetestuj zapytania, aby sprawdzić, czy wyszukiwanie działa zgodnie z oczekiwaniami. Jeśli używasz kolekcji programu Poster, zostanie utworzony trzeci indeks o nazwie `tutorial-second-analyzer` .

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Alternatywne podejścia

Powyższy analizator został zaprojektowany, aby zmaksymalizować elastyczność wyszukiwania. Jest to jednak koszt przechowywania wielu potencjalnie nieważnych warunków w indeksie.

W poniższym przykładzie pokazano inny Analizator, który może być również używany dla tego zadania. 

Analizator działa dobrze, chyba że dane wejściowe, takie jak `14255550100` , utrudniają logiczne rozdzielenie numeru telefonu. Na przykład Analizator nie może oddzielić kodu kraju, `1` od kodu obszaru `425` . Ta niezgodność spowodowałaby zwrócenie do powyższej liczby, jeśli użytkownik nie dołączył kodu kraju do wyszukiwania.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

W poniższym przykładzie można zobaczyć, że numer telefonu jest podzielony na fragmenty, które zwykle oczekują wyszukania użytkownika.

|Dane wejściowe|Dane wyjściowe|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

W zależności od wymagań może to być wydajniejsze rozwiązanie problemu.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

Dla uproszczenia w tym samouczku przedstawiono tworzenie trzech nowych indeksów. Jednak często można usunąć i utworzyć ponownie indeksy podczas wczesnych etapów tworzenia. Można usunąć indeks w Azure Portal lub użyć następującego wywołania interfejsu API:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono proces tworzenia i testowania analizatora niestandardowego. Utworzono indeks, indeksowane dane, a następnie zapytanie względem indeksu, aby zobaczyć, jakie wyniki wyszukiwania zostały zwrócone. W tym miejscu użyto interfejsu API analizy tekstu, aby zobaczyć, jak działa proces analizy leksykalnej.

Analizator zdefiniowany w tym samouczku oferuje łatwe rozwiązanie do wyszukiwania numerów telefonów, tym samym procesem można użyć do utworzenia niestandardowego analizatora telefonu dla dowolnego scenariusza, który może mieć.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy pracujesz nad własną subskrypcją, dobrym pomysłem jest usunięcie zasobów, które nie są już potrzebne na końcu projektu. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku wszystkie zasoby lub grupy zasobów w okienku nawigacji po lewej stronie.

## <a name="next-steps"></a>Następne kroki

Teraz, po zapoznaniu się ze sposobem tworzenia analizatora niestandardowego, przyjrzyjmy się wszystkim różnym filtrom, tokenizatory i analizatorom, które umożliwiają tworzenie zaawansowanych funkcji wyszukiwania.

> [!div class="nextstepaction"]
> [Analizatory niestandardowe na platformie Azure Wyszukiwanie poznawcze](index-add-custom-analyzers.md)