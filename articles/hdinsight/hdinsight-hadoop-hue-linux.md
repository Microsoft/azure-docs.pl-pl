---
title: Odcień usługi Hadoop w klastrach opartych na systemie Linux w usłudze HDInsight — Azure
description: Dowiedz się, jak zainstalować odcień w klastrach usługi HDInsight i użyć tunelowania, aby skierować żądania do odcienia. Użyj odcieni, aby przeglądać magazyn i uruchamiać Hive lub świnie.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 8d4663aac6af4abb8d9855d2f972965e997d9c92
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945687"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalowanie i używanie odcienia w klastrach usługi HDInsight Hadoop

Dowiedz się, jak zainstalować odcień w klastrach usługi HDInsight i użyć tunelowania, aby skierować żądania do odcienia.

## <a name="what-is-hue"></a>Co to jest odcień?

Odcień to zestaw aplikacji sieci Web służący do współdziałania z klastrem Apache Hadoop. Można użyć odcienia do przeglądania magazynu skojarzonego z klastrem usługi Hadoop (WASB, w przypadku klastrów usługi HDInsight), uruchamiania zadań Hive i skryptów świńskich itd. W przypadku instalacji odcienia w klastrze usługi HDInsight Hadoop dostępne są następujące składniki.

* Edytor Hive Beeswax
* Apache świni
* Menedżer magazynu metadanych
* Apache Oozie
* FileBrowser (który nakomunikuje się z kontenerem domyślnym WASB)
* Przeglądarka zadań

> [!WARNING]  
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane, a pomoc techniczna firmy Microsoft ułatwiają izolowanie i rozwiązywanie problemów związanych z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, która ułatwia dalsze Rozwiązywanie problemu. Może to skutkować rozwiązaniem problemu lub zapytaniem o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się Szczegółowa wiedza dla tej technologii. Na przykład istnieje wiele witryn społeczności, których można użyć, takich jak: [Microsoft Q&stronie pytania dotyczącej usługi HDInsight](/answers/topics/azure-hdinsight.html) [https://stackoverflow.com](https://stackoverflow.com) . Również projekty Apache mają witryny projektu [https://apache.org](https://apache.org) , na przykład: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalowanie odcienia przy użyciu akcji skryptu

Skorzystaj z informacji podanych w poniższej tabeli dla akcji skryptu. Zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md) , aby uzyskać szczegółowe instrukcje dotyczące korzystania z akcji skryptu.

> [!NOTE]  
> Aby można było zainstalować odcień w klastrach usługi HDInsight, zalecanym rozmiarem węzła głównego jest co najmniej A4 (8 rdzeni, 14 GB pamięci).

|Właściwość |Wartość |
|---|---|
|Typ skryptu:|-Niestandardowe|
|Nazwa|Zainstaluj odcień|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Typy węzłów:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Używanie odcienia z klastrami usługi HDInsight

W zwykłych klastrach można mieć tylko jedno konto użytkownika z odcienią. W przypadku dostępu przez użytkowników, należy włączyć [pakiet Enterprise Security](./domain-joined/hdinsight-security-overview.md) w klastrze. Tunelowanie SSH jest jedynym sposobem uzyskania dostępu do odcienia w klastrze po jego uruchomieniu. Tunelowanie za pośrednictwem protokołu SSH umożliwia przechodzenie ruchu bezpośrednio do węzła głównego klastra, w którym jest uruchomione. Po zakończeniu aprowizacji klastra wykonaj następujące kroki, aby użyć odcienia w klastrze usługi HDInsight.

> [!NOTE]  
> Zalecamy używanie przeglądarki Firefox sieci Web, aby postępować zgodnie z poniższymi instrukcjami.

1. Korzystając z informacji w temacie [Używanie tunelowania SSH do uzyskiwania dostępu do interfejsu użytkownika usługi Apache Ambari Web, ResourceManager, JobHistory, NameNode, Oozie i innego interfejsu użytkownika sieci Web](hdinsight-linux-ambari-ssh-tunnel.md) , można utworzyć tunel SSH od systemu klienckiego do klastra usługi HDInsight, a następnie skonfigurować przeglądarkę sieci Web tak, aby korzystała z tunelu SSH jako serwera proxy.

