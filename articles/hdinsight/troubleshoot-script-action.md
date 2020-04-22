---
title: Rozwiązywanie problemów z akcjami skryptu w usłudze Azure HDInsight
description: Ogólne kroki rozwiązywania problemów z akcjami skryptów w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/21/2020
ms.openlocfilehash: b1e6b674edc155e0aa6c88ad360eb59864eebee4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771978"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Rozwiązywanie problemów z akcjami skryptu w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

Interfejs użytkownika sieci Web Apache Ambari umożliwia wyświetlanie informacji zarejestrowanych przez akcje skryptów. Jeśli skrypt nie powiedzie się podczas tworzenia klastra, dzienniki znajdują się na domyślnym koncie magazynu klastra. Ta sekcja zawiera informacje na temat sposobu pobierania dzienników przy użyciu obu tych opcji.

### <a name="apache-ambari-web-ui"></a>Apache Ambari interfejs użytkownika sieci Web

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Na pasku w górnej części strony wybierz pozycję **ops.** Lista wyświetla bieżące i poprzednie operacje wykonywane w klastrze za pośrednictwem ambari.

    ![Pasek interfejsu użytkownika w sieci Ambari z wybraną pozy](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Znajdź wpisy, które **zostały uruchomione\_w** kolumnie **Operacje.** Te wpisy są tworzone po uruchomieniu akcji skryptu.

    ![Operacje akcji skryptu Apache Ambari](./media/troubleshoot-script-action/ambari-script-action.png)

    Aby wyświetlić dane wyjściowe **STDOUT** i **STDERR,** wybierz wpis **run\customscriptaction** i przejdź do szczegółów łącza. To dane wyjściowe są generowane, gdy skrypt jest uruchamiany i może mieć przydatne informacje.

### <a name="default-storage-account"></a>Domyślne konto magazynu

Jeśli utworzenie klastra nie powiedzie się z powodu błędu skryptu, dzienniki są przechowywane na koncie magazynu klastra.

* Dzienniki pamięci masowej `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`są dostępne pod adresem .

    ![Dzienniki akcji skryptu](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    W tym katalogu dzienniki są zorganizowane oddzielnie dla **węzła headnode,** **węzła roboczego**i **węzła zookeeper**. Zobacz poniższe przykłady:

    * **Główka**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Węzeł pracownika**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Węzeł zookeeper**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Wszystkie **stdout** i **stderr** odpowiedniego hosta są przesyłane na konto magazynu. Istnieje jeden **wynik\*wyjściowy- .txt** i **błędy-\*.txt** dla każdej akcji skryptu. Plik **output*.txt** zawiera informacje o identyfikatorze URI skryptu, który został uruchomiony na hoście. Przykładem tych informacji jest następujący tekst:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Możliwe, że wielokrotnie tworzysz klaster akcji skryptu o tej samej nazwie. W takim przypadku można odróżnić odpowiednie dzienniki na podstawie nazwy folderu **DATE.** Na przykład struktura folderów dla **klastra, mycluster**, utworzony w różnych dniach, jest podobna do następujących wpisów dziennika:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Jeśli tego samego dnia utworzysz klaster akcji skryptu o tej samej nazwie, możesz użyć unikatowego prefiksu do zidentyfikowania odpowiednich plików dziennika.

* Jeśli utworzysz klaster w pobliżu 12:00, północy, możliwe, że pliki dziennika obejmują dwa dni. W takim przypadku zobaczysz dwa różne foldery daty dla tego samego klastra.

* Przekazywanie plików dziennika do kontenera domyślnego może potrwać do pięciu minut, szczególnie w przypadku dużych klastrów. Więc jeśli chcesz uzyskać dostęp do dzienników, nie należy natychmiast usunąć klastra, jeśli akcja skryptu nie powiedzie się.

## <a name="ambari-watchdog"></a>Ambari watchdog

Nie zmieniaj hasła do strażnika Ambari, hdinsightwatchdog, w klastrze HDInsight opartym na systemie Linux. Zmiana hasła przerywa możliwość uruchamiania nowych akcji skryptu w klastrze HDInsight.

## <a name="cant-import-name-blobservice"></a>Nie można zaimportować nazwy BlobService

__Objawy__. Akcja skryptu kończy się niepowodzeniem. Tekst podobny do następującego błędu jest wyświetlany podczas wyświetlania operacji w Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Przyczyna__. Ten błąd występuje w przypadku uaktualnienia klienta usługi Azure Storage języka Python dołączonego do klastra HDInsight. Usługa HDInsight oczekuje klienta usługi Azure Storage 0.20.0.

__Rozdzielczość__. Aby rozwiązać ten problem, należy ręcznie połączyć się z każdym węzłem klastra za pomocą programu `ssh`. Uruchom następujące polecenie, aby ponownie zainstalować poprawną wersję klienta magazynu:

```bash
sudo pip install azure-storage==0.20.0
```

Aby uzyskać informacje na temat łączenia się z klastrem za pomocą funkcji SSH, zobacz Łączenie się z [programem HDInsight (Apache Hadoop) przy użyciu funkcji SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historia nie pokazuje skryptów używanych podczas tworzenia klastra

Jeśli klaster został utworzony przed 15 marca 2016 r., może nie być widoczny wpis w historii akcji skryptu. Zmiana rozmiaru klastra powoduje, że skrypty pojawiają się w historii akcji skryptu.

Istnieją dwa wyjątki:

* Klaster został utworzony przed 1 września 2015 r. Ta data jest, gdy akcje skryptu zostały wprowadzone. Żaden klaster utworzony przed tą datą nie mógł używać akcji skryptu do tworzenia klastra.

* Podczas tworzenia klastra użyto wielu akcji skryptu. Lub użyto tej samej nazwy dla wielu skryptów lub tej samej nazwy, tego samego identyfikatora URI, ale różne parametry dla wielu skryptów. W takich przypadkach pojawia się następujący błąd:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)