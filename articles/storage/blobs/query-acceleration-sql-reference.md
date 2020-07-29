---
title: Informacje dotyczące języka SQL przyspieszania zapytań (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak używać składni SQL Acceleration Query.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 3408970bcf5e34ce9f0f0afe9e723b4877dcd694
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193406"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Informacje dotyczące języka SQL przyspieszania zapytań (wersja zapoznawcza)

Przyspieszenie zapytań obsługuje język podobny do ANSI SQL, który umożliwia tworzenie zapytań dotyczących zawartości obiektu BLOB.  Dialekt SQL przyspieszania zapytań jest podzbiorem ANSI SQL, z ograniczonym zestawem obsługiwanych typów danych, operatorów itp., ale również rozszerza się w standardzie ANSI SQL, aby obsługiwał zapytania za pośrednictwem hierarchicznych formatów danych z częściową strukturą, takich jak JSON. 

> [!NOTE]
> Funkcja przyspieszenia zapytań jest w publicznej wersji zapoznawczej i jest dostępna w regionach Kanada Środkowa i Francja środkowa. Aby zapoznać się z ograniczeniami, zobacz artykuł [znane problemy](data-lake-storage-known-issues.md) . Aby zarejestrować się w wersji zapoznawczej, zobacz [ten formularz](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>Wybierz składnię

Jedyną instrukcją SQL obsługiwaną przez przyspieszenie zapytania jest instrukcja SELECT. Ten przykład zwraca wszystkie wiersze, dla których wyrażenie zwraca wartość true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

W przypadku danych w formacie CSV *tabela* musi być `BlobStorage` .  Oznacza to, że zapytanie zostanie uruchomione względem tego, który obiekt BLOB został określony w wywołaniu REST.
W przypadku danych w formacie JSON *tabela* jest "deskryptorem tabeli".   Zobacz sekcję [deskryptory tabeli](#table-descriptors) w tym artykule.

W poniższym przykładzie dla każdego wiersza, dla którego *wyrażenie* WHERE zwraca wartość true, ta instrukcja zwróci nowy wiersz, który jest wykonywany przy obliczaniu każdego z wyrażeń projekcji.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Poniższy przykład zwraca obliczenia zagregowane (na przykład wartość średnia określonej kolumny) nad każdym wierszem, dla którego *wyrażenie* zwraca wartość true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

Poniższy przykład zwraca odpowiednie przesunięcia dla dzielenia obiektu BLOB sformatowanego w formacie CSV.  Zobacz sekcję [sys. Split](#sys-split) w tym artykule.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Typy danych

|Typ danych|Opis|
|---------|-------------------------------------------|
|INT      |64-bitowa liczba całkowita ze znakiem.                     |
|FLOAT    |64-bitowa ("Podwójna precyzja") liczba zmiennoprzecinkowa.|
|PARAMETRY   |Ciąg Unicode o zmiennej długości.            |
|ZNACZNIK czasu|Punkt w czasie.                           |
|TYPU  |Prawda lub fałsz.                             |

Podczas odczytywania wartości z danych w formacie CSV wszystkie wartości są odczytywane jako ciągi.  Wartości ciągu mogą być konwertowane na inne typy przy użyciu wyrażeń RZUTowania.  Wartości mogą być niejawnie rzutowane na inne typy w zależności od kontekstu. Aby uzyskać więcej informacji, zobacz [pierwszeństwo typów danych (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Wyrażenia

### <a name="referencing-fields"></a>Odwołania do pól

W przypadku danych w formacie JSON lub danych w formacie CSV z wierszem nagłówka pola mogą być przywoływane przez nazwę.  Nazwy pól mogą być ujęte w cudzysłów lub bez cudzysłowów. Nazwy pól w cudzysłowie są ujęte w znaki podwójnego cudzysłowu ("), mogą zawierać spacje i uwzględniać wielkość liter.  Nazwy pól bez cudzysłowów nie uwzględniają wielkości liter i nie mogą zawierać żadnych znaków specjalnych.

W danych w formacie CSV pola mogą być również przywoływane przez liczebniki, poprzedzone znakiem podkreślenia (_).  Na przykład pierwsze pole może być przywoływane jako _1 lub jedenaste pole może być przywoływane jako _11.  Odwołania do pól według liczby porządkowej są przydatne w przypadku danych w formacie CSV, które nie zawierają wiersza nagłówka, w takim przypadku jedynym sposobem odwoływania się do określonego pola jest według liczby porządkowej.

### <a name="operators"></a>Operatory

Obsługiwane są następujące standardowe operatory SQL:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Jeśli typy danych po lewej i prawej stronie operatora są inne, konwersja automatyczna będzie wykonywana zgodnie z regułami określonymi tutaj: [pierwszeństwo typów danych (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

Język SQL przyspieszania zapytań obsługuje tylko bardzo mały podzestaw typów danych opisanych w tym artykule.  Zobacz sekcję [typy danych](#data-types) w tym artykule.

### <a name="casts"></a>Rzutowania

Język SQL przyspieszania zapytań obsługuje operator CAST, zgodnie z regułami w tym miejscu: [Konwersja typu danych (aparat bazy danych)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

Język SQL przyspieszania zapytań obsługuje tylko niewielki podzbiór typów danych omówionych w tym artykule.  Zobacz sekcję [typy danych](#data-types) w tym artykule.

### <a name="string-functions"></a>Funkcje ciągów

Język SQL przyspieszania zapytań obsługuje następujące standardowe funkcje ciągów SQL:

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

Funkcja [like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) ułatwia wyszukiwanie wzorca. Oto kilka przykładów, które wykorzystują funkcję [like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) do wyszukiwania ciągu danych ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i `` .

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

Obecnie Konwertujemy wszystkie [formaty daty standardowego IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>Funkcja DATE_ADD

Język SQL przyspieszania zapytań obsługuje rok, miesiąc, dzień, godzinę, minutę, sekundę dla ``DATE_ADD`` funkcji.

Przykłady:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>Funkcja DATE_DIFF

Język SQL przyspieszania zapytań obsługuje rok, miesiąc, dzień, godzinę, minutę, sekundę dla ``DATE_DIFF`` funkcji.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT — funkcja

W przypadku WYODRĘBNIenia innego niż część daty obsługiwana dla tej ``DATE_ADD`` funkcji język SQL przyspieszania zapytań obsługuje timezone_hour i timezone_minute jako część daty.

Przykłady:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>Funkcja TO_STRING

Przykłady:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

W tej tabeli opisano ciągi, których można użyć do określenia formatu danych wyjściowych ``TO_STRING`` funkcji.

|Ciąg formatu    |Dane wyjściowe                               |
|-----------------|-------------------------------------|
|RR               |Rok w formacie 2-cyfrowym — 1999 jako "99"|
|Y                |Rok w formacie 4-cyfrowym               |
|yyyy             |Rok w formacie 4-cyfrowym               |
|M                |Miesiąc roku — 1                    |
|MM               |Miesiąc uzupełniony o zero — 01               |
|MMM              |Abbr. miesiąc roku — sty            |
|MMMM             |Pełny miesiąc — może                      |
|d                |Dzień miesiąca (1-31)                  |
|dd               |Zerowy dzień miesiąca (01-31)     |
|a                |AM lub PM                             |
|h                |Godzina dnia (1-12)                   |
|hh               |Zero uzupełnionych godzin od dnia (01-12)     |
|H                |Godzina dnia (0-23)                   |
|HH               |Zero z uzupełnioną godziną dnia (00-23)      |
|m                |Minuta godziny (0-59)                |
|mm               |Zero uzupełnionej minuty (00-59)           |
|s                |Sekunda (0-59)             |
|ss               |Zero uzupełnione s (00-59)          |
|S                |Ułamek sekund (0,1 – 0.9)        |
|SS               |Ułamek sekund (0,01-0,99)      |
|BEZPIECZNEGO              |Ułamek sekund (0,001-0.999)    |
|X                |Przesunięcie w godzinach                      |
|XX lub XXXX       |Przesunięcie w godzinach i minutach (+ 0430)  |
|XXX lub XXXXX     |Przesunięcie w godzinach i minutach (-07:00) |
|x                |Przesunięcie w godzinach (7)                  |
|XX lub xxxx       |Przesunięcie w ciągu godziny i minuty (+ 0530)    |
|XXX lub XXXXX     |Przesunięcie w ciągu godziny i minuty (+ 05:30)   |

#### <a name="to_timestamp-function"></a>Funkcja TO_TIMESTAMP

Obsługiwane są tylko formaty IS08601.

Przykłady:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Możesz również użyć funkcji, ``UTCNOW`` Aby uzyskać czas systemowy.


## <a name="aggregate-expressions"></a>Wyrażenia agregujące

Instrukcja SELECT może zawierać jedno lub więcej wyrażeń projekcji lub pojedyncze wyrażenie agregujące.  Obsługiwane są następujące wyrażenia agregujące:

|Wyrażenie|Opis|
|--|--|
|[COUNT ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Zwraca liczbę rekordów, które pasują do wyrażenia predykatu.|
|[COUNT (wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Zwraca liczbę rekordów, dla których wyrażenie jest inne niż null.|
|[Średnia (wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Zwraca średnią wartości wyrażenia inne niż null.|
|[MIN (wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Zwraca minimalną wartość wyrażenia o wartości innej niż null.|
|[Max (wyrażenie](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |Zwraca maksymalną wartość różną od null wyrażenia.|
|[SUM (wyrażenie)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Zwraca sumę wszystkich wartości innych niż null wyrażenia.|

### <a name="missing"></a>NIEDOSTĘPNY

``IS MISSING``Operator jest jedynym niestandardowym, który obsługuje język SQL przyspieszania zapytań.  W przypadku danych JSON w przypadku braku pola w określonym rekordzie wejściowym pole Expression ``IS MISSING`` zostanie obliczone do wartości logicznej true.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Deskryptory tabeli

W przypadku danych CSV nazwa tabeli jest zawsze `BlobStorage` .  Przykład:

```sql
SELECT * FROM BlobStorage
```

W przypadku danych JSON dostępne są dodatkowe opcje:

```sql
SELECT * FROM BlobStorage[*].path
```

Pozwala to na zapytania przez podzbiory danych JSON.

W przypadku zapytań JSON można wspominać o ścieżce w części klauzuli FROM. Ścieżki te pomogą analizować podzestaw danych JSON. Ścieżki te mogą odwoływać się do wartości tablicy i obiektów JSON.

Przyjrzyjmy się przykładowi bardziej szczegółowo.

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

Może zainteresować tylko `warehouses` obiekt JSON z powyższych danych. `warehouses`Obiekt jest typem tablicy JSON, więc można go wspominać w klauzuli FROM. Przykładowe zapytanie może wyglądać podobnie do tego.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Zapytanie pobiera wszystkie pola, ale wybiera tylko szerokość geograficzną.

Jeśli chcesz uzyskać dostęp tylko do `dimensions` wartości obiektu JSON, możesz użyć odwołującego się do tego obiektu w zapytaniu. Przykład:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Pozwala to również ograniczyć dostęp do elementów członkowskich `dimensions` obiektu. Jeśli chcesz uzyskać dostęp do innych elementów członkowskich pól JSON i wewnętrznych wartości obiektów JSON, możesz użyć zapytań, takich jak pokazano w następującym przykładzie:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage i BlobStorage [ \* ] odnoszą się do całego obiektu. Jeśli jednak ścieżka znajduje się w klauzuli FROM, należy użyć BlobStorage [ \* ]. Path

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. Split

Jest to specjalna forma instrukcji SELECT, która jest dostępna tylko dla danych w formacie CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Użyj tej instrukcji w przypadkach, w których chcesz pobrać, a następnie przetworzyć rekordy danych CSV w partiach. Dzięki temu możesz przetwarzać rekordy równolegle, zamiast pobierać wszystkie rekordy jednocześnie. Ta instrukcja nie zwraca rekordów z pliku CSV. Zamiast tego zwraca kolekcję rozmiarów partii. Następnie można użyć każdego rozmiaru partii do pobrania partii rekordów danych. 

Użyj parametru *split_size* , aby określić liczbę bajtów, które mają zawierać każda partia zadań. Na przykład jeśli chcesz przetworzyć tylko 10 MB danych jednocześnie, będzie to wyglądać następująco:, `SELECT sys.split(10485760)FROM BlobStorage` ponieważ 10 MB jest równe 10 485 760 bajtów. Każda partia zadań będzie zawierać tyle rekordów, ile można dopasować do tych 10 MB. 

W większości przypadków rozmiar każdej partii będzie nieco większy niż określona liczba. Wynika to z faktu, że partia nie może zawierać rekordu częściowego. Jeśli ostatni rekord w partii zaczyna się przed końcem progu, partia będzie większa, aby mogła zawierać cały rekord. Rozmiar ostatniej partii będzie prawdopodobnie mniejszy niż określony rozmiar.

>[!NOTE]
> Split_size musi być co najmniej 10 MB (10485760).

## <a name="see-also"></a>Zobacz także

- [Przyspieszenie zapytań Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration.md)
- [Filtrowanie danych przy użyciu przyspieszania zapytań Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration-how-to.md)

