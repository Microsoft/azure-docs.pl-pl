---
title: 'Architektura: Apache Hadoop lokalna do usługi Azure HDInsight'
description: Poznaj najlepsze rozwiązania dotyczące architektury dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: af9b1f42140c5656c5f55a98c2d635d59e130db5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533737"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania dotyczące architektury

W tym artykule przedstawiono zalecenia dotyczące architektury systemów Azure HDInsight. Jest ona częścią serii, która oferuje najlepsze rozwiązania ułatwiające Migrowanie lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Korzystanie z wielu klastrów zoptymalizowanych pod kątem obciążenia

Wiele lokalnych wdrożeń Apache Hadoop obejmuje jeden duży klaster, który obsługuje wiele obciążeń. Ten pojedynczy klaster może być skomplikowany i może wymagać złamania poszczególnych usług, aby wszystkie współpracowały ze sobą. Migrowanie lokalnych klastrów Hadoop do usługi Azure HDInsight wymaga zmiany podejścia.

Klastry usługi Azure HDInsight są przeznaczone do określonego typu obliczeń użycia. Ponieważ magazyn może być współużytkowany przez wiele klastrów, możliwe jest utworzenie wielu klastrów obliczeniowych zoptymalizowanych pod kątem obciążenia, aby zaspokoić potrzeby różnych zadań. Każdy typ klastra ma optymalną konfigurację dla tego konkretnego obciążenia. W poniższej tabeli wymieniono obsługiwane typy klastrów w usłudze HDInsight i odpowiadające im obciążenia.

|Obciążenie|Typ klastra usługi HDInsight|
|---|---|
|Przetwarzanie wsadowe (ETL/ELT)|Hadoop, Spark|
|Magazynowanie danych|Hadoop, Spark, zapytanie interaktywne|
|IoT/Streaming|Kafka, burza, Spark|
|NoSQL transakcyjne przetwarzanie|HBase|
|Interakcyjne i szybsze zapytania z buforowaniem w pamięci|Zapytanie interakcyjne|
|Analiza danych|Usługi ML, Spark|

W poniższej tabeli przedstawiono różne metody, których można użyć do utworzenia klastra usługi HDInsight.

