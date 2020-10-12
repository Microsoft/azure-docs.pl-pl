---
title: Skalowanie rozmiarów klastrów — Azure HDInsight
description: Skalowanie klastra Apache Hadoop elastycznie w celu dopasowania do obciążenia w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 44cfc5b651bdd5dc0d7abee575bd964ad0b603d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505016"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skalowanie klastrów usługi Azure HDInsight

Usługa HDInsight zapewnia elastyczność z opcjami umożliwiającymi skalowanie w górę i w dół liczby węzłów procesu roboczego w klastrach. Elastyczność pozwala zmniejszyć klaster po godzinach lub w weekendy. I rozwijać je podczas szczytowego zapotrzebowania na działalność biznesową.

Skaluj klaster przed okresowym przetwarzaniem wsadowym, aby klaster miał odpowiednie zasoby.  Po zakończeniu przetwarzania i przejściu w dół klaster usługi HDInsight jest skalowany w dół do mniejszej liczby węzłów procesu roboczego.

Klaster można skalować ręcznie przy użyciu jednej z metod opisanych poniżej. Możesz również użyć opcji [skalowania](hdinsight-autoscale-clusters.md) automatycznego, aby automatycznie skalować w górę i w dół w odpowiedzi na określone metryki.

> [!NOTE]  
> Obsługiwane są tylko klastry z usługą HDInsight w wersji 3.1.3 lub nowszej. Jeśli nie masz pewności, jaka jest wersja klastra, możesz sprawdzić stronę właściwości.

## <a name="utilities-to-scale-clusters"></a>Narzędzia do skalowania klastrów

Firma Microsoft udostępnia następujące narzędzia do skalowania klastrów:

