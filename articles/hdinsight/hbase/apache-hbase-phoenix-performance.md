---
title: Wydajność Phoenix w usłudze Azure HDInsight
description: Najlepsze rozwiązania w celu zoptymalizowania wydajności Apache Phoenix klastrów usługi Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: ffba3b986b35c375d0404d9d2bae5af79f93c54f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944805"
---
# <a name="apache-phoenix-performance-best-practices"></a>Najlepsze rozwiązania w zakresie wydajności dla rozwiązania Apache Phoenix

Najważniejszym aspektem [Apache Phoenix](https://phoenix.apache.org/) wydajności jest zoptymalizowanie podstawowej platformy [Apache HBase](https://hbase.apache.org/). Phoenix tworzy relacyjny model danych korzystającego HBase, który konwertuje zapytania SQL na operacje HBase, takie jak skanowanie. Projekt schematu tabeli, wybór i kolejność pól w kluczu podstawowym oraz użycie indeksów wszystkie wpływają na wydajność w Phoenix.

## <a name="table-schema-design"></a>Projekt schematu tabeli

Podczas tworzenia tabeli w Phoenix, ta tabela jest przechowywana w tabeli HBase. Tabela HBase zawiera grupy kolumn (rodziny kolumn), do których uzyskuje się dostęp ze sobą. Wiersz w tabeli Phoenix jest wierszem w tabeli HBase, gdzie każdy wiersz składa się z komórek z wersjami skojarzonych z co najmniej jedną kolumną. Logicznie, pojedynczy wiersz HBase jest kolekcją par klucz-wartość, z których każda ma tę samą wartość rowkey. Oznacza to, że każda para klucz-wartość ma atrybut rowkey, a wartość tego atrybutu rowkey jest taka sama dla konkretnego wiersza.

Projekt schematu tabeli Phoenix obejmuje projekt klucza podstawowego, projekt rodziny kolumn, projekt poszczególnych kolumn i sposób partycjonowania danych.

### <a name="primary-key-design"></a>Projekt klucza podstawowego

Klucz podstawowy zdefiniowany w tabeli w Phoenix określa, jak dane są przechowywane w rowkey źródłowej tabeli HBase. W programie HBase jedynym sposobem uzyskania dostępu do określonego wiersza jest rowkey. Ponadto dane przechowywane w tabeli HBase są sortowane według rowkey. Phoenix kompiluje wartość rowkey, łącząc wartości każdej kolumny w wierszu w kolejności, w jakiej są zdefiniowane w kluczu podstawowym.

Na przykład tabela kontaktów ma imię i nazwisko, nazwisko, numer telefonu i adres, wszystkie w tej samej rodzinie kolumn. Można zdefiniować klucz podstawowy w oparciu o rosnący numer sekwencyjny:

|rowkey|       adres|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Michał|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Jeśli jednak często wykonujesz zapytania według nazwiska, ten klucz podstawowy może nie działać prawidłowo, ponieważ każde zapytanie wymaga pełnego skanowania tabeli, aby odczytać wartość każdego lastName. Zamiast tego można zdefiniować klucz podstawowy w kolumnach lastName, firstName i numer ubezpieczenia społecznego. W tej ostatniej kolumnie można odróżnić dwa rezydentów pod tym samym adresem o tej samej nazwie, na przykład ojciec i syn.

|rowkey|       adres|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Michał|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Przy użyciu nowego klucza podstawowego klucze wierszy generowane przez Phoenix byłyby następujące:

|rowkey|       adres|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-Jan-111|1111 San Gabriel Dr.|1-425-000-0002|    Michał|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

W pierwszym wierszu powyżej dane dla rowkey są reprezentowane w sposób pokazany:

|rowkey|       key|   wartość|
|------|--------------------|---|
|  Dole-Jan-111|adres |1111 San Gabriel Dr.|  
|  Dole-Jan-111|phone |1-425-000-0002|  
|  Dole-Jan-111|firstName |Michał|  
|  Dole-Jan-111|lastName |Dole|  
|  Dole-Jan-111|socialSecurityNum |111|

Ta rowkey teraz przechowuje zduplikowaną kopię danych. Należy wziąć pod uwagę rozmiar i liczbę kolumn uwzględnionych w kluczu podstawowym, ponieważ ta wartość jest uwzględniana w każdej komórce w źródłowej tabeli HBase.

Ponadto, jeśli klucz podstawowy ma wartości, które są monotonicznie, należy utworzyć tabelę z *pakietami soli* , aby uniknąć tworzenia hotspotów zapisu — zobacz [dane partycji](#partition-data).

### <a name="column-family-design"></a>Projektowanie rodziny kolumn

Jeśli niektóre kolumny są dostępne częściej niż inne, należy utworzyć wiele rodzin kolumn, aby oddzielić często używane kolumny od rzadko używanych kolumn.

Ponadto, jeśli pewne kolumny mają być dostępne razem, należy umieścić te kolumny w tej samej rodzinie kolumn.

### <a name="column-design"></a>Projektowanie kolumn

* Przechowuj kolumny VARCHAR w obszarze około 1 MB z powodu kosztów we/wy dużych kolumn. Podczas przetwarzania zapytań HBase materializuje komórki w całości przed wysłaniem ich do klienta, a klient otrzymuje je w pełni przed przekazaniem ich do kodu aplikacji.
* Przechowuj wartości kolumn przy użyciu formatu kompaktowego, takiego jak protobuf, Avro, msgpack lub BSON. KOD JSON nie jest zalecany, ponieważ jest większy.
* Rozważ kompresję danych przed magazynem, aby zmniejszyć opóźnienia i koszty operacji we/wy.

### <a name="partition-data"></a>Partycjonowanie danych

Phoenix pozwala kontrolować liczbę regionów, w których są dystrybuowane dane, co może znacząco zwiększyć wydajność odczytu i zapisu. Podczas tworzenia tabeli w Phoenix można wyciągać lub wstępnie podzielić dane.

Aby przeprowadzić solenie tabeli podczas jej tworzenia, określ liczbę zasobników soli:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Ta sól dzieli tabelę na wartości kluczy podstawowych, wybierając wartości automatycznie. 

Aby kontrolować miejsce, w którym występuje podział tabeli, można wstępnie podzielić tabelę, podając wartości zakresu, na których występuje podział. Na przykład, aby utworzyć tabelę podzieloną na trzy regiony:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Projekt indeksu

Indeks Phoenix jest tabelą HBase, która przechowuje kopię niektórych lub wszystkich danych z tabeli indeksowanej. Indeks poprawia wydajność dla określonych typów zapytań.

Jeśli zdefiniowano wiele indeksów, a następnie kwerenda tabeli, Phoenix automatycznie wybiera najlepszy indeks zapytania. Indeks podstawowy jest tworzony automatycznie w oparciu o wybrane klucze podstawowe.

W przypadku zaplanowanych zapytań można również utworzyć indeksy pomocnicze, określając ich kolumny.

Podczas projektowania indeksów:

* Należy tworzyć tylko potrzebne indeksy.
* Ogranicz liczbę indeksów w często aktualizowanych tabelach. Aktualizacje tabeli przekładają się na zapisy zarówno w tabeli głównej, jak i w tabelach indeksów.

## <a name="create-secondary-indexes"></a>Tworzenie indeksów pomocniczych

Indeksy pomocnicze mogą zwiększyć wydajność odczytu przez włączenie pełnego skanowania tabeli w celu przeszukiwania punktów, koszt miejsca do magazynowania i szybkość zapisywania. Indeksy pomocnicze mogą być dodawane lub usuwane po utworzeniu tabeli i nie wymagają wprowadzania zmian w istniejących zapytaniach — zapytania działają szybciej. W zależności od potrzeb należy rozważyć utworzenie indeksów pokrytych, indeksów funkcjonalnych lub obu.

### <a name="use-covered-indexes"></a>Użyj indeksów uwzględnionych

Indeksy objęte usługą są indeksami, które zawierają dane z wiersza oprócz wartości, które są indeksowane. Po znalezieniu żądanego wpisu indeksu nie ma potrzeby uzyskiwania dostępu do tabeli podstawowej.

Przykładowo w przykładowej tabeli kontaktów można utworzyć indeks pomocniczy tylko dla kolumny socialSecurityNum. Ten indeks pomocniczy przyspieszy zapytania, które są filtrowane według wartości socialSecurityNum, ale pobieranie innych wartości pól wymaga innego odczytania względem głównej tabeli.

|rowkey|       adres|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-Jan-111|1111 San Gabriel Dr.|1-425-000-0002|    Michał|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Jeśli jednak zazwyczaj chcesz wyszukać imię i nazwisko socialSecurityNum, możesz utworzyć indeks pokryty, który zawiera imię i nazwisko, jako rzeczywiste dane w tabeli indeksów:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Ten indeks objęty usługą umożliwia pozyskanie wszystkich danych z tabeli zawierającej indeks pomocniczy przy użyciu następującej kwerendy:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Używanie indeksów funkcjonalnych

Indeksy funkcjonalne umożliwiają utworzenie indeksu dla dowolnego wyrażenia, które ma być używane w zapytaniach. Gdy masz indeks funkcjonalny, a zapytanie używa tego wyrażenia, indeks może być używany do pobierania wyników, a nie tabeli danych.

Można na przykład utworzyć indeks, aby zezwolić na wyszukiwanie bez uwzględniania wielkości liter w połączeniu z imię i nazwisko osoby:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Projekt zapytania

Główne zagadnienia dotyczące projektowania zapytań to:

* Zapoznaj się z planem zapytania i sprawdź jego oczekiwane zachowanie.
* Wydajnie Dołącz.

### <a name="understand-the-query-plan"></a>Opis planu zapytania

W elemencie [SqlLine](http://sqlline.sourceforge.net/)Użyj wyjaśnień i zapytania SQL, aby wyświetlić plan operacji wykonywanych przez Phoenix. Sprawdź, czy plan:

* W razie potrzeby używa klucza podstawowego.
* Używa odpowiednich indeksów pomocniczych, a nie tabeli danych.
* Używa skanowania zakresu lub POMIJAj skanowanie wszędzie tam, gdzie to możliwe, a nie na SKANOWAniu tabeli.

#### <a name="plan-examples"></a>Przykłady planu

Załóżmy na przykład, że masz tabelę o nazwie loty, która przechowuje informacje o opóźnieniu lotów.

Aby wybrać wszystkie loty z airlineid `19805` , gdzie airlineid to pole, które nie znajduje się w kluczu podstawowym lub w dowolnym indeksie:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Uruchom polecenie Wyjaśnij w następujący sposób:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

Plan zapytania wygląda następująco:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

W tym planie należy zwrócić uwagę na pełne skanowanie fraz za pośrednictwem lotów. Ta fraza wskazuje, że wykonanie przeskanuje tabelę nad wszystkimi wierszami w tabeli, zamiast korzystać z bardziej wydajnego skanowania zakresu lub opcji skanowania.

Teraz Załóżmy, że chcesz wykonać zapytanie dotyczące lotów 2 stycznia 2014 dla przewoźnika, `AA` gdzie jego flightnum była większa niż 1. Załóżmy, że kolumny Year, month, dayOfMonth, Key i flightnum istnieją w przykładowej tabeli i są częścią złożonego klucza podstawowego. Zapytanie będzie wyglądać następująco:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Sprawdźmy plan dla tego zapytania przy użyciu:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Plan wynikający z tego:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

Wartości w nawiasach kwadratowych są zakresem wartości kluczy podstawowych. W takim przypadku wartości zakresu są stałe z rokiem 2014, month 1 i dayOfMonth 2, ale zezwalają na wartości flightnum, zaczynając od 2 i w górę ( `*` ). Ten plan zapytania potwierdza, że klucz podstawowy jest używany zgodnie z oczekiwaniami.

Następnie utwórz indeks w tabeli loty o nazwie `carrier2_idx` , która znajduje się tylko w polu przewoźnik. Ten indeks zawiera także flightdate, tailnum, Origin i flightnum jako kolumny omówione, których dane są również przechowywane w indeksie.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Załóżmy, że chcesz uzyskać przewoźnika wraz z flightdate i tailnum, tak jak w poniższym zapytaniu:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

Powinien zostać wyświetlony następujący indeks:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

Aby zapoznać się z pełną listą elementów, które mogą pojawić się w wynikach planu wyjaśnień, zobacz sekcję "wyjaśnienia planów" w [przewodniku dostrajania Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Wydajne przyłączanie

Ogólnie rzecz biorąc, chcesz uniknąć sprzężeń, chyba że jedna strona jest mała, szczególnie w przypadku częstych zapytań.

W razie potrzeby można wykonywać duże sprzężenia ze `/*+ USE_SORT_MERGE_JOIN */` wskazówką, ale duże sprzężenie jest kosztowną operacją w porównaniu z ogromną liczbą wierszy. Jeśli całkowity rozmiar wszystkich tabel po prawej stronie przekroczy dostępną pamięć, użyj `/*+ NO_STAR_JOIN */` wskazówki.

## <a name="scenarios"></a>Scenariusze

W poniższych wytycznych opisano niektóre typowe wzorce.

### <a name="read-heavy-workloads"></a>Obciążenia z dużą ilością odczytu

W przypadku przypadków użycia z dużym obciążeniem upewnij się, że używasz indeksów. Dodatkowo, aby zaoszczędzić czas odczytu, rozważ utworzenie indeksów pokrytych.

### <a name="write-heavy-workloads"></a>Obciążenia o dużych możliwościach zapisu

W przypadku obciążeń intensywnie korzystających z zapisu, w których klucz podstawowy jest monotonicznie rosnący, Utwórz zasobniki soli, aby uniknąć konieczności zapisu hotspotów, kosztem ogólnej przepływności odczytu z powodu dodatkowych koniecznych skanowania. Ponadto w przypadku używania UPSERT do zapisywania dużej liczby rekordów należy wyłączyć funkcję automatycznego zatwierdzania i wsadowe rekordy.

### <a name="bulk-deletes"></a>Usuwanie zbiorcze

Podczas usuwania dużego zestawu danych należy włączyć funkcję automatycznego zatwierdzania przed wygenerowaniem zapytania usuwania, aby klient nie musiał pamiętać kluczy wierszy dla wszystkich usuniętych wierszy. Automatyczne zatwierdzanie uniemożliwia klientowi buforowanie wierszy, których dotyczy usunięcie, dzięki czemu w Phoenix można je usunąć bezpośrednio na serwerach regionów bez kosztów powrotu ich do klienta.

### <a name="immutable-and-append-only"></a>Niezmienne i tylko do dołączania

Jeśli Twój Scenariusz preferuje przyspieszenie zapisu przez integralność danych, rozważ wyłączenie zapisu z wyprzedzeniem podczas tworzenia tabel:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Aby uzyskać szczegółowe informacje na temat tego i innych opcji, zobacz [Apache Phoenix gramatyki](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Następne kroki

* [Przewodnik dostrajania Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Indeksy pomocnicze](https://phoenix.apache.org/secondary_indexing.html)