|Narzędzie|Na podstawie przeglądarki|Wiersz polecenia|Interfejs API REST|SDK|
|---|---|---|---|---|
|[Witryna Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Interfejs wiersza polecenia platformy Azure (Ver 1,0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[Zestaw SDK platformy .NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true)||||X|
|[Zestaw SDK dla języka Python](/python/api/overview/azure/hdinsight)||||X|
|[Zestaw SDK Java](/java/api/overview/azure/hdinsight)||||X|
|[Szablony usługi Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Aby uzyskać więcej informacji, zobacz artykuł [typy klastrów w usłudze HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Korzystanie z przejściowych klastrów na żądanie

Klastry usługi HDInsight mogą nie być używane przez długi czas. W celu ułatwienia oszczędzania kosztów zasobów Usługa HDInsight obsługuje klastry przejściowe na żądanie, które mogą zostać usunięte po pomyślnym zakończeniu obciążenia.

Po usunięciu klastra skojarzone konto magazynu i zewnętrzne metadane nie zostaną usunięte. Klaster można później utworzyć ponownie, używając tych samych kont magazynu i metadanych.

Azure Data Factory można użyć do zaplanowania tworzenia klastrów usługi HDInsight na żądanie. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Rozdzielenie magazynu od obliczeń

Typowe wdrożenia lokalnej usługi Hadoop używają tego samego zestawu maszyn do przechowywania i przetwarzania danych. Ze względu na to, że są one współzlokalizowane, obliczenia i magazyn muszą być skalowane jednocześnie.

W klastrach usługi HDInsight magazyn nie musi znajdować się w lokalizacji obliczeniowej i może znajdować się w usłudze Azure Storage, Azure Data Lake Storage lub obu. Oddzielenie magazynu od obliczeń wiąże się z następującymi korzyściami:

- Udostępnianie danych w wielu klastrach.
- Używanie klastrów przejściowych od momentu, gdy dane nie są zależne od klastra.
- Obniżony koszt magazynu.
- Skalowanie magazynu i obliczanie osobno.
- Replikacja danych między regionami.

Klastry obliczeniowe są tworzone w pobliżu zasobów konta magazynu w regionie świadczenia usługi Azure, aby zmniejszyć koszty związane z rozdzieleniem mocy obliczeniowej i magazynu. Szybkie sieci sprawiają, że węzły obliczeniowe uzyskują dostęp do danych w usłudze Azure Storage.

## <a name="use-external-metadata-stores"></a>Korzystanie z zewnętrznych magazynów metadanych

Istnieją dwa główne magazyny, które współpracują z klastrami usługi HDInsight: [Apache Hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/). Magazyn metadanych Hive to centralne repozytorium schematów, które może być używane przez aparaty przetwarzania danych, w tym Hadoop, Spark, LLAP, Presto i Apache świni. Magazyn metadanych Oozie przechowuje szczegółowe informacje o planowaniu i stanie w toku i ukończonych zadaniach usługi Hadoop.

Usługa HDInsight używa Azure SQL Database dla magazynów Hive i Oozie. Istnieją dwa sposoby konfigurowania magazynu metadanych w klastrach usługi HDInsight:

1. Domyślny magazyn metadanych

    - Brak dodatkowych kosztów.
    - Magazyn metadanych jest usuwany po usunięciu klastra.
    - Magazynu metadanych nie można udostępniać między różnymi klastrami.
    - Używa podstawowej bazy danych SQL platformy Azure, która ma pięć limitów jednostek DTU.

1. Niestandardowy zewnętrzny magazyn metadanych

    - Określ zewnętrzny Azure SQL Database jako magazyn metadanych.
    - Klastry można tworzyć i usuwać bez utraty metadanych, w tym szczegółów zadania Oozie schematu Hive.
    - Pojedynczy magazyn bazy danych magazynu może być współużytkowany z różnymi typami klastrów.
    - Magazyn metadanych można skalować odpowiednio do wymagań.
    - Aby uzyskać więcej informacji, zobacz [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Najlepsze rozwiązania dotyczące magazynu metadanych Hive

Poniżej przedstawiono niektóre najlepsze rozwiązania dotyczące usługi HDInsight magazyn metadanych Hive:

- Użyj niestandardowego zewnętrznego magazynu metadanych, aby oddzielić zasoby i metadane obliczeniowe.
- Zacznij od wystąpienia usługi Azure SQL w warstwie S2, które zapewnia 50 jednostek DTU i 250 GB miejsca w magazynie. Jeśli widzisz wąskie gardło, możesz skalować bazę danych w górę.
- Nie udostępniaj magazynu metadanych utworzonego dla jednej wersji klastra usługi HDInsight z klastrami o innej wersji. Różne wersje programu Hive używają różnych schematów. Nie można na przykład udostępnić magazynu metadanych w klastrach Hive 1,2 i Hive 2,1.
- Okresowo twórz kopię zapasową niestandardowego magazynu metadanych.
- Przechowuj magazyn metadanych i klaster usługi HDInsight w tym samym regionie.
- Monitoruj magazyn metadanych pod kątem wydajności i dostępności przy użyciu narzędzi do monitorowania Azure SQL Database, takich jak dzienniki Azure Portal lub Azure Monitor.
- Wykonaj `ANALYZE TABLE` polecenie wymagane do generowania statystyk dla tabel i kolumn. Na przykład `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Najlepsze rozwiązania dotyczące różnych obciążeń

- Rozważ użycie klastra LLAP na potrzeby interaktywnych zapytań Hive przy użyciu ulepszonego czasu odpowiedzi [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) to nowa funkcja w programie Hive 2,0, która umożliwia buforowanie zapytań w pamięci. LLAP sprawia, że zapytania Hive znacznie szybsze, do [26x szybciej niż w przypadku programu Hive 1. x w niektórych przypadkach](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Rozważ użycie zadań platformy Spark zamiast zadań Hive.
- Rozważ zamianę zapytań opartych na Impala z zapytaniami LLAP.
- Rozważ zastępowanie zadań MapReduce za pomocą zadań platformy Spark.
- Rozważ zastępowanie zadań wsadowych o małym opóźnieniu, korzystając z zadań przetwarzania strumieniowego platformy Spark.
- Rozważ użycie Azure Data Factory (ADF) 2,0 do organizowania danych.
- Rozważ Ambari zarządzania klastrem.
- Zmień magazyn danych z lokalnego systemu plików HDFS na WASB lub ADLS lub ADFS na potrzeby przetwarzania skryptów.
- Rozważ użycie Ranger RBAC w tabelach i inspekcjach Hive.
- Rozważ użycie CosmosDB zamiast MongoDB lub Cassandra.

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii:

- [Najlepsze rozwiązania dotyczące infrastruktury do migracji Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)