1. Użyj [polecenia SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po nawiązaniu połączenia użyj następującego polecenia, aby uzyskać w pełni kwalifikowaną nazwę domeny węzła głównego podstawowego:

    ```bash
    hostname -f
    ```

    Spowoduje to zwrócenie nazwy podobnej do następującej:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Jest to nazwa hosta głównego węzła głównego, w którym znajduje się witryna internetowa odcienia.

1. Użyj przeglądarki, aby otworzyć Portal odcienia pod adresem `http://HOSTNAME:8888` . Zastąp nazwę hosta nazwą uzyskaną w poprzednim kroku.

   > [!NOTE]  
   > Gdy logujesz się po raz pierwszy, zostanie wyświetlony monit o utworzenie konta w celu zalogowania się do portalu odcień. Poświadczenia określone w tym miejscu będą ograniczone do portalu i nie są powiązane z poświadczeniami administratora lub użytkownika SSH określonymi podczas aprowizacji klastra.

    ![Okno logowania portalu odcienia usługi HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Określ poświadczenia dla portalu odcienia")

### <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. W portalu odcienia wybierz pozycję **edytory zapytań**, a następnie wybierz pozycję **Hive** , aby otworzyć Edytor Hive.

    ![Portal odcienia usługi HDInsight Użyj edytora Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Korzystanie z programu Hive")

2. Na karcie **Pomoc** w obszarze **baza danych** powinna zostać wyświetlona wartość **hivesampletable**. Jest to Przykładowa tabela, która jest dostarczana ze wszystkimi klastrami Hadoop w usłudze HDInsight. Wprowadź przykładowe zapytanie w okienku po prawej stronie i zobacz dane wyjściowe na karcie **wyniki** w okienku poniżej, jak pokazano na przechwytywaniu ekranu.

    ![Zapytanie programu Hive portalu odcienia usługi HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Uruchom zapytanie programu Hive")

    Możesz również użyć karty **Wykres** , aby zobaczyć wizualną reprezentację wyniku.

### <a name="browse-the-cluster-storage"></a>Przeglądanie magazynu klastra

1. W portalu odcienia wybierz pozycję **przeglądarka plików** w prawym górnym rogu paska menu.
2. Domyślnie przeglądarka plików otwiera się w katalogu **/User/Myuser** . Wybierz ukośnik w prawo przed katalogiem użytkownika w ścieżce, aby przejść do katalogu głównego kontenera usługi Azure Storage skojarzonego z klastrem.

    ![Przeglądarka plików portalu odcienia usługi HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Użyj przeglądarki plików")

3. Kliknij prawym przyciskiem myszy plik lub folder, aby wyświetlić dostępne operacje. Użyj przycisku **Przekaż** w prawym górnym rogu, aby przekazać pliki do bieżącego katalogu. Użyj przycisku **Nowy** , aby utworzyć nowe pliki lub katalogi.

> [!NOTE]  
> W przeglądarce plików odcienia można wyświetlić tylko zawartość domyślnego kontenera skojarzonego z klastrem usługi HDInsight. Wszystkie dodatkowe konta magazynu/kontenery, które mogą być skojarzone z klastrem, nie będą dostępne przy użyciu przeglądarki plików. Jednak dodatkowe kontenery skojarzone z klastrem będą zawsze dostępne dla zadań Hive. Na przykład, jeśli wprowadzisz polecenie `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` w edytorze Hive, zobaczysz również zawartość dodatkowych kontenerów. W tym poleceniu **newcontainer** nie jest domyślnym kontenerem skojarzonym z klastrem.

## <a name="important-considerations"></a>Istotne zagadnienia

1. Skrypt służący do instalowania odcienia instaluje go tylko w podstawowym węzła głównego klastra.

1. Podczas instalacji usługi Hadoop (HDFS, PRZĘDZy, MR2, Oozie) są ponownie uruchamiane w celu zaktualizowania konfiguracji. Po zakończeniu instalowania przez skrypt odcienia może upłynąć trochę czasu, zanim inne usługi Hadoop będą mogły zostać uruchomione. Może to mieć wpływ na wydajność odcienia. Po uruchomieniu wszystkich usług odcień będzie w pełni funkcjonalny.

1. Odcień nie rozpoznaje Apache Tez zadań, które są bieżącym ustawieniem domyślnym dla programu Hive. Jeśli chcesz użyć MapReduce jako aparatu wykonywania programu Hive, zaktualizuj skrypt tak, aby używał następującego polecenia w skrypcie:

   `set hive.execution.engine=mr;`

1. W przypadku klastrów systemu Linux możesz mieć scenariusz, w którym usługi działają w podstawowym węzła głównego, podczas gdy Menedżer zasobów może być uruchomiona na serwerze pomocniczym. Taki scenariusz może spowodować błędy (pokazane poniżej) podczas korzystania z odcienia, aby wyświetlić szczegóły uruchamiania zadań w klastrze. Można jednak wyświetlić szczegóły zadania, gdy zadanie zostało zakończone.

   ![Przykładowy komunikat o błędzie portalu odcienia](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Błąd portalu odcienia")

   Jest to spowodowane znanym problemem. W ramach tego problemu należy zmodyfikować Ambari tak, aby aktywne Menedżer zasobów również działały na podstawowym węzła głównego.

1. Odcienie rozumie WebHDFS, podczas gdy klastry HDInsight używają usługi Azure Storage za pomocą programu `wasbs://` . Tak więc skrypt niestandardowy używany z akcją skryptu instaluje WebWasb, która jest usługą zgodną z WebHDFS na potrzeby rozmowy z WASB. W związku z tym, mimo że portal odcienia mówi system plików HDFS w miejscu (na przykład gdy przenosisz wskaźnik myszy nad **przeglądarką pliku**), powinien on być interpretowany jako WASB.

## <a name="next-steps"></a>Następne kroki

[Zainstaluj język R w klastrach usługi HDInsight](./r-server/r-server-overview.md). Użyj dostosowywania klastra, aby zainstalować język R w klastrach Hadoop usługi HDInsight. R to język i środowisko "open source" do obliczeń statystycznych. Zapewnia setki wbudowanych funkcji statystycznych i własnego języka programowania, który łączy aspekty funkcjonalne i programowanie zorientowane obiektowo. Zapewnia również szerokie możliwości graficzne.