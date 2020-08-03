---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: 339926fbd3c96f6f6c279d29676950b9915b4256
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87484163"
---
# <a name="azure-hdinsight-release-notes"></a>Informacje o wersji usługi Azure HDInsight

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-07302020"></a>Data wydania: 07/30/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="support-for-sparkcruise"></a>Obsługa SparkCruise
SparkCruise to system automatycznego ponownego użycia obliczeń dla platformy Spark. Wybiera wspólne podwyrażeniey do zmaterializowania w oparciu o obciążenie przeszłe zapytania. SparkCruise materializuje te podwyrażenia w ramach przetwarzania zapytań i ponownego wykorzystania obliczeń są automatycznie stosowane w tle. Można korzystać z SparkCruise bez żadnych modyfikacji kodu Spark.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Obsługa widoku programu Hive dla usługi HDInsight 4,0
Widok Hive platformy Apache Ambari został zaprojektowany z założeniami ułatwiającymi tworzenie, optymalizowanie i wykonywanie zapytań Hive z poziomu przeglądarki sieci Web. Widok Hive jest obsługiwany natywnie dla klastrów usługi HDInsight 4,0, począwszy od tej wersji. Nie dotyczy istniejących klastrów. Musisz porzucić i utworzyć ponownie klaster, aby uzyskać wbudowany widok Hive.
 
### <a name="support-tez-view-for-hdinsight-40"></a>Obsługa widoku tez dla usługi HDInsight 4,0
Widok Apache Tez służy do śledzenia i debugowania wykonywania zadania Hive tez. Widok tez jest obsługiwany natywnie dla usługi HDInsight 4,0 od tej wersji. Nie dotyczy istniejących klastrów. Aby uzyskać wbudowany widok tez, należy porzucić i ponownie utworzyć klaster.

## <a name="deprecation"></a>Przestarzałe
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Zakończenie obsługi platformy Spark 2.1 i 2.2 w klastrze platformy Spark w usłudze HDInsight 3.6
Począwszy od lipca 1 2020, klienci nie mogą tworzyć nowych klastrów Spark z platformami Spark 2,1 i 2,2 w usłudze HDInsight 3,6. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście do platformy Spark 2,3 w usłudze HDInsight 3,6 do czerwca 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Zakończenie obsługi platformy Spark 2.3 w klastrze platformy Spark w usłudze HDInsight 4.0
Począwszy od 1 2020 lipca, klienci nie mogą tworzyć nowych klastrów Spark z platformą Spark 2,3 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście na platformę Spark 2.4 w usłudze HDInsight 4.0 do 30 czerwca 2020 r., aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Zakończenie obsługi platformy Kafka 1.1 w klastrze platformy Kafka w usłudze HDInsight 4.0
Począwszy od lipca 1 2020, klienci nie będą mogli tworzyć nowych klastrów Kafka z Kafka 1,1 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście na platformę Kafka 2.1 w usłudze HDInsight 4.0 do 30 czerwca 2020 r., aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.

## <a name="behavior-changes"></a>Zmiany zachowania
### <a name="ambari-stack-version-change"></a>Zmiana wersji stosu Ambari
W tej wersji Ambari wersja została zmieniona z 2. x. x. x na 4,1. Możesz uzyskać wersję Ambari z interfejsu użytkownika Ambari > informacje o programie.

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Brak przyszłych zmian, do których należy zwrócić uwagę.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

Poniżej JIRAs są ponownie przyłączone do platformy Hive:
* [GAŁĄŹ-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [GAŁĄŹ-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [GAŁĄŹ-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [GAŁĄŹ-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [GAŁĄŹ-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [GAŁĄŹ-18786](https://issues.apache.org/jira/browse/HIVE-18786)

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).