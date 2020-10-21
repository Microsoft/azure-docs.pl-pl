---
title: Pracuj z ciągami w zapytaniach dziennika Azure Monitor | Microsoft Docs
description: Opisuje sposób edytowania, porównywania, wyszukiwania i wykonywania różnych operacji na ciągach w kwerendach dziennika Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/19/2020
ms.openlocfilehash: 7838f9f1febcab073633dbb4af011e99acbe22d3
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310289"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Pracuj z ciągami w zapytaniach dziennika Azure Monitor


> [!NOTE]
> Przed ukończeniem tego samouczka należy zakończyć pracę [z Azure Monitor Log Analytics](get-started-portal.md) i [zacząć korzystać z zapytań dzienników Azure monitor](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano sposób edytowania, porównywania, wyszukiwania i wykonywania różnych operacji na ciągach.

Każdy znak w ciągu ma numer indeksu, zgodnie z jego lokalizacją. Pierwszy znak jest przy indeksie 0, następny znak to 1 itd. Różne funkcje ciągów używają numerów indeksu, jak pokazano w poniższych sekcjach. W wielu poniższych przykładach użyto polecenia **Print** , aby zademonstrować manipulowanie ciągami bez użycia określonego źródła danych.


## <a name="strings-and-escaping-them"></a>Ciągi i ucieczki
Wartości ciągów są opakowane z pojedynczym lub podwójnym cudzysłowem. Ukośnik odwrotny ( \\ ) jest używany do ucieczki znaków następującego znaku, takiego jak \t dla karty, \n dla wiersza i \" znaku cudzysłowu.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Aby zapobiec \\ działaniu jako znak ucieczki, Dodaj " \@ " jako prefiks do ciągu:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Porównanie ciągów

Operator       |Opis                         |Case-Sensitive|Przykład (yields `true` )
---------------|------------------------------------|--------------|-----------------------
`==`           |Równa się                              |Tak           |`"aBc" == "aBc"`
`!=`           |Nie równa się                          |Tak           |`"abc" != "ABC"`
`=~`           |Równa się                              |Nie            |`"abc" =~ "ABC"`
`!~`           |Nie równa się                          |Nie            |`"aBc" !~ "xyz"`
`has`          |Po prawej stronie jest całym terminem po lewej stronie |Nie|`"North America" has "america"`
`!has`         |Po prawej stronie nie ma pełnego terminu po lewej stronie       |Nie            |`"North America" !has "amer"` 
`has_cs`       |Po prawej stronie jest całym terminem po lewej stronie |Tak|`"North America" has_cs "America"`
`!has_cs`      |Po prawej stronie nie ma pełnego terminu po lewej stronie       |Tak            |`"North America" !has_cs "amer"` 
`hasprefix`    |Po prawej stronie jest termin prefiksu po lewej stronie         |Nie            |`"North America" hasprefix "ame"`
`!hasprefix`   |Po prawej stronie nie jest terminem prefiksu po lewej stronie     |Nie            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Po prawej stronie jest termin prefiksu po lewej stronie         |Tak            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Po prawej stronie nie jest terminem prefiksu po lewej stronie     |Tak            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Po prawej stronie jest sufiks terminu po lewej stronie         |Nie            |`"North America" hassuffix "ica"`
`!hassuffix`   |Po prawej stronie nie ma sufiksu terminu po lewej stronie     |Nie            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Po prawej stronie jest sufiks terminu po lewej stronie         |Tak            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Po prawej stronie nie ma sufiksu terminu po lewej stronie     |Tak            |`"North America" !hassuffix_cs "icA"`
`contains`     |Prawa strona występuje jako podsekwencja po lewej stronie  |Nie            |`"FabriKam" contains "BRik"`
`!contains`    |Prawa strona nie występuje po lewej stronie           |Nie            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Prawa strona występuje jako podsekwencja po lewej stronie  |Tak           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Prawa strona nie występuje po lewej stronie           |Tak           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Po prawej stronie jest początkową podsekwencją lewej strony|Nie            |`"Fabrikam" startswith "fab"`
`!startswith`  |Po prawej stronie nie jest początkową podsekwencją lewej strony|Nie        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Po prawej stronie jest początkową podsekwencją lewej strony|Tak            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Po prawej stronie nie jest początkową podsekwencją lewej strony|Tak        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Po prawej stronie jest zamykaną podsekwencją lewej strony|Nie             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Po prawej stronie nie jest zamykana podsekwencja po lewej stronie|Nie         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Po prawej stronie jest zamykaną podsekwencją lewej strony|Tak             |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`    |Po prawej stronie nie jest zamykana podsekwencja po lewej stronie|Tak         |`"Fabrikam" !endswith_cs "brik"`
`matches regex`|po lewej stronie zawiera dopasowanie po prawej stronie        |Tak           |`"Fabrikam" matches regex "b.*k"`
`in`           |Równa się jednemu z elementów       |Tak           |`"abc" in ("123", "345", "abc")`
`!in`          |Nie równa się żadnym elementom   |Tak           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>CountOf

Zlicza wystąpienia podciągu w ciągu. Można dopasować zwykłe ciągi lub użyć wyrażenia regularnego. Dopasowania w postaci zwykłego ciągu mogą się pokrywać, gdy nie są zgodne wyrażenia regularne.

### <a name="syntax"></a>Składnia
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumentu
- `text` -Ciąg wejściowy 
- `search` -Zwykłego ciągu lub wyrażenia regularnego do dopasowania wewnątrz tekstu.
- `kind` - _normalne_  |  _wyrażenie regularne_ (domyślnie: Normal).

### <a name="returns"></a>Zwraca

Liczba określająca, ile razy można dopasować ciąg wyszukiwania w kontenerze. Dopasowania w postaci zwykłego ciągu mogą się pokrywać, gdy nie są zgodne wyrażenia regularne.

### <a name="examples"></a>Przykłady

#### <a name="plain-string-matches"></a>Dopasowania zwykłego ciągu

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Dopasowanie wyrażenia regularnego

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Wyodrębnij

Pobiera dopasowanie wyrażenia regularnego z danego ciągu. Opcjonalnie konwertuje wyodrębniony podciąg na określony typ.

### <a name="syntax"></a>Składnia

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenty

- `regex` -Wyrażenie regularne.
- `captureGroup` -Dodatnia stała całkowita wskazująca grupę przechwytywania do wyodrębnienia. 0 dla całego dopasowania, 1 dla wartości dopasowanej przez pierwszy znak "(" nawias ")" w wyrażeniu regularnym, 2 lub więcej dla kolejnych nawiasów.
- `text` -Ciąg do wyszukania.
- `typeLiteral` -Opcjonalny literał typu (na przykład typeof (Long)). Jeśli jest podany, wyodrębniony podciąg jest konwertowany na ten typ.

### <a name="returns"></a>Zwraca
Podciąg dopasowany do wskazanej grupy przechwytywania grupa przechwytywania, opcjonalnie przekonwertowany na typeLiteral.
Jeśli nie ma dopasowania lub konwersja typu nie powiedzie się, zwraca wartość null.

### <a name="examples"></a>Przykłady

Poniższy przykład wyodrębnia ostatni oktet *ComputerIP* z rekordu pulsu:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Poniższy przykład wyodrębnia ostatni oktet, rzutuje go do *rzeczywistego* typu (Number) i oblicza następną wartość IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

W poniższym przykładzie *śledzenie* ciągów jest wyszukiwane dla definicji "Duration". Dopasowanie jest rzutowane na *prawdziwe* i mnożone przez stałą czasową (1 s) *, która rzutuje czas trwania na typ TimeSpan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, noskłonność

- *IsEmpty* zwraca wartość true, jeśli argument jest ciągiem pustym lub wartością null (patrz również *IsNull*).
- *isnotempty* zwraca wartość true, jeśli argument nie jest ciągiem pustym ani wartością null (Zobacz również *IsNotNull*). alias: *noskłonność*.

### <a name="syntax"></a>Składnia

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Przykłady

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Dzieli adres URL na jego części (protokół, host, port itp.) i zwraca obiekt słownika zawierający części jako ciągi.

### <a name="syntax"></a>Składnia

```
parseurl(urlstring)
```

### <a name="examples"></a>Przykłady

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Wynik będzie:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>Zastąp

Zamienia wszystkie dopasowania wyrażenia regularnego na inny ciąg. 

### <a name="syntax"></a>Składnia

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenty

- `regex` -Wyrażenie regularne, które ma zostać dopasowane przez. Może zawierać grupy przechwytywania w nawiasach klamrowych ().
- `rewrite` — Wyrażenie regularne zastąpienia dla dowolnego dopasowania wykonanego przez zgodne wyrażenie regularne. Użyj \ 0, aby odwołać się do całego dopasowania, \ 1 dla pierwszej grupy przechwytywania, \ 2 itd. dla kolejnych grup przechwytywania.
- `input_text` -Ciąg wejściowy do wyszukania.

### <a name="returns"></a>Zwraca
Tekst po zamianie wszystkich odpowiedników wyrażenia regularnego na oceny ponownego zapisu. Dopasowania nie nakładają się.

### <a name="examples"></a>Przykłady

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Mogą mieć następujące wyniki:

Działanie                                        |zastąpienie
------------------------------------------------|----------------------------------------------------------
4663 — podjęto próbę uzyskania dostępu do obiektu  |Identyfikator działania 4663: podjęto próbę uzyskania dostępu do obiektu.


## <a name="split"></a>split

Dzieli dany ciąg zgodnie z określonym ogranicznikiem i zwraca tablicę wynikowych podciągów.

### <a name="syntax"></a>Składnia
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumentu

- `source` -Ciąg, który ma zostać podzielony według określonego ogranicznika.
- `delimiter` — Ogranicznik, który będzie używany w celu podzielenia ciągu źródłowego.
- `requestedIndex` — Opcjonalny indeks oparty na wartości zero. Jeśli jest podany, zwracana tablica ciągów będzie przechowywać tylko ten element (jeśli istnieje).


### <a name="examples"></a>Przykłady

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Łączy argumenty ciągu (obsługuje 1-16 argumentów).

### <a name="syntax"></a>Składnia
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Przykłady
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Zwraca długość ciągu.

### <a name="syntax"></a>Składnia
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Przykłady
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>podciąg

Wyodrębnia podciąg z danego ciągu źródłowego, rozpoczynając od określonego indeksu. Opcjonalnie można określić długość żądanego podciągu.

### <a name="syntax"></a>Składnia
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumentu

- `source` -Ciąg źródłowy, z którego zostanie pobrany podciąg.
- `startingIndex` — Znak początkowy na podstawie zera dla żądanego podciągu.
- `length` -Opcjonalny parametr, który może służyć do określania żądanych długości zwracanego podciągu.

### <a name="examples"></a>Przykłady
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>ToLower, ToUpper

Konwertuje dany ciąg na małe lub wielkie litery.

### <a name="syntax"></a>Składnia
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Przykłady
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Następne kroki
Kontynuuj korzystanie z zaawansowanych samouczków:
* [Funkcje agregacji](aggregations.md)
* [Agregacje zaawansowane](advanced-aggregations.md)
* [Wykresy i diagramy](charts.md)
* [Praca z danymi JSON i strukturami danych](json-data-structures.md)
* [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
* [Sprzężenia — analiza krzyżowa](joins.md)
