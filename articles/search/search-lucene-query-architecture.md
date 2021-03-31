---
title: Pełna kwerenda tekstowa i architektura aparatu indeksowania (Lucene)
titleSuffix: Azure Cognitive Search
description: Bada przetwarzanie zapytań Lucene i koncepcje pobierania dokumentów dla wyszukiwania pełnotekstowego w odniesieniu do usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002752"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Wyszukiwanie pełnotekstowe w usłudze Azure Wyszukiwanie poznawcze

Ten artykuł jest przeznaczony dla deweloperów, którzy potrzebują bardziej szczegółowego wglądu w sposób działania wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze. W przypadku zapytań tekstowych usługa Azure Cognitive Search zwraca oczekiwane wyniki w większości scenariuszy, okazjonalnie jednak możesz otrzymywać wyniki, które wydają się w jakiś sposób „nieprawidłowe”. W takiej sytuacji wiedza na temat czterech etapów wykonywania zapytań Lucene (analizowanie zapytania, analiza leksykalna, dopasowywanie dokumentów i ocenianie) może pomóc w określeniu, jakie zmiany parametrów zapytania lub konfiguracji indeksu umożliwią uzyskanie oczekiwanych wyników. 

> [!Note] 
> Usługa Azure Wyszukiwanie poznawcze używa Lucene do wyszukiwania pełnotekstowego, ale integracja z usługą Lucene nie jest wyczerpująca. Selektywnie uwidaczniamy i rozszerzył funkcję Lucene, aby umożliwić scenariuszom ważne dla platformy Azure Wyszukiwanie poznawcze. 

## <a name="architecture-overview-and-diagram"></a>Przegląd i diagram architektury

Przetwarzanie zapytania wyszukiwania pełnotekstowego rozpoczyna się od analizy tekstu zapytania w celu wyodrębnienia wyszukiwanych terminów. Aparat wyszukiwania używa indeksu do pobierania dokumentów ze zgodnymi warunkami. Poszczególne terminy zapytań są czasami podzielone i ponownie tworzone w nowych formularzach w celu rzutowania szerszej sieci o to, co można uznać za potencjalne dopasowanie. Zestaw wyników jest następnie sortowany według wyniku oceny przypisanego do każdego indywidualnego pasującego dokumentu. Te w górnej części listy rangi są zwracane do aplikacji wywołującej.

Przeprowadzenie wykonywania zapytania ma cztery etapy: 

1. Analizowanie zapytań 
2. Analiza leksykalna 
3. Pobieranie dokumentów 
4. Scoring (Ocenianie) 

Na poniższym diagramie przedstawiono składniki używane do przetwarzania żądania wyszukiwania. 

 ![Diagram architektury zapytania Lucene na platformie Azure Wyszukiwanie poznawcze][1]


| Główne składniki | Opis funkcjonalny | 
|----------------|------------------------|
|**Analizatory zapytań** | Oddziel terminy zapytania od operatorów zapytań i Utwórz strukturę zapytania (drzewo zapytań) do wysłania do aparatu wyszukiwania. |
|**Analizatory** | Wykonaj analizę leksykalną na temat terminów zapytań. Ten proces może polegać na transformacje, usunięciu lub rozwinięciu warunków zapytania. |
|**Indeks** | Wydajna struktura danych służąca do przechowywania i organizowania warunków wyszukiwania wyodrębnionych z zindeksowanych dokumentów. |
|**Aparat wyszukiwania** | Pobiera i ocenia pasujące dokumenty na podstawie zawartości odwróconego indeksu. |

## <a name="anatomy-of-a-search-request"></a>Anatomia żądania wyszukiwania

Żądanie Search to kompletna Specyfikacja, która powinna zostać zwrócona w zestawie wyników. W najprostszym formularzu jest to puste zapytanie, które nie ma żadnych kryteriów żadnego rodzaju. Bardziej realistyczny przykład obejmuje parametry, kilka terminów zapytania, które mogą być ograniczone do określonych pól, z ewentualnym wyrażeniem filtru i kolejnością reguł.  

Poniższy przykład to żądanie wyszukiwania, które może zostać wysłane do usługi Azure Wyszukiwanie poznawcze przy użyciu [interfejsu API REST](/rest/api/searchservice/search-documents).  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