|Narzędzie | Opis|
|---|---|
|[Moduł Az programu PowerShell](https://docs.microsoft.com/powershell/azure)|[`Set-AzHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Moduł AzureRM programu PowerShell](https://docs.microsoft.com/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) | [`az hdinsight resize`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Klasyczny interfejs wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure Portal](https://portal.azure.com)|Otwórz okienko klastra usługi HDInsight, wybierz pozycję **rozmiar klastra** w menu po lewej stronie, a następnie w okienku rozmiar klastra wpisz liczbę węzłów procesu roboczego i wybierz pozycję Zapisz.|  

![Azure Portal skalowanie — opcja klastra](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Korzystając z dowolnej z tych metod, można skalować klaster usługi HDInsight w górę lub w dół w ciągu kilku minut.

> [!IMPORTANT]  
> * Klasyczny interfejs wiersza polecenia platformy Azure jest przestarzały i powinien być używany tylko z klasycznym modelem wdrażania. W przypadku wszystkich innych wdrożeń Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
> * Moduł AzureRM programu PowerShell jest przestarzały.  Użyj [AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) wszędzie tam, gdzie to możliwe.

## <a name="impact-of-scaling-operations"></a>Wpływ operacji skalowania

W przypadku **dodawania** węzłów do działającego klastra usługi HDInsight (skalowanie w górę) nie ma to wpływu na zadania. Nowe zadania mogą być bezpiecznie przesłane, gdy proces skalowania jest uruchomiony. Jeśli operacja skalowania nie powiedzie się, błąd spowoduje pozostawienie klastra w stanie funkcjonalnym.

Jeśli **usuniesz** węzły (skalowanie w dół), oczekujące lub uruchomione zadania zakończą się niepowodzeniem po zakończeniu operacji skalowania. Ten błąd jest spowodowany tym, że niektóre z usług są ponownie uruchamiane podczas procesu skalowania. Klaster może zostać zablokowany w trybie awaryjnym podczas operacji skalowania ręcznego.

Wpływ zmiany liczby węzłów danych różni się w zależności od typu klastra obsługiwanego przez usługi HDInsight:

* Apache Hadoop

    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w uruchomionym klastrze usługi Hadoop bez wpływu na żadne zadania. Nowe zadania mogą być również przesyłane, gdy operacja jest w toku. Błędy w operacji skalowania są bezpiecznie obsługiwane. Klaster zawsze pozostaje w stanie funkcjonalności.

    Gdy klaster usługi Hadoop jest skalowany z mniejszą liczbą węzłów danych, niektóre usługi są uruchamiane ponownie. To zachowanie powoduje, że wszystkie uruchomione i oczekujące zadania kończą się niepowodzeniem po zakończeniu operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.

* Apache HBase

    Można bezproblemowo dodawać lub usuwać węzły do klastra HBase, gdy jest on uruchomiony. Serwery regionalne są automatycznie równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można jednak ręcznie zrównoważyć serwery regionalne. Zaloguj się do węzła głównego klastra i uruchom następujące polecenia:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Aby uzyskać więcej informacji na temat używania powłoki HBase, zobacz Rozpoczynanie [pracy z przykładem Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Można bezproblemowo dodawać lub usuwać węzły danych, gdy burza jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania należy ponownie zrównoważyć topologię. Ponowne równoważenie umożliwia topologii dopasowanie [ustawień równoległości](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) na podstawie nowej liczby węzłów w klastrze. Aby ponownie zrównoważyć uruchomione topologie, użyj jednej z następujących opcji:

  * Interfejs użytkownika sieci Web burzy

    Wykonaj następujące kroki, aby ponownie zrównoważyć topologię przy użyciu interfejsu użytkownika burzy.

    1. Otwórz `https://CLUSTERNAME.azurehdinsight.net/stormui` w przeglądarce sieci Web, gdzie `CLUSTERNAME` to nazwa klastra burzy. Jeśli zostanie wyświetlony monit, wprowadź nazwę administratora klastra usługi HDInsight (administratora) i hasło określone podczas tworzenia klastra.

    1. Wybierz topologię, którą chcesz ponownie zrównoważyć, a następnie wybierz przycisk **Zrównoważ** ponownie. Wprowadź opóźnienie przed wykonaniem operacji ponownego równoważenia.

        ![Ponowne równoważenie skali burzy usługi HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

  * Narzędzie interfejsu wiersza polecenia (CLI)

    Połącz się z serwerem i użyj następującego polecenia, aby ponownie zrównoważyć topologię:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Można także określić parametry, aby zastąpić wskazówki równoległości początkowo dostarczone przez topologię. Na przykład poniższy kod ponownie konfiguruje `mytopology` topologię do 5 procesów roboczych, 3 wykonawców dla składnika Blue-elementu Spout i 10 programów wykonujących dla żółtego składnika.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Należy ponownie zrównoważyć repliki partycji po przeprowadzeniu operacji skalowania. Aby uzyskać więcej informacji, zobacz [wysoka dostępność danych w dokumencie Apache Kafka w usłudze HDInsight](./kafka/apache-kafka-high-availability.md) .

* Apache Hive LLAP

    Po skalowaniu do `N` węzłów procesu roboczego Usługa HDInsight automatycznie ustawi następujące konfiguracje i ponownie uruchomi gałąź.

  * Maksymalna łączna liczba współbieżnych zapytań: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Liczba węzłów używanych przez LLAP Hive: `num_llap_nodes  = N`
  * Liczba węzłów do uruchomienia demona Hive LLAP: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Jak bezpiecznie skalować klaster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skalowanie w dół klastra z uruchomionymi zadaniami

Aby uniknąć niepowodzenia wykonywanych zadań podczas operacji skalowania w dół, możesz wypróbować trzy rzeczy:

1. Poczekaj na zakończenie zadań przed skalowaniem klastra w dół.
1. Ręcznie Zakończ zadania.
1. Prześlij ponownie zadania po zakończeniu operacji skalowania.

Aby wyświetlić listę oczekujących i uruchomionych zadań, można użyć **interfejsu użytkownika Menedżer zasobów**przędzy, wykonując następujące czynności:

1. Na [Azure Portal](https://portal.azure.com/)wybierz swój klaster.  Klaster zostanie otwarty na nowej stronie portalu.
2. W widoku głównym przejdź do strony **pulpity nawigacyjne klastra**  >  **Ambari Home**. Wprowadź poświadczenia klastra.
3. Z poziomu interfejsu użytkownika Ambari wybierz pozycję **przędza** na liście usług w menu po lewej stronie.  
4. Na stronie PRZĘDZa wybierz pozycję **szybkie linki** i umieść kursor nad aktywnym węzłem głównym, a następnie wybierz pozycję **Menedżer zasobów interfejsie użytkownika**.

    ![Szybkie linki Menedżer zasobów Apache Ambari](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Możesz bezpośrednio uzyskać dostęp do interfejsu użytkownika Menedżer zasobów przy użyciu programu `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Zostanie wyświetlona lista zadań wraz z bieżącym stanem. Na zrzucie ekranu jest aktualnie uruchomione jedno zadanie:

![Menedżer zasobów aplikacji interfejsu użytkownika](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Aby ręcznie skasować tę uruchomioną aplikację, wykonaj następujące polecenie w powłoce SSH:

```bash
yarn application -kill <application_id>
```

Na przykład:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Utknięcie w trybie awaryjnym

Podczas skalowania w dół klastra Usługa HDInsight używa interfejsów zarządzania Apache Ambari do pierwszej likwidacji dodatkowych węzłów procesu roboczego. Węzły replikują swoje bloki systemu plików HDFS do innych węzłów procesów roboczych w trybie online. Następnie Usługa HDInsight bezpiecznie skaluje klaster. W trakcie operacji skalowania system plików HDFS przechodzi do trybu awaryjnego. System plików HDFS powinien zostać wyprowadzony po zakończeniu skalowania. W niektórych przypadkach system plików HDFS jest blokowany w trybie awaryjnym podczas operacji skalowania ze względu na to, że jest on w trakcie replikacji.

Domyślnie system plików HDFS jest skonfigurowany z `dfs.replication` ustawieniem 1, które określa, ile kopii poszczególnych bloków plików jest dostępnych. Każda kopia bloku plików jest przechowywana w innym węźle klastra.

Gdy oczekiwana liczba kopii bloku nie jest dostępna, system plików HDFS przechodzi do trybu awaryjnego i Ambari generuje alerty. System plików HDFS może wejść w tryb awaryjny dla operacji skalowania. Klaster może zostać zablokowany w trybie awaryjnym, jeśli wymagana liczba węzłów nie zostanie wykryta na potrzeby replikacji.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Przykładowe błędy w przypadku włączenia trybu awaryjnego

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Można przejrzeć nazwy dzienników węzłów z `/var/log/hadoop/hdfs/` folderu, w czasie, gdy klaster został przeskalowany, aby zobaczyć, kiedy przeszedł tryb awaryjny. Pliki dziennika mają nazwę `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

Głównym powodem jest to, że gałąź czasowa jest zależna od plików tymczasowych w systemie plików HDFS podczas wykonywania zapytań. Gdy system plików HDFS przechodzi w tryb awaryjny, gałąź nie może uruchamiać zapytań, ponieważ nie może zapisywać w systemie plików HDFS. Pliki tymczasowe w systemie plików HDFS znajdują się na dysku lokalnym zainstalowanym na maszynach wirtualnych poszczególnych węzłów procesu roboczego. Pliki są replikowane między innymi węzłami procesu roboczego w trzech replikach, co najmniej.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak uniemożliwić blokowanie usługi HDInsight w trybie awaryjnym

Istnieje kilka sposobów, aby uniemożliwić pozostawienie usługi HDInsight w trybie awaryjnym:

* Zatrzymaj wszystkie zadania Hive przed skalowaniem w dół usługi HDInsight. Alternatywnie Zaplanuj proces skalowania w dół, aby uniknąć konfliktu z uruchomionymi zadaniami programu Hive.
* Ręcznie Wyczyść pliki katalogów magazynu Hive `tmp` w systemie plików HDFS przed skalowaniem w dół.
* Maksymalnie Skaluj w dół usługi HDInsight do trzech węzłów procesów roboczych. Należy unikać przechodzenia do jednego węzła procesu roboczego.
* Uruchom polecenie, aby wyjść z trybu awaryjnego, w razie potrzeby.

W poniższych sekcjach opisano te opcje.

#### <a name="stop-all-hive-jobs"></a>Zatrzymaj wszystkie zadania Hive

Zatrzymaj wszystkie zadania Hive przed skalowaniem w dół do jednego węzła procesu roboczego. Jeśli obciążenie jest zaplanowane, po wykonaniu operacji Hive wykonaj skalowanie w dół.

Zatrzymywanie zadań programu Hive przed skalowaniem umożliwia zminimalizowanie liczby plików w folderze tmp (jeśli istnieje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ręczne czyszczenie plików tymczasowych Hive

Jeśli gałąź została pozostawiona za pliki tymczasowe, można ręcznie oczyścić te pliki przed skalowaniem w dół, aby uniknąć trybu awaryjnego.

1. Sprawdź, która lokalizacja jest używana dla plików tymczasowych programu Hive, przeglądając `hive.exec.scratchdir` właściwość konfiguracji. Ten parametr jest ustawiany w `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zatrzymaj usługi Hive i upewnij się, że wszystkie zapytania i zadania zostały ukończone.

1. Wystaw zawartość katalogu tymczasowego znalezionego powyżej, `hdfs://mycluster/tmp/hive/` Aby zobaczyć, czy zawiera on pliki:

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

1. Jeśli wiesz, że gałąź jest wykonywana z tymi plikami, możesz je usunąć. Upewnij się, że w usłudze Hive nie są uruchomione żadne zapytania, szukając na stronie interfejsu użytkownika Menedżer zasobów przędzy.

    Przykładowy wiersz polecenia usuwania plików z systemu plików HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skalowanie usługi HDInsight do trzech lub większej liczby węzłów procesu roboczego

Jeśli klastry są wykonywane w trybie awaryjnym często podczas skalowania do mniej niż trzy węzły procesów roboczych, należy pozostawić co najmniej trzy węzły procesu roboczego.

Trzy węzły procesu roboczego są droższe niż skalowanie w dół tylko do jednego węzła procesu roboczego. Jednak ta akcja uniemożliwi uruchamianie klastra w trybie awaryjnym.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Skalowanie usługi HDInsight do jednego węzła procesu roboczego

Nawet jeśli klaster jest skalowany w dół do jednego węzła, węzeł procesu roboczego 0 nadal będzie przeżyje. Węzeł procesu roboczego 0 nigdy nie może zostać zlikwidowany.

#### <a name="run-the-command-to-leave-safe-mode"></a>Uruchom polecenie, aby wyjść z trybu awaryjnego

Końcową opcją jest wykonanie polecenia Opuść tryb bezpieczny. Jeśli w systemie plików HDFS wprowadzono tryb awaryjny z powodu replikacji pliku Hive, wykonaj następujące polecenie, aby wyjść z trybu awaryjnego:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skalowanie klastra Apache HBase

Serwery regionów są automatycznie równoważone w ciągu kilku minut od zakończenia operacji skalowania. Aby ręcznie zrównoważyć serwery regionów, wykonaj następujące czynności:

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

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

* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md#scale-clusters)
