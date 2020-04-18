---
title: Optymalizuj zadania platformy Spark pod kątem wydajności — usługa Azure HDInsight
description: Pokaż typowe strategie najlepszej wydajności klastrów Platformy Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/17/2020
ms.openlocfilehash: 5012b5abf12beadbcb18f21fe2fe6ebfb076598a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617968"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optymalizuj zadania Apache Spark w hdinsight

Dowiedz się, jak zoptymalizować konfigurację klastra Apache Spark dla określonego obciążenia.  Najczęstszym wyzwaniem jest ciśnienie pamięci, z powodu nieprawidłowych konfiguracji (takich jak wykonawców o niewłaściwych rozmiarach). Ponadto długotrwałe operacje i zadania, które powodują operacje kartezjańskie. Można przyspieszyć zadania z odpowiednim buforowania i pozwalając na [pochylenie danych](#optimize-joins-and-shuffles). Aby uzyskać najlepszą wydajność, należy monitorować i przeglądać długotrwałe i zużywające zasoby wykonania zadań platformy Spark. Aby uzyskać informacje na temat rozpoczynania pracy z programem Apache Spark w programie HDInsight, zobacz [Tworzenie klastra platformy Spark apache przy użyciu portalu Azure](apache-spark-jupyter-spark-sql-use-portal.md).

W poniższych sekcjach opisano typowe optymalizacje i zalecenia dotyczące zadań platformy Spark.

## <a name="choose-the-data-abstraction"></a>Wybieranie abstrakcji danych

Starsze wersje platformy Spark używają rddów do wyodrębnienia danych, spark 1.3 i 1.6 wprowadziły dataframes i zestawy danych, odpowiednio. Należy wziąć pod uwagę następujące względne zalety:

* **Ramki danych**
    * Najlepszy wybór w większości sytuacji.
    * Zapewnia optymalizację zapytań za pośrednictwem programu Catalyst.
    * Generowanie kodu całego etapu.
    * Bezpośredni dostęp do pamięci.
    * Niskie obciążenie związane z wyrzucaniem elementów bezużytecznych (GC).
    * Nie tak przyjazne dla deweloperów jak Zestawy danych, ponieważ nie ma żadnych kontroli w czasie kompilacji lub programowania obiektów domeny.
* **Zestawach danych**
    * Dobre w złożonych rurociągach ETL, gdzie wpływ na wydajność jest dopuszczalny.
    * Nie jest dobre w agregacjach, gdzie wpływ na wydajność może być znaczny.
    * Zapewnia optymalizację zapytań za pośrednictwem programu Catalyst.
    * Przyjazne dla deweloperów, zapewniając programowanie obiektów domeny i sprawdzanie w czasie kompilacji.
    * Dodaje obciążenie serializacji/deserializacji.
    * Wysokie obciążenie GC.
    * Przerywa generowanie kodu całego etapu.
* **RdD**
    * Nie trzeba używać rdds, chyba że trzeba utworzyć nowy niestandardowy RDD.
    * Brak optymalizacji zapytań za pośrednictwem catalyst.
    * Brak generowania kodu całego etapu.
    * Wysokie obciążenie GC.
    * Należy używać starszych interfejsów API platformy Spark 1.x.

## <a name="use-optimal-data-format"></a>Użyj optymalnego formatu danych

Spark obsługuje wiele formatów, takich jak csv, json, xml, parkiet, ork i avro. Spark można rozszerzyć, aby obsługiwać wiele innych formatów z zewnętrznymi źródłami danych - aby uzyskać więcej informacji, zobacz [pakiety Apache Spark](https://spark-packages.org).

Najlepszym formatem wydajności jest parkiet z *kompresją zgryźliwą*, która jest domyślną w Spark 2.x. Parkiet przechowuje dane w formacie kolumnowym i jest wysoce zoptymalizowany w spark.

## <a name="select-default-storage"></a>Wybieranie magazynu domyślnego

Podczas tworzenia nowego klastra platformy Spark można wybrać usługę Azure Blob Storage lub Usługa Azure Data Lake Storage jako domyślny magazyn klastra. Obie opcje zapewniają korzyści z długoterminowego przechowywania dla klastrów przejściowych. Dzięki czemu dane nie są automatycznie usuwane po usunięciu klastra. Można odtworzyć klaster przejściowy i nadal uzyskiwać dostęp do danych.

| Typ sklepu | System plików | Szybkość | Przejściowe | Przypadki użycia |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standardowa** | Tak | Klaster przejściowy |
| Usługa Azure Blob Storage (bezpieczna) | **osy:**//url/ | **Standardowa** | Tak | Klaster przejściowy |
| Azure Data Lake Storage Gen 2| **abfs:**//url/ | **Szybciej** | Tak | Klaster przejściowy |
| Usługa Azure Data Lake Storage Gen 1| **adl:**//url/ | **Szybciej** | Tak | Klaster przejściowy |
| Lokalne pliki HDF | **hdfs:**//url/ | **Najszybszy** | Nie | Interaktywny klaster 24/7 |

Aby uzyskać pełny opis opcji magazynu, zobacz [Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Korzystanie z pamięci podręcznej

Spark zapewnia własne rodzime mechanizmy buforowania, które mogą `.persist()` `.cache()`być `CACHE TABLE`używane za pomocą różnych metod, takich jak , i . To natywne buforowanie jest skuteczne w przypadku małych zestawów danych i w potokach ETL, w których należy buforować wyniki pośrednie. Jednak buforowanie natywne platformy Spark obecnie nie działa dobrze z partycjonowania, ponieważ w buforowanej tabeli nie przechowuje danych partycjonowania. Bardziej ogólną i niezawodną techniką buforowania jest *buforowanie warstw magazynowych.*

* Natywne buforowanie iskry (nie zalecane)
    * Dobre dla małych zestawów danych.
    * Nie działa z partycjonowania, które mogą ulec zmianie w przyszłych wersjach Platformy Spark.

* Buforowanie na poziomie pamięci masowej (zalecane)
    * Można zaimplementować na HDInsight za pomocą funkcji [Pamięci podręcznej we/wy.](apache-spark-improve-performance-iocache.md)
    * Używa buforowania w pamięci i SSD.

* Lokalne hdfs (zalecane)
    * `hdfs://mycluster`Ścieżka.
    * Używa buforowania SSD.
    * Buforowane dane zostaną utracone po usunięciu klastra, co wymaga odbudowania pamięci podręcznej.

## <a name="use-memory-efficiently"></a>Wydajne korzystanie z pamięci

Spark działa poprzez umieszczenie danych w pamięci. Zarządzanie zasobami pamięci jest kluczowym aspektem optymalizacji wykonywania zadań platformy Spark.  Istnieje kilka technik, które można zastosować do efektywnego korzystania z pamięci klastra.

* Preferuj mniejsze partycje danych i konto dla rozmiaru danych, typów i dystrybucji w strategii partycjonowania.
* Należy wziąć pod uwagę [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo)nowsze, bardziej wydajne , a nie domyślnej serializacji Java.
* Preferuj używanie przędzy, `spark-submit` ponieważ oddziela się ono partią.
* Monitoruj i dostrajaj ustawienia konfiguracji platformy Spark.

Dla porównania struktura pamięci Spark i niektóre parametry pamięci wykonawcy klucza są wyświetlane na następnym obrazie.

### <a name="spark-memory-considerations"></a>Zagadnienia dotyczące pamięci iskierki

Jeśli używasz Apache Hadoop YARN, a następnie YARN kontroluje pamięć używaną przez wszystkie kontenery w każdym węźle Platformy Spark.  Na poniższym diagramie przedstawiono kluczowe obiekty i ich relacje.

![Zarządzanie pamięcią iskry przędzy](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Aby rozwiązać komunikaty "poza pamięcią", spróbuj:

* Przegląd DAG Management Shuffles. Zmniejsz przez zmniejszenie po stronie mapy, dane źródłowe przed partycją (lub bucketize), maksymalizuj pojedyncze przetasowania i zmniejsz ilość wysyłanych danych.
* Preferuj `ReduceByKey` ze stałym `GroupByKey`limitem pamięci do , który zapewnia agregacje, okna i inne funkcje, ale ma ann nieograniczony limit pamięci.
* Preferuj `TreeReduce`, który wykonuje więcej pracy na `Reduce`wykonawcach lub partycjach, do , który działa na sterowniku.
* Użyj dataframes zamiast obiektów RDD niższego poziomu.
* Tworzenie ComplexTypes, które hermetyzują akcje, takie jak "Top N", różne agregacje lub operacje okna.

Aby uzyskać dodatkowe kroki rozwiązywania problemów, zobacz [OutOfMemoryError wyjątki dla Platformy Apache Spark w usłudze Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="optimize-data-serialization"></a>Optymalizacja serializacji danych

Zadania platformy Spark są dystrybuowane, więc odpowiednie serializacji danych jest ważne dla najlepszej wydajności.  Istnieją dwie opcje serializacji dla platformy Spark:

* Serializacja w języku Java jest domyślna.
* `Kryo`serializacja jest nowszym formatem i może skutkować szybszą i bardziej kompaktową serializacją niż Java.  `Kryo`wymaga zarejestrowania klas w programie i nie obsługuje jeszcze wszystkich typów serializable.

## <a name="use-bucketing"></a>Użyj wiadra

Zasobnik jest podobny do partycjonowania danych. Ale każde wiadro może zawierać zestaw wartości kolumn, a nie tylko jeden. Ta metoda działa dobrze do partycjonowania na dużych (w milionach lub więcej) liczba wartości, takich jak identyfikatory produktu. Wiadro jest określane przez mieszanie klucza łyżki wiersza. Tabele wyiedzone w zasobniku oferują unikatowe optymalizacje, ponieważ przechowują metadane dotyczące sposobu ich posortowania i sortowania.

Niektóre zaawansowane funkcje wiadra to:

* Optymalizacja zapytań na podstawie metainformaty w zasobnikach.
* Zoptymalizowane agregacje.
* Zoptymalizowane sprzężenia.

Można użyć partycjonowania i zasobników w tym samym czasie.

## <a name="optimize-joins-and-shuffles"></a>Optymalizuj sprzężenia i przetasowania

Jeśli masz wolne zadania na Sprzężenie lub Shuffle, przyczyną jest prawdopodobnie *pochylenie danych*. Pochylenie danych jest asymetrią w danych zadania. Na przykład zadanie mapy może potrwać 20 sekund. Ale uruchomienie zadania, w którym dane są przyłączane lub tasowane, trwa wiele godzin. Aby naprawić pochylenie danych, należy sól cały klucz lub użyć *izolowanej soli* tylko dla niektórych podzbiór kluczy. Jeśli używasz izolowanej soli, należy dodatkowo filtrować, aby wyizolować podzbiór solonych kluczy w sprzężeniach map. Inną opcją jest wprowadzenie kolumny zasobnika i wstępnie agregacji w zasobnikach pierwszy.

Innym czynnikiem powodującym powolne sprzężenia może być typ sprzężenia. Domyślnie spark używa `SortMerge` typu sprzężenia. Ten typ sprzężenia najlepiej nadaje się do dużych zestawów danych. Ale w przeciwnym razie jest kosztowne obliczeniowo, ponieważ musi najpierw posortować lewą i prawą stronę danych przed ich scaleniem.

Sprzężenie `Broadcast` najlepiej nadaje się do mniejszych zestawów danych lub gdy jedna strona sprzężenia jest znacznie mniejsza niż druga strona. Ten typ sprzężenia emituje jedną stronę do wszystkich wykonawców, a więc wymaga więcej pamięci dla transmisji w ogóle.

Typ sprzężenia można zmienić `spark.sql.autoBroadcastJoinThreshold`w konfiguracji, ustawiając lub za pomocą interfejsów API elementu DataFrame (`dataframe.join(broadcast(df2))`).

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

Jeśli używasz tabel wyiadowy, masz trzeci `Merge` typ sprzężenia, sprzężenie. Poprawnie wstępnie podzielony na partycje i wstępnie posortowany zestaw `SortMerge` danych pominie kosztowną fazę sortowania z sprzężenia.

Kolejność łączy sprawy, szczególnie w bardziej złożonych zapytań. Zacznij od najbardziej selektywnych sprzężeń. Ponadto przenieś sprzężenia, które zwiększają liczbę wierszy po agregacji, gdy jest to możliwe.

Aby zarządzać równoległości dla sprzężeń kartezjańskich, można dodać struktury zagnieżdżone, okna i być może pominąć jeden lub więcej kroków w zadaniu platformy Spark.

## <a name="customize-cluster-configuration"></a>Dostosowywanie konfiguracji klastra

W zależności od obciążenia klastra platformy Spark można określić, że nieobsadza konfiguracja platformy Spark spowoduje bardziej zoptymalizowane wykonanie zadania platformy Spark.  Testowanie porównawcze z przykładowymi obciążeniami w celu sprawdzenia poprawności wszystkich konfiguracji klastra innych niż domyślne.

Oto kilka typowych parametrów, które można dostosować:

|Parametr |Opis |
|---|---|
|--num-executory|Ustawia odpowiednią liczbę executors.|
|--executor-rdzenie|Ustawia liczbę rdzeni dla każdego wykonawcy. Zazwyczaj powinny mieć średniej wielkości executors, jak inne procesy zużywają niektóre z dostępnej pamięci.|
|--executor-memory|Ustawia rozmiar pamięci dla każdego executora, który kontroluje rozmiar sterty na YARN. Pozostaw trochę pamięci do wykonania narzutów.|

### <a name="select-the-correct-executor-size"></a>Wybierz odpowiedni rozmiar wykonawcy

Przy podejmowaniu decyzji konfiguracji wykonawca, należy wziąć pod uwagę java wyrzucania elementów bezużytecznych (GC) obciążenie.

* Czynniki zmniejszające rozmiar wykonawcy:
    1. Zmniejsz rozmiar sterty poniżej 32 GB, aby zachować obciążenie GC < 10%.
    2. Zmniejsz liczbę rdzeni, aby utrzymać obciążenie GC < 10%.

* Czynniki zwiększające rozmiar wykonawcy:
    1. Zmniejsz obciążenie komunikacji między wykonawcami.
    2. Zmniejsz liczbę otwartych połączeń między executorami (N2) w większych klastrach (>100 executorów).
    3. Zwiększ rozmiar sterty, aby pomieścić zadania wymagające dużej ilości pamięci.
    4. Opcjonalnie: Zmniejsz obciążenie pamięci na wykonawcę.
    5. Opcjonalnie: Zwiększ użycie i współbieżność przez nadmierne subskrybowanie procesora CPU.

Zgodnie z ogólną zasadą, przy wyborze rozmiaru wykonawcy:

1. Zacznij od 30 GB na wykonawcę i rozprowauj dostępne rdzenie maszyny.
2. Zwiększ liczbę rdzeni executor dla większych klastrów (> 100 executors).
3. Modyfikuj rozmiar na podstawie przebiegów próbnych i na poprzednich czynnikach, takich jak obciążenie GC.

Podczas uruchamiania równoczesnych zapytań należy wziąć pod uwagę:

1. Zacznij od 30 GB na wykonawcę i wszystkie rdzenie maszyny.
2. Tworzenie wielu równoległych aplikacji Spark przez oversubscribing CPU (około 30% poprawy opóźnienia).
3. Rozdziel zapytania w aplikacjach równoległych.
4. Modyfikuj rozmiar na podstawie przebiegów próbnych i na poprzednich czynnikach, takich jak obciążenie GC.

Aby uzyskać więcej informacji na temat używania Ambari do konfigurowania executorów, zobacz [Ustawienia platformy Spark Apache - Spark executors](apache-spark-settings.md#configuring-spark-executors).

Monitorowanie wydajności zapytań w przypadku odstających lub innych problemów z wydajnością, patrząc na widok osi czasu. Również wykres SQL, statystyki pracy i tak dalej. Aby uzyskać informacje na temat debugowania zadań platformy Spark przy użyciu programu YARN i serwera spark History, zobacz [Zadania debugowania platformy Spark uruchomione w usłudze Azure HDInsight](apache-spark-job-debugging.md). Aby uzyskać wskazówki dotyczące korzystania z serwera osi czasu YARN, zobacz [Dostęp do dzienników aplikacji Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Czasami jeden lub kilka executors są wolniejsze niż inne, a zadania trwać znacznie dłużej do wykonania. Ta powolność często występuje w większych klastrach (> 30 węzłów). W takim przypadku należy podzielić pracę na większą liczbę zadań, aby harmonogram mógł zrekompensować powolne zadania. Na przykład mają co najmniej dwa razy więcej zadań niż liczba rdzeni executor w aplikacji. Można również włączyć spekulacyjne `conf: spark.speculation = true`wykonywanie zadań za pomocą .

## <a name="optimize-job-execution"></a>Optymalizacja wykonywania zadań

* Pamięć podręczna w razie potrzeby, na przykład jeśli używasz danych dwa razy, a następnie buforuj je.
* Emituj zmienne do wszystkich wykonawców. Zmienne są serializowane tylko raz, co powoduje szybsze wyszukiwanie.
* Użyj puli wątków na sterowniku, co powoduje szybsze działanie dla wielu zadań.

Regularnie monitoruj uruchomione zadania pod kątem problemów z wydajnością. Jeśli potrzebujesz więcej informacji na temat niektórych problemów, należy wziąć pod uwagę jeden z następujących narzędzi do profilowania wydajności:

* [Narzędzie Intel PAL](https://github.com/intel-hadoop/PAT) monitoruje użycie procesora, pamięci masowej i przepustowości sieci.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profiluje spark i kod wykonawcy.

Kluczem do wydajności zapytania Spark 2.x jest aparat wolframu, który zależy od generowania kodu na całym etapie. W niektórych przypadkach generowanie kodu całego etapu może być wyłączone. Na przykład, jeśli w wyrażeniu`string`agregacji jest `SortAggregate` używany typ `HashAggregate`niezduszalny ( ) pojawia się zamiast pliku . Na przykład, aby uzyskać lepszą wydajność, spróbuj wykonać następujące czynności, a następnie ponownie włącz generowanie kodu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Następne kroki

* [Debugowanie zadań platformy Apache Spark uruchomionych w usłudze Azure HDInsight](apache-spark-job-debugging.md)
* [Zarządzanie zasobami klastra Platformy Spark apache w programie HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)
* [Strojenie Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Jak rzeczywiście Tune Your Apache Spark Praca więc pracują](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [`Kryo Serialization`](https://github.com/EsotericSoftware/kryo)
