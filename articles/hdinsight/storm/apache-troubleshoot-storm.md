---
title: Rozwiązywanie problemów z burzą przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z Apache Storm z usługą Azure HDInsight.
keywords: Azure HDInsight, burza, często zadawane pytania, Przewodnik rozwiązywania problemów, typowe problemy
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: c81084c77b355a5d60c72564c58a98e08da14312
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946308"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Apache Storm przy użyciu usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania umożliwiające pracę z [Apache Storm](https://storm.apache.org/) ładunkiami w programie [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak mogę uzyskać dostęp do interfejsu użytkownika burzy w klastrze?

Dostępne są dwie opcje uzyskiwania dostępu do interfejsu użytkownika burzy z przeglądarki:

### <a name="apache-ambari-ui"></a>Interfejs użytkownika Apache Ambari

1. Przejdź do pulpitu nawigacyjnego Ambari.
2. Na liście usług wybierz pozycję **burza**.
3. W menu **szybkie linki** wybierz pozycję **interfejs użytkownika burzy**.

### <a name="direct-link"></a>Link bezpośredni

Możesz uzyskać dostęp do interfejsu użytkownika burzy pod następującym adresem URL:

`https://<cluster DNS name>/stormui`

Przykład: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak mogę transferu informacji o punkcie kontrolnym centrum zdarzeń burzy elementu Spout z jednej topologii do innej?

