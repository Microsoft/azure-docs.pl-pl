---
title: Odwołanie do języka SQL akceleracji kwerendy (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak używać składni sql akceleracji kwerendy.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772121"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Odwołanie do języka SQL akceleracji kwerendy (wersja zapoznawcza)

Akceleracja zapytań obsługuje język podobny do języka SQL ANSI do wyrażania zapytań za pośrednictwem zawartości obiektu blob.  Dialekt SQL przyspieszania kwerendy jest podzbiorem ANSI SQL, z ograniczonym zestawem obsługiwanych typów danych, operatorów itp., ale także rozszerza się na ANSI SQL, aby obsługiwać zapytania w hierarchicznych formatach danych półstrukturacyjnych, takich jak JSON. 

> [!NOTE]
> Funkcja przyspieszania kwerend jest dostępna w publicznej wersji zapoznawczej i jest dostępna w regionach Kanada Środkowa i Francja Central. Aby przejrzeć ograniczenia, zobacz artykuł [Znane problemy.](data-lake-storage-known-issues.md) Aby zarejestrować się w wersji zapoznawczej, zobacz [ten formularz](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>WYBIERZ Składnię

Jedyną instrukcją SQL obsługiwana przez akcelerację kwerendy jest instrukcja SELECT. W tym przykładzie zwraca każdy wiersz, dla którego wyrażenie zwraca wartość true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

W przypadku danych w formacie CSV *tabela* musi być `BlobStorage`.  Oznacza to, że kwerenda będzie uruchamiana względem dowolnego obiektu blob został określony w wywołaniu REST.
W przypadku danych w formacie JSON *tabela* jest "deskryptorem tabeli".   Zobacz [sekcję Deskryptory tabeli](#table-descriptors) tego artykułu.

W poniższym przykładzie dla każdego wiersza, dla którego *wyrażenie* WHERE zwraca wartość true, ta instrukcja zwróci nowy wiersz, który jest wykonany z oceny każdego z wyrażeń rzutowania.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Poniższy przykład zwraca obliczenia agregacji (Na przykład: średnia wartość określonej kolumny) nad każdym z wierszy, dla których *wyrażenie* zwraca wartość true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

Poniższy przykład zwraca odpowiednie przesunięcia do dzielenia obiektu blob w formacie CSV.  Zobacz sekcję [Sys.Split](#sys-split) tego artykułu.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Typy danych

|Typ danych|Opis|
|---------|-------------------------------------------|
|INT      |64-bitowa podpisana integer.                     |
|Float    |64-bitowy ("podwójna precyzja") zmiennoprzecinkiem.|
|Ciąg   |Ciąg Unicode o zmiennej długości.            |
|Sygnatury czasowej|Punkt w czasie.                           |
|Boolean  |Prawda lub fałsz.                             |

Podczas odczytywania wartości z danych w formacie CSV wszystkie wartości są odczytywane jako ciągi.  Wartości ciągów mogą być konwertowane na inne typy przy użyciu wyrażeń CAST.  Wartości mogą być rzutowanie niejawnie do innych typów w zależności od kontekstu. Aby uzyskać więcej informacji, zobacz [Pierwszeństwo typu danych (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Wyrażenia

### <a name="referencing-fields"></a>Odwoływanie się do pól

W przypadku danych w formacie JSON lub danych w formacie CSV z wierszem nagłówka pola mogą być odwoływane za pomocą nazwy.  Nazwy pól mogą być cytowane lub niecytowane. Podane nazwy pól są ujęte w znaki cudzysłowu ("), mogą zawierać spacje i są rozróżniane.  Nazwy pól niecytowanych są niewrażliwe na wielkości liter i nie mogą zawierać żadnych znaków specjalnych.

W danych w formacie CSV pola mogą być również odwoływane przez porządkowe, poprzedzone znakiem podkreślenia (_).  Na przykład do pierwszego pola można odwoływać się jako _1 lub jedenaste pole może być przywołytą jako _11.  Odwoływanie się do pól przez porządkowe jest przydatne w przypadku danych w formacie CSV, które nie zawierają wiersza nagłówka, w którym to przypadku jedynym sposobem odwoływania się do określonego pola jest liczba porządkowa.

### <a name="operators"></a>Operatory

Obsługiwane są następujące standardowe operatory SQL:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Jeśli typy danych po lewej i prawej stronie operatora są różne, automatyczna konwersja będzie przeprowadzana zgodnie z regułami określonymi w tym miejscu: [Pierwszeństwo typu danych (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

Język SQL przyspieszania kwerendy obsługuje tylko bardzo mały podzbiór typów danych omówionych w tym artykule.  Zobacz sekcję [Typy danych](#data-types) tego artykułu.

### <a name="casts"></a>Rzuca

Język SQL przyspieszania kwerendy obsługuje operator CAST, zgodnie z regułami tutaj: [Konwersja typu danych (Aparat baz danych)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

Język SQL przyspieszania kwerendy obsługuje tylko niewielki podzbiór typów danych omówionych w tym artykule.  Zobacz sekcję [Typy danych](#data-types) tego artykułu.

### <a name="string-functions"></a>Funkcje ciągów

Język SQL przyspieszania kwerendy obsługuje następujące standardowe funkcje ciągu SQL:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Oto kilka przykładów:

|Funkcja|Przykład|Wynik|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

Funkcja [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) pomaga wyszukać wzorzec. Oto kilka przykładów, które używają funkcji [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) do ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``wyszukiwania ciągu danych .

|Zapytanie|Przykład|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Funkcje daty

Obsługiwane są następujące standardowe funkcje daty SQL:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Obecnie konwertujemy wszystkie [formaty dat standardowego IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD funkcja

Język SQL przyspieszania zapytania obsługuje rok, miesiąc, dzień, ``DATE_ADD`` godzinę, minutę, drugi dla funkcji.

Przykłady:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF funkcja

Język SQL przyspieszania zapytania obsługuje rok, miesiąc, dzień, ``DATE_DIFF`` godzinę, minutę, drugi dla funkcji.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>WYCIĄG, funkcja

W przypadku wyodrębniać inne ``DATE_ADD`` niż część daty obsługiwane dla funkcji, akceleracja kwerendy język SQL obsługuje timezone_hour i timezone_minute jako część daty.

Przykłady:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING funkcja

Przykłady:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

W tej tabeli opisano ciągi, których można ``TO_STRING`` użyć do określenia formatu wyjściowego funkcji.

|Ciąg formatu    |Dane wyjściowe                               |
|-----------------|-------------------------------------|
|RR               |Rok w formacie dwucyfrowym – 1999 jako "99"|
|t                |Rok w formacie 4-cyfrowym               |
|yyyy             |Rok w formacie 4-cyfrowym               |
|M                |Miesiąc roku – 1                    |
|MM               |Miesiąc wyściełany zero – 01               |
|MMM              |Abbr. miesiąc roku -JAN            |
|MMMM             |Pełny miesiąc – maj                      |
|d                |Dzień miesiąca (1-31)                  |
|dd               |Zero wyściełane dzień miesiąca (01-31)     |
|a                |AM lub PM                             |
|h                |Godzina dnia (1-12)                   |
|hh               |Zero wyściełane godziny od dnia (01-12)     |
|H                |Godzina dnia (0-23)                   |
|HH               |Zero wyściełane godzina dnia (00-23)      |
|m                |Minuta godziny (0-59)                |
|mm               |Minuta wyściełana (00-59)           |
|s                |Drugi z minut (0-59)             |
|ss               |Zero wyściełane sekundy (00-59)          |
|S                |Ułamek sekundy (0,1-0,9)        |
|SS               |Ułamek sekundy (0,01-0,99)      |
|Sss              |Ułamek sekundy (0,001-0,999)    |
|X                |Przesunięcie w godzinach                      |
|XX lub XXXX       |Przesunięcie w godzinach i minutach (+0430)  |
|XXX lub XXXXX     |Przesunięcie w godzinach i minutach (-07:00) |
|x                |Przesunięcie w godzinach (7)                  |
|xx lub xxxx       |Przesunięcie w godzinę i minutę (+0530)    |
|Xxx lub xxxxx     |Przesunięcie w godzinę i minutę (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP funkcja

Obsługiwane są tylko formaty IS08601.

Przykłady:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Można również użyć ``UTCNOW`` tej funkcji, aby uzyskać czas systemowy.


## <a name="aggregate-expressions"></a>Wyrażenia agregucjonauraz

Instrukcja SELECT może zawierać jedno lub więcej wyrażeń rzutowania lub pojedyncze wyrażenie agregowane.  Obsługiwane są następujące wyrażenia agregacji:

|Wyrażenie|Opis|
|--|--|
|[ZLICZ(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Zwraca liczbę rekordów, które odpowiadały wyrażeniu predykatu.|
|[COUNT(wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Zwraca liczbę rekordów, dla których wyrażenie jest niezerowe.|
|[ŚREDNIA(wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Zwraca średnią wartości wyrażenia innych niż null.|
|[MIN(wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Zwraca minimalną wartość wyrażenia innej niż null.|
|[MAX(wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |Zwraca maksymalną wartość wyrażenia innej niż null.|
|[SUMA(wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Zwraca sumę wszystkich wartości wyrażenia innych niż null.|

### <a name="missing"></a>Brakuje

Operator ``IS MISSING`` jest jedynym niestandardowym, który obsługuje język SQL przyspieszenia kwerendy.  W przypadku danych JSON, jeśli w określonym rekordzie ``IS MISSING`` wejściowym brakuje pola, pole wyrażenia zostanie ocenione jako wartość logiczna true.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Deskryptory tabeli

W przypadku danych CSV nazwa `BlobStorage`tabeli jest zawsze .  Przykład:

```sql
SELECT * FROM BlobStorage
```

W przypadku danych JSON dostępne są dodatkowe opcje:

```sql
SELECT * FROM BlobStorage[*].path
```

Dzięki temu zapytania dotyczące podzbiorów danych JSON.

W przypadku zapytań JSON można wspomnieć o ścieżce w części klauzuli FROM. Te ścieżki pomogą przeanalizować podzbiór danych JSON. Ścieżki te mogą odwoływać się do wartości tablicy JSON i object.

Weźmy przykład, aby zrozumieć to bardziej szczegółowo.

Oto nasze przykładowe dane:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Możesz być zainteresowany tylko `warehouses` obiekt JSON z powyższych danych. Obiekt `warehouses` jest typem tablicy JSON, więc można o tym wspomnieć w klauzuli FROM. Przykładowe zapytanie może wyglądać mniej więcej tak.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Kwerenda pobiera wszystkie pola, ale wybiera tylko szerokość geograficzną.

Jeśli chcesz uzyskać dostęp `dimensions` tylko do wartości obiektu JSON, można użyć odwołać się do tego obiektu w kwerendzie. Przykład:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Ogranicza to również dostęp do `dimensions` członków obiektu. Jeśli chcesz uzyskać dostęp do innych elementów członkowskich pól JSON i wartości wewnętrznych obiektów JSON, możesz użyć kwerendy, takiej jak pokazano w poniższym przykładzie:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage i BlobStorage[\*] odnoszą się do całego obiektu. Jeśli jednak masz ścieżkę w klauzuli FROM, musisz użyć funkcji BlobStorage[\*].

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split

Jest to specjalna forma instrukcji SELECT, która jest dostępna tylko dla danych w formacie CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Ta instrukcja jest używana w przypadkach, gdy chcesz pobrać, a następnie przetwarzać rekordy danych CSV w partiach. W ten sposób można przetwarzać rekordy równolegle, zamiast pobierać wszystkie rekordy jednocześnie. Ta instrukcja nie zwraca rekordów z pliku CSV. Zamiast tego zwraca kolekcję rozmiarów partii. Następnie można użyć każdego rozmiaru partii, aby pobrać partię rekordów danych. 

Parametr *split_size* służy do określania liczby bajtów, które mają zawierać każda partia. Na przykład jeśli chcesz przetworzyć tylko 10 MB danych naraz, instrukcja `SELECT sys.split(10485760)FROM BlobStorage` będzie wyglądać tak: ponieważ 10 MB jest równa 10,485,760 bajtów. Każda partia będzie zawierać tyle rekordów, ile może zmieścić się w tych 10 MB. 

W większości przypadków rozmiar każdej partii będzie nieco wyższy niż liczba określona. Dzieje się tak, ponieważ partia nie może zawierać rekordu częściowego. Jeśli ostatni rekord w partii rozpoczyna się przed końcem progu, partia będzie większa, dzięki czemu może zawierać pełny rekord. Rozmiar ostatniej partii będzie prawdopodobnie mniejszy niż rozmiar określony.

>[!NOTE]
> split_size musi wynosić co najmniej 10 MB (10485760).

## <a name="see-also"></a>Zobacz też

- [Akceleracja zapytań usługi Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration.md)
- [Filtrowanie danych przy użyciu akceleracji zapytań usługi Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration-how-to.md)

