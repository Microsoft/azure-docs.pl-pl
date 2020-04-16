---
title: Optymalizuj zapytania gałęzi w usłudze Azure HDInsight
description: W tym artykule opisano sposób optymalizacji zapytań hive Apache dla Hadoop w hdinsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 4955df718dcc8f169232052979ccf4a636c3be80
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390292"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optymalizowanie zapytań technologii Apache Hive w usłudze Azure HDInsight

W usłudze Azure HDInsight istnieje kilka typów klastrów i technologii, które można uruchamiać apache hive kwerend. Wybierz odpowiedni typ klastra, aby zoptymalizować wydajność pod kątem potrzeb związanych z obciążeniem.

Na przykład wybierz typ klastra **kwerendinterakcyjnych,** aby zoptymalizować kwerendy `ad hoc`interaktywne. Wybierz apache **Hadoop** typu klastra, aby zoptymalizować dla zapytań hive używane jako proces wsadowy. **Spark** i **HBase** typy klastra można również uruchamiać zapytania hive. Aby uzyskać więcej informacji na temat uruchamiania zapytań hive w różnych typach klastrów USŁUGI HDInsight, zobacz [Co to jest Gałąź Apache i HiveQL w usłudze Azure HDInsight?](hadoop/hdinsight-use-hive.md).

Klastry HDInsight typu klastra Hadoop nie są domyślnie zoptymalizowane pod kątem wydajności. W tym artykule opisano niektóre z najbardziej typowych metod optymalizacji wydajności gałęzi, które można zastosować do zapytań.

## <a name="scale-out-worker-nodes"></a>Skalowanie w poziomie węzłów procesu roboczego

Zwiększenie liczby węzłów procesu roboczego w klastrze HDInsight umożliwia pracę przy użyciu większej liczby maperów i reduktorów, które mają być uruchamiane równolegle. W umiań HDInsight można zwiększyć skalę w poziomie:

* Podczas tworzenia klastra można określić liczbę węzłów procesu roboczego przy użyciu witryny Azure portal, usługi Azure PowerShell lub interfejsu wiersza polecenia.  Więcej informacji można znaleźć w artykule [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów usługi HDInsight). Poniższy zrzut ekranu przedstawia konfigurację węzła procesu roboczego w witrynie Azure portal:
  
    ![Węzły rozmiaru klastra portalu Azure](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* Po utworzeniu można również edytować liczbę węzłów procesu roboczego, aby dalej skalować w poziomie klastra bez ponownego tworzenia jednego z nich:

    ![Rozmiar klastra skalowania portalu Azure](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

Aby uzyskać więcej informacji na temat skalowania usługi HDInsight, zobacz [Skalowanie klastrów HDInsight](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Użyj Apache Tez zamiast Map Reduce

[Apache Tez](https://tez.apache.org/) jest alternatywnym aparatem wykonawczym do silnika MapReduce. Klastry HDInsight oparte na systemie Linux mają domyślnie włączoną funkcję Tez.

![Diagram przeglądu programu HDInsight Apache Tez](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez jest szybszy bo:

* **Wykonaj ukierunkowany wykres acykliczny (DAG) jako jedno zadanie w silniku MapReduce**. DAG wymaga, aby każdy zestaw maperów był następujący od jednego zestawu reduktorów. To wymaganie powoduje, że wiele zadań MapReduce zostać wydzielone dla każdej kwerendy hive. Tez nie ma takiego ograniczenia i może przetwarzać złożone DAG jako jedno zadanie minimalizujące obciążenie związane z uruchamianiem zadania.
* **Pozwala uniknąć niepotrzebnych zapisów**. Wiele zadań są używane do przetwarzania tej samej kwerendy hive w silniku MapReduce. Dane wyjściowe każdego zadania MapReduce są zapisywane w usad HDFS dla danych pośrednich. Ponieważ Tez minimalizuje liczbę zadań dla każdej kwerendy hive, jest w stanie uniknąć niepotrzebnych zapisów.
* **Minimalizuje opóźnienia rozruchu**. Tez jest w stanie lepiej zminimalizować opóźnienie rozruchu, zmniejszając liczbę maperów, które musi uruchomić, a także poprawiając optymalizację w całym.
* **Ponownie używa kontenerów**. W miarę możliwości Tez będzie ponownie używać kontenerów, aby upewnić się, że opóźnienie uruchamiania kontenerów jest zmniejszona.
* **Techniki ciągłej optymalizacji**. Tradycyjnie optymalizacja została wykonana podczas fazy kompilacji. Jednak więcej informacji na temat danych wejściowych jest dostępna, które umożliwiają lepszą optymalizację w czasie wykonywania. Tez używa technik ciągłej optymalizacji, które pozwalają zoptymalizować plan dalej w fazie wykonawczej.

Aby uzyskać więcej informacji na temat tych pojęć, zobacz [Apache TEZ](https://tez.apache.org/).

Można włączyć dowolną kwerendę hive Tez, prefiksując kwerendę za pomocą następującego polecenia set:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Partycjonowanie gałęzi

Operacje we/wy są głównym wąskim gardłem wydajności dla uruchamiania zapytań hive. Wydajność można poprawić, jeśli ilość danych, które muszą być odczytane można zmniejszyć. Domyślnie zapytania hive skanują całe tabele gałęzi. Jednak w przypadku kwerend, które muszą skanować tylko niewielką ilość danych (na przykład kwerendy z filtrem), to zachowanie tworzy niepotrzebne obciążenie. Partycjonowanie gałęzi umożliwia rozmnażanie hive dostęp tylko niezbędną ilość danych w tabelach hive.

Partycjonowanie gałęzi jest implementowane przez reorganizowanie nieprzetworzonych danych w nowe katalogi. Każda partycja ma swój własny katalog plików. Partycjonowanie jest definiowane przez użytkownika. Na poniższym diagramie przedstawiono partycjonowanie tabeli gałęzi według kolumny *Rok*. Nowy katalog jest tworzony dla każdego roku.

![Partycjonowanie gałęzi hdinsight Apache](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Niektóre zagadnienia partycjonowania:

* **Nie pod partycje** — partycjonowanie na kolumnach z tylko kilka wartości może spowodować kilka partycji. Na przykład partycjonowanie na płeć tworzy tylko dwie partycje do utworzenia (mężczyzna i kobieta), więc zmniejszyć opóźnienie o maksymalnie połowę.
* **Nie przejmuj partycji** — z drugiej strony tworzenie partycji w kolumnie o unikatowej wartości (na przykład userid) powoduje wiele partycji. Ponad partycji powoduje wiele stresu na nazwę klastra, ponieważ ma do obsługi dużej liczby katalogów.
* **Unikaj pochylania danych** — wybierz klucz partycjonowania mądrze, aby wszystkie partycje były równe rozmiar. Na przykład partycjonowanie w kolumnie *Stan* może wypaczyć dystrybucji danych. Ponieważ stan Kalifornia ma populację prawie 30x, że Vermont, rozmiar partycji jest potencjalnie wypaczone i wydajność może się znacznie różnić.

Aby utworzyć tabelę partycji, należy użyć klauzuli *Partitioned By:*

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

Po utworzeniu tabeli podzielonej na partycje można utworzyć statyczne partycjonowanie lub dynamiczne partycjonowanie.

* **Statyczne partycjonowanie** oznacza, że masz już podzielone dane w odpowiednich katalogach. Za pomocą partycji statycznych można ręcznie dodać partycje hive na podstawie lokalizacji katalogu. Przykładem jest poniższy fragment kodu.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dynamiczne partycjonowanie** oznacza, że chcesz, aby hive automatycznie tworzyła partycje. Ponieważ tabela partycjonowania została już utworzona z tabeli przemieszczania, wystarczy wstawić dane do tabeli podzielonej na partycje:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Aby uzyskać więcej informacji, zobacz [Tabele podzielone na partycje](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Użyj formatu ORCFile

Gałąź obsługuje różne formaty plików. Przykład:

* **Tekst:** domyślny format pliku i działa z większością scenariuszy.
* **Avro**: działa dobrze w scenariuszach interoperacyjności.
* **ORC/Parkiet**: najlepiej nadaje się do wydajności.

Format ORC (Optimized Row Columnar) to bardzo wydajny sposób przechowywania danych hive. W porównaniu do innych formatów, ORC ma następujące zalety:

* obsługa typów złożonych, w tym DateTime i typów złożonych i częściowo ustrukturyzowanych.
* do 70% kompresji.
* indeksuje co 10 000 wierszy, co pozwala na pomijanie wierszy.
* znaczny spadek wykonania w czasie wykonywania.

Aby włączyć format ORC, należy najpierw utworzyć tabelę z klauzulą *Przechowywaną jako ORC:*

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Następnie należy wstawić dane do tabeli ORC z tabeli przemieszczania. Przykład:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

Więcej informacji na temat formatu ORC można przeczytać w [podręczniku Apache Hive Language](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Wektoryzacja

Wektoryzacja umożliwia gałęzi do przetwarzania partii 1024 wierszy razem zamiast przetwarzania jeden wiersz naraz. Oznacza to, że proste operacje są wykonywane szybciej, ponieważ mniej kodu wewnętrznego musi działać.

Aby włączyć prefiks wektoryzacji kwerendy hive z następującym ustawieniem:

```hive
set hive.vectorized.execution.enabled = true;
```

Aby uzyskać więcej informacji, zobacz [Wektorowane wykonanie kwerendy](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Inne metody optymalizacji

Istnieje więcej metod optymalizacji, które można wziąć pod uwagę, na przykład:

* **Gałąź zasobnika:** technika, która pozwala klastrowania lub segment dużych zestawów danych w celu optymalizacji wydajności kwerendy.
* **Optymalizacja sprzężenia:** optymalizacja planowania wykonywania zapytań hive w celu zwiększenia wydajności sprzężeń i zmniejszenia zapotrzebowania na wskazówki dla użytkowników. Aby uzyskać więcej informacji, zobacz [Optymalizacja dołączania](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zwiększ reduktory**.

## <a name="next-steps"></a>Następne kroki

W tym artykule poznaliście kilka typowych metod optymalizacji zapytań hive. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Użyj ula Apache w funkcji HDInsight](hadoop/hdinsight-use-hive.md)
* [Analizowanie danych opóźnienia lotu przy użyciu interaktywnej kwerendy w programie HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analizowanie danych Twittera przy użyciu ula Apache w hdinsight](hdinsight-analyze-twitter-data-linux.md)
