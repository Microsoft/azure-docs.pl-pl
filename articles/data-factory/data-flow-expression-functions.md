---
title: Funkcje wyrażeń w przepływie danych mapowania
description: Dowiedz się więcej o funkcjach wyrażeń w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/04/2021
ms.openlocfilehash: ec1ea7d727278076944b8cc11f47a1af587e6591
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440160"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Wyrażenia transformacji danych w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="expression-functions"></a>Funkcje wyrażenia

W Data Factory Użyj języka wyrażeń funkcji mapowanie przepływu danych, aby skonfigurować przekształcenia danych.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Wartość bezwzględna liczby.  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość cosinusa odwrotnego.  
* ``acos(1) -> 0.0``  
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dodaje parę ciągów lub cyfr. Dodaje datę do kilku dni. Dodaje czas trwania do sygnatury czasowej. Dołącza jedną tablicę podobnego typu do innej. Analogicznie jak operator +.  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Dodaj dni do daty lub sygnatury czasowej. Taki sam jak operator + dla daty.  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Dodaj miesiące do daty lub sygnatury czasowej. Opcjonalnie można przekazać strefę czasową.  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operator logiczny i. Taki sam jak &&.  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza odwrotną wartość sinusa.  
* ``asin(0) -> 0.0``  
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza odwrotną wartość styczną.  
* ``atan(0) -> 0.0``  
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Zwraca kąt w radianach między dodatnią osią x płaszczyzny a punktem podanym przez współrzędne.  
* ``atan2(0, 0) -> 0.0``  
___
### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
Sprawdza, czy pierwsza wartość znajduje się między dwiema innymi wartościami włącznie. Wartości liczbowe, ciąg i DateTime można porównać * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___
### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Operatory bitowe i typu całkowitego. Analogicznie jak operator & * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___
### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Operator bitowy OR w ramach typów całkowitych. Analogicznie jak | zakład * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___
### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Operator bitowy OR w ramach typów całkowitych. Analogicznie jak | zakład * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___
### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Oblicza pliku blake2 Digest zestawu kolumn o różnych typach danych pierwotnych o długości bitowej, która może być wielokrotnością 8 z przedziału 8 & 512. Może służyć do obliczania odcisku palca dla wiersza * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___
### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
Oblicza pliku blake2 Digest zestawu kolumn o różnych typach danych pierwotnych o długości bitowej, która może być wielokrotnością 8 z przedziału 8 & 512. Może służyć do obliczania odcisku palca dla wiersza * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Na podstawie zmiennych warunków stosuje jedną wartość lub drugą. Jeśli liczba danych wejściowych jest parzysta, druga z nich ma wartość NULL dla ostatniego warunku.  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza pierwiastek modułu dla liczby.  
* ``cbrt(8) -> 2.0``  
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Zwraca najmniejszą liczbę całkowitą nie mniejszą od liczby.  
* ``ceil(-0.1) -> 0``  
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Zwraca pierwszą wartość spoza wartości null z zestawu danych wejściowych. Wszystkie dane wejściowe powinny być tego samego typu.  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___
### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
Zbiera wszystkie wartości wyrażenia w agregowanej grupie do tablicy. Struktury mogą być zbierane i przekształcane w alternatywne struktury w trakcie tego procesu. Liczba elementów będzie równa liczbie wierszy w tej grupie i może zawierać wartości null. Liczba zebranych elementów powinna być mała.  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___
### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
Pobiera wszystkie kolumny wyjściowe dla strumienia. Opcjonalną nazwę strumienia można przekazać jako drugi argument.  
* ``columnNames()``
* ``columnNames('DeriveStream')``

