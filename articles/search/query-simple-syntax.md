---
title: Prosta składnia zapytań
titleSuffix: Azure Cognitive Search
description: Odwołanie do prostej składni zapytania używanej na potrzeby zapytań wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516576"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Prosta Składnia zapytania w usłudze Azure Wyszukiwanie poznawcze

Platforma Azure Wyszukiwanie poznawcze implementuje dwa języki zapytań opartych na Lucene: [prosty Analizator zapytań](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) i [Analizator zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Prosty parser jest bardziej elastyczny i spróbuje zinterpretować żądanie nawet wtedy, gdy nie jest doskonale złożony. Ze względu na tę elastyczność jest to wartość domyślna dla zapytań w usłudze Azure Wyszukiwanie poznawcze.

Prosta składnia jest używana w wyrażeniach zapytań przesyłanych w **`search`** parametrach żądania [przeszukiwania dokumentów (interfejsu API REST)](/rest/api/searchservice/search-documents) , nie należy mylić ze [składnią OData](query-odata-filter-orderby-syntax.md) użytą [**`$filter`**](search-filters.md) dla [**`$orderby`**](search-query-odata-orderby.md) wyrażeń i w tym samym żądaniu. Parametry OData mają różne składnie i reguły służące do konstruowania zapytań, ciągów ucieczki itd.

Chociaż prosty analizator składni jest oparty na [prostej klasie analizatora zapytań Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , implementacja w wyszukiwanie poznawcze wyklucza rozmyte wyszukiwanie. Jeśli potrzebujesz [wyszukiwania rozmytego](search-query-fuzzy.md), rozważ zamiast tego alternatywną [pełną składnię zapytania Lucene](query-lucene-syntax.md) .

## <a name="example-simple-syntax"></a>Przykład (składnia prosta)

Chociaż **`queryType`** jest ustawiony poniżej, jest to wartość domyślna i można ją pominąć, chyba że jest przywracany z alternatywnego typu. Poniższy przykład to wyszukiwanie niezależnych terminów z wymaganiem, że wszystkie zgodne dokumenty obejmują "pulę".

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

**`searchMode`** Parametr jest istotny w tym przykładzie. Zawsze, gdy operatory logiczne znajdują się w zapytaniu, należy ogólnie ustawić, `searchMode=all` Aby upewnić się, że *wszystkie* kryteria są zgodne. W przeciwnym razie można użyć domyślnego, `searchMode=any` który preferuje odzyskanie z dokładnością.

Aby uzyskać więcej przykładów, zobacz [Przykłady prostych składni zapytań](search-query-simple-examples.md). Aby uzyskać szczegółowe informacje na temat żądania zapytania i parametrów, zobacz [Wyszukiwanie dokumentów (interfejs API REST)](/rest/api/searchservice/Search-Documents).

## <a name="keyword-search-on-terms-and-phrases"></a>Wyszukiwanie słów kluczowych na warunkach i frazach

Ciągi przesłane do **`search`** parametru mogą zawierać warunki lub frazy w dowolnym obsługiwanym języku, operatory logiczne, operatory pierwszeństwa, symbole wieloznaczne lub prefiksu dla "zaczyna się od" zapytań, znaków ucieczki i znaków kodowania adresu URL. **`search`** Parametr jest opcjonalny. Nieokreślony, wyszukiwanie ( `search=*` lub `search=" "` ) zwraca górne dokumenty 50 w dowolnej kolejności (niesklasyfikowanej).

+ *Wyszukiwanie warunków* jest zapytaniem co najmniej jednego terminu, gdzie dowolne warunki są uważane za dopasowanie.

+ *Wyszukiwanie frazy* jest dokładną frazą ujętą w znaki cudzysłowu `" "` . Na przykład podczas ```Roach Motel``` (bez cudzysłowów) Wyszukiwanie dokumentów zawierających ```Roach``` i/lub ```Motel``` wszędzie w dowolnej kolejności ```"Roach Motel"``` (z cudzysłowami) będzie pasować tylko do dokumentów, które zawierają całą frazę i w tej kolejności (w dalszym ciągu stosuje się analizę leksykalną). 

  W zależności od klienta wyszukiwania może zajść potrzeba ucieczki znaków cudzysłowu w wyszukiwaniu fraz. Na przykład w ogłoszeniu post w żądaniu POST wyszukiwanie frazy `"Roach Motel"` w treści żądania zostanie określone jako `"\"Roach Motel\""` .

Domyślnie wszystkie warunki lub frazy, które zostały przesłane w parametrze, są przenoszone do **`search`** analizy leksykalnej. Upewnij się, że rozumiesz zachowanie tokenizacji analizatora. Często, gdy wyniki zapytania są nieoczekiwane, powód może być śledzony do sposobu, w jaki są tokeny w czasie zapytania.

Dowolny tekst z co najmniej jednym warunkiem jest uznawany za prawidłowy punkt początkowy wykonywania zapytania. Usługa Azure Wyszukiwanie poznawcze będzie pasować do dokumentów zawierających dowolne lub wszystkie warunki, w tym wszelkie odmiany znalezione podczas analizy tekstu.

Tak samo jak te dźwięki, istnieje jeden aspekt wykonywania zapytania w usłudze Azure Wyszukiwanie poznawcze, który *może* dawać nieoczekiwane wyniki, zwiększając zamiast zmniejszania wyników wyszukiwania, tak aby więcej warunków i operatorów zostało dodanych do ciągu wejściowego. Czy to rozwinięcie jest zależne od dołączenia operatora NOT, połączonego z **`searchMode`** ustawieniem parametru, które określa, jak nie jest interpretowane w warunkach i lub lub. Aby uzyskać więcej informacji, zobacz operator NOT w obszarze [Operatory logiczne](#boolean-operators).

## <a name="boolean-operators"></a>Operatory logiczne

Operatory logiczne można osadzić w ciągu zapytania, aby zwiększyć dokładność dopasowania. W prostej składni operatory logiczne są oparte na znakach. Operatory tekstu, takie jak słowo i, nie są obsługiwane.

| Znak | Przykład | Użycie |
|----------- |--------|-------|
| `+` | `pool + ocean` | Operacja i. Na przykład program `pool + ocean` określa, że dokument musi zawierać oba warunki.|
| `|` | `pool | ocean` | Operacja OR wyszukuje dopasowanie, gdy zostanie znaleziony termin. W przykładzie aparat zapytań zwróci dopasowanie do dokumentów zawierających jeden `pool` lub `ocean` lub oba te elementy. Ponieważ lub jest domyślnym operatorem połączenia, można go również pozostawić, tak aby `pool ocean` był odpowiednikiem  `pool | ocean` .|
| `-` | `pool – ocean` | Operacja nie zwraca dopasowań w dokumentach, które wykluczają ten termin. <br/><br/>Aby uzyskać oczekiwane zachowanie w wyrażeniu, rozważ ustawienie **`searchMode=all`** dla żądania. W przeciwnym razie, pod wartością domyślną **`searchMode=any`** , uzyskasz dopasowania na `pool` , a także dopasowań wszystkich dokumentów, które nie zawierają `ocean` , co może być wiele dokumentów. **`searchMode`** Parametr żądania zapytania kontroluje, czy termin z operatorem NOT jest ANDed lub logicznie innym warunkiem w zapytaniu (przy założeniu, że nie ma `+` `|` operatora OR w innych warunkach). Użycie **`searchMode=all`** zwiększa precyzję zapytań, dołączając mniejszą liczbę wyników i domyślnie — będzie interpretowane jako "i not". <br/><br/>Podczas decydowania o **`searchMode`** ustawieniu należy wziąć pod uwagę wzorce interakcji użytkownika dotyczące zapytań w różnych aplikacjach. Użytkownicy poszukujący informacji mogą dołączać operator do zapytania, w przeciwieństwie do witryn handlu elektronicznego, które mają bardziej wbudowaną strukturę nawigacji. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Zapytania prefiksu

W przypadku zapytań "zaczyna się od" Dodaj operator sufiksu ( `*` ) jako symbol zastępczy dla pozostałej części terminu. Zapytanie prefiksu musi rozpoczynać się od co najmniej jednego znaku alfanumerycznego, aby można było dodać operatora sufiksu.

| Znak | Przykład | Użycie |
|----------- |--------|-------|
| `*` | `lingui*` będzie pasować do "lingwistyczn" lub "Linguini" | Gwiazdka ( `*` ) reprezentuje jeden lub więcej znaków dowolnej długości, bez uwzględnienia wielkości liter.  |

Podobnie jak w przypadku filtrów, zapytanie o prefiks szuka dokładnego dopasowania. W związku z tym nie ma oceny znaczenia (wyniki wyszukiwania są 1,0). Należy pamiętać, że zapytania z prefiksami mogą być powolne, szczególnie jeśli indeks jest duży, a prefiks składa się z niewielkiej liczby znaków. Alternatywna metodologia, taka jak Edge n-gram tokenizacji, może szybciej działać.

Prosta składnia obsługuje tylko Dopasowywanie prefiksów. W przypadku dopasowania sufiksu lub wrostkowe do końca lub środka okresu Użyj [pełnej składni Lucene dla wyszukiwania symboli wieloznacznych](query-lucene-syntax.md#bkmk_wildcard).

## <a name="escaping-search-operators"></a>Operatory wyszukiwania ucieczki  

W prostej składni Operatory wyszukiwania zawierają następujące znaki: `+ | " ( ) ' \`  

Jeśli którykolwiek z tych znaków jest częścią tokenu w indeksie, należy to zrobić, wpisując jego prefiks z pojedynczym ukośnikiem odwrotnym ( `\` ) w zapytaniu. Załóżmy na przykład, że użyto analizatora niestandardowego dla całego terminu tokenizacji, a indeks zawiera ciąg "możliwość zaprojektowania + Hotel". Aby uzyskać dokładne dopasowanie dla tego tokenu, Wstaw znak ucieczki: `search=luxury\+hotel` .

Aby elementy były proste dla bardziej typowych przypadków, istnieją dwa wyjątki od tej reguły, w przypadku których ucieczki nie są konieczne:  

+ Operatora NOT nie `-` należy zmienić tylko wtedy, gdy jest to pierwszy znak po odstępie. Jeśli `-` pojawia się w środku (na przykład w `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), można pominąć ucieczkę.

+ Operator sufiksu `*` musi zostać zmieniony tylko w przypadku, gdy jest to ostatni znak przed odstępem. Jeśli `*` pojawia się w środku (na przykład w `4*4=16` ), nie jest wymagana żadna ucieczka.

> [!NOTE]  
> Domyślnie Analizator standardowej spowoduje usunięcie i przerwanie wyrazów w łącznikach, odstępach, znakach i innych znakach podczas [analizy leksykalnej](search-lucene-query-architecture.md#stage-2-lexical-analysis). Jeśli potrzebujesz znaków specjalnych do pozostawania w ciągu zapytania, może być konieczne Analizator, który zachowuje je w indeksie. Niektóre z nich to analizatory [języka](index-add-language-analyzers.md)naturalnego firmy Microsoft, które zachowują wyrazy z wyrazami lub niestandardową, aby uzyskać bardziej złożone wzorce. Aby uzyskać więcej informacji, zobacz [częściowe terminy, wzorce i znaki specjalne](search-query-partial-matching.md).

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie znaków niebezpiecznych i zastrzeżonych w adresach URL

Upewnij się, że wszystkie znaki niebezpieczne i zarezerwowane są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest identyfikatorem fragmentu/kotwicą w adresie URL. Znak musi być zakodowany `%23` w przypadku użycia w adresie URL. "&" i "=" są przykładami znaków zarezerwowanych, ponieważ oddzielają one parametry i określają wartości na platformie Azure Wyszukiwanie poznawcze. Aby uzyskać więcej informacji, zobacz [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

Niebezpieczne znaki to ``" ` < > # % { } | \ ^ ~ [ ]`` . Znaki zarezerwowane są `; / ? : @ = + &` .

## <a name="special-characters"></a>Znaki specjalne

W pewnych okolicznościach można wyszukać znak specjalny, taki jak emoji "❤" lub znak "€". W takich przypadkach upewnij się, że analizator nie filtruje tych znaków. Analizator standardowy pomija wiele znaków specjalnych, z wyłączeniem ich z indeksu.

Analizatory, które będą tokenize znaki specjalne, obejmują Analyzer "białych", który uwzględnia wszystkie sekwencje znaków oddzielone odstępami jako tokeny (więc ciąg "❤" byłby uznawany za token). Ponadto Analizator języka, taki jak Microsoft English Analyzer ("en. Microsoft"), przyjmuje ciąg "€" jako token. Możesz [przetestować Analizator](/rest/api/searchservice/test-analyzer) , aby zobaczyć, jakie tokeny generuje dla danego zapytania.

W przypadku używania znaków Unicode upewnij się, że symbole są prawidłowo w adresie URL zapytania (na przykład dla "❤" będzie używana sekwencja ucieczki `%E2%9D%A4+` ). Program ogłaszający automatycznie wykonuje to tłumaczenie.  

## <a name="precedence-grouping"></a>Priorytet (grupowanie)

Za pomocą nawiasów można tworzyć podzapytania, w tym operatory w instrukcji języka nawiasów. Program przeszuka na przykład `motel+(wifi|luxury)` dokumenty zawierające termin "Motel" i "Wi-Fi" lub "możliwość zaprojektowania" (lub oba te elementy).

## <a name="query-size-limits"></a>Limity rozmiaru zapytań

Jeśli aplikacja generuje zapytania wyszukiwania programowo, zalecamy zaprojektowanie go w taki sposób, aby nie generował zapytań o nieograniczonego rozmiaru. 

+ W polu GET długość adresu URL nie może przekroczyć 8 KB.

+ W przypadku wpisów (i innych żądań), gdzie treść żądania zawiera `search` i inne parametry, takie jak `filter` i `orderby` , maksymalny rozmiar to 16 MB, gdzie maksymalna liczba klauzul w `search` (wyrażenia oddzielone ZNAKami i, lub itd.) to 1024. Obowiązuje również limit około 32 KB na rozmiar każdego pojedynczego okresu zapytania. Aby uzyskać więcej informacji, zobacz [limity żądań interfejsu API](search-limits-quotas-capacity.md#api-request-limits).

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz programowo tworzyć zapytania, zapoznaj się z tematem [wyszukiwanie pełnotekstowe w usłudze Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md) , aby zrozumieć etapy przetwarzania zapytań i wpływ analizy tekstu.

Możesz również zapoznać się z następującymi artykułami, aby dowiedzieć się więcej na temat konstrukcji zapytania:

+ [Przykłady zapytań dla prostego wyszukiwania](search-query-simple-examples.md)
+ [Przykłady zapytań dla pełnego wyszukiwania Lucene](search-query-lucene-examples.md)
+ [Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/Search-Documents)
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [Składnia wyrażenia Filter i Select (OData)](query-odata-filter-orderby-syntax.md)