---
title: Prosta składnia zapytań
titleSuffix: Azure Cognitive Search
description: Odwołanie do prostej składni zapytania używanej na potrzeby zapytań wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923385"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Prosta Składnia zapytania w usłudze Azure Wyszukiwanie poznawcze

Platforma Azure Wyszukiwanie poznawcze implementuje dwa języki zapytań opartych na Lucene: [prosty Analizator zapytań](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) i [Analizator zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Prosty parser jest bardziej elastyczny i spróbuje zinterpretować żądanie nawet wtedy, gdy nie jest doskonale złożony. Ze względu na tę elastyczność jest to wartość domyślna dla zapytań w usłudze Azure Wyszukiwanie poznawcze. 

Prosta składnia jest używana w wyrażeniach zapytań przesyłanych w `search` parametrach [żądania przeszukiwania dokumentów](/rest/api/searchservice/search-documents), które nie należy mylić ze [składnią OData](query-odata-filter-orderby-syntax.md) użytą dla parametru [wyrażeń $Filter](search-filters.md) tego samego interfejsu API dokumentów wyszukiwania. `search`Parametry i `$filter` mają inną składnię z własnymi regułami tworzenia zapytań, ciągów ucieczki itd.

Chociaż prosty parser jest oparty na [prostej klasie analizatora zapytań programu Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , implementacja na platformie Azure wyszukiwanie poznawcze wyklucza Wyszukiwanie rozmyte. Jeśli potrzebujesz [wyszukiwania rozmytego](search-query-fuzzy.md) lub innych zaawansowanych formularzy zapytań, zamiast tego Rozważ alternatywną [pełną składnię zapytania Lucene](query-lucene-syntax.md) .

## <a name="invoke-simple-parsing"></a>Wywołaj prostą analizę

Wartością domyślną jest prosta składnia. Wywołanie jest wymagane tylko w przypadku resetowania składni z pełny na prosty. Aby jawnie ustawić składnię, użyj `queryType` parametru wyszukiwania. Prawidłowe wartości to `queryType=simple` lub `queryType=full` , gdzie `simple` jest ustawieniem domyślnym i `full` wywołuje [pełny Analizator zapytań Lucene](query-lucene-syntax.md) dla bardziej zaawansowanych zapytań. 

## <a name="syntax-fundamentals"></a>Podstawy składni

Dowolny tekst z co najmniej jednym warunkiem jest uznawany za prawidłowy punkt początkowy wykonywania zapytania. Usługa Azure Wyszukiwanie poznawcze będzie pasować do dokumentów zawierających dowolne lub wszystkie warunki, w tym wszelkie odmiany znalezione podczas analizy tekstu.

Tak samo jak te dźwięki, istnieje jeden aspekt wykonywania zapytania w usłudze Azure Wyszukiwanie poznawcze, który *może* dawać nieoczekiwane wyniki, zwiększając zamiast zmniejszania wyników wyszukiwania, tak aby więcej warunków i operatorów zostało dodanych do ciągu wejściowego. Czy to rozwinięcie jest zależne od dołączenia operatora NOT, połączonego z ustawieniem parametru **searchmode** , które określa, jak nie jest interpretowane w warunkach i lub lub. Aby uzyskać więcej informacji, zobacz [not operator](#not-operator).

### <a name="precedence-operators-grouping"></a>Operatory pierwszeństwa (grupowanie)

Za pomocą nawiasów można tworzyć podzapytania, w tym operatory w instrukcji języka nawiasów. Program przeszuka na przykład `motel+(wifi|luxury)` dokumenty zawierające termin "Motel" i "Wi-Fi" lub "możliwość zaprojektowania" (lub oba te elementy).

Grupowanie pól jest podobne, ale zakresy grupowanie do jednego pola. Na przykład `hotelAmenities:(gym+(wifi|pool))` szuka pola "hotelAmenities" dla "treningów" i "Wi-Fi", "treningów" i "Pool".  

### <a name="escaping-search-operators"></a>Operatory wyszukiwania ucieczki  

W prostej składni Operatory wyszukiwania zawierają następujące znaki: `+ | " ( ) ' \`  

Jeśli którykolwiek z tych znaków jest częścią tokenu w indeksie, należy to zrobić, wpisując jego prefiks z pojedynczym ukośnikiem odwrotnym ( `\` ) w zapytaniu. Załóżmy na przykład, że użyto analizatora niestandardowego dla całego terminu tokenizacji, a indeks zawiera ciąg "możliwość zaprojektowania + Hotel". Aby uzyskać dokładne dopasowanie dla tego tokenu, Wstaw znak ucieczki:  `search=luxury\+hotel` . 

Aby elementy były proste dla bardziej typowych przypadków, istnieją dwa wyjątki od tej reguły, w przypadku których ucieczki nie są konieczne:  

+ Operatora NOT nie `-` należy zmienić tylko wtedy, gdy jest to pierwszy znak po odstępie. Jeśli `-` pojawia się w środku (na przykład w `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), można pominąć ucieczkę.

+ Operator sufiksu `*` musi zostać zmieniony tylko w przypadku, gdy jest to ostatni znak przed odstępem. Jeśli `*` pojawia się w środku (na przykład w `4*4=16` ), nie jest wymagana żadna ucieczka.

> [!NOTE]  
> Domyślnie Analizator standardowej spowoduje usunięcie i przerwanie wyrazów w łącznikach, odstępach, znakach i innych znakach podczas [analizy leksykalnej](search-lucene-query-architecture.md#stage-2-lexical-analysis). Jeśli potrzebujesz znaków specjalnych do pozostawania w ciągu zapytania, może być konieczne Analizator, który zachowuje je w indeksie. Niektóre z nich to analizatory [języka](index-add-language-analyzers.md)naturalnego firmy Microsoft, które zachowują wyrazy z wyrazami lub niestandardową, aby uzyskać bardziej złożone wzorce. Aby uzyskać więcej informacji, zobacz [częściowe terminy, wzorce i znaki specjalne](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie znaków niebezpiecznych i zastrzeżonych w adresach URL

Upewnij się, że wszystkie znaki niebezpieczne i zarezerwowane są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest identyfikatorem fragmentu/kotwicą w adresie URL. Znak musi być zakodowany `%23` w przypadku użycia w adresie URL. "&" i "=" są przykładami znaków zarezerwowanych, ponieważ oddzielają one parametry i określają wartości na platformie Azure Wyszukiwanie poznawcze. Aby uzyskać więcej informacji, zobacz [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

Niebezpieczne znaki to ``" ` < > # % { } | \ ^ ~ [ ]`` . Znaki zarezerwowane są `; / ? : @ = + &` .

### <a name="querying-for-special-characters"></a>Wykonywanie zapytań o znaki specjalne

W pewnych okolicznościach można wyszukać znak specjalny, taki jak emoji "❤" lub "€". W takim przypadku upewnij się, że używany Analizator nie filtruje tych znaków.  Analizator standardowy ignoruje wiele znaków specjalnych, dzięki czemu nie staną się tokenami w indeksie.

Dlatego najpierw należy upewnić się, że używasz analizatora, który będzie uwzględniać te tokeny elementów. Na przykład Analizator "odstępów" bierze pod uwagę wszystkie sekwencje znaków oddzielone odstępami jako tokeny, więc ciąg "❤" byłby uznawany za token. Ponadto Analizator, taki jak Microsoft English Analyzer ("en. Microsoft"), uwzględnia ciąg "€" jako token. Możesz [przetestować Analizator](/rest/api/searchservice/test-analyzer) , aby zobaczyć, jakie tokeny generuje dla danego zapytania.

W przypadku używania znaków Unicode upewnij się, że symbole są prawidłowo w adresie URL zapytania (na przykład dla "❤" będzie używana sekwencja ucieczki `%E2%9D%A4+` ). Program ogłaszający automatycznie wykonuje to tłumaczenie.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Limity rozmiaru zapytań

 Istnieje ograniczenie rozmiaru zapytań, które można wysłać do usługi Azure Wyszukiwanie poznawcze. W szczególności można mieć co najwyżej 1024 klauzul (wyrażenia oddzielone znakami i, lub itd.). Obowiązuje również limit około 32 KB na rozmiar każdego pojedynczego okresu zapytania. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zalecamy zaprojektowanie go w taki sposób, aby nie generował zapytań o nieograniczonego rozmiaru.  

## <a name="boolean-search"></a>Wyszukiwanie wartości logicznych

Można osadzić operatory logiczne (i, nie) w ciągu zapytania w celu utworzenia bogatego zestawu kryteriów, względem których znaleziono pasujące dokumenty. 

### <a name="and-operator-"></a>AND — operator `+`

Operator AND jest znakiem plus. Na przykład program przeszuka `wifi + luxury` dokumenty zawierające zarówno elementy `wifi` , jak i `luxury` .

### <a name="or-operator-"></a>OR — operator `|`

Operator OR jest pionowym znakiem kreski lub potoku. Na przykład program przeszuka `wifi | luxury` dokumenty zawierające jeden `wifi` lub `luxury` oba te elementy.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT — operator `-`

Operator NOT jest znakiem minus. Program przeszuka na przykład `wifi –luxury` dokumenty, które mają `wifi` termin i/lub nie mają `luxury` .

Parametr **searchmode** w żądaniu zapytania kontroluje, czy termin z operatorem NOT jest ANDed lub logicznie innym warunkiem w zapytaniu (przy założeniu, że nie `+` ma `|` operatora OR w innych warunkach). Prawidłowe wartości to include `any` lub `all` .

`searchMode=any` zwiększa odwoływanie zapytań przez dołączenie większej liczby wyników i domyślnie `-` będzie interpretowane jako "lub" nie ". Na przykład program `wifi -luxury` będzie pasował do dokumentów, które zawierają termin `wifi` lub te, które nie zawierają warunków `luxury` .

`searchMode=all` zwiększa precyzję zapytań, dołączając mniejszą liczbę wyników i domyślnie — będzie interpretowana jako "i". Na przykład program `wifi -luxury` będzie pasował do dokumentów zawierających termin `wifi` i nie zawiera terminu "możliwość zaprojektowania". Jest to raczej bardziej intuicyjne zachowanie `-` operatora. W związku z tym należy rozważyć użycie zamiast tego, `searchMode=all` `searchMode=any` Jeśli chcesz zoptymalizować wyszukiwanie pod kątem precyzji zamiast odwołania, *a* użytkownicy często używają `-` operatora w wyszukiwaniach.

Podczas decydowania o ustawieniu **searchmode** należy wziąć pod uwagę wzorce interakcji użytkownika dotyczące zapytań w różnych aplikacjach. Użytkownicy poszukujący informacji mogą dołączać operator do zapytania, w przeciwieństwie do witryn handlu elektronicznego, które mają bardziej wbudowaną strukturę nawigacji.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>Dopasowanie prefiksu wieloznacznego (*,?)

W przypadku zapytań "zaczyna się od" Dodaj operator sufiksu jako symbol zastępczy dla pozostałej części terminu. Użyj gwiazdki `*` dla wielu znaków lub `?` dla pojedynczych znaków. Na przykład, `lingui*` będzie pasować do "lingwistyczn" lub "Linguini", z uwzględnieniem wielkości liter. 

Podobnie jak w przypadku filtrów, zapytanie o prefiks szuka dokładnego dopasowania. W związku z tym nie ma oceny znaczenia (wyniki wyszukiwania są 1,0). Należy pamiętać, że zapytania z prefiksami mogą być powolne, szczególnie jeśli indeks jest duży, a prefiks składa się z niewielkiej liczby znaków. Alternatywna metodologia, taka jak Edge n-gram tokenizacji, może szybciej działać.

W przypadku innych wariantów kwerend symboli wieloznacznych, takich jak sufiks lub wrostkowe dopasowywania do końca lub środka okresu, użyj [pełnej składni Lucene dla wyszukiwania symboli wieloznacznych](query-lucene-syntax.md#bkmk_wildcard).

## <a name="phrase-search-"></a>Wyszukiwanie fraz `"`

Wyszukiwanie warunków jest zapytania dla co najmniej jednego terminu, gdzie dowolne z warunków jest uważane za dopasowanie. Wyszukiwanie frazy jest dokładną frazą ujętą w znaki cudzysłowu `" "` . Na przykład podczas `Roach Motel` (bez cudzysłowów) Wyszukiwanie dokumentów zawierających `Roach` i/lub `Motel` wszędzie w dowolnej kolejności `"Roach Motel"` (z cudzysłowami) będzie pasować tylko do dokumentów, które zawierają całą frazę i w tej kolejności (w dalszym ciągu stosuje się analizę leksykalną).

## <a name="see-also"></a>Zobacz też  

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Przykłady zapytań dla prostego wyszukiwania](search-query-simple-examples.md)
+ [Przykłady zapytań dla pełnego wyszukiwania Lucene](search-query-lucene-examples.md)
+ [Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/Search-Documents)
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [Składnia wyrażenia OData](query-odata-filter-orderby-syntax.md)