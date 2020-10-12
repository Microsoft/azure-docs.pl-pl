---
title: Tworzenie funkcji dla danych w klastrze Azure HDInsight Hadoop — proces nauki danych zespołu
description: Przykłady zapytań programu Hive, które generują funkcje w danych przechowywanych w klastrze Azure HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6261e31fd84b9471fa4ea5d30e1d6a4afbac9115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085382"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań programu Hive
W tym dokumencie przedstawiono sposób tworzenia funkcji dla danych przechowywanych w klastrze Azure HDInsight Hadoop przy użyciu zapytań programu Hive. Te zapytania Hive używają osadzonych funkcji User-Defined Hive (UDF), które są udostępniane przez skrypty.

Operacje wymagające tworzenia funkcji mogą być czasochłonne. Wydajność zapytań Hive stanie się bardziej krytyczna w takich przypadkach i można ją ulepszyć, dostrajając pewne parametry. Dostrajanie tych parametrów jest omówione w ostatniej sekcji.

Przykłady przedstawionych zapytań są specyficzne dla scenariuszy [danych dotyczących podróży z NYCem](https://chriswhong.com/open-data/foil_nyc_taxi/) , które są również dostępne w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania mają już określony schemat danych i są gotowe do przesłania do uruchomienia. W ostatniej sekcji opisano parametry, które użytkownicy mogą dostrajać, aby można było ulepszyć wydajność zapytań Hive.

To zadanie jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto usługi Azure Storage. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Zainicjowano obsługę niestandardowego klastra Hadoop z usługą HDInsight.  Jeśli potrzebujesz instrukcji, zobacz [Dostosowywanie klastrów Azure HDInsight Hadoop na potrzeby zaawansowanej analizy](customize-hadoop-cluster.md).
* Dane zostały przekazane do tabel programu Hive w klastrach Azure HDInsight Hadoop. Jeśli tak nie jest, postępuj zgodnie z instrukcjami [tworzenia i ładowania danych do tabel programu Hive](move-hive-tables.md) , aby najpierw przekazać dane do tabel programu Hive.
* Włączono dostęp zdalny do klastra. Jeśli potrzebujesz instrukcji, zobacz [dostęp do węzła głównego klastra usługi Hadoop](customize-hadoop-cluster.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Generowanie funkcji
W tej sekcji przedstawiono kilka przykładów sposobu generowania funkcji przy użyciu zapytań programu Hive. Po wygenerowaniu dodatkowych funkcji można je dodać jako kolumny do istniejącej tabeli lub utworzyć nową tabelę z dodatkowymi funkcjami i kluczem podstawowym, które następnie można dołączyć do oryginalnej tabeli. Poniżej przedstawiono przykłady:

1. [Generowanie funkcji opartej na częstotliwości](#hive-frequencyfeature)
2. [Ryzyko zmiennych kategorii w klasyfikacji binarnej](#hive-riskfeature)
3. [Wyodrębnij funkcje z pola DateTime](#hive-datefeatures)
4. [Wyodrębnij funkcje z pola tekstowego](#hive-textfeatures)
5. [Obliczanie odległości między współrzędnymi GPS](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Generowanie funkcji opartej na częstotliwości
Często przydatne jest obliczenie częstotliwości poziomów zmiennej kategorii lub częstotliwości niektórych kombinacji poziomów z wielu zmiennych kategorii. Aby obliczyć te częstotliwości, użytkownicy mogą użyć następującego skryptu:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Ryzyko zmiennych kategorii w klasyfikacji binarnej
W klasyfikacji binarnej zmienne kategorii inne niż liczbowe muszą być konwertowane na funkcje liczbowe, gdy używane modele pobierają tylko funkcje liczbowe. Ta konwersja jest wykonywana przez zastąpienie każdego nieliczbowego poziomu ryzykiem. W tej sekcji przedstawiono niektóre ogólne zapytania programu Hive, które obliczają wartości ryzyka (log szanse) zmiennej kategorii.

```hiveql
set smooth_param1=1;
set smooth_param2=20;
select
    <column_name1>,<column_name2>,
    ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
from
    (
    select
        <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
    from
        (
        select
            <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
        from <databasename>.<tablename>
        )a
    group by <column_name1>, <column_name2>
    )b
```

W tym przykładzie zmienne `smooth_param1` i `smooth_param2` są ustawione tak, aby Wygładź wartości ryzyka obliczone na podstawie danych. Elementy ryzyka mają zakres między-plików inf i inf. Ryzyko > 0 wskazuje, że prawdopodobieństwo, że element docelowy jest równy 1, jest większy niż 0,5.

Po obliczeniu tabeli ryzyka użytkownicy mogą przypisywać wartości ryzyka do tabeli przez przyłączenie jej do tabeli ryzyka. W poprzedniej sekcji podano zapytanie łączenia Hive.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Wyodrębnij funkcje z pól daty i godziny
Program Hive zawiera zestaw UDF do przetwarzania pól DateTime. W programie Hive domyślnym formatem DateTime jest "RRRR-MM-DD 00:00:00" (na przykład "1970-01-01 12:21:32"). W tej sekcji przedstawiono przykłady wyodrębniające dzień miesiąca, miesiąc z pola DateTime oraz inne przykłady, które konwertują ciąg DateTime w formacie innym niż domyślny format na ciąg DateTime w formacie domyślnym.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

W tej kwerendzie Hive założono, że *\<datetime field>* jest ona w domyślnym formacie DateTime.

Jeśli pole DateTime nie ma formatu domyślnego, należy najpierw skonwertować pole DateTime na sygnaturę czasową systemu UNIX, a następnie przekonwertować sygnaturę czasową systemu UNIX na ciąg DateTime, który jest w formacie domyślnym. Gdy element DateTime ma format domyślny, użytkownicy mogą zastosować osadzoną UDF DateTime w celu wyodrębnienia funkcji.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

W tej kwerendzie, jeśli *\<datetime field>* ma wzór podobny do *03/26/2015 12:04:39*, wartość * \<pattern of the datetime field> "* powinna być `'MM/dd/yyyy HH:mm:ss'` . Aby go przetestować, użytkownicy mogą uruchamiać

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

*Hivesampletable* w tym zapytaniu jest wstępnie instalowany we wszystkich klastrach Azure HDInsight Hadoop domyślnie po zainicjowaniu obsługi klastrów.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Wyodrębnij funkcje z pól tekstowych
Gdy tabela programu Hive ma pole tekstowe, które zawiera ciąg słów, które są rozdzielane spacjami, następujące zapytanie wyodrębnia długość ciągu i liczbę wyrazów w ciągu.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Obliczanie odległości między zestawami współrzędnych GPS
Zapytanie zawarte w tej sekcji można bezpośrednio zastosować do danych podróży z NYCą. Celem tego zapytania jest pokazanie, jak zastosować osadzoną funkcję matematyczną w programie Hive w celu wygenerowania funkcji.

Pola, które są używane w tym zapytaniu, to współrzędne GPS dotyczące lokalizacji odbioru i Dropoff, nazwanych * \_ długości*geograficznej, *pobrania \_ szerokości geograficznej*, *Dropoff \_ *i *Dropoff \_ szerokości geograficznej*. Zapytania obliczające bezpośrednią odległość między współrzędnymi odbioru i Dropoff są następujące:

```hiveql
set R=3959;
set pi=radians(180);
select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
    *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
    *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
    +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
    pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxi.trip
where pickup_longitude between -90 and 0
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and 0
and dropoff_latitude between 30 and 90
limit 10;
```

Równania matematyczne obliczające odległość między dwoma współrzędnymi GPS można znaleźć w witrynie <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type scripters</a> , utworzonej przez Peterowi Lapisu. W tym języku JavaScript funkcja `toRad()` jest tylko *lat_or_lon*pi/180, która konwertuje stopnie na radiany. W tym miejscu *lat_or_lon* jest szerokość lub długość geograficzna. Ponieważ gałąź nie udostępnia funkcji `atan2` , ale udostępnia funkcję `atan` , `atan2` Funkcja jest implementowana przez `atan` funkcję w powyższym zapytaniu Hive przy użyciu definicji podanej w witrynie <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Tworzenie obszaru roboczego](./media/create-features-hive/atan2new.png)

Pełną listę programu Hive Embedded UDF można znaleźć w sekcji **wbudowane funkcje** w witrynie <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Tematy zaawansowane: dostrajanie parametrów Hive w celu zwiększenia szybkości zapytania
Domyślne ustawienia parametrów klastra Hive mogą nie być odpowiednie dla zapytań Hive i danych przetwarzanych przez zapytania. W tej sekcji omówiono niektóre parametry, które użytkownicy mogą dostosowywać, aby zwiększyć wydajność zapytań programu Hive. Użytkownicy muszą dodać zapytania dostrajania parametrów przed zapytania dotyczące przetwarzania danych.

1. **Przestrzeń sterty języka Java**: w przypadku kwerend obejmujących łączenie dużych zestawów danych lub przetwarzania długich rekordów **Uruchamianie poza stertą** jest jednym z typowych błędów. Ten błąd można uniknąć, ustawiając parametry *MapReduce. map. Java.* *domapreduce. Task. IO. Sort. MB* do żądanych wartości. Oto przykład:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Ten parametr przypisuje pamięć o pojemności 4 GB do przestrzeni sterty Java, a jednocześnie sortuje bardziej wydajne przez przydzielenie większej ilości pamięci. Dobrym pomysłem jest odtwarzanie przy użyciu tych alokacji, jeśli występują błędy niepowodzeń zadań związane z miejscem sterty.

1. **Rozmiar bloku systemu plików DFS**: ten parametr ustawia najmniejszą liczbę danych przechowywanych w systemie plików. Jeśli na przykład rozmiar bloku systemu plików DFS to 128 MB, wówczas wszystkie dane o rozmiarze mniejszym niż i do 128 MB są przechowywane w jednym bloku. Dane, które są większe niż 128 MB, są przydzielane do dodatkowych bloków. 
2. Wybór małego rozmiaru bloku powoduje, że w usłudze Hadoop są duże nadpory, ponieważ węzeł name ma przetwarzać wiele innych żądań, aby znaleźć odpowiedni blok odnoszący się do pliku. Zalecanym ustawieniem dotyczącym gigabajtów (lub większych) danych jest:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. **Optymalizacja operacji JOIN w programie Hive**: Chociaż operacje JOIN w mapie/zmniejszą strukturę zwykle odbywają się w fazie zmniejszania, czasami można osiągnąć ogromne zyski przez przyłączenia do harmonogramu w fazie mapy (zwanej również "mapjoins"). Ustaw tę opcję:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **Określanie liczby odwzorowań do Hive**: podczas gdy usługa Hadoop zezwala użytkownikowi na ustawienie liczby elementów ograniczających, liczba mapera nie jest zwykle ustawiana przez użytkownika. Lewę, która umożliwia pewne kontrolę nad tą liczbą, jest wybranie zmiennych Hadoop *mapred. min. Split. size* i *mapred. max. Split. size* , ponieważ rozmiar każdego zadania mapy jest określany przez:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Zazwyczaj wartość domyślna:
    
   - *mapred. min. Split. size* ma wartość 0, czyli
   - *mapred. max. Split. size* jest **Long. Max** i 
   - *system plików DFS. Block. size* to 64 MB.

     Jak widać, uwzględniając rozmiar danych, dostrajanie tych parametrów przez "ustawienie" pozwala nam dostroić liczbę użytych odwzorowań.

4. Oto kilka innych **zaawansowanych opcji** optymalizacji wydajności Hive. Te opcje umożliwiają ustawienie pamięci przydzieloną na potrzeby mapowania i zmniejszania zadań, co może być przydatne w przypadku dostosowywania wydajności. Należy pamiętać, że wartość *MapReduce. zredukować. Memory. MB* nie może być większa niż rozmiar pamięci fizycznej każdego węzła procesu roboczego w klastrze usługi Hadoop.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```

