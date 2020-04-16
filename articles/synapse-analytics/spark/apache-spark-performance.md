---
title: Optymalizuj zadania platformy Spark pod kątem wydajności w usłudze Azure Synapse Analytics
description: Ten artykuł zawiera wprowadzenie do platformy Apache Spark w usłudze Azure Synapse Analytics i różnych pojęć.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6ffe7f3d9faf82c892975e9ffa03b383d3610c36
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424622"
---
# <a name="optimize-apache-spark-jobs-preview-in-azure-synapse-analytics"></a>Optymalizuj zadania Apache Spark (wersja zapoznawcza) w usłudze Azure Synapse Analytics

Dowiedz się, jak zoptymalizować konfigurację klastra [Apache Spark](https://spark.apache.org/) dla określonego obciążenia.  Najczęstszym wyzwaniem jest ciśnienie pamięci, z powodu nieprawidłowych konfiguracji (szczególnie niewłaściwych rozmiarów executors), długotrwałych operacji i zadań, które powodują operacje kartezjańskie. Można przyspieszyć zadania z odpowiednim buforowania i pozwalając na [pochylenie danych](#optimize-joins-and-shuffles). Aby uzyskać najlepszą wydajność, należy monitorować i przeglądać długotrwałe i zużywające zasoby wykonania zadań platformy Spark.

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

Najlepszym formatem wydajności jest parkiet z *kompresją zgryźliwą*, która jest domyślną w Spark 2.x. Parkiet przechowuje dane w formacie kolumnowym i jest wysoce zoptymalizowany w spark. Ponadto podczas *zgryźliwej kompresji* może spowodować większe pliki niż powiedzieć kompresji gzip. Ze względu na podzielony charakter tych plików będą one szybciej rozpakować]

## <a name="use-the-cache"></a>Korzystanie z pamięci podręcznej

Spark zapewnia własne rodzime mechanizmy buforowania, które mogą `.persist()` `.cache()`być `CACHE TABLE`używane za pomocą różnych metod, takich jak , i . To natywne buforowanie jest skuteczne w przypadku małych zestawów danych, a także w potokach ETL, w których należy buforować wyniki pośrednie. Jednak buforowanie natywne platformy Spark obecnie nie działa dobrze z partycjonowania, ponieważ w buforowanej tabeli nie przechowuje danych partycjonowania.

## <a name="use-memory-efficiently"></a>Wydajne korzystanie z pamięci

Platforma Spark działa poprzez umieszczanie danych w pamięci, więc zarządzanie zasobami pamięci jest kluczowym aspektem optymalizacji wykonywania zadań platformy Spark.  Istnieje kilka technik, które można zastosować do efektywnego korzystania z pamięci klastra.

