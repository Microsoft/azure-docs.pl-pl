---
title: Rozwiązywanie problemów z programem Hive przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z usługami Apache Hive i Azure HDInsight.
keywords: Azure HDInsight, Hive, często zadawane pytania, Przewodnik rozwiązywania problemów, często zadawane pytania
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: a76974b8daddf225b0d9b8b581475d657322847e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931442"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Troubleshoot Apache Hive by using Azure HDInsight (Rozwiązywanie problemów z technologią Apache Hive za pomocą usługi Azure HDInsight)

Zapoznaj się z najważniejszymi pytaniami i ich rozwiązaniami podczas pracy z Apache Hive ładunku w programie Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak mogę wyeksportować magazyn metadanych Hive i zaimportować go do innego klastra?

### <a name="resolution-steps"></a>Kroki rozwiązania

1. Połącz się z klastrem usługi HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowy odczyt](#additional-reading-end).

2. Uruchom następujące polecenie w klastrze usługi HDInsight, z którego chcesz wyeksportować magazyn metadanych:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   To polecenie generuje plik o nazwie allatables. SQL.

3. Skopiuj plik alltables. SQL do nowego klastra usługi HDInsight, a następnie uruchom następujące polecenie:

    ```apache
    hive -f alltables.sql
    ```

Kod w krokach rozwiązania założono, że ścieżki danych w nowym klastrze są takie same jak ścieżki danych w starym klastrze. Jeśli ścieżki danych są różne, można ręcznie edytować wygenerowany `alltables.sql` plik w celu odzwierciedlenia zmian.

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak mogę zlokalizować dzienników Hive w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązania

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz **dodatkowy odczyt**.

2. Aby wyświetlić dzienniki klienta programu Hive, użyj następującego polecenia:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Aby wyświetlić dzienniki magazyn metadanych Hive, użyj następującego polecenia:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Aby wyświetlić dzienniki serwera Hive, użyj następującego polecenia:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak mogę uruchomić powłokę programu Hive z określonymi konfiguracjami w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązania

1. Określ parę klucz-wartość konfiguracji podczas uruchamiania powłoki programu Hive. Aby uzyskać więcej informacji, zobacz [dodatkowy odczyt](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Aby wyświetlić listę wszystkich efektywnych konfiguracji w usłudze Hive Shell, użyj następującego polecenia:

   ```apache
   hive> set;
   ```

   Na przykład użyj następującego polecenia, aby uruchomić powłokę programu Hive z włączonym rejestrowaniem debugowania w konsoli programu:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Właściwości konfiguracji programu Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak mogę analizować Apache Tez DAG danych w ścieżce krytycznej dla klastra?

### <a name="resolution-steps"></a>Kroki rozwiązania

1. Aby analizować Apache Tez ukierunkowany wykres (DAG) na wykresie krytycznym dla klastra, Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [dodatkowy odczyt](#additional-reading-end).

2. W wierszu polecenia uruchom następujące polecenie:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Aby wyświetlić listę innych analizatorów, których można użyć do analizowania tez DAG, użyj następującego polecenia:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Musisz podać Przykładowy program jako pierwszy argument.

   Prawidłowe nazwy programów to:
    - **ContainerReuseAnalyzer**: Drukuj szczegóły ponownego użycia kontenera w Dag
    - **CriticalPath**: Znajdź ścieżkę KRYTYCZNą Dag
    - **LocalityAnalyzer**: szczegóły drukowania w Dag
    - **ShuffleTimeAnalyzer**: Analizuj szczegóły czasu losowego w Dag
    - **SkewAnalyzer**: Analizuj szczegóły pochylenia w Dag
    - **SlowNodeAnalyzer**: drukowanie szczegółów węzła w Dag
    - **SlowTaskIdentifier**: Drukuj wolne szczegóły zadania w Dag
    - **SlowestVertexAnalyzer**: Drukuj najwolniejsze szczegóły wierzchołka w Dag
    - **SpillAnalyzer**: drukowanie szczegółów rozlania w Dag
    - **TaskConcurrencyAnalyzer**: Wydrukuj szczegóły współbieżności zadania w Dag
    - **VertexLevelCriticalPathAnalyzer**: Znajdź ścieżkę krytyczną na poziomie wierzchołka w Dag

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak mogę pobrać danych tez DAG z klastra?

#### <a name="resolution-steps"></a>Kroki rozwiązania

Istnieją dwa sposoby zbierania danych tez DAG:

- Za pomocą wiersza polecenia:

    Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. W wierszu polecenia wpisz następujące polecenie:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Użyj widoku Ambari tez:

  1. Przejdź do Ambari.
  2. Przejdź do widoku tez (w obszarze ikony kafelków w prawym górnym rogu).
  3. Wybierz DAG, które chcesz wyświetlić.
  4. Wybierz pozycję **Pobierz dane**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Materiały uzupełniające

[Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]