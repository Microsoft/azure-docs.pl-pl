---
title: Skalowanie rozmiarów klastrów — Azure HDInsight
description: Elastyczne skalowanie klastra Apache Hadoop w celu dopasowania obciążenia do Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1c388cb070c66fc3a2322c358bc4113ed2106c77
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761853"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skalowanie Azure HDInsight klastra

Usługa HDInsight zapewnia elastyczność dzięki opcjam skalowania w górę i w dół liczby węzłów procesu roboczego w klastrach. Ta elastyczność pozwala zmniejszyć klaster po godzinach pracy lub w weekendy. Rozszerzaj ją w szczytowych wymaganiach biznesowych.

Przeskalij klaster w górę przed okresowym przetwarzaniem wsadowym, aby zapewnić klastrowi odpowiednie zasoby.  Po zakończeniu przetwarzania i spadku użycia przeskalij klaster usługi HDInsight w dół do mniejszej liczby węzłów procesu roboczego.

Klaster można skalować ręcznie przy użyciu jednej z metod opisanych poniżej. Możesz również użyć opcji [automatycznego skalowania,](hdinsight-autoscale-clusters.md) aby automatycznie skalować w górę i w dół w odpowiedzi na określone metryki.

> [!NOTE]  
> Obsługiwane są tylko klastry z usługą HDInsight w wersji 3.1.3 lub wyższej. Jeśli nie masz pewności co do wersji klastra, możesz sprawdzić stronę Właściwości.

## <a name="utilities-to-scale-clusters"></a>Narzędzia do skalowania klastrów

Firma Microsoft udostępnia następujące narzędzia do skalowania klastrów:

