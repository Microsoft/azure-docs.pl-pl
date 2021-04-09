---
title: Wskazówki dotyczące Apache Spark w usłudze Azure HDInsight
description: Poznaj wskazówki dotyczące używania Apache Spark w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938807"
---
# <a name="apache-spark-guidelines"></a>Wskazówki dotyczące Apache Spark

Ten artykuł zawiera różne wskazówki dotyczące używania Apache Spark w usłudze Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Jak mogę uruchomić lub przesłać zadania platformy Spark?

| Opcja | Dokumenty |
|---|---|
| Programu vscode | [Użyj narzędzi Hive & platformy Spark dla Visual Studio Code](../hdinsight-for-vscode.md) |
| Notesy programu Jupyter | [Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Samouczek: Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for IntelliJ](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Samouczek: tworzenie aplikacji Scala Maven dla platformy Apache Spark w usłudze HDInsight przy użyciu środowiska IntelliJ](./apache-spark-create-standalone-application.md) |
| Notesy Zeppelin | [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Zdalne przesyłanie zadania za pomocą usługi Livy | [Przesyłanie zadań zdalnych do klastra usługi HDInsight Spark przy użyciu interfejsu API REST platformy Apache Spark](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie to przepływ pracy i system koordynacji, który zarządza zadaniami usługi Hadoop.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Można użyć usługi Livy do uruchamiania interakcyjnych powłok Spark lub przesyłania zadań wsadowych do uruchamiania na platformie Spark.|
|[Azure Data Factory Apache Spark](../../data-factory/transform-data-using-spark.md)|Działanie platformy Spark w potoku Data Factory wykonuje program platformy Spark we własnym lub [w klastrze usługi HDInsight na żądanie.|
|[Azure Data Factory Apache Hive](../../data-factory/transform-data-using-hadoop-hive.md)|Działanie programu Hive w usłudze HDInsight w potoku Data Factory wykonuje zapytania Hive w klastrze usługi HDInsight w twoim własnym lub na żądanie.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Jak mogę monitorować i debugować zadania platformy Spark?

| Opcja | Dokumenty |
|---|---|
| Azure Toolkit for IntelliJ | [Błąd debugowania zadań platformy Spark z użyciem Azure Toolkit for IntelliJ (wersja zapoznawcza)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ za pośrednictwem protokołu SSH | [Lokalne lub zdalne debugowanie aplikacji platformy Apache Spark w klastrze usługi HDInsight za pomocą zestawu narzędzi Azure Toolkit for IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ za pośrednictwem sieci VPN | [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w usłudze HDInsight za pośrednictwem sieci VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Wykres zadania na serwerze historii Apache Spark | [Debugowanie i diagnozowanie aplikacji platformy Apache Spark za pomocą rozszerzonego serwera historii platformy Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Jak mogę sprawić, że moje zadania platformy Spark działają wydajniej?

| Opcja | Dokumenty |
|---|---|
| Pamięć podręczna we/wy | [Poprawianie wydajności Apache Spark obciążeń przy użyciu usługi Azure HDInsight we/wy (wersja zapoznawcza)](./apache-spark-improve-performance-iocache.md) |
| Opcje konfiguracji | [Optymalizowanie zadań platformy Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Jak mogę połączyć się z innymi usługami platformy Azure?

| Opcja | Dokumenty |
|---|---|
| Apache Hive w usłudze HDInsight | [Integrowanie Apache Spark i Apache Hive z łącznikiem magazynu Hive](../interactive-query/apache-hive-warehouse-connector.md) |
| Usługa Apache HBase w usłudze HDInsight | [Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka w usłudze HDInsight | [Samouczek: używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Usługa Azure Synapse Link dla usługi Azure Cosmos DB](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>Jakie są opcje magazynu?

| Opcja | Dokumenty |
|---|---|
| Usługa Azure Data Lake Storage 2. generacji | [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Usługa Azure Data Lake Storage 1. generacji | [Używanie Azure Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob Storage | [Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)
* [Optymalizowanie Apache Spark zadań w usłudze HDInsight](apache-spark-perf.md)
