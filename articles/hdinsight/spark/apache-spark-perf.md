---
title: Optymalizowanie zadań platformy Spark pod kątem wydajności — usługa Azure HDInsight
description: Pokaż typowe strategie dla najlepszej wydajności klastrów Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 736653e82f753341fbbdfb795f229145bba96162
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188195"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optymalizowanie Apache Spark zadań w usłudze HDInsight

Dowiedz się, jak zoptymalizować konfigurację klastra Apache Spark dla określonego obciążenia.  Najczęstszym wyzwaniem jest wykorzystanie pamięci z powodu nieprawidłowych konfiguracji (takich jak wykonawcy o nieprawidłowym rozmiarze). Ponadto długotrwałe operacje i zadania, które powodują operacje kartezjańskiego. Można przyspieszyć zadania z odpowiednimi buforowaniem i zezwalać na [pochylenie danych](#optimize-joins-and-shuffles). Aby uzyskać najlepszą wydajność, Monitoruj i sprawdzaj długotrwałe i czasochłonne wykonywanie zadań platformy Spark. Aby uzyskać informacje na temat rozpoczynania pracy z usługą Apache Spark w usłudze HDInsight, zobacz [Tworzenie klastra Apache Spark przy użyciu Azure Portal](apache-spark-jupyter-spark-sql-use-portal.md).

W poniższych sekcjach opisano typowe optymalizacje i rekomendacje zadań platformy Spark.

## <a name="choose-the-data-abstraction"></a>Wybieranie abstrakcji danych

Starsze wersje platformy Spark używają odporne do danych abstrakcyjnych, Spark 1,3 i 1,6 wprowadzają odpowiednio dane Frame i DataSets. Rozważ następujące względne wartości:

* **Ramki danych**
    * Najlepszy wybór w większości sytuacji.
    * Zapewnia optymalizację zapytania za poorednictwem katalizatora.
    * Generowanie kodu w całym etapie.
    * Bezpośredni dostęp do pamięci.
    * Obciążenie niskiego wyrzucania elementów bezużytecznych.
    * Nie jako zestawy danych, które nie są przyjaznymi dla deweloperów, ponieważ nie są sprawdzane w czasie kompilacji ani programowanie obiektów domeny.
* **Zestawów danych**
    * Dobre w złożonych potokach ETL, w których wpływ na wydajność jest akceptowalny.
    * Niedobre w agregacjach, w których wpływ na wydajność może być znaczny.
    * Zapewnia optymalizację zapytania za poorednictwem katalizatora.
    * Przyjazne dla deweloperów, zapewniając programowanie obiektów domeny i sprawdzanie w czasie kompilacji.
    * Dodaje narzuty serializacji/deserializacji.
    * Duże obciążenie GC.
    * Przerywa generowanie kodu w całym etapie.
* **Odporne**
    * Nie musisz używać odporne, chyba że trzeba utworzyć nową niestandardową RDD.
    * Brak optymalizacji zapytania za poorednictwem katalizatora.
    * Brak generowania kodu w całości.
    * Duże obciążenie GC.
    * Należy używać starszych interfejsów API Spark 1. x.

## <a name="use-optimal-data-format"></a>Korzystanie z optymalnego formatu danych

Platforma Spark obsługuje wiele formatów, takich jak CSV, JSON, XML, Parquet, Orc i Avro. Platforma Spark może zostać rozszerzona w celu obsługi wielu formatów z zewnętrznymi źródłami danych — Aby uzyskać więcej informacji, zobacz [Apache Spark Packages](https://spark-packages.org).

Najlepszym formatem wydajności jest Parquet z *kompresją przyciągania*, która jest wartością domyślną w platformie Spark 2. x. Parquet przechowuje dane w formacie kolumnowym i jest wysoce zoptymalizowany w Spark.

## <a name="select-default-storage"></a>Wybieranie magazynu domyślnego

Podczas tworzenia nowego klastra Spark można wybrać platformę Azure Blob Storage lub Azure Data Lake Storage jako domyślny magazyn klastra. Obie opcje umożliwiają korzystanie z magazynu długoterminowego na potrzeby klastrów przejściowych. W związku z tym dane nie zostaną automatycznie usunięte po usunięciu klastra. Możesz ponownie utworzyć tymczasowy klaster i nadal uzyskiwać dostęp do danych.

| Typ sklepu | System plików | Szybkość | Administracyjnej | Przypadki użycia |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//URL/ | **Standardowa** | Tak | Przejściowy klaster |
| Azure Blob Storage (bezpieczna) | **wasbs:**//URL/ | **Standardowa** | Tak | Przejściowy klaster |
| Azure Data Lake Storage Gen 2| **ABFS:**//URL/ | **Większej** | Tak | Przejściowy klaster |
| Azure Data Lake Storage Gen 1| **ADL:**//URL/ | **Większej** | Tak | Przejściowy klaster |
| Lokalny system plików HDFS | **HDFS:**//URL/ | **Najlepszy** | Nie | Interaktywny klaster 24/7 |

Pełny opis opcji magazynu można znaleźć w temacie [porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Użyj pamięci podręcznej

Platforma Spark zapewnia własne natywne mechanizmy buforowania, które mogą być używane przez różne metody, `.persist()`takie `.cache()`jak, `CACHE TABLE`, i. Natywne buforowanie jest efektywne w przypadku małych zestawów danych i potoków ETL, w których należy przechować pośrednie wyniki. Jednak natywne buforowanie Spark nie działa prawidłowo z partycjonowaniem, ponieważ w pamięci podręcznej tabela nie zachowuje danych partycjonowania. Bardziej generyczną i niezawodną techniką buforowania jest *buforowanie warstwy magazynowania*.

* Natywne buforowanie Spark (niezalecane)
    * Dobre dla małych zestawów danych.
    * Nie działa z partycjonowaniem, co może ulec zmianie w przyszłych wersjach platformy Spark.

* Buforowanie na poziomie magazynu (zalecane)
    * Można zaimplementować w usłudze HDInsight przy użyciu funkcji [pamięci podręcznej we/wy](apache-spark-improve-performance-iocache.md) .
    * Używa buforowania w pamięci i dysku SSD.

* Lokalny system plików HDFS (zalecane)
    * `hdfs://mycluster`ścieżka.
    * Używa buforowania SSD.
    * Dane w pamięci podręcznej zostaną utracone po usunięciu klastra, co wymaga odbudowania pamięci podręcznej.

## <a name="use-memory-efficiently"></a>Wydajne korzystanie z pamięci

Platforma Spark działa przez umieszczenie danych w pamięci. Zarządzanie zasobami pamięci jest kluczowym aspektem optymalizacji wykonywania zadań platformy Spark.  Istnieje kilka technik, które można zastosować do wydajnego używania pamięci klastra.

* Preferuj mniejsze partycje danych i konta na potrzeby rozmiaru danych, typów i dystrybucji w strategii partycjonowania.
* Weź pod uwagę nowszą, [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo)wydajniejszą, a nie domyślną serializację języka Java.
* Preferuj używanie PRZĘDZy, która jest oddzielana `spark-submit` przez usługę Batch.
* Monitorowanie i dostrajanie ustawień konfiguracji platformy Spark.

Dla odwołania, struktura pamięci platformy Spark i niektóre parametry pamięci modułu wykonującego klucze są wyświetlane na następnym obrazie.

### <a name="spark-memory-considerations"></a>Uwagi dotyczące pamięci platformy Spark

Jeśli używasz Apache Hadoop PRZĘDZy, PRZĘDZa kontroluje pamięć używaną przez wszystkie kontenery w każdym węźle Spark.  Na poniższym diagramie przedstawiono obiekty kluczowe i ich relacje.

![Zarządzanie pamięcią w ramach PRZĘDZy](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Aby rozwiązać komunikaty o braku pamięci, spróbuj wykonać następujące działania:

* Przejrzyj DAGe w celu zarządzania nimi. Zmniejszaj dane źródłowe, które znajdują się na dysku (lub dzielenia), Maksymalizuj, przełączając pojedyncze losowo i zmniejszając ilość wysyłanych danych.
* Preferuj `ReduceByKey` ze stałym limitem pamięci `GroupByKey`na, który zapewnia agregacje, okna i inne funkcje, ale ma limit pamięci nieograniczonej Ann.
* Preferuj `TreeReduce`, która wykonuje więcej pracy na wszystkich wykonawcach lub partycjach, `Reduce`do, które działają na sterowniku.
* Używaj ramek dataframes zamiast obiektów RDD niskiego poziomu.
* Utwórz ComplexType, które hermetyzują akcje, takie jak "pierwsze N", różne agregacje lub operacje okienkowe.

Dodatkowe kroki rozwiązywania problemów można znaleźć [w temacie OutOfMemoryError Exceptions for Apache Spark in Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="optimize-data-serialization"></a>Optymalizowanie serializacji danych

Zadania platformy Spark są dystrybuowane, więc odpowiednia Serializacja danych jest ważna dla najlepszej wydajności.  Istnieją dwie opcje serializacji dla platformy Spark:

* Wartością domyślną jest Serializacja języka Java.
* `Kryo`Serializacja jest w nowszym formacie i może prowadzić do szybszej i bardziej kompaktowej serializacji niż w przypadku języka Java.  `Kryo`wymaga zarejestrowania klas w programie i nie obsługuje jeszcze wszystkich typów możliwych do serializacji.

## <a name="use-bucketing"></a>Używanie zasobników

Przedziałowanie jest podobne do partycjonowania danych. Ale każdy zasobnik może przechowywać zestaw wartości kolumn, a nie tylko jeden. Ta metoda dobrze sprawdza się w przypadku partycjonowania na dużą liczbę (w milionach lub więcej) wartości, takich jak identyfikatory produktów. Zasobnik jest określany przez mieszanie klucza zasobnika wiersza. Tabele z przedziałów oferują unikatowe optymalizacje, ponieważ przechowują metadane dotyczące ich przedziałów i sortowania.

Niektóre zaawansowane funkcje zasobników to:

* Optymalizacja zapytania oparta na meta-informacjach o przedziale.
* Zoptymalizowane agregacje.
* Zoptymalizowane sprzężenia.

Można używać partycjonowania i tworzenia pakietów jednocześnie.

## <a name="optimize-joins-and-shuffles"></a>Optymalizuj sprzężenia i Losuj

Jeśli masz wolne zadania w sprzężeniu lub losowo, przyczyną jest prawdopodobnie *pochylenie danych*. Skośność danych jest asymetrią w danych zadania. Na przykład zadanie mapy może trwać 20 sekund. Jednak uruchomienie zadania, w którym dane są przyłączone lub przetworzone w ciągu kilku godzin. Aby naprawić pochylenie danych, należy pozbyć się całego klucza lub użyć *odizolowanej soli* tylko dla pewnego podzestawu kluczy. Jeśli używasz izolowanej soli, należy dodatkowo przefiltrować, aby odizolować podzbiór kluczy solonych w sprzężeniu mapy. Kolejną opcją jest wprowadzenie kolumny przedziału i wstępnego agregacji w zasobnikach.

Innym czynnikiem powodującym powolne sprzężenie może być typ sprzężenia. Domyślnie platforma Spark używa typu `SortMerge` sprzężenia. Ten typ sprzężenia jest najlepiej dostosowany do dużych zestawów danych. Ale jest w inny sposób wyliczany, ponieważ należy najpierw posortować po lewej i prawej stronie danych przed ich scaleniem.

`Broadcast` Sprzężenie najlepiej nadaje się w przypadku mniejszych zestawów danych lub gdy jedna strona sprzężenia jest znacznie mniejsza od drugiej strony. Ten typ sprzężenia emituje jeden bok do wszystkich wykonawców, a więc wymaga więcej pamięci do emisji ogólnie.

Można zmienić typ sprzężenia w konfiguracji przez ustawienie `spark.sql.autoBroadcastJoinThreshold`lub można ustawić wskazówkę sprzężenia przy użyciu interfejsów API Dataframe (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Jeśli używasz tabel z przedziałem, możesz `Merge` dołączyć trzeci typ sprzężenia. Prawidłowo wstępnie podzielony i wstępnie posortowany zestaw danych pominie kosztowną fazę sortowania z `SortMerge` sprzężenia.

Kolejność sprzężeń, szczególnie w bardziej złożonych zapytaniach. Zacznij od najbardziej wybiórczych sprzężeń. Ponadto należy przenieść sprzężenia, które zwiększają liczbę wierszy po agregacji, jeśli jest to możliwe.

Aby zarządzać równoległością sprzężeń kartezjańskiego, można dodać zagnieżdżone struktury, okna i prawdopodobnie pominąć jeden lub więcej kroków w zadaniu platformy Spark.

## <a name="customize-cluster-configuration"></a>Dostosuj konfigurację klastra

W zależności od obciążenia klastra platformy Spark można określić niedomyślną konfigurację platformy Spark, co spowoduje bardziej zoptymalizowane wykonywanie zadań platformy Spark.  Wykonaj testy testowe z przykładowymi obciążeniami, aby zweryfikować wszystkie inne niż domyślne konfiguracje klastrów.

Poniżej przedstawiono niektóre typowe parametry, które można dostosować:

|Parametr |Opis |
|---|---|
|--NUM-wykonawcy|Ustawia odpowiednią liczbę wykonawców.|
|--wykonawcze-rdzenie|Ustawia liczbę rdzeni dla każdego wykonawcy. Zwykle powinny być stosowane wykonawcy o rozmiarze średnim, ponieważ inne procesy zużywają część dostępnej pamięci.|
|--Wykonawca pamięci|Ustawia rozmiar pamięci dla każdego wykonawcy, który kontroluje rozmiar sterty w PRZĘDZe. Pozostaw pewną ilość pamięci, aby wykonać obciążenie.|

### <a name="select-the-correct-executor-size"></a>Wybierz odpowiedni rozmiar wykonawcy

Podczas podejmowania decyzji o konfiguracji programu wykonującego należy wziąć pod uwagę narzut na wyrzucanie elementów bezużytecznych języka Java.

* Czynniki zmniejszające rozmiar programu wykonującego:
    1. Zmniejsz rozmiar sterty poniżej 32 GB, aby zachować obciążenie GC < 10%.
    2. Zmniejsz liczbę rdzeni, aby zachować obciążenie GC < 10%.

* Czynniki zwiększające rozmiar wykonawcy:
    1. Zmniejszenie obciążenia komunikacji między wykonawcami.
    2. Zmniejsz liczbę otwartych połączeń między modułami wykonującymi (N2) w większych klastrach (>100).
    3. Zwiększ rozmiar sterty, aby zapewnić obsługę zadań intensywnie korzystających z pamięci.
    4. Opcjonalnie: Zmniejsz obciążenie pamięci dla modułu wykonawczego.
    5. Opcjonalne: Zwiększ użycie i współbieżność przez zasubskrybowanie procesora CPU.

Jako ogólna zasada, podczas wybierania rozmiaru wykonawcy:

1. Zacznij od 30 GB na wykonawcę i Dystrybuuj dostępne rdzenie maszynowe.
2. Zwiększ liczbę rdzeni wykonawców dla większych klastrów (> 100).
3. Zmodyfikuj rozmiar na podstawie zarówno w przypadku wersji próbnej, jak i w powyższych czynnikach, takich jak obciążenie GC.

Podczas uruchamiania współbieżnych zapytań należy rozważyć następujące kwestie:

1. Zacznij od 30 GB na wykonawcę i wszystkich rdzeni maszynowych.
2. Utwórz wiele równoległych aplikacji platformy Spark przez zasubskrybowanie procesora CPU (około 30% poprawy opóźnienia).
3. Dystrybuuj zapytania w aplikacjach równoległych.
4. Zmodyfikuj rozmiar na podstawie zarówno w przypadku wersji próbnej, jak i w powyższych czynnikach, takich jak obciążenie GC.

Aby uzyskać więcej informacji na temat używania Ambari do konfigurowania modułów wykonujących, zobacz [ustawienia Apache Spark — wykonawcze platformy Spark](apache-spark-settings.md#configuring-spark-executors).

Monitoruj wydajność zapytań pod kątem wartości odstających lub innych problemów z wydajnością, przeglądając widok oś czasu. Program SQL Graph, statystyki zadań i tak dalej. Aby uzyskać informacje na temat debugowania zadań platformy Spark przy użyciu PRZĘDZy i serwera historii platformy Spark, zobacz [debugowanie Apache Spark zadania uruchomione w usłudze Azure HDInsight](apache-spark-job-debugging.md). Aby uzyskać porady na temat używania serwera osi czasu PRZĘDZy, zobacz [dostęp Apache Hadoop Dzienniki aplikacji przędzy](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Czasami jeden lub kilka z wykonawców jest wolniejsze niż inne, a wykonywanie zadań trwa znacznie dłużej. Ten spowolnienie często odbywa się w większych klastrach (> 30 węzłach). W takim przypadku należy podzielić pracę na większą liczbę zadań, aby harmonogram mógł wyrównać powolne zadania. Na przykład należy mieć co najmniej dwa razy więcej zadań jako liczbę rdzeni wykonawców w aplikacji. Możesz również włączyć spekulacyjne wykonywanie zadań za pomocą `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optymalizuj wykonywanie zadania

* Pamięć podręczna w razie potrzeby, na przykład w przypadku używania danych dwa razy, należy ją buforować.
* Emituj zmienne do wszystkich wykonawców. Zmienne są serializowane tylko raz, co powoduje szybsze wyszukiwanie.
* Użyj puli wątków w sterowniku, co skutkuje krótszą operacją dla wielu zadań.

Regularnie Monitoruj uruchomione zadania pod kątem problemów z wydajnością. Jeśli potrzebujesz więcej szczegółowych informacji na temat niektórych problemów, weź pod uwagę jeden z następujących narzędzi profilowania wydajności:

* [Narzędzie Intel PAL](https://github.com/intel-hadoop/PAT) monitoruje użycie procesora CPU, magazynu i przepustowości sieci.
* Program [Oracle Java 8 profil kontroli misja](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) Spark i kod wykonawczy.

Klucz do wydajności zapytań Spark 2. x jest aparatem, który zależy od generacji całego etapu. W niektórych przypadkach generowanie kodu w całym etapie może być wyłączone. Na przykład, jeśli w wyrażeniu agregacji jest używany niemodyfikowalny typ (`string`), `SortAggregate` pojawia się zamiast `HashAggregate`. Na przykład w celu uzyskania lepszej wydajności spróbuj wykonać następujące czynności, a następnie ponownie włączyć generowanie kodu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Następne kroki

* [Debugowanie zadań platformy Apache Spark uruchomionych w usłudze Azure HDInsight](apache-spark-job-debugging.md)
* [Zarządzanie zasobami klastra Apache Spark w usłudze HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)
* [Dostrajanie Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Jak w rzeczywistości dostroić zadania Apache Spark, aby działały](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [`Kryo Serialization`](https://github.com/EsotericSoftware/kryo)