W przypadku tego żądania aparat wyszukiwania wykonuje następujące czynności:

1. Filtruje dokumenty, w których cena jest równa co najmniej $60 i mniejszej niż $300.
2. Wykonuje zapytanie. W tym przykładzie zapytanie wyszukiwania składa się z fraz i terminów: `"Spacious, air-condition* +\"Ocean view\""` (zwykle nie są one wprowadzane do interpunkcji, ale z uwzględnieniem ich w przykładzie pozwala nam wyjaśnić, jak obsługiwane są analizatory). W przypadku tego zapytania aparat wyszukiwania skanuje pola Description i title określone w `searchFields` dla dokumentów, które zawierają "Ocean View", a także w przypadku terminu "spacious" lub warunków, które zaczynają się od prefiksu "warunki powietrza". `searchMode`Parametr jest używany do dopasowania w dowolnym okresie (wartość domyślna) lub wszystkie z nich, w przypadkach, gdy termin nie jest jawnie wymagany ( `+` ).
3. Porządkuje wynikowy zestaw hoteli przez bliskość do danej lokalizacji geograficznej, a następnie zwraca do aplikacji wywołującej. 

Większość tego artykułu dotyczy przetwarzania *zapytania wyszukiwania*: `"Spacious, air-condition* +\"Ocean view\""` . Filtrowanie i porządkowanie są poza zakresem. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą interfejsu API wyszukiwania](/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Etap 1. analizowanie zapytań 

Jak wspomniano, ciąg zapytania jest pierwszym wierszem żądania: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

Analizator zapytań oddziela operatory (takie jak `*` i `+` w przykładzie) z wyszukiwanych terminów i dekonstruuje zapytanie wyszukiwania w *podzapytaniach* obsługiwanego typu: 

+ *zapytanie warunkowe* dla warunków autonomicznych (takich jak Spacious)
+ *zapytanie zwrotne* dla cytowanych terminów (na przykład widoku Ocean)
+ *zapytanie dotyczące prefiksu* dla warunków, po których następuje operator prefiksu `*` (np. warunek powietrza)

Aby zapoznać się z pełną listą obsługiwanych typów zapytań, zobacz [składnia zapytań Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operatory skojarzone z podzapytaniem określają, czy zapytanie "musi być" lub "powinno być" spełnione, aby dokument mógł zostać uznany za zgodny. Na przykład, `+"Ocean view"` jest "musi" ze względu na `+` operatora. 

Analizator zapytań przekształca podzapytania w *drzewo zapytań* (wewnętrzną strukturę reprezentującą zapytanie), które przekazuje do aparatu wyszukiwania. W pierwszym etapie analizy zapytania drzewo zapytania wygląda następująco.  

 ![Dowolna wartość logiczna wyszukiwania zapytań][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Obsługiwane analizatory: proste i pełne Lucene 

 Na platformie Azure Wyszukiwanie poznawcze są uwidaczniane dwa różne języki zapytań `simple` (domyślne) i `full` . Ustawiając `queryType` parametr z żądaniem wyszukiwania, możesz poinformować analizatora zapytań o wybranym języku zapytania, aby wie, jak interpretować operatory i składnię. [Prosty język zapytań](/rest/api/searchservice/simple-query-syntax-in-azure-search) jest intuicyjny i niezawodny, często odpowiedni do interpretowania danych wprowadzanych przez użytkownika bez przetwarzania po stronie klienta. Obsługuje operatory zapytań znane z wyszukiwarek sieci Web. [Pełny język zapytań Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search), który można uzyskać przez ustawienie `queryType=full` , rozszerza domyślny prosty język zapytań, dodając obsługę większej liczby operatorów i typów zapytań, takich jak symbole wieloznaczne, rozmyte, wyrażenia regularnego i zapytania o zakres pól. Na przykład wyrażenie regularne wysyłane w prostej składni zapytania jest interpretowane jako ciąg zapytania, a nie wyrażenie. Przykładowe żądanie w tym artykule używa pełnego języka zapytań Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Wpływ przeszukiwania na analizator składni 

Innym parametrem żądania wyszukiwania, który ma wpływ na analizowanie, jest `searchMode` parametr. Steruje operatorem domyślnym dla zapytań logicznych: dowolne (domyślne) lub wszystkie.  

Gdy `searchMode=any` jest to domyślny, ogranicznik odstępu między Spacious i warunkiem obpowietrzanym jest lub ( `||` ), co oznacza, że przykładowy tekst zapytania odpowiada: 

```
Spacious,||air-condition*+"Ocean view" 
```

Jawne operatory, takie jak `+` w `+"Ocean view"` , są niejednoznaczne w konstrukcji zapytania logicznego (termin *musi* być zgodny). Mniej oczywiste to sposób interpretacji pozostałych warunków: Spacious i stanu powietrza. Czy aparat wyszukiwania powinien znaleźć dopasowania w widoku oceanu *i* Spacious *i* warunkach powietrza? Lub powinien znaleźć widok oceanu i *jedno* z pozostałych warunków? 

Domyślnie ( `searchMode=any` ) aparat wyszukiwania przyjmuje szersze interpretacje. Każde pole *powinno* być dopasowane, odzwierciedlające semantykę "lub". Początkowe drzewo zapytań przedstawione wcześniej, z dwoma operacjami "powinien", pokazuje wartość domyślną.  

Załóżmy, że teraz ustawimy `searchMode=all` . W takim przypadku miejsce jest interpretowane jako operacja "i". Każdy z pozostałych warunków musi być obecny w dokumencie, aby można go było zakwalifikować jako odpowiednik. Wyniki przykładowego zapytania byłyby interpretowane w następujący sposób: 

```
+Spacious,+air-condition*+"Ocean view"
```

Zmodyfikowane drzewo zapytań dla tego zapytania byłoby następujące, gdzie pasujący dokument jest częścią wspólną wszystkich trzech podzapytań: 

 ![Wartość Boolean przeszukiwania zapytań logicznych][3]

> [!Note] 
> Wybór `searchMode=any` powyżej `searchMode=all` stanowi decyzję, która najlepiej dotarła do uruchamiania reprezentatywnych zapytań. Użytkownicy, którzy mogą dołączać operatory (typowi podczas wyszukiwania w sklepie dokumentów), mogą znaleźć wyniki bardziej intuicyjne, jeśli są `searchMode=all` informować konstrukcje zapytania logicznego. Aby uzyskać więcej informacji na temat współpraca między `searchMode` operatorami i, zobacz [prostą składnię zapytania](/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Etap 2: Analiza leksykalna 

Analizatory leksykalne przetwarzają *zapytania warunkowe* i *zapytania zwrotne* po strukturze drzewa zapytań. Analizator akceptuje dane wejściowe dostarczone przez analizator przez parser, przetwarza tekst, a następnie wysyła zwrotne terminy do włączenia do drzewa zapytań. 

Najbardziej powszechną formą analizy leksykalnej jest *Analiza językowa* , która przekształca terminy zapytania na podstawie reguł specyficznych dla danego języka: 

* Zmniejszanie terminu zapytania do głównego formularza słowa 
* Usuwanie nieistotnych wyrazów (Stop-słowa, takich jak "" lub "i" w języku angielskim) 
* Dzielenie złożonego słowa na części składnika 
* Małe litery wielką literą 

Wszystkie te operacje mają na celu wymazanie różnic między danymi wejściowymi wprowadzonymi przez użytkownika i postanowieniami przechowywanymi w indeksie. Takie operacje wykraczają poza przetwarzanie tekstu i wymagają dokładnej znajomości samego języka. Aby dodać tę warstwę świadomości lingwistycznej, platforma Azure Wyszukiwanie poznawcze obsługuje długą listę [analizatorów języka](/rest/api/searchservice/language-support) zarówno z luce, jak i od firmy Microsoft.

> [!Note]
> Wymagania dotyczące analizy mogą być od minimum do rozwinięcia w zależności od danego scenariusza. Złożoność analizy leksykalnej można kontrolować przez wybranie jednego ze wstępnie zdefiniowanych analizatorów lub utworzenie własnego [niestandardowego analizatorze](/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analizatory są objęte zakresem pól, które można przeszukiwać i są określone jako część definicji pola. Pozwala to na zmianę analizy leksykalnej na podstawie poszczególnych pól. Nieokreślony, używany jest *Standardowy* Analizator Lucene.

W naszym przykładzie przed analizą początkowe drzewo zapytań ma termin "spacious" z wielką literą "S" i przecinkiem, który interpretuje Analizator zapytań jako część terminu zapytania (przecinek nie jest uważany za operator języka zapytań).  

Gdy domyślny Analizator przetwarza termin, będzie pisany małymi literami "Ocean View" i "spacious" i usunąć znak przecinka. Zmodyfikowane drzewo zapytań będzie wyglądać następująco: 

 ![Zapytanie logiczne z przeanalizowanymi warunkami][4]

### <a name="testing-analyzer-behaviors"></a>Zachowania analizatora testowego 

Zachowanie analizatora można przetestować za pomocą [interfejsu API analizy](/rest/api/searchservice/test-analyzer). Podaj tekst, który chcesz analizować, aby zobaczyć, jakie terminy zostaną wygenerowane przez analizator. Na przykład aby zobaczyć, jak Analizator standardowy przetworzy tekst "warunek powietrza", można wydać następujące żądanie:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

Analizator standardowy dzieli tekst wejściowy na następujące dwa tokeny, dodając do nich adnotacje, takie jak początkowe i końcowe przesunięcia (używane do wyróżniania trafień), a także ich położenie (używane do dopasowywania fraz):

```json
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Wyjątki dla analizy leksykalnej 

Analiza leksykalna dotyczy tylko typów zapytań, które wymagają pełnych warunków — zapytania warunkowego lub zapytania zwrotnego. Nie ma zastosowania do typów zapytań z niekompletnymi terminami — zapytania prefiksowego, zapytania wieloznacznego, zapytania wyrażenia regularnego lub zapytania rozmytego. Te typy zapytań, w tym zapytanie prefiksu z terminem `air-condition*` w naszym przykładzie, są dodawane bezpośrednio do drzewa zapytań, pomijając etap analizy. Jedynym przekształceniem wykonywanym na warunkach zapytań tych typów jest lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Etap 3: pobieranie dokumentów 

Pobieranie dokumentów odnosi się do znajdowania dokumentów z pasującymi terminami w indeksie. Ten etap jest zrozumiały dla przykładu. Zacznijmy od indeksu hoteli o następującym prostym schemacie: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Należy założyć, że ten indeks zawiera cztery następujące dokumenty: 

```json
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
```

**Jak są indeksowane terminy**

Aby zrozumieć pobieranie, pomocne jest zapoznanie się z kilkoma podstawowymi informacjami na temat indeksowania. Jednostka magazynowa jest odwróconym indeksem, po jednym dla każdego pola z możliwością wyszukiwania. W odwróconym indeksie jest posortowaną listą wszystkich terminów ze wszystkich dokumentów. Każdy termin jest mapowany na listę dokumentów, w których występuje, jak pokazano w poniższym przykładzie.

Aby utworzyć warunki w odwróconym indeksie, aparat wyszukiwania wykonuje analizę leksykalną nad zawartością dokumentów, podobnie jak w przypadku przetwarzania zapytań:

1. *Dane wejściowe tekstu* są przesyłane do analizatora, małe litery, usunięte z interpunkcji i tak dalej, w zależności od konfiguracji analizatora. 
2. *Tokeny* są danymi wyjściowymi analizy leksykalnej.
3. *Warunki* są dodawane do indeksu.

Jest to typowy, ale nie jest wymagany, aby używać tych samych analizatorów dla operacji wyszukiwania i indeksowania, tak aby terminy zapytania wyglądały tak, jak terminy w indeksie.

> [!Note]
> Usługa Azure Wyszukiwanie poznawcze pozwala określić różne analizatory do indeksowania i wyszukiwania za pomocą `indexAnalyzer` dodatkowych `searchAnalyzer` parametrów i pól. Jeśli nie zostanie określony, Analizator zostanie ustawiony z `analyzer` właściwością dla indeksowania i wyszukiwania.  

**Odwrócony indeks dla przykładowych dokumentów**

Powracamy do naszego przykładu dla pola **title** indeks odwrócony wygląda następująco:

| Okres | Lista dokumentów |
|------|---------------|
| atman | 1 |
| sekwencje | 2 |
| turystyczn | 1, 3 |
| utworzeniu | 4  |
| playa | 3 |
| Ostateczny | 3 |
| przetraktuj | 4 |

W polu title tylko *Hotel* pojawia się w dwóch dokumentach: 1, 3.

W przypadku pola **Description** indeks jest następujący:

| Okres | Lista dokumentów |
|------|---------------|
| dmuchaw | 3
| oraz | 4
| sekwencje | 1
| przygotować | 3
| potrafisz | 3
| odległość | 1
| Wyspa | 2
| kaua ʻ i | 2
| znajduje | 2
| north | 2
| utworzeniu | 1, 2, 3
| z | 2
| on |2
| cichy | 4
| natrysk  | 1, 3
| secluded | 4
| zejście | 2
| spacious | 1
| względem zasobu | 1, 2
| na wartość | 1
| widok | 1, 2, 3
| wykorzystaniem | 1
| with | 3


**Pasujące terminy zapytania względem indeksowanych terminów**

Mając powyższe odwrócone indeksy, wróćmy do przykładowego zapytania i zobaczysz, jak znaleziono pasujące dokumenty dla naszego przykładowego zapytania. Odwołaj się, że końcowe drzewo zapytań wygląda następująco: 

 ![Zapytanie logiczne z przeanalizowanymi warunkami][4]

Podczas wykonywania zapytania poszczególne zapytania są wykonywane niezależnie od pól, które można przeszukiwać. 

+ TermQuery, "spacious", pasuje do dokumentu 1 (Hotel ATMAN). 

+ PrefixQuery "powietrze-Condition *" nie pasuje do żadnych dokumentów. 

  Jest to zachowanie, które czasami odrzuca deweloperów. Mimo że warunkiem zastosowanym w powietrzu istnieje w dokumencie, jest on podzielony na dwa warunki przez analizatora domyślnego. Odwołaj te zapytania prefiksu, które zawierają częściowe warunki, nie są analizowane. W związku z tym terminy z prefiksem "warunki powietrza" są wyszukiwane w odwróconym indeksie i nie można go znaleźć.

+ PhraseQuery, "widok oceanu", wyszukuje terminy "Ocean" i "View" i sprawdza bliskość terminów w oryginalnym dokumencie. Dokumenty 1, 2 i 3 pasują do tego zapytania w polu Opis. Dokument Uwaga 4 ma termin Ocean w tytule, ale nie jest uważany za dopasowanie, ponieważ szukasz frazy "Ocean View", a nie poszczególnych wyrazów. 

> [!Note]
> Zapytanie wyszukiwania jest wykonywane niezależnie dla wszystkich pól z możliwością wyszukiwania w indeksie usługi Azure Wyszukiwanie poznawcze, chyba że zostaną ograniczone pola ustawione za pomocą `searchFields` parametru, jak pokazano w przykładowym żądaniu wyszukiwania. Zwracane są dokumenty pasujące do któregokolwiek z wybranych pól. 

W odniesieniu do zapytania, w przypadku którego jest to zgodne z danymi, mają one wartość 1, 2, 3. 

## <a name="stage-4-scoring"></a>Etap 4: ocenianie  

Każdy dokument w zestawie wyników wyszukiwania ma przypisany wynik istotności. Funkcja oceny przydatności polega na klasyfikowaniu wyższych dokumentów, które najlepiej odpowiadają na pytania użytkownika w postaci wydanej przez zapytanie wyszukiwania. Wynik jest obliczany na podstawie właściwości statystycznych pasujących do siebie warunków. Na początku formuły oceniania jest [TF/IDF (częstotliwość okresów — odwrotna częstotliwość dokumentu)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). W zapytaniach zawierających rzadki i często spotykane terminy TF/IDF promuje wyniki zawierające rzadkie terminy. Na przykład w indeksie hipotetycznym ze wszystkimi artykułami witryny Wikipedia z dokumentów, które pasują do *prezesa*, dokumenty pasujące do *prezesa* są uważane za bardziej odpowiednie niż dokumenty pasujące do *programu*.


### <a name="scoring-example"></a>Przykład oceniania

Odwołaj trzy dokumenty, które pasują do naszego przykładowego zapytania:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
```

Dokument 1 najlepiej pasuje do zapytania, ponieważ w polu Opis występuje zarówno termin *Spacious* , jak i wymagany *Widok oceanu* frazy. Dwa następne dokumenty pasują tylko do widoku "oznaczenie *oceanu*". Może być zaskakujące, że Ocena istotności dla dokumentów 2 i 3 różni się, nawet jeśli pasują do zapytania w ten sam sposób. Wynika to z faktu, że formuła oceniania zawiera więcej składników niż tylko TF/IDF. W takim przypadku dokument 3 został przyznany nieco większym wynikiem, ponieważ jego opis jest krótszy. Dowiedz się więcej na temat [praktycznej formuły oceniania](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) , aby zrozumieć, jak długość pola i inne czynniki mogą mieć wpływ na ocenę istotności.

Niektóre typy zapytań (symbol wieloznaczny, prefiks, wyrażenie regularne) zawsze tworzą stały wynik do ogólnego wyniku dokumentu. Pozwala to na dopasowania znalezione za pomocą rozszerzania zapytania, które mają być zawarte w wynikach, ale bez wpływania na klasyfikację. 

Przykład ilustruje to zagadnienia. Wyszukiwanie przy użyciu symboli wieloznacznych, w tym wyszukiwania prefiksów, jest niejednoznaczne według definicji, ponieważ dane wejściowe są częściowym ciągiem z potencjalnymi dopasowaniami do bardzo dużej liczby różnych postanowień (należy wziąć pod uwagę dane wejściowe "przewodnika *" z dopasowań znalezionych w "przewodnikach", "Tourettes" i "Tourmaline"). Mając na względzie charakter tych wyników, nie ma możliwości racjonalnego wyznaczenia, które warunki są bardziej cenne niż inne. Z tego powodu ignoruje okresowe częstotliwości, gdy ocenia wyniki w kwerendach typów symboli wieloznacznych, prefiksu i wyrażenia regularnego. W wieloczęściowym żądaniu wyszukiwania, które zawiera częściowe i kompletne warunki, wyniki z częściowych danych wejściowych są dołączane do stałej oceny, aby uniknąć odchylenia na potencjalnie nieoczekiwanych dopasowań.

### <a name="score-tuning"></a>Dostrajanie wyniku

Istnieją dwa sposoby dostrajania ocen przydatności na platformie Azure Wyszukiwanie poznawcze:

1. **Profile oceniania** promują dokumenty na uporządkowanej liście wyników na podstawie zestawu reguł. W naszym przykładzie możemy rozważyć, że dokumenty dopasowane w polu title są bardziej odpowiednie niż dokumenty pasujące do pola Description. Ponadto, jeśli nasz indeks miał pole ceny dla każdego hotelu, możemy wspierać dokumenty o niższej cenie. Dowiedz się więcej, jak [dodać profile oceniania do indeksu wyszukiwania.](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Podwyższanie poziomu terminu** (dostępne tylko w pełnej składni zapytań Lucene) zawiera operator zwiększania poziomu `^` , który można zastosować do dowolnej części drzewa zapytań. W naszym przykładzie zamiast wyszukiwania na powyższym *stanie powietrza* \* , jeden może wyszukać dokładny *warunek powietrza* lub prefiks, ale dokumenty, które pasują do dokładnego terminu, są klasyfikowane przez zastosowanie podwyższania poziomu zapytania warunkowego: * warunek powietrza ^ 2 | | warunki powietrza * *. Dowiedz się więcej o [zwiększaniu warunków](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Ocenianie w rozproszonym indeksie

Wszystkie indeksy na platformie Azure Wyszukiwanie poznawcze są automatycznie dzielone na wiele fragmentów, dzięki czemu możemy szybko rozpowszechnić indeks między wieloma węzłami podczas skalowania usługi w górę lub w dół. Po wydaniu żądania Search zostanie ono wystawione osobno dla każdego fragmentuu. Wyniki z poszczególnych fragmentu są następnie scalane i uporządkowane według wyniku (jeśli nie zdefiniowano innej kolejności). Ważne jest, aby wiedzieć, że funkcja oceniania liczy okres zapytania względem częstotliwości odwracania dokumentu we wszystkich dokumentach w fragmentu, a nie całej fragmentów!

Oznacza to, że ocena przydatności *może* być różna dla identycznych dokumentów, jeśli znajdują się one w różnych fragmentów. Na szczęście takie różnice mają być niewidoczne, ponieważ liczba dokumentów w indeksie rośnie ze względu na bardziej równomierny rozkład. Nie można założyć, że fragmentu dany dokument zostanie umieszczony. Jednak przy założeniu, że klucz dokumentu nie ulegnie zmianie, będzie on zawsze przypisany do tego samego fragmentu.

Ogólnie rzecz biorąc, Ocena dokumentu nie jest najlepszym atrybutem do porządkowania dokumentów, jeśli trwałość zamówienia jest ważna. Na przykład, jeśli dwa dokumenty mają identyczny wynik, nie ma gwarancji, która jest najpierw wyświetlana w kolejnych uruchomieniach tego samego zapytania. Wynik dokumentu powinien zawierać jedynie ogólny sens dotyczący istotności dokumentu względem innych dokumentów w zestawie wyników.

## <a name="conclusion"></a>Podsumowanie

Sukcesy aparatów wyszukiwania internetowego zgłosiły oczekiwania na wyszukiwanie pełnotekstowe w danych prywatnych. W przypadku niemal dowolnego rodzaju środowiska wyszukiwania oczekujemy, że aparat zapoznaje się z intencją, nawet jeśli warunki są błędne lub niekompletne. Możemy nawet oczekiwać dopasowania w oparciu o niemal równoważne warunki lub synonimy, które nigdy nie zostały określone.

Z technicznego punktu widzenia wyszukiwanie pełnotekstowe jest wysoce skomplikowany, wymagając zaawansowanej analizy językowej i systematyczne podejścia do przetwarzania w taki sposób, że warunki zapytania w postaci "podwójna, rozwiń i Przekształć" w celu dostarczenia odpowiedniego wyniku. Mając na względzie złożone złożoności, istnieje wiele czynników, które mogą wpływać na wynik zapytania. Z tego powodu należy zainwestować czas, aby zrozumieć, Mechanics wyszukiwanie pełnotekstowe oferuje materialne korzyści podczas próby pracy przez nieoczekiwane wyniki.  

W tym artykule zbadano wyszukiwanie pełnotekstowe w kontekście usługi Azure Wyszukiwanie poznawcze. Mamy nadzieję, że zapewnisz wystarczającą wiedzę na temat rozpoznawania potencjalnych przyczyn i rozwiązań dotyczących rozwiązywania typowych problemów z zapytaniami. 

## <a name="next-steps"></a>Następne kroki

+ Skompiluj przykładowy indeks, wypróbuj różne zapytania i przejrzyj wyniki. Aby uzyskać instrukcje, zobacz [Kompilowanie i wykonywanie zapytań względem indeksu w portalu](search-get-started-portal.md#query-index).

+ Wypróbuj dodatkową składnię zapytania z przykładowej sekcji [Wyszukaj dokumenty](/rest/api/searchservice/search-documents#bkmk_examples) lub z [prostej składni zapytania](/rest/api/searchservice/simple-query-syntax-in-azure-search) w Eksploratorze wyszukiwania w portalu.

+ Przejrzyj [Profile oceniania](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , jeśli chcesz dostroić klasyfikację w aplikacji wyszukiwania.

+ Dowiedz się, jak stosować [analizatory leksykalne charakterystyczne dla języka](/rest/api/searchservice/language-support).

+ [Skonfiguruj Niestandardowe analizatory](/rest/api/searchservice/custom-analyzers-in-azure-search) dla minimalnej ilości przetwarzania lub wyspecjalizowanego przetwarzania dla określonych pól.

## <a name="see-also"></a>Zobacz też

[Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/search-documents) 

[Prosta składnia zapytań](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Pełna składnia zapytań Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Obsługa wyników wyszukiwania](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png