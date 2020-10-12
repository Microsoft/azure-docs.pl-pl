---
title: Korzystanie z SparkCruise w usłudze Azure HDInsight w celu przyspieszenia Apache Spark zapytań
description: Dowiedz się, jak za pomocą platformy optymalizacji SparkCruise poprawić wydajność zapytań Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 1a73b4707f83d6a23dffc20d95aa7b8a0fa465b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88649061"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise w usłudze Azure HDInsight

W tym dokumencie omówiono funkcję usługi Azure HDInsight *SparkCruise*, która automatycznie ponownie używa obliczeń Apache Spark, aby zwiększyć wydajność zapytań.

## <a name="overview"></a>Omówienie

Zapytania uruchamiane na platformie analizy, takie jak Apache Spark, są rozłożone na plan zapytania zawierający mniejsze podzapytania. Te podzapytania mogą wielokrotnie pojawiać się w planach zapytań dla wielu zapytań. Za każdym razem, gdy się pojawiają, są one ponownie wykonywane w celu zwrócenia wyników. Ponowne wykonywanie tego samego zapytania może jednak być niewydajne i prowadzić do niepotrzebnych kosztów obliczeniowych.

*SparkCruise* to platforma optymalizacji obciążeń, która może ponownie używać typowych obliczeń, co zmniejsza ogólny czas wykonywania zapytania i koszty transferu danych. Platforma używa koncepcji *widoku z materiałami*, który jest kwerendą, której wyniki są przechowywane w postaci wstępnie obliczonego formularza. Te wyniki można następnie ponownie wykorzystać, gdy zapytanie zostanie wyświetlone później, zamiast ponownie obliczać wyniki.

## <a name="setup-and-installation"></a>Konfiguracja i instalacja

Usługa SparkCruise jest dostępna we wszystkich klastrach usługi HDInsight 4,0 z platformą Spark 2,3 lub 2,4. Pliki biblioteki SparkCruise są instalowane w `/opt/peregrine/` katalogu klastra usługi HDInsight. Aby działały prawidłowo, *SparkCruise* wymaga następujących właściwości konfiguracji, które są domyślnie ustawione.

* `spark.sql.queryExecutionListeners` jest ustawiona na `com.microsoft.peregrine.spark.listeners.PlanLogListener` , co umożliwia rejestrowanie planów zapytań.
* `spark.sql.extensions` jest ustawiona na `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , co umożliwia stosowanie reguł Optymalizatora dla materializację online i wielokrotnego użytku.

## <a name="computation-reuse-in-spark-sql"></a>Ponowne użycie obliczeń w programie Spark SQL

W poniższym scenariuszu przykładowym pokazano, jak używać *SparkCruise* do optymalizowania zapytań Apache Spark. 

1. Protokół SSH do węzła głównego klastra platformy Spark.
1. Wpisz `spark-shell`.
1. Uruchom Poniższy fragment kodu, który uruchamia kilka podstawowych zapytań przy użyciu przykładowych danych w klastrze.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Za pomocą narzędzia do analizy zapytań *SparkCruise* można analizować plany zapytania dotyczące poprzednich zapytań, które są przechowywane w dziennikach aplikacji platformy Spark. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Wyświetl dane wyjściowe procesu analizy, który jest plikiem opinii. Ten plik opinii zawiera adnotacje dla przyszłych zapytań Spark SQL. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`analyze`Polecenie analizuje plany zapytania i tworzy tabelaryczną reprezentację obciążenia. W tej tabeli obciążeń można wykonywać zapytania przy użyciu notesu *WorkloadInsights* zawartego w repozytorium [przykładów SparkCruise usługi HDInsight](https://github.com/Azure-Samples/azure-sparkcruise-samples) . Następnie `views` polecenie identyfikuje typowe wyrażenia podplanu i wybiera interesujące wyrażenia podplanów dla przyszłej materializację i ponownego użycia. Dane wyjściowe to plik opinii zawierający adnotacje dla przyszłych zapytań Spark SQL. 

`show`Polecenie wyświetla dane wyjściowe podobne do następującego tekstu:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

Plik opinii zawiera wpisy w następującym formacie: `subplan-identifier [Materialize|Reuse] input/path/to/action` . W tym przykładzie istnieją dwa unikatowe podpisy: jeden reprezentujący pierwsze dwa powtarzające się zapytania, a drugi reprezentujący predykat filtru w ostatnich dwóch zapytaniach. W tym pliku opinii następujące zapytania po ponownym przesłaniu będą automatycznie zmaterializowania i ponownie używały wspólnych planów. 

Aby przetestować optymalizacje, wykonaj kolejny zestaw przykładowych zapytań.

1. Wpisz `spark-shell`.
1. Wykonaj następujący fragment kodu

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Przejrzyj ponownie plik informacji zwrotnych, aby zobaczyć podpisy zapytań, które zostały już użyte.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`show`Polecenie zwraca dane wyjściowe podobne do następującego tekstu:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Mimo że wartość literału w zapytaniu zmieniła `'11%'` się z na `'12%'` , *SparkCruise* może nadal dopasować poprzednie zapytania do nowych zapytań z niewielkimi zmianami, takimi jak ewolucja wartości literałów i wersji zestawu danych. W przypadku wprowadzenia istotnych zmian w zapytaniu można ponownie uruchomić Narzędzie analizy, aby zidentyfikować nowe zapytania, które mogą być ponownie używane.

W tle *SparkCruise* wyzwala podzapytanie w celu materializacji wybranego podplanu z pierwszego zapytania, które zawiera. Późniejsze zapytania mogą bezpośrednio odczytywać materiałowe podplany, a nie ich ponowne przetwarzanie. W tym obciążeniu plany te zostaną zaoferowane w trybie online przez pierwsze i trzecie zapytanie. Można zobaczyć zmianę planu zapytań po materiałach wspólnych podplanów.

Można sprawdzić, czy w kolejnych zapytaniach są dostępne cztery nowe materiałowe wyrażenia, które mają być ponownie używane.

## <a name="clean-up"></a>Czyszczenie

Pliki opinii, materiałowe podplany i dzienniki zapytań są utrwalane w sesjach platformy Spark. Aby usunąć te pliki, uruchom następujące polecenie:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z notesu Insights w celu określenia korzyści z SparkCruise](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Poprawianie wydajności Apache Spark obciążeń przy użyciu pamięci podręcznej we/wy usługi Azure HDInsight](apache-spark-improve-performance-iocache.md)
* [Optymalizowanie Apache Spark zadań w usłudze HDInsight](./apache-spark-perf.md)
* [SparkCruise: ponowne użycie obliczeń Handsfree w platformie Spark](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Wskazówki dotyczące Apache Spark w usłudze Azure HDInsight](./spark-best-practices.md)
