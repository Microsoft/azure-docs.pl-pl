---
title: Optymalizowanie zadań platformy Spark pod kątem wydajności
description: Ten artykuł zawiera wprowadzenie do Apache Spark w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b94ece73d5f9dc9b8343e45fb1f616599b9a1c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96450931"
---
# <a name="optimize-apache-spark-jobs-in-azure-synapse-analytics"></a>Optymalizowanie Apache Spark zadań w usłudze Azure Synapse Analytics

Dowiedz się, jak zoptymalizować konfigurację klastra [Apache Spark](https://spark.apache.org/) dla określonego obciążenia.  Najczęstszym wyzwaniem jest wykorzystanie pamięci spowodowane niewłaściwą konfiguracją (zwłaszcza funkcji wykonawczych o nieprawidłowym rozmiarze), długotrwałymi operacjami i zadaniami, których wynikiem są działania kartezjańskie. Można przyspieszyć zadania z odpowiednimi buforowaniem i zezwalać na [pochylenie danych](#optimize-joins-and-shuffles). Aby uzyskać najlepszą wydajność, Monitoruj i sprawdzaj długotrwałe i czasochłonne wykonywanie zadań platformy Spark.

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
* **RDD**
  * Nie musisz używać odporne, chyba że trzeba utworzyć nową niestandardową RDD.
  * Brak optymalizacji zapytania za poorednictwem katalizatora.
  * Brak generowania kodu w całości.
  * Duże obciążenie GC.
  * Należy używać starszych interfejsów API Spark 1. x.

## <a name="use-optimal-data-format"></a>Korzystanie z optymalnego formatu danych

Platforma Spark obsługuje wiele formatów, takich jak CSV, JSON, XML, Parquet, Orc i Avro. Platforma Spark może zostać rozszerzona w celu obsługi wielu formatów z zewnętrznymi źródłami danych — Aby uzyskać więcej informacji, zobacz [Apache Spark Packages](https://spark-packages.org).

Najlepszym formatem wydajności jest Parquet z *kompresją przyciągania*, która jest wartością domyślną w platformie Spark 2. x. Parquet przechowuje dane w formacie kolumnowym i jest wysoce zoptymalizowany w Spark. Ponadto *kompresja przyciągania* może spowodować zwiększenie ilości plików niż w przypadku kompresji gzip. Ze względu na rodzaj podziału tych plików, będą one w szybszym dekompresji.

## <a name="use-the-cache"></a>Korzystanie z pamięci podręcznej

Platforma Spark zapewnia własne natywne mechanizmy buforowania, które mogą być używane przez różne metody, takie jak `.persist()` , `.cache()` , i `CACHE TABLE` . Natywne buforowanie jest efektywne w przypadku małych zestawów danych oraz potoków ETL, w których należy buforować pośrednie wyniki. Jednak natywne buforowanie Spark nie działa prawidłowo z partycjonowaniem, ponieważ w pamięci podręcznej tabela nie zachowuje danych partycjonowania.

## <a name="use-memory-efficiently"></a>Wydajne korzystanie z pamięci

Platforma Spark działa przez umieszczenie danych w pamięci, więc zarządzanie zasobami pamięci jest kluczowym aspektem optymalizacji wykonywania zadań platformy Spark.  Istnieje kilka technik, które można zastosować do wydajnego używania pamięci klastra.

* Preferuj mniejsze partycje danych i konta na potrzeby rozmiaru danych, typów i dystrybucji w strategii partycjonowania.
* Weź pod uwagę nowszą, wydajniejszą [serializację danych Kryo](https://github.com/EsotericSoftware/kryo)zamiast domyślnej serializacji języka Java.
* Monitorowanie i dostrajanie ustawień konfiguracji platformy Spark.

Dla odwołania, struktura pamięci platformy Spark i niektóre parametry pamięci modułu wykonującego klucze są wyświetlane na następnym obrazie.

### <a name="spark-memory-considerations"></a>Uwagi dotyczące pamięci platformy Spark

Apache Spark na platformie Azure Synapse korzysta z przędzy [Apache HADOOP](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)przędzy, a w przypadku wszystkich kontenerów w każdym węźle platformy Spark jest używana maksymalna suma pamięci.  Na poniższym diagramie przedstawiono obiekty kluczowe i ich relacje.

![Zarządzanie pamięcią w ramach PRZĘDZy](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Aby rozwiązać komunikaty o braku pamięci, spróbuj wykonać następujące działania:

* Przejrzyj DAGe w celu zarządzania nimi. Zredukuj dane źródłowe zmniejszania po stronie mapy (lub dzielenia), Maksymalizuj pojedyncze wartości losowe i Zmniejsz ilość wysyłanych danych.
* Preferuj `ReduceByKey` swój stały limit pamięci `GroupByKey` , który zapewnia agregacje, okna i inne funkcje, ale ma limit pamięci nieograniczonej.
* Preferuj `TreeReduce` , która wykonuje więcej pracy na wszystkich wykonawcach lub partycjach, do `Reduce` , które działają na sterowniku.
* Wykorzystanie ramek danych zamiast obiektów RDD niskiego poziomu.
* Utwórz ComplexType, które hermetyzują akcje, takie jak "pierwsze N", różne agregacje lub operacje okienkowe.

## <a name="optimize-data-serialization"></a>Optymalizowanie serializacji danych

Zadania platformy Spark są dystrybuowane, więc odpowiednia Serializacja danych jest ważna dla najlepszej wydajności.  Istnieją dwie opcje serializacji dla platformy Spark:

* Wartością domyślną jest Serializacja języka Java.
* Serializacja Kryo jest w nowszym formacie i może powodować szybsze i bardziej kompaktowe serializacji niż w przypadku języka Java.  Kryo wymaga zarejestrowania klas w programie i nie obsługuje jeszcze wszystkich typów możliwych do serializacji.

## <a name="use-bucketing"></a>Korzystanie z zasobników

Przepełnianie jest podobne do partycjonowania danych, ale każdy zasobnik może przechowywać zestaw wartości kolumn, a nie tylko jeden. Przedziały dobrze sprawdzają się w przypadku partycjonowania na dużą liczbę (w milionach lub więcej) wartości, takich jak identyfikatory produktów. Zasobnik jest określany przez mieszanie klucza zasobnika wiersza. Tabele z przedziałów oferują unikatowe optymalizacje, ponieważ przechowują metadane dotyczące ich przedziałów i sortowania.

Niektóre zaawansowane funkcje zasobników to:

* Optymalizacja zapytania oparta na meta-informacjach o przedziale.
* Zoptymalizowane agregacje.
* Zoptymalizowane sprzężenia.

Można używać partycjonowania i tworzenia pakietów jednocześnie.

## <a name="optimize-joins-and-shuffles"></a>Optymalizowanie sprzężeń i odczytów losowych

Jeśli masz wolne zadania w sprzężeniu lub losowo, przyczyną jest prawdopodobnie *pochylenie danych*, czyli asymetrii w danych zadania. Na przykład zadanie mapy może trwać 20 sekund, ale uruchomione jest zadanie, w którym dane są przyłączone lub przetworzone w postaci przełączenia. Aby naprawić pochylenie danych, należy pozbyć się całego klucza lub użyć *odizolowanej soli* tylko dla pewnego podzestawu kluczy. Jeśli używasz izolowanej soli, należy dodatkowo przefiltrować, aby odizolować podzbiór kluczy solonych w sprzężeniu mapy. Kolejną opcją jest wprowadzenie kolumny przedziału i wstępnego agregacji w zasobnikach.

Innym czynnikiem powodującym powolne sprzężenie może być typ sprzężenia. Domyślnie platforma Spark używa `SortMerge` typu sprzężenia. Ten typ sprzężenia jest najlepiej dostosowany do dużych zestawów danych, ale jest w inny sposób obliczany, ponieważ należy najpierw posortować po lewej i prawej stronie danych przed ich scaleniem.

`Broadcast`Sprzężenie najlepiej nadaje się w przypadku mniejszych zestawów danych lub gdy jedna strona sprzężenia jest znacznie mniejsza od drugiej strony. Ten typ sprzężenia emituje jeden bok do wszystkich wykonawców, a więc wymaga więcej pamięci do emisji ogólnie.

Można zmienić typ sprzężenia w konfiguracji przez ustawienie lub można `spark.sql.autoBroadcastJoinThreshold` ustawić wskazówkę sprzężenia przy użyciu interfejsów API Dataframe ( `dataframe.join(broadcast(df2))` ).

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

Jeśli używasz tabel z przedziałem, możesz dołączyć trzeci typ sprzężenia `Merge` . Prawidłowo wstępnie podzielony i wstępnie posortowany zestaw danych pominie kosztowną fazę sortowania z `SortMerge` sprzężenia.

Kolejność sprzężeń, szczególnie w bardziej złożonych zapytaniach. Zacznij od najbardziej wybiórczych sprzężeń. Ponadto należy przenieść sprzężenia, które zwiększają liczbę wierszy po agregacji, jeśli jest to możliwe.

Aby zarządzać równoległością sprzężeń kartezjańskiego, można dodać zagnieżdżone struktury, okna i prawdopodobnie pominąć jeden lub więcej kroków w zadaniu platformy Spark.

### <a name="select-the-correct-executor-size"></a>Wybierz odpowiedni rozmiar wykonawcy

Podczas podejmowania decyzji o konfiguracji programu wykonującego należy wziąć pod uwagę narzut na wyrzucanie elementów bezużytecznych języka Java.

* Czynniki zmniejszające rozmiar programu wykonującego:
  * Zmniejsz rozmiar sterty poniżej 32 GB, aby zachować obciążenie GC < 10%.
  * Zmniejsz liczbę rdzeni, aby zachować obciążenie GC < 10%.

* Czynniki zwiększające rozmiar wykonawcy:
  * Zmniejszenie obciążenia komunikacji między wykonawcami.
  * Zmniejsz liczbę otwartych połączeń między modułami wykonującymi (N2) w większych klastrach (>100).
  * Zwiększ rozmiar sterty, aby zapewnić obsługę zadań intensywnie korzystających z pamięci.
  * Opcjonalnie: Zmniejsz obciążenie pamięci dla modułu wykonawczego.
  * Opcjonalne: Zwiększ wykorzystanie i współbieżność przez zasubskrybowanie procesora CPU.

Jako ogólna reguła kciuka podczas wybierania rozmiaru wykonawcy:

* Zacznij od 30 GB na wykonawcę i Dystrybuuj dostępne rdzenie maszynowe.
* Zwiększ liczbę rdzeni wykonawców dla większych klastrów (> 100).
* Zmodyfikuj rozmiar na podstawie zarówno w przypadku wersji próbnej, jak i w powyższych czynnikach, takich jak obciążenie GC.

Podczas uruchamiania współbieżnych zapytań należy wziąć pod uwagę następujące kwestie:

* Zacznij od 30 GB na wykonawcę i wszystkich rdzeni maszynowych.
* Utwórz wiele równoległych aplikacji platformy Spark przez zasubskrybowanie procesora CPU (około 30% poprawy opóźnienia).
* Dystrybuuj zapytania w aplikacjach równoległych.
* Zmodyfikuj rozmiar na podstawie zarówno w przypadku wersji próbnej, jak i w powyższych czynnikach, takich jak obciążenie GC.

Monitoruj wydajność zapytań pod kątem wartości odstających lub innych problemów z wydajnością, przeglądając widok oś czasu, program SQL Graph, statystyki zadań i tak dalej. Czasami jeden lub kilka z wykonawców jest wolniejsze niż inne, a wykonywanie zadań trwa znacznie dłużej. Często odbywa się to w większych klastrach (> 30 węzłach). W takim przypadku należy podzielić pracę na większą liczbę zadań, aby harmonogram mógł wyrównać powolne zadania. 

Na przykład należy mieć co najmniej dwa razy więcej zadań jako liczbę rdzeni wykonawców w aplikacji. Możesz również włączyć spekulacyjne wykonywanie zadań za pomocą `conf: spark.speculation = true` .

## <a name="optimize-job-execution"></a>Optymalizowanie wykonywania zadań

* Pamięć podręczna w razie potrzeby, na przykład w przypadku używania danych dwa razy, należy ją buforować.
* Emituj zmienne do wszystkich wykonawców. Zmienne są serializowane tylko raz, co powoduje szybsze wyszukiwanie.
* Użyj puli wątków w sterowniku, co skutkuje krótszą operacją dla wielu zadań.

Klucz do wydajności zapytań Spark 2. x jest aparatem, który zależy od generacji całego etapu. W niektórych przypadkach generowanie kodu w całym etapie może być wyłączone. 

Na przykład, jeśli `string` w wyrażeniu agregacji jest używany niemodyfikowalny typ (), `SortAggregate` pojawia się zamiast `HashAggregate` . Na przykład w celu uzyskania lepszej wydajności spróbuj wykonać następujące czynności, a następnie ponownie włączyć generowanie kodu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Następne kroki

- [Dostrajanie Apache Spark](https://spark.apache.org/docs/2.4.5/tuning.html)
- [Jak w rzeczywistości dostroić zadania Apache Spark, aby działały](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Serializacja Kryo](https://github.com/EsotericSoftware/kryo)
