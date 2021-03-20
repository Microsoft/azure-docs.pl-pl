---
title: Optymalizowanie magazynu danych dla Apache Spark — usługa Azure HDInsight
description: Dowiedz się, jak zoptymalizować magazyn danych do użycia z Apache Spark w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98926995"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Optymalizacja magazynu danych dla Apache Spark

W tym artykule omówiono strategie optymalizacji magazynu danych w celu wydajnego wykonywania zadań Apache Spark w usłudze Azure HDInsight.

## <a name="overview"></a>Omówienie

Platforma Spark obsługuje wiele formatów, takich jak CSV, JSON, XML, Parquet, Orc i Avro. Platforma Spark może zostać rozszerzona w celu obsługi wielu formatów z zewnętrznymi źródłami danych — Aby uzyskać więcej informacji, zobacz [Apache Spark Packages](https://spark-packages.org).

Najlepszym formatem wydajności jest Parquet z *kompresją przyciągania*, która jest wartością domyślną w platformie Spark 2. x. Parquet przechowuje dane w formacie kolumnowym i jest wysoce zoptymalizowany w Spark.

## <a name="choose-data-abstraction"></a>Wybieranie abstrakcji danych

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

## <a name="use-the-cache"></a>Korzystanie z pamięci podręcznej

Platforma Spark zapewnia własne natywne mechanizmy buforowania, które mogą być używane przez różne metody, takie jak `.persist()` , `.cache()` , i `CACHE TABLE` . Natywne buforowanie jest efektywne w przypadku małych zestawów danych i potoków ETL, w których należy przechować pośrednie wyniki. Jednak natywne buforowanie Spark nie działa prawidłowo z partycjonowaniem, ponieważ w pamięci podręcznej tabela nie zachowuje danych partycjonowania. Bardziej generyczną i niezawodną techniką buforowania jest *buforowanie warstwy magazynowania*.

* Natywne buforowanie Spark (niezalecane)
    * Dobre dla małych zestawów danych.
    * Nie działa z partycjonowaniem, co może ulec zmianie w przyszłych wersjach platformy Spark.

* Buforowanie na poziomie magazynu (zalecane)
    * Można zaimplementować w usłudze HDInsight przy użyciu funkcji [pamięci podręcznej we/wy](apache-spark-improve-performance-iocache.md) .
    * Używa buforowania w pamięci i dysku SSD.

* Lokalny system plików HDFS (zalecane)
    * `hdfs://mycluster` ścieżka.
    * Używa buforowania SSD.
    * Dane w pamięci podręcznej zostaną utracone po usunięciu klastra, co wymaga odbudowania pamięci podręcznej.

## <a name="optimize-data-serialization"></a>Optymalizowanie serializacji danych

Zadania platformy Spark są dystrybuowane, więc odpowiednia Serializacja danych jest ważna dla najlepszej wydajności.  Istnieją dwie opcje serializacji dla platformy Spark:

* Wartością domyślną jest Serializacja języka Java.
* `Kryo` Serializacja jest w nowszym formacie i może prowadzić do szybszej i bardziej kompaktowej serializacji niż w przypadku języka Java.  `Kryo` wymaga zarejestrowania klas w programie i nie obsługuje jeszcze wszystkich typów możliwych do serializacji.

## <a name="use-bucketing"></a>Korzystanie z zasobników

Przedziałowanie jest podobne do partycjonowania danych. Ale każdy zasobnik może przechowywać zestaw wartości kolumn, a nie tylko jeden. Ta metoda dobrze sprawdza się w przypadku partycjonowania na dużą liczbę (w milionach lub więcej) wartości, takich jak identyfikatory produktów. Zasobnik jest określany przez mieszanie klucza zasobnika wiersza. Tabele z przedziałów oferują unikatowe optymalizacje, ponieważ przechowują metadane dotyczące ich przedziałów i sortowania.

Niektóre zaawansowane funkcje zasobników to:

* Optymalizacja zapytania oparta na meta-informacjach o przedziale.
* Zoptymalizowane agregacje.
* Zoptymalizowane sprzężenia.

Można używać partycjonowania i tworzenia pakietów jednocześnie.

## <a name="next-steps"></a>Następne kroki

* [Optymalizowanie przetwarzania danych dla Apache Spark](optimize-cluster-configuration.md)
* [Optymalizuj użycie pamięci przez Apache Spark](optimize-memory-usage.md)
* [Optymalizuj konfigurację klastra dla Apache Spark](optimize-cluster-configuration.md)