___
### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Pobiera wszystkie kolumny wyjściowe dla strumienia. Opcjonalną nazwę strumienia można przekazać jako drugi argument.   
* ``columns()``
* ``columns('DeriveStream')``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Porównuje dwie wartości tego samego typu. Zwraca ujemną liczbę całkowitą, jeśli wartość1 < wartość2, 0 w przypadku Wartość1 = = wartość2, wartość dodatnia, jeśli wartość1 > wartość2.  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Łączy jednocześnie zmienną liczbę ciągów. Analogicznie jak operator + z ciągami.  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Łączy zmienną liczbę ciągów wraz z separatorem. Pierwszy parametr jest separatorem.  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Zwraca wartość true, jeśli dowolny element w podanej tablicy ma wartość true w podanym predykacie. Zawiera oczekiwane odwołanie do jednego elementu w funkcji predykatu jako #item.  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość cosinusa.  
* ``cos(10) -> -0.8390715290764524``  
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza cosinus hiperboliczny wartości.  
* ``cosh(0) -> 1.0``  
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Oblicza wartość skrótu CRC32 zestawu kolumn o różnych typach danych pierwotnych o długości bitowej, która może zawierać tylko wartości 0 (256), 224, 256, 384, 512. Można go użyć do obliczenia odcisku palca dla wiersza.  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Pobiera bieżącą datę, kiedy to zadanie zostanie uruchomione. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Pobiera bieżącą sygnaturę czasową, gdy zadanie zaczyna działać przy użyciu lokalnej strefy czasowej.  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Pobiera bieżącą sygnaturę czasową jako UTC. Jeśli bieżący czas ma być interpretowany w innej strefie czasowej niż strefa czasowa klastra, można przekazać opcjonalną strefę czasu w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Domyślnie jest to Bieżąca strefa czasowa. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). W celu przekonwertowania czasu UTC na inne użycie strefy czasowej `fromUTC()` .  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera dzień miesiąca, w którym nadana została dana data.  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera dzień tygodnia, w którym nadana została dana data. 1 — niedziela, 2-poniedziałek..., 7-Sobota.  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera dzień roku danego dnia.  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Czas trwania (w milisekundach) dla liczby dni.  
* ``days(2) -> 172800000L``  
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Konwertuje radiany na stopnie.  
* ``degrees(3.141592653589793) -> 180``  
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dzieli parę liczb. Analogicznie jak `/` operator.  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``  
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, czy ciąg jest zakończony przy użyciu podanego ciągu.  
* ``endsWith('dumbo', 'mbo') -> true``  
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie operatora równości. Analogicznie jak operator = =.  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Operator porównania jest ignorowany wielkości liter. Analogicznie jak operator <=>.  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___
### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Wyprowadza ciąg zgodnie z formatem. Wartości literałów dla akceptowalnego formatu to "JSON", "XML", "ECMAScript", "HTML", "Java".
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Oblicza silnię liczby.  
* ``factorial(5) -> 120``  
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Zawsze zwraca wartość false. Użyj funkcji, `syntax(false())` Jeśli istnieje kolumna o nazwie "false".  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Zwraca największą liczbę całkowitą nie większą niż liczba.  
* ``floor(-0.1) -> -1``  
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Koduje dany ciąg w formacie base64.  
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konwertuje na sygnaturę czasową z czasu UTC. Opcjonalnie można przekazać strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Domyślnie jest to Bieżąca strefa czasowa. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator porównywania porównania. Analogicznie jak operator >.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie z operatorem większym niż lub równym. Analogicznie jak operator >=.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Zwraca największą wartość z listy wartości jako dane wejściowe pomijające wartości null. Zwraca wartość null, jeśli wszystkie dane wejściowe mają wartość null.  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Sprawdza wartość kolumny według nazwy w strumieniu. Opcjonalną nazwę strumienia można przekazać jako drugi argument. Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów.  
* ``hasColumn('parent')``  
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Pobiera wartość godziny dla sygnatury czasowej. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Czas trwania (w milisekundach) dla liczby godzin.  
* ``hours(2) -> 7200000L``  
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
W oparciu o warunek stosuje jedną wartość lub drugą. Jeśli inne nie określono, jest uznawane za NULL. Obie wartości muszą być zgodne (numeryczne, ciąg...). * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Sprawdza, czy pierwszy parametr ma wartość null. Jeśli wartość nie jest równa null, zwracany jest pierwszy parametr. Jeśli wartość jest równa null, zwracany jest drugi parametr. Jeśli określono trzy parametry, zachowanie jest takie samo jak IIf (isNull (wartość1), wartość2, Wartość3) i trzeci parametr jest zwracany, jeśli pierwsza wartość nie jest równa null.  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Sprawdza, czy element znajduje się w tablicy.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Konwertuje pierwszą literę każdego wyrazu na wielkie litery. Wyrazy są identyfikowane jako oddzielone odstępami.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Znajduje pozycję (1) podciągu w ciągu. Zwraca wartość 0, jeśli nie znaleziono.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy wiersz jest oznaczony do usunięcia. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Indeksem strumienia powinna być 1 lub 2, a wartość domyślna to 1.  
* ``isDelete()``  
* ``isDelete(1)``  
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy wiersz jest oznaczony jako błąd. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Indeksem strumienia powinna być 1 lub 2, a wartość domyślna to 1.  
* ``isError()``  
* ``isError(1)``  
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy wiersz jest oznaczony jako ignorowany. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Indeksem strumienia powinna być 1 lub 2, a wartość domyślna to 1.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy wiersz jest oznaczony do wstawienia. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Indeksem strumienia powinna być 1 lub 2, a wartość domyślna to 1.  
* ``isInsert()``  
* ``isInsert(1)``  
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy wiersz jest dopasowany podczas wyszukiwania. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Indeksem strumienia powinna być 1 lub 2, a wartość domyślna to 1.  
* ``isMatch()``  
* ``isMatch(1)``  
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Sprawdza, czy wartość jest RÓWNa NULL.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy wiersz jest oznaczony do aktualizacji. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Indeksem strumienia powinna być 1 lub 2, a wartość domyślna to 1.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy wiersz jest oznaczony do wstawienia. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Indeksem strumienia powinna być 1 lub 2, a wartość domyślna to 1.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Pobiera datę i ostatnią datę miesiąca.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Porównanie z operatorem mniejszym niż lub równym. Analogicznie jak operator <=.  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Wyodrębnij podciąg, rozpoczynając od indeksu 1 o liczbie znaków. Analogicznie jak podciąg (str, 1, n).  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Zwraca długość ciągu.  
* ``length('dumbo') -> 5``  
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator minus porównania. Analogicznie jak operator <.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie z operatorem mniejszym niż lub równym. Analogicznie jak operator <=.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Pobiera odległość Levenshtein między dwoma ciągami.  
* ``levenshtein('boys', 'girls') -> 4``  
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Wzorzec jest ciągiem, który jest dopasowany dosłownie. Wyjątki są następujące specjalne symbole: _ dopasowuje dowolny znak w danych wejściowych (podobny do. w ```posix``` wyrażeniach regularnych)% dopasowuje zero lub więcej znaków w danych wejściowych (podobnie jak. * w ```posix``` wyrażeniach regularnych).
Znak ucieczki to "". Jeśli znak ucieczki poprzedza symbol specjalny lub inny znak ucieczki, poniższy znak jest dopasowany dosłownie. Nie można użyć żadnego znaku ucieczki.  
* ``like('icecream', 'ice%') -> true``  
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Znajduje pozycję (1) podciągu w ciągu, rozpoczynając od określonej pozycji. Jeśli pozycja zostanie pominięta, jest uznawana za od początku ciągu. Zwraca wartość 0, jeśli nie znaleziono.  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Oblicza wartość dziennika. Opcjonalnie można podać numer Euler, jeśli jest używany.  
* ``log(100, 10) -> 2``  
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość dziennika na podstawie 10 podstawowych.  
* ``log10(100) -> 2``  
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Małe litery ciągu.  
* ``lower('GunChus') -> 'gunchus'``  
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Po lewej stronie jest to ciąg, który został dostarczony przez podane uzupełnienie, dopóki nie ma określonej długości. Jeśli ciąg jest równy lub większy niż długość, zostanie przycięty do długości.  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* "" LPAD ("Dumbo", 8, "<>") — > "<><dumbo'``  
___
### <code> LTRIM</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Lewy przycina ciąg znaków wiodących. Jeśli drugi parametr jest nieokreślony, przycina odstępy. W przeciwnym razie przycinanie dowolnego znaku określonego w drugim parametrze.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Oblicza skrót MD5 zestawu kolumn o różnych typach danych pierwotnych i zwraca ciąg szesnastkowy o 32 znaku. Można go użyć do obliczenia odcisku palca dla wiersza.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Pobiera wartość milisekundy daty. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Czas trwania (w milisekundach) dla liczby milisekund.  
* ``milliseconds(2) -> 2L``  
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Odejmuje liczby. Odejmij liczbę dni od daty. Odejmij czas trwania z sygnatury czasowej. Odejmij dwie sygnatury czasowe, aby uzyskać różnicę w milisekundach. Analogicznie jak operator.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Pobiera wartość minuty sygnatury czasowej. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Czas trwania (w milisekundach) dla liczby minut.  
* ``minutes(2) -> 120000L``  
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Moduł par numerów. Analogicznie jak operator%.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera wartość miesiąca daty lub sygnatury czasowej.  
* ``month(toDate('2012-8-8')) -> 8``  
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Pobiera liczbę miesięcy między dwiema datami. Obliczenia można zaokrąglić. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Mnoży parę liczb. Analogicznie jak operator *.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negacja liczby. Włącza liczbę dodatnią na wartość ujemną i odwrotnie.  
* ``negate(13) -> -13``  
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Zwraca następną unikatową sekwencję. Liczba występuje tylko po kolei w obrębie partycji i jest poprzedzona prefiksem.  
* ``nextSequence() == 12313112 -> false``  
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizuje wartość ciągu w celu oddzielenia znaków akcentu Unicode.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``  
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logiczny operator negacji.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie operatora not Equals. Taki sam jak operator! =.  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Sprawdza, czy wartość nie jest RÓWNa NULL.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Zwraca wartość NULL. Użyj funkcji, `syntax(null())` Jeśli istnieje kolumna o nazwie "null". Każda operacja, która używa, spowoduje wartość NULL.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operator logiczny OR. Analogicznie jak | |.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dodatnia para par liczb.  
* ``pmod(-20, 8) -> 4``  
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Zwraca bieżący identyfikator partycji, w której znajduje się wiersz wejściowy.  
* ``partitionId()``  
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Podnosi jedną liczbę do potęgi innej.  
* ``power(10, 2) -> 100``  
___
### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
Zwraca liczbę losową z podanym opcjonalnym inicjatorem w partycji. Inicjator powinien być wartością stałą i jest używany w połączeniu z identyfikatorem partitionId, aby generować wartości losowe * ``random(1) == 1 -> false``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Wyodrębnij pasujący podciąg dla danego wzorca wyrażenia regularnego. Ostatni parametr identyfikuje grupę dopasowania i domyślnie ma wartość 1 w przypadku pominięcia. Użyj " <regex> " (cytat tyłu), aby dopasować ciąg bez ucieczki.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, czy ciąg jest zgodny z danym wzorcem wyrażenia regularnego. Użyj " <regex> " (cytat tyłu), aby dopasować ciąg bez ucieczki.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Zamień wszystkie wystąpienia wzorca wyrażenia regularnego na inny podciąg w danym ciągu Użyj " <regex> " (cudzysłów tylny), aby dopasować ciąg bez ucieczki.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Dzieli ciąg na podstawie ogranicznika na podstawie wyrażenia regularnego i zwraca tablicę ciągów.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Zamień wszystkie wystąpienia podciągu na inny podciąg w podanym ciągu. Jeśli ostatni parametr zostanie pominięty, jest on domyślnie ciągiem pustym.  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Odwraca ciąg.  
* ``reverse('gunchus') -> 'suhcnug'``  
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Wyodrębnia podciąg z liczbą znaków z prawej strony. Analogicznie jak podciąg (str, LENGTH (str)-n, n).  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, czy ciąg jest zgodny z danym wzorcem wyrażenia regularnego.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Zaokrągla liczbę w postaci opcjonalnej skali i opcjonalnego trybu zaokrąglania. W przypadku pominięcia skali wartość domyślna to 0. Jeśli tryb zostanie pominięty, zostanie przyjmowana wartość domyślna ROUND_HALF_UP (5). Wartości zaokrąglania obejmują 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Prawe konsole do ciągu przez dostarczone uzupełnienie, dopóki nie ma określonej długości. Jeśli ciąg jest równy lub większy niż długość, zostanie przycięty do długości.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___
### <code>rtrim</code>RTRIM</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Prawy przycina ciąg znaków końcowych. Jeśli drugi parametr jest nieokreślony, przycina odstępy. W przeciwnym razie przycinanie dowolnego znaku określonego w drugim parametrze.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Pobiera drugą wartość daty. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Czas trwania w milisekundach przez liczbę sekund.  
* ``seconds(2) -> 2000L``  
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Oblicza skrót SHA-1 zestawu kolumn o różnych typach danych pierwotnych i zwraca ciąg szesnastkowy znaku 40. Można go użyć do obliczenia odcisku palca dla wiersza.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Oblicza skrót SHA-2 zestawu kolumn o różnych typach danych pierwotnych o długości bitowej, która może zawierać tylko wartości 0 (256), 224, 256, 384, 512. Można go użyć do obliczenia odcisku palca dla wiersza.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość sinusa.  
* ``sin(2) -> 0.9092974268256817``  
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość sinus hiperboliczny.  
* ``sinh(0) -> 0.0``  
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Pobiera ```soundex``` kod dla ciągu.  
* ``soundex('genius') -> 'G520'``  
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Dzieli ciąg na podstawie ogranicznika i zwraca tablicę ciągów.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza pierwiastek kwadratowy liczby.  
* ``sqrt(9) -> 3``  
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, czy ciąg rozpoczyna się od podanego ciągu.  
* ``startsWith('dumbo', 'du') -> true``  
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odejmij dni od daty lub sygnatury czasowej. Analogicznie jak operator-dla daty.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odejmij miesiące od daty lub sygnatury czasowej.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Wyodrębnia podciąg o określonej długości z położenia. Pozycja ma 1 na podstawie. Jeśli długość zostanie pominięta, jest ona domyślnie zakończona ciągiem.  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość styczną.  
* ``tan(0) -> 0.0``  
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość tangensa hiperbolicznego.  
* ``tanh(0) -> 0.0``  
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Zastąp jeden zestaw znaków innym zestawem znaków w ciągu. Znaki mają od 1 do 1 zamianę.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Przycina ciąg wiodących i końcowych znaków. Jeśli drugi parametr jest nieokreślony, przycina odstępy. W przeciwnym razie przycinanie dowolnego znaku określonego w drugim parametrze.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Zawsze zwraca wartość true. Użyj funkcji, `syntax(true())` Jeśli istnieje kolumna o nazwie "true".  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Dopasowuje typ kolumny. Mogą być używane tylko w wyrażeniach wzorców. liczba jest zgodna z typem Short, Integer, Long, Double, float lub decimal, całk dopasowuje Short, Integer, Long, ułamkowe dopasowanie Double, float, Decimal i DateTime dopasowuje typ daty lub sygnatury czasowej.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___
### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Niewyprowadza ciąg w formacie. Wartości literałów dla akceptowalnego formatu to "JSON", "XML", "ECMAScript", "HTML", "Java".
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Wielkie litery ciągu.  
* ``upper('bojjus') -> 'BOJJUS'``  
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Zwraca wygenerowany identyfikator UUID.  
* ``uuid()``  
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera tydzień roku danego dnia.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Czas trwania (w milisekundach) dla liczby tygodni.  
* ``weeks(2) -> 1209600000L``  
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logiczny operator XOR. Taki sam jak operator ^.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera wartość year dla daty.  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="aggregate-functions"></a>Funkcje agregujące
Poniższe funkcje są dostępne tylko w transformacjach agregacji, tabeli przestawnej, operatora UNPIVOT i oknach.
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera średnią wartości w kolumnie.  
* ``avg(sales)``  
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryteriów pobiera średnią wartości kolumny.  
* ``avgIf(region == 'West', sales)``  
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Pobiera zagregowaną liczbę wartości. Jeśli kolumny opcjonalne są określone, ignoruje wartości NULL w liczbie.  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Pobiera zagregowaną liczbę różnych wartości zestawu kolumn.  
* ``countDistinct(custId, custName)``  
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Na podstawie kryteriów pobiera zagregowaną liczbę wartości. Jeśli opcjonalna kolumna jest określona, ignoruje wartości NULL w liczbie.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Pobiera kowariancję populacji między dwiema kolumnami.  
* ``covariancePopulation(sales, profit)``  
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera kowariancję populacji dwóch kolumn.  
* ``covariancePopulationIf(region == 'West', sales)``  
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Pobiera kowariancję przykładową dwóch kolumn.  
* ``covarianceSample(sales, profit)``  
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera kowariancję przykładową dwóch kolumn.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Pobiera pierwszą wartość grupy kolumn. Jeśli drugi parametr ignoreNulls zostanie pominięty, przyjmuje się wartość false.  
* ``first(sales)``  
* ``first(sales, false)``  
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera wartość kurtoza kolumny.  
* ``kurtosis(sales)``  
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów Pobiera wartość kurtoza kolumny.  
* ``kurtosisIf(region == 'West', sales)``  
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Pobiera ostatnią wartość grupy kolumn. Jeśli drugi parametr ignoreNulls zostanie pominięty, przyjmuje się wartość false.  
* ``last(sales)``  
* ``last(sales, false)``  
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Pobiera maksymalną wartość kolumny.  
* ``max(sales)``  
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na podstawie kryteriów Pobiera maksymalną wartość kolumny.  
* ``maxIf(region == 'West', sales)``  
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera średnią wartości w kolumnie. Taka sama jak średnia.  
* ``mean(sales)``  
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryteriów pobiera znaczenie wartości w kolumnie. Analogicznie jak avgIf.  
* ``meanIf(region == 'West', sales)``  
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Pobiera minimalną wartość kolumny.  
* ``min(sales)``  
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na podstawie kryteriów pobiera minimalną wartość kolumny.  
* ``minIf(region == 'West', sales)``  
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera skośność kolumny.  
* ``skewness(sales)``  
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera skośność kolumny.  
* ``skewnessIf(region == 'West', sales)``  
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera odchylenie standardowe kolumny.  
* ``stdDev(sales)``  
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera odchylenie standardowe kolumny.  
* ``stddevIf(region == 'West', sales)``  
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera odchylenie standardowe populacji kolumny.  
* ``stddevPopulation(sales)``  
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera odchylenie standardowe populacji kolumny.  
* ``stddevPopulationIf(region == 'West', sales)``  
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera przykładowe odchylenie standardowe kolumny.  
* ``stddevSample(sales)``  
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera przykładowe odchylenie standardowe kolumny.  
* ``stddevSampleIf(region == 'West', sales)``  
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera sumę zagregowaną kolumny liczbowej.  
* ``sum(col)``  
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera zagregowaną sumę różnych wartości kolumny liczbowej.  
* ``sumDistinct(col)``  
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryteriów pobiera zagregowaną sumę kolumny liczbowej. Warunek może być oparty na dowolnej kolumnie.  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryteriów pobiera zagregowaną sumę kolumny liczbowej. Warunek może być oparty na dowolnej kolumnie.  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera wariancję kolumny.  
* ``variance(sales)``  
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera wariancję kolumny.  
* ``varianceIf(region == 'West', sales)``  
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera wariancję populacji kolumny.  
* ``variancePopulation(sales)``  
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera wariancję populacji kolumny.  
* ``variancePopulationIf(region == 'West', sales)``  
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera wariancję nieobciążonej kolumny.  
* ``varianceSample(sales)``  
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów pobiera wariancję nieobciążonej kolumny.  
* ``varianceSampleIf(region == 'West', sales)``  