* Preferuj mniejsze partycje danych i konto dla rozmiaru danych, typów i dystrybucji w strategii partycjonowania.
* Należy wziąć pod uwagę nowsze, bardziej wydajne [serializacji danych Kryo](https://github.com/EsotericSoftware/kryo), a nie domyślnej serializacji Java.
* Monitoruj i dostrajaj ustawienia konfiguracji platformy Spark.

Dla porównania struktura pamięci Spark i niektóre parametry pamięci wykonawcy klucza są wyświetlane na następnym obrazie.

### <a name="spark-memory-considerations"></a>Zagadnienia dotyczące pamięci iskierki

Apache Spark w usłudze Azure Synapse używa YARN [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), YARN kontroluje maksymalną sumę pamięci używanej przez wszystkie kontenery w każdym węźle Platformy Spark.  Na poniższym diagramie przedstawiono kluczowe obiekty i ich relacje.

![Zarządzanie pamięcią iskry przędzy](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Aby rozwiązać komunikaty "poza pamięcią", spróbuj:

* Przegląd DAG Management Shuffles. Zmniejsz przez zmniejszenie po stronie mapy, dane źródłowe przed partycją (lub bucketize), maksymalizuj pojedyncze przetasowania i zmniejsz ilość wysyłanych danych.
* Preferuj `ReduceByKey` ze stałym `GroupByKey`limitem pamięci do , który zapewnia agregacje, okna i inne funkcje, ale ma ann nieograniczony limit pamięci.
* Preferuj `TreeReduce`, który wykonuje więcej pracy na `Reduce`wykonawcach lub partycjach, do , który działa na sterowniku.
* Wykorzystaj elementy DataFrame zamiast obiektów RDD niższego poziomu.
* Tworzenie ComplexTypes, które hermetyzują akcje, takie jak "Top N", różne agregacje lub operacje okna.

## <a name="optimize-data-serialization"></a>Optymalizacja serializacji danych

Zadania platformy Spark są dystrybuowane, więc odpowiednie serializacji danych jest ważne dla najlepszej wydajności.  Istnieją dwie opcje serializacji dla platformy Spark:

* Serializacja w języku Java jest domyślna.
* Kryo serializacji jest nowszy format i może spowodować szybsze i bardziej kompaktowe serializacji niż Java.  Kryo wymaga zarejestrowania klas w programie i nie obsługuje jeszcze wszystkich typów serializable.

## <a name="use-bucketing"></a>Użyj wiadra

Zasobnik jest podobny do partycjonowania danych, ale każde wiadro może zawierać zestaw wartości kolumn, a nie tylko jeden. Bucketing działa dobrze do partycjonowania na dużych (w milionach lub więcej) liczba wartości, takich jak identyfikatory produktów. Wiadro jest określane przez mieszanie klucza łyżki wiersza. Tabele wyiedzone w zasobniku oferują unikatowe optymalizacje, ponieważ przechowują metadane dotyczące sposobu ich posortowania i sortowania.

Niektóre zaawansowane funkcje wiadra to:

* Optymalizacja zapytań na podstawie metainformaty w zasobnikach.
* Zoptymalizowane agregacje.
* Zoptymalizowane sprzężenia.

Można użyć partycjonowania i zasobników w tym samym czasie.

## <a name="optimize-joins-and-shuffles"></a>Optymalizuj sprzężenia i przetasowania

Jeśli masz wolne zadania na Sprzężenie lub Shuffle, przyczyną jest prawdopodobnie *pochylenie danych*, który jest asymetrii w danych zadania. Na przykład zadanie mapy może potrwać 20 sekund, ale uruchomienie zadania, w którym dane są przyłączane lub tasowane, trwa wiele godzin. Aby naprawić pochylenie danych, należy sól cały klucz lub użyć *izolowanej soli* tylko dla niektórych podzbiór kluczy. Jeśli używasz izolowanej soli, należy dodatkowo filtrować, aby wyizolować podzbiór solonych kluczy w sprzężeniach map. Inną opcją jest wprowadzenie kolumny zasobnika i wstępnie agregacji w zasobnikach pierwszy.

Innym czynnikiem powodującym powolne sprzężenia może być typ sprzężenia. Domyślnie spark używa `SortMerge` typu sprzężenia. Ten typ sprzężenia najlepiej nadaje się do dużych zestawów danych, ale w przeciwnym razie jest kosztowny pod względem obliczeniowym, ponieważ musi najpierw posortować lewą i prawą stronę danych przed ich scaleniem.

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

### <a name="select-the-correct-executor-size"></a>Wybierz odpowiedni rozmiar wykonawcy

Przy podejmowaniu decyzji konfiguracji wykonawca, należy wziąć pod uwagę java wyrzucania elementów bezużytecznych (GC) obciążenie.

* Czynniki zmniejszające rozmiar wykonawcy:
  * Zmniejsz rozmiar sterty poniżej 32 GB, aby zachować obciążenie GC < 10%.
  * Zmniejsz liczbę rdzeni, aby utrzymać obciążenie GC < 10%.

* Czynniki zwiększające rozmiar wykonawcy:
  * Zmniejsz obciążenie komunikacji między wykonawcami.
  * Zmniejsz liczbę otwartych połączeń między executorami (N2) w większych klastrach (>100 executorów).
  * Zwiększ rozmiar sterty, aby pomieścić zadania wymagające dużej ilości pamięci.
  * Opcjonalnie: Zmniejsz obciążenie pamięci na wykonawcę.
  * Opcjonalnie: Zwiększ wykorzystanie i współbieżność przez nadmierne subskrybowanie procesora CPU.

Zgodnie z ogólną zasadą przy wyborze rozmiaru wykonawcy:

* Zacznij od 30 GB na wykonawcę i rozprowauj dostępne rdzenie maszyny.
* Zwiększ liczbę rdzeni executor dla większych klastrów (> 100 executors).
* Modyfikuj rozmiar na podstawie przebiegów próbnych i na poprzednich czynnikach, takich jak obciążenie GC.

Podczas uruchamiania równoczesnych zapytań należy wziąć pod uwagę następujące kwestie:

* Zacznij od 30 GB na wykonawcę i wszystkie rdzenie maszyny.
* Tworzenie wielu równoległych aplikacji Spark przez oversubscribing CPU (około 30% poprawy opóźnienia).
* Rozdziel zapytania w aplikacjach równoległych.
* Modyfikuj rozmiar na podstawie przebiegów próbnych i na poprzednich czynnikach, takich jak obciążenie GC.

Monitoruj wydajność kwerendy pod kątem odstających lub innych problemów z wydajnością, patrząc na widok osi czasu, wykres SQL, statystyki zadań i tak dalej. Czasami jeden lub kilka executors są wolniejsze niż inne, a zadania trwać znacznie dłużej do wykonania. Często dzieje się tak w przypadku większych klastrów (> 30 węzłów). W takim przypadku należy podzielić pracę na większą liczbę zadań, aby harmonogram mógł zrekompensować powolne zadania. 

Na przykład mają co najmniej dwa razy więcej zadań niż liczba rdzeni executor w aplikacji. Można również włączyć spekulacyjne `conf: spark.speculation = true`wykonywanie zadań za pomocą .

## <a name="optimize-job-execution"></a>Optymalizacja wykonywania zadań

* Pamięć podręczna w razie potrzeby, na przykład jeśli używasz danych dwa razy, a następnie buforuj je.
* Emituj zmienne do wszystkich wykonawców. Zmienne są serializowane tylko raz, co powoduje szybsze wyszukiwanie.
* Użyj puli wątków na sterowniku, co powoduje szybsze działanie dla wielu zadań.

Kluczem do wydajności zapytania Spark 2.x jest aparat wolframu, który zależy od generowania kodu na całym etapie. W niektórych przypadkach generowanie kodu całego etapu może być wyłączone. 

Na przykład, jeśli w wyrażeniu`string`agregacji jest `SortAggregate` używany typ `HashAggregate`niezduszalny ( ) pojawia się zamiast pliku . Na przykład, aby uzyskać lepszą wydajność, spróbuj wykonać następujące czynności, a następnie ponownie włącz generowanie kodu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Następne kroki

- [Strojenie Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
- [Jak rzeczywiście Tune Your Apache Spark Praca więc pracują](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo Serializacja](https://github.com/EsotericSoftware/kryo)
