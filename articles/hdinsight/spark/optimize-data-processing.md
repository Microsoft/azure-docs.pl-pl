---
title: Optymalizowanie przetwarzania danych dla Apache Spark — usługa Azure HDInsight
description: Dowiedz się, jak wybrać najbardziej wydajne operacje przetwarzania danych na Apache Spark za pomocą usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944713"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Optymalizacja przetwarzania danych dla Apache Spark

W tym artykule omówiono sposób optymalizowania konfiguracji klastra Apache Spark w celu uzyskania najlepszej wydajności w usłudze Azure HDInsight.

## <a name="overview"></a>Omówienie

Jeśli masz wolne zadania w sprzężeniu lub losowo, przyczyną jest prawdopodobnie *pochylenie danych*. Skośność danych jest asymetrią w danych zadania. Na przykład zadanie mapy może trwać 20 sekund. Jednak uruchomienie zadania, w którym dane są przyłączone lub przetworzone w ciągu kilku godzin. Aby naprawić pochylenie danych, należy pozbyć się całego klucza lub użyć *odizolowanej soli* tylko dla pewnego podzestawu kluczy. Jeśli używasz izolowanej soli, należy dodatkowo przefiltrować, aby odizolować podzbiór kluczy solonych w sprzężeniu mapy. Kolejną opcją jest wprowadzenie kolumny przedziału i wstępnego agregacji w zasobnikach.

Innym czynnikiem powodującym powolne sprzężenie może być typ sprzężenia. Domyślnie platforma Spark używa `SortMerge` typu sprzężenia. Ten typ sprzężenia jest najlepiej dostosowany do dużych zestawów danych. Ale jest w inny sposób wyliczany, ponieważ należy najpierw posortować po lewej i prawej stronie danych przed ich scaleniem.

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

## <a name="optimize-job-execution"></a>Optymalizowanie wykonywania zadań

* Pamięć podręczna w razie potrzeby, na przykład w przypadku używania danych dwa razy, należy ją buforować.
* Emituj zmienne do wszystkich wykonawców. Zmienne są serializowane tylko raz, co powoduje szybsze wyszukiwanie.
* Użyj puli wątków w sterowniku, co skutkuje krótszą operacją dla wielu zadań.

Regularnie Monitoruj uruchomione zadania pod kątem problemów z wydajnością. Jeśli potrzebujesz więcej szczegółowych informacji na temat niektórych problemów, weź pod uwagę jeden z następujących narzędzi profilowania wydajności:

* [Narzędzie Intel PAL](https://github.com/intel-hadoop/PAT) monitoruje użycie procesora CPU, magazynu i przepustowości sieci.
* Program [Oracle Java 8 profil kontroli misja](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) Spark i kod wykonawczy.

Klucz do wydajności zapytań Spark 2. x jest aparatem, który zależy od generacji całego etapu. W niektórych przypadkach generowanie kodu w całym etapie może być wyłączone. Na przykład, jeśli `string` w wyrażeniu agregacji jest używany niemodyfikowalny typ (), `SortAggregate` pojawia się zamiast `HashAggregate` . Na przykład w celu uzyskania lepszej wydajności spróbuj wykonać następujące czynności, a następnie ponownie włączyć generowanie kodu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Następne kroki

* [Optymalizuj magazyn danych dla Apache Spark](optimize-data-storage.md)
* [Optymalizuj użycie pamięci przez Apache Spark](optimize-memory-usage.md)
* [Optymalizuj konfigurację klastra dla Apache Spark](optimize-cluster-configuration.md)