## <a name="array-functions"></a>Funkcje tablicy
Funkcje tablic wykonują przekształcenia w strukturach danych, które są tablicami. Obejmują one specjalne słowa kluczowe do adresowania elementów tablicy i indeksów:

* ```#acc``` reprezentuje wartość, która ma zostać uwzględniona w pojedynczym wyjściu podczas zmniejszania tablicy
* ```#index``` reprezentuje bieżący indeks tablicy wraz z numerami indeksu tablicy ```#index2, #index3 ...```
* ```#item``` reprezentuje bieżącą wartość elementu w tablicy.

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
Tworzy tablicę elementów. Wszystkie elementy powinny być tego samego typu. Jeśli nie określono żadnych elementów, wartością domyślną jest pusta tablica ciągów. Analogicznie jak operator tworzenia [].  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Filtruje elementy z tablicy, które nie spełniają podanego predykatu. Filtr oczekuje odwołania do jednego elementu w funkcji predykatu jako #item.  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___
### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Znajdź pierwszy element z tablicy zgodnej z warunkiem. Przyjmuje funkcję filtru, gdzie można adresować element w tablicy jako #item. W przypadku głęboko zagnieżdżonych map można odwołać się do map nadrzędnych za pomocą notacji #item_n (#item_1, #item_2...).  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></kod><br/><br/> mapuje każdy element tablicy do nowego elementu przy użyciu podanego wyrażenia. Mapa oczekuje odwołania do jednego elementu w wyrażeniu Function as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></kod><br/><br/> mapuje każdy element tablicy do nowego elementu przy użyciu podanego wyrażenia. Mapa oczekuje odwołania do jednego elementu w funkcji wyrażenia jako #item i odwołania do elementu index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></kod><br/><br/> gromadzi elementy w tablicy. Funkcja ograniczania oczekuje odwołania do akumulowana i jednego elementu w pierwszej funkcji wyrażenia jako #acc i #item i oczekuje wartości, która będzie używana jako #result do użycia w drugim wyrażeniuession function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></kod><br/><br/> znajduje rozmiar array or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></kod><br/><br/> wyodrębnia podzbiór tablicy z położenia. Pozycja ma 1 na podstawie. W przypadku pominięcia długości wartość domyślna to end of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></kod><br/><br/> Sortuje tablicę przy użyciu podanej funkcji predykatu. Funkcja Sort oczekuje odwołania do dwóch kolejnych elementów w funkcji Expression jako #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item and a reference to the element index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Accumulates elements in an array. Reduce expects a reference to an accumulator and one element in the first expression function as #acc and #item and it expects the resulting value as #result to be used in the second expression function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
Finds the size of an array or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extracts a subset of an array from a position. Position is 1 based. If the length is omitted, it is defaulted to end of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Sorts the array using the provided predicate function. Sort expects a reference to two consecutive elements in the expression function as #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  

