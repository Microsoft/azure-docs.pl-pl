---
title: Dodawanie analizatorów języka do pól ciągów
titleSuffix: Azure Cognitive Search
description: Wielojęzykowa analiza leksykalna dla zapytań innych niż angielskie i indeksów w usłudze Azure Wyszukiwanie poznawcze.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ac11b7bc7e53c214f872d400565d50009479afcb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604427"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Dodawanie analizatorów języka do pól ciągów w indeksie Wyszukiwanie poznawcze platformy Azure

*Analizator języka* jest określonym typem [analizatora tekstu](search-analyzers.md) , który wykonuje analizę leksykalną przy użyciu reguł lingwistycznych języka docelowego. Każde pole z możliwością wyszukiwania ma właściwość **analizatora** . Jeśli zawartość składa się z przetłumaczonych ciągów, takich jak oddzielne pola w języku angielskim i chińskim, można określić analizatory języka dla każdego pola, aby uzyskać dostęp do zaawansowanych funkcji językowych tych analizatorów.

## <a name="when-to-use-a-language-analyzer"></a>Kiedy używać analizatora języka

Należy wziąć pod uwagę Analizator języka, gdy świadomość wyrazu lub struktury zdań dodaje wartość do analizy tekstu. Typowym przykładem jest skojarzenie nietypowych formularzy zleceń ("Przesuń" i "dobieranie") lub w liczbie mnogiej ("myszy" i "mysz"). Bez świadomości językowej te ciągi są analizowane wyłącznie w zależności od cech fizycznych, co nie może przechwycić połączenia. Ze względu na to, że duże fragmenty tekstu mogą mieć tę zawartość, pola zawierające opisy, przeglądy lub podsumowania są dobrym kandydatami dla analizatora języka.