Podczas tworzenia topologii odczytanych z usługi Azure Event Hubs przy użyciu pliku elementu Spout. jar z centrum zdarzeń burza (HDInsight) należy wdrożyć topologię o tej samej nazwie w nowym klastrze. Należy jednak zachować dane punktów kontrolnych, które zostały zatwierdzone do [Apache ZooKeeper](https://zookeeper.apache.org/) w starym klastrze.

### <a name="where-checkpoint-data-is-stored"></a>Miejsce przechowywania danych punktów kontrolnych

Dane punktów kontrolnych dla przesunięć są przechowywane przez centrum zdarzeń elementu Spout w dozorcy w dwóch ścieżkach głównych:

- Nietransakcyjne punkty kontrolne elementu Spout są przechowywane w `/eventhubspout` .

- Dane transakcyjnego punktu kontrolnego elementu Spout są przechowywane w `/transactional` .

### <a name="how-to-restore"></a>Jak przywrócić

Aby pobrać skrypty i biblioteki, których używasz do eksportowania danych z programu dozorcy, a następnie zaimportuj dane z powrotem do dozorcy z nową nazwą, zobacz [przykłady burzy](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)w usłudze HDInsight.

Folder lib zawiera pliki jar, które zawierają implementację operacji eksportu/importu. Folder bash zawiera przykładowy skrypt demonstrujący sposób eksportowania danych z serwera dozorcy w starym klastrze, a następnie zaimportowania go z powrotem do serwera dozorcy w nowym klastrze.

Uruchom skrypt [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) z węzłów dozorcy, aby wyeksportować i zaimportować dane. Zaktualizuj skrypt do odpowiedniej wersji Hortonworks Data Platform (HDP). (Pracujemy nad tym, że te skrypty są ogólne w usłudze HDInsight. Skrypty ogólne można uruchamiać z dowolnego węzła w klastrze bez modyfikacji użytkownika.

Polecenie eksportu zapisuje metadane w ścieżce rozproszony system plików Apache Hadoop (HDFS) (na platformie Azure Blob Storage lub Azure Data Lake Storage) w ustawionej lokalizacji.

### <a name="examples"></a>Przykłady

#### <a name="export-offset-metadata"></a>Eksportowanie metadanych przesunięcia

1. Użyj protokołu SSH, aby przejść do klastra dozorcy w klastrze, z którego ma zostać wyeksportowane przesunięcie punktu kontrolnego.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby wyeksportować dane przesunięcia dozorcy do `/stormmetadta/zkdata` ścieżki HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importuj metadane przesunięcia

1. Użyj protokołu SSH, aby przejść do klastra dozorcy w klastrze, z którego należy zaimportować przesunięcie punktu kontrolnego.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby zaimportować dane przesunięcia dozorcy z ścieżki HDFS `/stormmetadata/zkdata` do serwera dozorcy w klastrze docelowym:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Usuwanie metadanych przesunięcia, dzięki czemu topologie mogą rozpocząć przetwarzanie danych od początku lub z sygnatury czasowej wybranej przez użytkownika

1. Użyj protokołu SSH, aby przejść do klastra dozorcy w klastrze, z którego należy usunąć przesunięcie punktu kontrolnego.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby usunąć wszystkie dane przesunięcia dozorcy w bieżącym klastrze:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak mogę zlokalizować plików binarnych burzy w klastrze?

Pliki binarne burzy dla bieżącego stosu HDP znajdują się w `/usr/hdp/current/storm-client` . Lokalizacja jest taka sama w przypadku węzłów głównych i węzłów procesu roboczego.

Może istnieć wiele plików binarnych dla określonych wersji HDP w/usr/HDP (na przykład `/usr/hdp/2.5.0.1233/storm` ). `/usr/hdp/current/storm-client`Folder jest symlinked do najnowszej wersji działającej w klastrze.

Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) i [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Jak mogę ustalić topologii wdrażania klastra burzy?

Najpierw Zidentyfikuj wszystkie składniki, które są zainstalowane wraz z usługą HDInsight. Klaster burzy składa się z czterech kategorii węzłów:

* Węzły bramy
* Węzły główne
* Dozorcy węzły
* Węzły procesu roboczego

### <a name="gateway-nodes"></a>Węzły bramy

Węzeł bramy to Usługa bramy i zwrotny serwer proxy, który umożliwia publiczny dostęp do aktywnej usługi zarządzania Ambari. Obsługuje ona również wybór lidera Ambari.

### <a name="head-nodes"></a>Węzły główne

Węzły główne burzy są uruchomione następujące usługi:
* Nimbus
* Serwer Ambari
* Serwer metryk Ambari
* Moduł zbierający metryk Ambari
 
### <a name="zookeeper-nodes"></a>Dozorcy węzły

Usługa HDInsight zawiera dozorcy kworum z trzema węzłami. Rozmiar kworum został ustalony i nie można go zmienić.

Usługi burzy w klastrze są skonfigurowane do automatycznego używania kworum dozorcy.

### <a name="worker-nodes"></a>Węzły procesu roboczego

W węzłach procesu roboczego burzy są uruchomione następujące usługi:
* Nadzorca
* Maszyny wirtualne Java dla procesów roboczych (JVMs), na potrzeby uruchamiania topologii
* Agent Ambari

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Jak mogę zlokalizować dane binarne usługi Event Hub elementu Spout na potrzeby programowania?

Aby uzyskać więcej informacji na temat używania aplikacji elementu Spout. jar centrum zdarzeń z topologią, zobacz następujące zasoby.

### <a name="java-based-topology"></a>Topologia oparta na języku Java

[Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia języka C# (mono w przypadku klastrów burzowych usługi HDInsight 3.4 + Linux)

[Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Najnowsze Apache Storm plików binarnych elementu Spout centrum zdarzeń dla klastrów burzy usługi HDInsight 3.5 + Linux

Aby dowiedzieć się, jak używać najnowszej elementu Spout centrum zdarzeń burzy, które współpracuje z klastrami burzy usługi HDInsight 3.5 + Linux, zobacz [plik Readme MVN-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Przykłady kodu źródłowego

Zapoznaj się z [przykładami](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) dotyczącymi odczytywania i zapisywania z usługi Azure Event Hub przy użyciu topologii Apache Storm (napisanych w języku Java) w klastrze usługi Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Jak mogę zlokalizować pliki konfiguracji burzy Log4J 2 w klastrach?

Aby zidentyfikować pliki konfiguracji [Apache Log4J 2](https://logging.apache.org/log4j/2.x/) dla usług burzy.

### <a name="on-head-nodes"></a>Na węzłach głównych

Konfiguracja Nimbus Log4J jest odczytana z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

### <a name="on-worker-nodes"></a>Na węzłach procesu roboczego

Konfiguracja Log4J opiekuna jest odczytywana z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

Plik konfiguracji Log4J procesu roboczego jest odczytywany z programu `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` .

Pokazują `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>To nie jest wyjątek lidera

Podczas przesyłania topologii użytkownik może otrzymać komunikat o błędzie podobny do: `Topology submission exception, cause not a leader, the current leader is NimbusInfo` .

Aby rozwiązać ten problem, użytkownik może potrzebować zaplikować bilet w celu ponownego uruchomienia lub ponownego uruchomienia węzłów. Aby uzyskać więcej informacji, zobacz [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).