## <a name="cached-lookup-functions"></a>Buforowane funkcje wyszukiwania
Poniższe funkcje są dostępne tylko w przypadku korzystania z zbuforowanego wyszukiwania, gdy został on dołączony do buforowanego ujścia.
___
### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
Wyszukuje pierwszy wiersz z buforowanego ujścia, używając określonych kluczy, które pasują do kluczy z buforowanego ujścia.
* ``cacheSink#lookup(movieId)``  
___
### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
Wyszukuje wszystkie dopasowane wiersze z buforowanego ujścia, używając określonych kluczy, które pasują do kluczy z buforowanego ujścia.
* ``cacheSink#mlookup(movieId)``  
___
### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
Zwraca pierwszy wiersz wyników ujścia pamięci podręcznej * ``cacheSink#output()``  
___
### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
Zwraca cały wyjściowy zestaw wierszy wyników ujścia pamięci podręcznej * ``cacheSink#outputs()``
___


## <a name="conversion-functions"></a>Funkcje konwersji

Funkcje konwersji służą do konwertowania danych i testowania dla typów danych

<code>isBoolean</code>
<code><b>isBoolean(<value1> : string) => boolean</b></code><br/><br/>
Sprawdza, czy wartość ciągu jest wartością logiczną, zgodnie z regułami ``toBoolean()``
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``