|Narzędzie | Opis|
|---|---|
|[Moduł Az programu PowerShell](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Moduł AzureRM programu PowerShell](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Interfejs wiersza polecenia platformy Azure](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az_hdinsight_resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Klasyczny interfejs wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Witryna Azure Portal](https://portal.azure.com)|Otwórz okienko klastra usługi HDInsight, wybierz pozycję Rozmiar klastra w menu po lewej stronie, a następnie w okienku Rozmiar klastra wpisz liczbę węzłów procesu roboczego i wybierz pozycję Zapisz. |  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure Portal skalowania klastra":::

Za pomocą dowolnej z tych metod można skalować klaster usługi HDInsight w górę lub w dół w ciągu kilku minut.

> [!IMPORTANT]  
> * Klasyczny interfejs wiersza polecenia platformy Azure jest przestarzały i powinien być używany tylko z klasycznym modelem wdrażania. W przypadku wszystkich innych wdrożeń użyj interfejsu wiersza [polecenia platformy Azure.](/cli/azure/)
> * Moduł AzureRM programu PowerShell jest przestarzały.  Używaj modułu [Az zawsze, gdy](/powershell/azure/new-azureps-module-az) jest to możliwe.

## <a name="impact-of-scaling-operations"></a>Wpływ operacji skalowania

Dodanie **węzłów** do uruchomionego klastra usługi HDInsight (skalowanie w górę) nie będzie mieć wpływu na zadania. Nowe zadania można bezpiecznie przesłać, gdy proces skalowania jest uruchomiony. Jeśli operacja skalowania nie powiedzie się, awaria pozostawi klaster w stanie funkcjonalnym.

Jeśli **usuniesz węzły** (skalowanie w dół), oczekujące lub uruchomione zadania nie powiodą się po zakończeniu operacji skalowania. Ten błąd wynika z ponownego uruchomienia niektórych usług podczas procesu skalowania. Klaster może zostać zablokowany w trybie awaryjnym podczas ręcznego skalowania.

Wpływ zmiany liczby węzłów danych różni się dla każdego typu klastra obsługiwanego przez usługi HDInsight:

* Apache Hadoop

    Możesz bezproblemowo zwiększyć liczbę węzłów procesu roboczego w uruchomionym klastrze hadoop bez wpływu na żadne zadania. Podczas operacji można również przesłać nowe zadania. Błędy operacji skalowania są bezpiecznie obsługiwane. Klaster jest zawsze w stanie funkcjonalnym.

    Gdy klaster hadoop jest skalowany w dół z mniejszą liczbą węzłów danych, niektóre usługi są ponownie uruchomione. To zachowanie powoduje niepowodzenie wszystkich uruchomionych i oczekujących zadań po zakończeniu operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.

* Apache HBase

    Możesz bezproblemowo dodawać lub usuwać węzły do klastra HBase, gdy jest uruchomiony. Serwery regionalne są automatycznie równoważyć w ciągu kilku minut od ukończenia operacji skalowania. Można jednak ręcznie zrównoważyć serwery regionalne. Zaloguj się do węzła headnode klastra i uruchom następujące polecenia:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Aby uzyskać więcej informacji na temat korzystania z powłoki HBase, zobacz Wprowadzenie do [przykładu bazy danych Apache HBase w u usługi HDInsight.](hbase/apache-hbase-tutorial-get-started-linux.md)

* Apache Storm

    Podczas działania systemu Storm można bezproblemowo dodawać lub usuwać węzły danych. Jednak po pomyślnym zakończeniu operacji skalowania należy ponownie zrównoważyć topologię. Ponowne równoważenie umożliwia topologii dostosowanie ustawień [równoległości](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) na podstawie nowej liczby węzłów w klastrze. Aby ponownie zrównoważyć uruchomione topologie, użyj jednej z następujących opcji:

  * Internetowy interfejs użytkownika systemu Storm

    Aby ponownie zrównoważyć topologię przy użyciu interfejsu użytkownika systemu Storm, należy wykonać poniższe kroki.

    1. Otwórz `https://CLUSTERNAME.azurehdinsight.net/stormui` w przeglądarce internetowej, gdzie to nazwa klastra `CLUSTERNAME` Storm. Jeśli zostanie wyświetlony monit, wprowadź nazwę i hasło administratora klastra usługi HDInsight określone podczas tworzenia klastra.

    1. Wybierz topologię, którą chcesz ponownie zrównoważyć, a następnie wybierz przycisk **Ponowne równoważenie.** Wprowadź opóźnienie przed operacją ponownego równoważenia.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="Ponowne równoważenie skali storm w umacie HDInsight":::

  * Narzędzie interfejsu wiersza polecenia

    Połącz się z serwerem i użyj następującego polecenia, aby ponownie zrównoważyć topologię:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Można również określić parametry, aby zastąpić wskazówki równoległości pierwotnie dostarczone przez topologię. Na przykład poniższy kod ponownie konfiguruje topologię na 5 procesów roboczych, 3 wykonawcy dla składnika blue-spout i 10 wykonawców dla składnika `mytopology` yellow-bolt.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Repliki partycji należy ponownie zrównoważyć po operacjach skalowania. Aby uzyskać więcej informacji, zobacz dokument High availability of data with Apache Kafka on HDInsight (Wysoka dostępność danych [za pomocą Apache Kafka u usługi HDInsight).](./kafka/apache-kafka-high-availability.md)

* Apache Hive LLAP

    Po skalowaniu do węzłów procesu roboczego hdInsight automatycznie ustawi następujące konfiguracje `N` i ponownie uruchomi program Hive.

  * Maksymalna łączna liczba współbieżnych zapytań: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Liczba węzłów używanych przez llap usługi Hive: `num_llap_nodes  = N`
  * Liczba węzłów do uruchamiania demona LLAP hive: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Bezpieczne skalowanie klastra w dół

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skalowanie w dół klastra z uruchomionymi zadaniami

Aby uniknąć awarii uruchomionych zadań podczas operacji skalowania w dół, możesz wypróbować trzy czynności:

1. Zaczekaj na zakończenie zadań przed skalowaniem klastra w dół.
1. Ręcznie zakończ zadania.
1. Ponownie prześlij zadania po zakończeniu operacji skalowania.

Aby wyświetlić listę oczekujących i uruchomionych zadań, możesz użyć interfejsu użytkownika Resource Manager **YARN,** następujące kroki:

1. Z [Azure Portal](https://portal.azure.com/)wybierz klaster.  Klaster zostanie otwarty na nowej stronie portalu.
2. Z widoku głównego przejdź do strony głównej **Pulpity nawigacyjne**  >  **klastra Ambari**. Wprowadź poświadczenia klastra.
3. W interfejsie użytkownika systemu Ambari wybierz pozycję **YARN** z listy usług w menu po lewej stronie.  
4. Na stronie YARN wybierz pozycję **Szybkie linki** i umieść kursor nad aktywnym węzłem głównym, a następnie wybierz pozycję **Resource Manager interfejsu użytkownika.**

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Szybkie linki do interfejsu użytkownika Resource Manager Apache Ambari":::

Bezpośredni dostęp do interfejsu użytkownika Resource Manager za `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` pomocą .

Zostanie wyświetlona lista zadań wraz z ich bieżącym stanem. Na zrzucie ekranu jest aktualnie uruchomione jedno zadanie:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager aplikacji interfejsu użytkownika":::

Aby ręcznie zakończyć działanie tej uruchomionej aplikacji, wykonaj następujące polecenie z powłoki SSH:

```bash
yarn application -kill <application_id>
```

Na przykład:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Utknięcie w trybie awaryjnym

Podczas skalowania klastra w dół usługa HDInsight używa interfejsów zarządzania Apache Ambari, aby najpierw zlikwidować dodatkowe węzły procesu roboczego. Węzły replikują swoje bloki systemu HDFS do innych węzłów procesu roboczego online. Następnie usługa HDInsight bezpiecznie skaluje klaster w dół. System plików HDFS przechodzi w tryb awaryjny podczas operacji skalowania. System plików HDFS powinien wyjść po zakończeniu skalowania. Jednak w niektórych przypadkach system plików HDFS jest blokowany w trybie awaryjnym podczas operacji skalowania z powodu pod replikacji bloków plików.

Domyślnie system plików HDFS jest konfigurowany z ustawieniem 1, które określa, ile kopii `dfs.replication` każdego bloku plików jest dostępnych. Każda kopia bloku plików jest przechowywana w innym węźle klastra.

Gdy oczekiwana liczba kopii blokowych nie jest dostępna, system plików HDFS przechodzi w tryb awaryjny, a system Ambari generuje alerty. System plików HDFS może wprowadzić tryb awaryjny dla operacji skalowania. Klaster może zostać zablokowany w trybie awaryjnym, jeśli wymagana liczba węzłów nie zostanie wykryta na potrzeby replikacji.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Przykładowe błędy przy włączonym trybie awaryjnym

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Możesz przejrzeć dzienniki węzła nazwy z folderu w pobliżu czasu skalowania klastra, aby sprawdzić, kiedy został `/var/log/hadoop/hdfs/` wprowadzony w tryb awaryjny. Pliki dziennika mają nazwę `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

Główną przyczyną było to, że program Hive zależy od plików tymczasowych w systemie plików HDFS podczas uruchamiania zapytań. Gdy system plików HDFS przechodzi w tryb awaryjny, hive nie może uruchamiać zapytań, ponieważ nie może zapisywać w systemie plików HDFS. Pliki tymczasowe w systemie plików HDFS znajdują się na dysku lokalnym zainstalowanym na poszczególnych maszyn wirtualnych węzła procesu roboczego. Pliki są replikowane między innymi węzłami procesu roboczego z co najmniej trzema replikami.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak zapobiec utknięciu w trybie awaryjnym przez hdInsight

Istnieje kilka sposobów zapobiegania pozostawianiu w trybie awaryjnym hdInsight:

* Zatrzymaj wszystkie zadania Hive przed skalowaniem w dół w umacie HDInsight. Możesz też zaplanować proces skalowania w dół, aby uniknąć konfliktu z uruchamianiem zadań Hive.
* Przed skalowaniem w dół ręcznie wyczyść pliki katalogu tymczasowego programu Hive `tmp` w systemie plików HDFS.
* Skalowanie w dół usługi HDInsight tylko do trzech węzłów roboczych ( minimum). Unikaj pracy tylko z jednym węzłem procesu roboczego.
* Uruchom polecenie , aby w razie potrzeby opuścić tryb awaryjny.

W poniższych sekcjach opisano te opcje.

#### <a name="stop-all-hive-jobs"></a>Zatrzymywanie wszystkich zadań Hive

Zatrzymaj wszystkie zadania Hive przed skalowaniem w dół do jednego węzła procesu roboczego. Jeśli obciążenie jest zaplanowane, po zakończeniu pracy z hive wykonaj skalowanie w dół.

Zatrzymanie zadań Hive przed skalowaniem pomaga zminimalizować liczbę plików scratch w folderze tmp (jeśli są).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ręczne czyszczenie plików scratch programu Hive

Jeśli program Hive pozostawił pliki tymczasowe, można ręcznie wyczyścić te pliki przed skalowaniem w dół, aby uniknąć trybu bezpiecznego.

1. Sprawdź, która lokalizacja jest używana dla plików tymczasowych hive, patrząc na `hive.exec.scratchdir` właściwość konfiguracji. Ten parametr jest ustawiany w `/etc/hive/conf/hive-site.xml` parametrze :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zatrzymaj usługi Hive i upewnij się, że wszystkie zapytania i zadania zostały ukończone.

1. Wyświetl zawartość katalogu tymczasowego znalezionego powyżej, aby `hdfs://mycluster/tmp/hive/` sprawdzić, czy zawiera on jakiekolwiek pliki:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Oto przykładowe dane wyjściowe, gdy istnieją pliki:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Jeśli wiesz, że za pomocą tych plików jest wykonywana hive, możesz je usunąć. Upewnij się, że program Hive nie ma uruchomionych żadnych zapytań, spoglądając na stronę interfejsu użytkownika Resource Manager Yarn.

    Przykładowy wiersz polecenia do usuwania plików z systemu plików HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skalowanie usługi HDInsight do co najmniej trzech węzłów procesu roboczego

Jeśli klastry często są zablokowane w trybie awaryjnym podczas skalowania w dół do mniej niż trzech węzłów procesu roboczego, zachowaj co najmniej trzy węzły procesu roboczego.

Posiadanie trzech węzłów procesu roboczego jest bardziej kosztowne niż skalowanie w dół tylko do jednego węzła procesu roboczego. Jednak ta akcja uniemożliwi klastrowi utknięcie w trybie awaryjnym.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Skalowanie w dół do jednego węzła roboczego w umacie HDInsight

Nawet jeśli klaster zostanie przeskalowany w dół do jednego węzła, węzeł roboczy 0 nadal będzie w stanie ocalenia. Nigdy nie można zlikwidować węzła roboczego 0.

#### <a name="run-the-command-to-leave-safe-mode"></a>Uruchom polecenie , aby opuścić tryb awaryjny

Ostatnia opcja polega na wykonaniu polecenia pozostaw tryb awaryjny. Jeśli system plików HDFS został wprowadzony w tryb awaryjny z powodu pod replikacji pliku Programu Hive, wykonaj następujące polecenie, aby wyjść z trybu bezpiecznego:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skalowanie klastra Apache HBase w dół

Serwery regionów są automatycznie równoważyć w ciągu kilku minut po zakończeniu operacji skalowania. Aby ręcznie zrównoważyć serwery regionów, wykonaj następujące czynności:

1. Połącz się z klastrem usługi HDInsight przy użyciu połączenia SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom powłokę HBase:

    ```bash
    hbase shell
    ```

3. Użyj następującego polecenia, aby ręcznie zrównoważyć serwery regionów:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Następne kroki

* [Automatyczne skalowanie klastrów usługi Azure HDInsight](hdinsight-autoscale-clusters.md)

Aby uzyskać szczegółowe informacje na temat skalowania klastra usługi HDInsight, zobacz:

* [Zarządzanie klastrami Apache Hadoop w umacie HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md#scale-clusters)