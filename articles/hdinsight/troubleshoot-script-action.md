---
title: Rozwiązywanie problemów z akcjami skryptu w usłudze Azure HDInsight
description: Ogólne kroki rozwiązywania problemów dotyczących akcji skryptów w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 73b958964db2d0b308dd6dfc34024d61ce5ad8af
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871439"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Rozwiązywanie problemów z akcjami skryptu w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

Za pomocą interfejsu użytkownika sieci Web Apache Ambari można wyświetlać informacje zarejestrowane przez akcje skryptu. Jeśli skrypt zakończy się niepowodzeniem podczas tworzenia klastra, dzienniki znajdują się na domyślnym koncie magazynu klastra. Ta sekcja zawiera informacje dotyczące sposobu pobierania dzienników przy użyciu obu tych opcji.

### <a name="apache-ambari-web-ui"></a>Interfejs użytkownika sieci Web Apache Ambari

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Na pasku w górnej części strony wybierz wpis **Ops** . Na liście są wyświetlane bieżące i poprzednie operacje wykonywane w klastrze za pomocą Ambari.

    :::image type="content" source="./media/troubleshoot-script-action/hdi-apache-ambari-nav.png" alt-text="Pasek interfejsu użytkownika sieci Web Ambari z wybranym elementem Ops" border="true":::

1. Znajdź w kolumnie **operacje** wpisy z **uruchomioną \_ customscriptactioną** . Te wpisy są tworzone po uruchomieniu akcji skryptu.

    :::image type="content" source="./media/troubleshoot-script-action/ambari-script-action.png" alt-text="Operacje akcji skryptu Apache Ambari" border="true":::

    Aby wyświetlić dane wyjściowe **stdout** i **stderr** , wybierz wpis **run\customscriptaction** i przejdź do szczegółów przez linki. Ta wartość wyjściowa jest generowana, gdy skrypt jest uruchamiany i może mieć przydatne informacje.

### <a name="default-storage-account"></a>Domyślne konto magazynu

Jeśli utworzenie klastra zakończy się niepowodzeniem z powodu błędu skryptu, dzienniki są przechowywane na koncie magazynu klastra.

* Dzienniki magazynu są dostępne pod adresem `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` .

    :::image type="content" source="./media/troubleshoot-script-action/script-action-logs-in-storage.png" alt-text="Dzienniki akcji skryptu" border="true":::

    W tym katalogu dzienniki są zorganizowane osobno dla węzła **węzła głównego**, **Node-Worker** i **dozorcy**. Zobacz poniższe przykłady:

    * **Węzła głównego**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Węzeł procesu roboczego**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Węzeł dozorcy**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Wszystkie **stdout** i **stderr** odpowiedniego hosta są przekazywane do konta magazynu. Istnieje jedno **wyjście- \* . txt** i **Błędy- \* . txt** dla każdej akcji skryptu. Plik **Output-*. txt** zawiera informacje o identyfikatorze URI skryptu, który został uruchomiony na hoście. Poniżej przedstawiono przykład tych informacji:

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* Istnieje możliwość wielokrotnego utworzenia klastra akcji skryptu o tej samej nazwie. W takim przypadku można odróżnić odpowiednie dzienniki na podstawie nazwy folderu **daty** . Na przykład struktura folderów dla klastra, obiekt **webcluster**, utworzony w różnych datach, wygląda podobnie jak w przypadku następujących wpisów dziennika:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Jeśli utworzysz klaster akcji skryptu o tej samej nazwie w tym samym dniu, możesz użyć unikatowego prefiksu, aby zidentyfikować odpowiednie pliki dziennika.

* Jeśli utworzysz klaster o 12:00 AM i północy, istnieje możliwość, że pliki dziennika obejmują dwa dni. W takim przypadku zobaczysz dwa różne foldery dat dla tego samego klastra.

* Przekazywanie plików dziennika do kontenera domyślnego może potrwać do 5 minut, szczególnie w przypadku dużych klastrów. Dlatego jeśli chcesz uzyskać dostęp do dzienników, nie musisz natychmiast usunąć klastra, jeśli akcja skryptu zakończy się niepowodzeniem.

## <a name="ambari-watchdog"></a>Ambari = licznik

Nie zmieniaj hasła dla Ambari licznika alarmowego hdinsightwatchdog, w klastrze usługi HDInsight opartej na systemie Linux. Zmiana hasła przerywa możliwość uruchamiania nowych akcji skryptów w klastrze usługi HDInsight.

## <a name="cant-import-name-blobservice"></a>Nie można zaimportować nazwy BlobService

__Objawy__. Akcja skryptu kończy się niepowodzeniem. Podczas wyświetlania operacji w Ambari jest wyświetlany tekst podobny do następującego błędu:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Przyczyna__. Ten błąd występuje w przypadku uaktualniania klienta usługi Azure Storage w języku Python, który jest dołączony do klastra HDInsight. Usługa HDInsight oczekuje 0.20.0 klienta usługi Azure Storage.

__Rozwiązanie__. Aby rozwiązać ten problem, należy ręcznie połączyć się z każdym węzłem klastra przy użyciu polecenia `ssh` . Uruchom następujące polecenie, aby ponownie zainstalować poprawną wersję klienta magazynu:

```bash
sudo pip install azure-storage==0.20.0
```

Aby uzyskać informacje na temat nawiązywania połączenia z klastrem przy użyciu protokołu SSH, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) za pośrednictwem protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>W historii nie są wyświetlane skrypty używane podczas tworzenia klastra

Jeśli klaster został utworzony przed 15 marca 2016, w historii akcji skryptu może nie być wyświetlany wpis. Zmiany rozmiarów klastra powodują, że skrypty pojawiają się w historii akcji skryptu.

Istnieją dwa wyjątki:

* Klaster został utworzony przed 1 września 2015. Ta data jest w przypadku wprowadzenia akcji skryptu. Każdy klaster utworzony przed tą datą nie mógł używać akcji skryptu do tworzenia klastra.

* Podczas tworzenia klastra użyto wielu akcji skryptu. Lub użyto tej samej nazwy dla wielu skryptów lub tej samej nazwy, tego samego identyfikatora URI, ale różnych parametrów dla wielu skryptów. W takich przypadkach zostanie wyświetlony następujący błąd:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]