<code>isByte</code>
<code><b>isByte(<value1> : string) => boolean</b></code><br/><br/>
Sprawdza, czy wartość ciągu jest wartością typu Byte z uwzględnieniem opcjonalnego formatu zgodnie z regułami ``toByte()``
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``

<code>isDate</code>
<code><b>isDate (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Sprawdza, czy wejściowy ciąg daty jest datą używającą opcjonalnego formatu daty wejściowej. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. Jeśli format daty wejścia zostanie pominięty, format domyślny to ``yyyy-[M]M-[d]d`` . Akceptowane formaty to ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``

<code>isShort</code>
<code><b>isShort (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Sprawdzenia wartości ciągu to krótka wartość, która ma format opcjonalny zgodnie z regułami ``toShort()``
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``

<code>isInteger</code>
<code><b>isInteger (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Sprawdzenia wartości ciągu jest wartością całkowitą, która ma format opcjonalny, zgodnie z regułami ``toInteger()``
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``

<code>isLong</code>
<code><b>isLong (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Sprawdzenia wartości ciągu jest wartością długą z uwzględnieniem opcjonalnego formatu zgodnie z regułami ``toLong()``
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``

<code>isFloat</code>
<code><b>isFloat (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Sprawdzenia wartości ciągu jest wartością zmiennoprzecinkową z uwzględnieniem opcjonalnego formatu zgodnie z regułami ``toFloat()``
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``

<code>isDouble</code>
<code><b>isDouble (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Sprawdza, czy wartość ciągu jest wartością podwójną, zgodnie z regułami ``toDouble()``
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``

<code>isDecimal</code>
<code><b>isDecimal (<value1> : string) => boolean</b></code><br/><br/>
Sprawdzenia wartości ciągu jest wartością dziesiętną, która ma format opcjonalny, zgodnie z regułami ``toDecimal()``
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``

<code>isTimestamp</code>
<code><b>isTimestamp (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Sprawdza, czy wejściowy ciąg daty jest sygnaturą czasową przy użyciu opcjonalnego wejściowego formatu znacznika czasu. Zapoznaj się z SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. Jeśli znacznik czasu zostanie pominięty, ``yyyy-[M]M-[d]d hh:mm:ss[.f...]`` używany jest domyślny wzorzec. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Sygnatura czasowa obsługuje maksymalnie milisekund dokładności z wartością 999, zobacz SimpleDateFormat języka Java dla dostępnych formatów.
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Koduje dany ciąg w formacie base64.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Konwertuje wszystkie wartości typu liczbowego/daty/sygnatury czasowej na reprezentację binarną.  
* ``toBinary(3) -> [0x11]``  
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Konwertuje wartość ('t ', 'true ', ' y ', ' yes ', ' 1 ') na true i ("f", "false", "n", "No", "0") na wartość false i wartość NULL dla żadnej innej wartości.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___
### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
Konwertuje wszystkie wartości liczbowe lub ciągi na wartość typu Byte. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java.  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Konwertuje wejściowy ciąg daty na datę przy użyciu opcjonalnego formatu daty wejściowej. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. Jeśli format daty wejścia zostanie pominięty, domyślny format to rrrr-[M] M-[d] d. Akceptowane formaty to: [rrrr, rrrr-[M] M, rrrr-[M] M-[d] d, rrrr-[M] M-[d] dT *].  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Konwertuje wszystkie wartości liczbowe lub ciągi na wartość dziesiętną. Jeśli precyzja i skala nie są określone, wartość domyślna to (10, 2). Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Opcjonalny format ustawień regionalnych w postaci języka BCP47, na przykład en-US, de, zh-CN.  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Konwertuje wszystkie wartości liczbowe lub ciągi na wartość typu Double. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Opcjonalny format ustawień regionalnych w postaci języka BCP47, na przykład en-US, de, zh-CN.  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Konwertuje wszystkie wartości liczbowe lub ciągi na wartość zmiennoprzecinkową. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina wszystkie podwójne.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Konwertuje wartość liczbową lub ciąg na liczbę całkowitą. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina wszystkie długie, zmiennoprzecinkowe, podwójnie.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Konwertuje dowolną liczbę lub ciąg na wartość długą. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina wszystkie zmiennoprzecinkowe, podwójnie.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Konwertuje dowolną liczbę lub ciąg na krótką wartość. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina wszystkie liczby całkowite, długie, zmiennoprzecinkowe, podwójnie.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Konwertuje pierwotny typ danych na ciąg. Dla liczb i dat można określić format. W przypadku nieokreślony system jest wybierany domyślnie. Format dziesiętny Java jest używany dla liczb. Zapoznaj się z SimpleDateFormat języka Java dla wszystkich możliwych formatów daty; domyślny format to RRRR-MM-DD.  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konwertuje ciąg na sygnaturę czasową, używając opcjonalnego formatu sygnatury czasowej. Jeśli znacznik czasu zostanie pominięty, domyślny wzorzec rrrr-[M] M-[d] d hh: mm: SS [. f...] jest używany. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Sygnatura czasowa obsługuje maksymalnie milisekund dokładności o wartości 999. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konwertuje sygnaturę czasową na czas UTC. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Domyślnie jest to Bieżąca strefa czasowa. Zapoznaj się z `SimpleDateFormat` klasą języka Java dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  

## <a name="metafunctions"></a>Funkcje

Funkcja Metadata przede wszystkim działa na metadanych w przepływie danych

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Znajdź element podrzędny w strukturze lub tablicy struktury, jeśli istnieje wiele dopasowań, zwracane jest pierwsze dopasowanie. W przypadku braku dopasowania zwraca wartość NULL. Zwracana wartość musi być typu konwertowana przez jedną z akcji konwersji typu (? Data,? ciąg...).  Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ````
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
* ````
___
### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wybiera wartość kolumny według nazwy w strumieniu źródła. Drugi argument jest nazwą strumienia pochodzenia. Jeśli istnieje wiele dopasowań, zwracane jest pierwsze dopasowanie. W przypadku braku dopasowania zwraca wartość NULL. Zwracana wartość musi być typu konwertowana przez jedną z funkcji konwersji typu (TO_DATE, TO_STRING...). Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów.  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___
### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wybiera tablicę kolumn według nazwy w strumieniu. Drugi argument jest strumieniem, z którego pochodzi. Jeśli istnieje wiele dopasowań, zwracane jest pierwsze dopasowanie. W przypadku braku dopasowania zwraca wartość NULL. Zwracana wartość musi być typu konwertowana przez jedną z funkcji konwersji typu (TO_DATE, TO_STRING...) Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów.
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wybiera wartość kolumny według nazwy w strumieniu. Opcjonalną nazwę strumienia można przekazać jako drugi argument. Jeśli istnieje wiele dopasowań, zwracane jest pierwsze dopasowanie. W przypadku braku dopasowania zwraca wartość NULL. Zwracana wartość musi być typu konwertowana przez jedną z funkcji konwersji typu (TO_DATE, TO_STRING...).  Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów.  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___
### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wybierz tablicę kolumn według nazwy w strumieniu. Opcjonalną nazwę strumienia można przekazać jako drugi argument. Jeśli istnieje wiele dopasowań, zwracane jest pierwsze dopasowanie. Jeśli nie ma żadnych dopasowań dla kolumny, wszystkie dane wyjściowe są wartością NULL. Zwracana wartość wymaga funkcji konwersji typu (toDate, toString,...).  Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów.
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Wybiera wartość kolumny według pozycji względnej (w oparciu o 1) w strumieniu. Jeśli pozycja jest poza zakresem, zwraca wartość NULL. Zwracana wartość musi być typu konwertowana przez jedną z funkcji konwersji typu (TO_DATE, TO_STRING...) Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów.  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  

## <a name="window-functions"></a>Funkcje okien
Poniższe funkcje są dostępne tylko w transformacjach okien.
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
Funkcja CumeDist oblicza pozycję wartości względem wszystkich wartości w partycji. Wynik jest liczbą wierszy poprzedzających lub równych bieżącemu wierszowi w kolejności partycji podzieloną przez łączną liczbę wierszy w partycji okna. Wszystkie wartości równe w kolejności będą oceniane do tego samego położenia.  
* ``cumeDist()``  
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Oblicza rangę wartości w grupie wartości określonej w klauzuli Order by okna. Wynik to jeden i liczba wierszy poprzedzających lub równych bieżącemu wierszowi w kolejności partycji. Wartości nie będą powodować przerw w sekwencji. Gęsta ranga działa nawet wtedy, gdy dane nie są sortowane i wyszukują zmiany wartości.  
* ``denseRank()``  
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Pobiera wartość pierwszego parametru oceniane n wierszy przed bieżącym wierszem. Drugi parametr to liczba wierszy do wyszukania, a wartość domyślna to 1. Jeśli nie ma tylu wierszy, zwracana jest wartość null, chyba że zostanie określona wartość domyślna.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Pobiera wartość pierwszego parametru oceniane n wierszy po bieżącym wierszu. Drugi parametr to liczba wierszy do przeszukania, a wartość domyślna to 1. Jeśli nie ma tylu wierszy, zwracana jest wartość null, chyba że zostanie określona wartość domyślna.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
```NTile```Funkcja dzieli wiersze dla każdej partycji okna do `n` zasobników od 1 do najwyżej `n` . Wartości zasobnika różnią się o co najwyżej 1. Jeśli liczba wierszy w partycji nie jest równo podzielona na liczbę przedziałów, pozostałe wartości są dystrybuowane jeden na przedział, rozpoczynając od pierwszego przedziału. ```NTile```Funkcja jest przydatna do obliczania ```tertiles``` , kwartylów, deciles i innych typowych statystyk podsumowujących. Funkcja oblicza dwie zmienne podczas inicjalizacji: rozmiar regularnego zasobnika będzie mieć jeden dodatkowy wiersz. Obie zmienne są zależne od rozmiaru bieżącej partycji. Podczas obliczeń funkcja śledzi bieżący numer wiersza, bieżący zasobnik i numer wiersza, w którym zostanie zmieniony zasobnik (bucketThreshold). Gdy bieżąca liczba wierszy osiągnie próg zasobnika, wartość przedziału jest zwiększana o jeden, a próg jest zwiększany o rozmiar zasobnika (plus jeden dodatkowy, jeśli bieżący zasobnik jest uzupełniony).  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Oblicza rangę wartości w grupie wartości określonej w klauzuli Order by okna. Wynik to jeden i liczba wierszy poprzedzających lub równych bieżącemu wierszowi w kolejności partycji. Wartości będą generować przerwy w sekwencji. Ranga działa nawet wtedy, gdy dane nie są sortowane i wyszukują zmiany wartości.  
* ``rank()``  
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Przypisuje sekwencyjne numery wierszy w oknie, rozpoczynając od 1.  
* ``rowNumber()``  

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak używać programu Expression Builder](concepts-data-flow-expression-builder.md).