Należy również wziąć pod uwagę analizatory języka, gdy zawartość składa się z ciągów języka innego niż zachodni. Chociaż [domyślnym analizatorem](search-analyzers.md#default-analyzer) jest język niezależny od, pojęcie używania spacji i znaków specjalnych (łączniki i ukośniki) do oddzielania ciągów jest bardziej odpowiednie dla języków zachodnich niż Zachodnie. 

Na przykład w języku chińskim, japońskim, koreańskim (CJK) i innych językach azjatyckich spacja nie musi być ogranicznikiem słowa. Rozważmy następujący ciąg japoński. Ponieważ nie zawiera spacji, Analizator języka niezależny od prawdopodobnie przeanalizuje cały ciąg jako jeden token, w rzeczywistości ciąg jest w rzeczywistości frazą.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

Dla powyższego przykładu pomyślne zapytanie musi zawierać pełny token lub częściowo token przy użyciu symbolu wieloznacznego sufiksu, co powoduje nienaturalne i ograniczanie wyszukiwania.

Lepszym rozwiązaniem jest wyszukiwanie pojedynczych słów: 明るい (jasno), 私たちの (nasz), 銀河系 (Galaxy). Korzystanie z jednego z analizatorów japońskich dostępnych w Wyszukiwanie poznawcze jest bardziej podobne do odblokowania tego zachowania, ponieważ te analizatory są lepiej wyposażone podczas dzielenia fragmentu tekstu na znaczące słowa w języku docelowym.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Porównywanie Lucene i analizatory firmy Microsoft

Usługa Azure Wyszukiwanie poznawcze obsługuje analizatory języka 35 obsługiwane przez Lucene i analizatory języka 50, których kopia zapasowa została zastosowana przez zastrzeżoną technologię przetwarzania języka naturalnego firmy Microsoft w pakiecie Office i Bing.

Niektórzy deweloperzy mogą preferować bardziej znane, proste i łatwe w użyciu rozwiązanie typu open source. Analizatory języka Lucene są szybsze, ale analizatory firmy Microsoft mają zaawansowane możliwości, takie jak Lematyzacja, rozliczanie wyrazów (w językach takich jak niemiecki, duński, holenderski, szwedzki, norweski, estoński, Kończenie, węgierski, słowacki) i rozpoznawanie jednostek (adresy URL, wiadomości e-mail, daty i cyfry). Jeśli to możliwe, należy uruchomić porównania obu analizatorów firmy Microsoft i Lucene, aby zdecydować, która z nich jest lepsza. 

Indeksowanie za pomocą analizatorów firmy Microsoft jest średnio dwa do trzech razy wolniejsze niż ich odpowiedniki w Lucene, w zależności od języka. W przypadku zapytań o średnim rozmiarze nie powinno się znacznie wpłynąć na wydajność wyszukiwania. 

### <a name="english-analyzers"></a>Analizatory angielskie

Domyślną analizatorem jest standardowa Lucene, które dobrze sprawdzają się w języku angielskim, ale być może nie jest to również program w języku angielskim lub Microsoft English Analyzer.

+ Analizator w języku angielskim Luces rozszerza analizatora standardowego. Usuwa possessives (końcowe) z wyrazów, stosuje rdzenie zgodnie z algorytmem rdzenia Porter i usuwa słowa zatrzymywania w języku angielskim.  

+ Program Microsoft English Analyzer wykonuje Lematyzacja zamiast rdzeni. Oznacza to, że może obsłużyć nietypowe i nieregularne formy wyrazów znacznie lepiej, co daje bardziej odpowiednie wyniki wyszukiwania 

## <a name="how-to-specify-a-language-analyzer"></a>Jak określić Analizator języka

Ustaw Analizator języka w polach "z możliwością wyszukiwania" typu EDM. String podczas definiowania pola.

Chociaż definicje pól mają kilka właściwości powiązanych z analizatorem, tylko właściwość "Analizator" może być używana dla analizatorów języka. Wartość "Analyzer" musi być jedną z analizatorów języka z listy analizatorów pomocy technicznej.

```json
{
  "name": "hotels-sample-index",
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
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

Aby uzyskać więcej informacji na temat tworzenia indeksu i ustawiania właściwości pola, zobacz [create index (REST)](/rest/api/searchservice/create-index). Aby uzyskać więcej informacji na temat analizy tekstu, zobacz [analizatory na platformie Azure wyszukiwanie poznawcze](search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>Obsługiwane analizatory języka

 Poniżej znajduje się lista obsługiwanych języków z nazwami Lucene i Microsoft Analyzer.  

| Język | Nazwa analizatora firmy Microsoft | Nazwa analizatora Lucene |
|----------|-------------------------|----------------------|
| Arabski   | ar. Microsoft | ar. Lucene |
| Armeński |           | HY. Lucene |
| Języku   | mld USD. Microsoft |  |
| Baskijski   |  | UE. Lucene |
| Bułgarski | BG. Microsoft | BG. Lucene |
| Kataloński  | CA. Microsoft | CA. Lucene |
| Chiński (uproszczony) | zh-Hans. Microsoft | zh-Hans. Lucene |
| Chiński (tradycyjny) | zh-Hant. Microsoft | zh-Hant. Lucene |
| Chorwacki | HR. Microsoft |  |
| Czeski | cs. Microsoft | cs. Lucene |
| Duński | da. Microsoft | da. Lucene |
| Niderlandzki | NL. Microsoft | NL. Lucene |
| Angielski | pl. Microsoft | pl. Lucene |
| Estoński | et. Microsoft |  |
| Fiński | Fi. Microsoft | Fi. Lucene |
| Francuski | fr. Microsoft | fr. Lucene |
| Galicyjski |  | GL. Lucene |
| Niemiecki | de. Microsoft | de. Lucene |
| Grecki | El. Microsoft | El. Lucene |
| Gudżarati | gu. Microsoft |  |
| Hebrajski | Firma Microsoft |  |
| Hindi | Witaj. Microsoft | Witaj. Lucene |
| Węgierski | HU. Microsoft | HU. Lucene |
| Islandzki | jest. Microsoft |  |
| Indonezyjski (Bahasa) | Identyfikator. Microsoft | Identyfikator. Lucene |
| Irlandzki |  | ga. Lucene |
| Włoski | IT. Microsoft | IT. Lucene |
| japoński | ja. Microsoft | ja. Lucene |
| Kannada | kN. Microsoft |  |
| Koreański | ko. Microsoft | ko. Lucene |
| Łotewski | LV. Microsoft | LV. Lucene |
| Litewski | lt. Microsoft |  |
| Malayalam | ml. Microsoft |  |
| Malajski (łaciński) | MS. Microsoft |  |
| Marathi | Mr. Microsoft |  |
| Norweski | NB. Microsoft | nie. Lucene |
| Perski |  | FA. Lucene |
| Polski | pl. Microsoft | pl. Lucene |
| Portugalski (Brazylia) | pt-br. Microsoft | pt-br. Lucene |
| Portugalski (Portugalia) | pt-pt. Microsoft | pt-pt. Lucene |
| Pendżabski | PA. Microsoft |  |
| Rumuński | ro. Microsoft | ro. Lucene |
| Rosyjski | ru. Microsoft | ru. Lucene |
| Serbski (cyrylica) | SR-cyrylica. Microsoft |  |
| Serbski (łaciński) | SR-Latin. Microsoft |  |
| Słowacki | SK. Microsoft |  |
| Słoweński | SL. Microsoft |  |
| Hiszpański | es. Microsoft | es. Lucene |
| Szwedzki | OHR. Microsoft | OHR. Lucene |
| Tamilski | Ta. Microsoft |  |
| Telugu | te. Microsoft |  |
| Tajlandzki | th. Microsoft | th. Lucene |
| Turecki | TR. Microsoft | TR. Lucene |
| Ukraiński | Wielka Brytania. Microsoft |  |
| Urdu | Twoje. Microsoft |  |
| Wietnamski | VI. Microsoft |  |

 Wszystkie analizatory z nazwami oznaczonymi jako " **Lucene** " są obsługiwane przez [analizatory języka Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Zobacz też  

+ [Tworzenie indeksu](search-what-is-an-index.md)
+ [Tworzenie indeksu w wielu językach](search-language-support.md)
+ [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index)  
+ [Klasa LexicalAnalyzerName (zestaw Azure SDK dla platformy .NET)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)