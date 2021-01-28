---
title: Migrowanie klastra HBase do nowej wersji — usługa Azure HDInsight
description: Jak przeprowadzić migrację klastrów Apache HBase do nowszej wersji w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 24a0c09ba78c668dab017ec80adda19f59d89a4f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942975"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrowanie klastra Apache HBase do nowej wersji

W tym artykule omówiono kroki wymagane do zaktualizowania klastra Apache HBase w usłudze Azure HDInsight do nowszej wersji.

Czas przestoju podczas uaktualniania powinien być minimalny w kolejności minut. Ten przestój jest spowodowany przez kroki, aby opróżnić wszystkie dane w pamięci, a następnie skonfigurować i ponownie uruchomić usługi w nowym klastrze. Wyniki będą się różnić w zależności od liczby węzłów, ilości danych i innych zmiennych.

## <a name="review-apache-hbase-compatibility"></a>Przegląd zgodności z Apache HBase

Przed uaktualnieniem oprogramowania Apache HBase upewnij się, że wersje HBase w klastrach źródłowym i docelowym są zgodne. Aby uzyskać więcej informacji, zobacz [Apache Hadoop składniki i wersje dostępne w usłudze HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Zdecydowanie zalecamy zapoznanie się z macierzą zgodności wersji w [książce HBase](https://hbase.apache.org/book.html#upgrading). Wszelkie niezgodności należy opisać w informacjach o wersji HBase.

Poniżej znajduje się przykładowa wersja macierzy zgodności. Wartość Y oznacza zgodność i N wskazuje potencjalną niezgodność:

| Typ zgodności | Wersja główna| Wersja pomocnicza | Patch |
| --- | --- | --- | --- |
| Zgodność sieci Client-Server | N | Y | Y |
| Zgodność Server-Server | N | Y | Y |
| Zgodność formatu pliku | N | Y | Y |
| Zgodność z interfejsem API klienta | N | Y | Y |
| Zgodność binarna klienta | N | N | Y |
| **Ograniczona zgodność interfejsu API po stronie serwera** |  |  |  |
| Stable | N | Y | Y |
| Ewoluuje | N | N | Y |
| Stanie | N | N | N |
| Zgodność zależności | N | Y | Y |
| Zgodność operacyjna | N | N | Y |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Uaktualnij z tą samą wersją główną oprogramowania Apache HBase

Aby uaktualnić klaster Apache HBase w usłudze Azure HDInsight, wykonaj następujące czynności:

1. Upewnij się, że aplikacja jest zgodna z nową wersją, jak pokazano w macierzy zgodności HBase i informacje o wersji. Przetestuj swoją aplikację w klastrze z uruchomioną docelową wersją usługi HDInsight i HBase.

1. [Skonfiguruj nowy docelowy klaster usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) przy użyciu tego samego konta magazynu, ale z inną nazwą kontenera:

   ![Użyj tego samego konta magazynu, ale Utwórz inny kontener](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Opróżniaj źródłowy klaster HBase, który jest uaktualnianym klastrem. HBase zapisuje dane przychodzące do magazynu w pamięci o nazwie _magazynu_. Gdy magazynu osiągnie określony rozmiar, HBase opróżnia go na dysk w celu długoterminowego przechowywania na koncie magazynu klastra. Podczas usuwania starego klastra memstores są odtwarzane, potencjalnie tracące dane. Aby ręcznie opróżnić magazynu dla każdej tabeli na dysk, uruchom następujący skrypt. Najnowsza wersja tego skryptu znajduje się w witrynie [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)platformy Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Zatrzymaj pozyskiwanie w starym klastrze HBase.

1. Aby upewnić się, że wszystkie ostatnie dane w magazynu są opróżniane, ponownie uruchom poprzedni skrypt.

1. Zaloguj się do programu [Apache Ambari](https://ambari.apache.org/) w starym klastrze ( `https://OLDCLUSTERNAME.azurehdidnsight.net` ) i Zatrzymaj usługi HBase Services. Po wyświetleniu monitu o potwierdzenie, że chcesz zatrzymać usługi, zaznacz to pole wyboru, aby włączyć tryb konserwacji dla HBase. Aby uzyskać więcej informacji na temat nawiązywania połączenia z usługą Ambari i korzystania z niej, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![W Ambari kliknij pozycję usługi > HBase > Zatrzymaj w obszarze Akcje usługi](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Zaznacz pole wyboru Włącz tryb konserwacji dla HBase, a następnie potwierdź](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Jeśli nie korzystasz z klastrów HBase z funkcją ulepszone zapisy, Pomiń ten krok. Jest to konieczne tylko w przypadku klastrów HBase z ulepszoną funkcją zapisywania.

   Utwórz kopię zapasową katalogu WAL w systemie HDFS, uruchamiając poniższe polecenia z sesji SSH na dowolnym węźle dozorcy lub węzłach procesu roboczego oryginalnego klastra.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup**
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup**
   ```
    
1. Zaloguj się do usługi Ambari w nowym klastrze usługi HDInsight. Zmień `fs.defaultFS` ustawienie systemu HDFS, aby wskazywała nazwę kontenera używanego przez oryginalny klaster. To ustawienie znajduje się w obszarze **HDFS > konfiguracjami > zaawansowanej > zaawansowanej lokacji głównej**.

   ![W Ambari kliknij pozycję usługi > HDFS > konfiguracje > zaawansowane](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

   ![W Ambari Zmień nazwę kontenera](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Jeśli nie korzystasz z klastrów HBase z funkcją ulepszone zapisy, Pomiń ten krok. Jest to konieczne tylko w przypadku klastrów HBase z ulepszoną funkcją zapisywania.

   Zmień `hbase.rootdir` ścieżkę, aby wskazywała kontener oryginalnego klastra.

   ![W Ambari Zmień nazwę kontenera dla HBase ROOTDIR](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
    
1. Jeśli nie korzystasz z klastrów HBase z funkcją ulepszone zapisy, Pomiń ten krok. Jest to konieczne tylko w przypadku klastrów HBase z ulepszonymi funkcjami zapisywania i tylko w przypadkach, gdy oryginalny klaster był klastrem HBase z ulepszoną funkcją zapisywania.

   Wyczyść dane dozorcy i WAL FS dla tego nowego klastra. Wydaj następujące polecenia w dowolnym węźle dozorcy lub w węzłach procesu roboczego:

   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit

   hdfs dfs -rm -r hdfs://mycluster/hbasewal**
   ```

1. Jeśli nie korzystasz z klastrów HBase z funkcją ulepszone zapisy, Pomiń ten krok. Jest to konieczne tylko w przypadku klastrów HBase z ulepszoną funkcją zapisywania.
   
   Przywróć wartość WAL dir do nowego klastra HDFS z sesji SSH na dowolnym węźle dozorcy lub w węzłach procesu roboczego nowego klastra.
   
   ```bash
   hdfs dfs -cp /hbase-wal-backup/hbasewal hdfs://mycluster/**
   ```
   
1. Jeśli uaktualniasz usługi HDInsight 3,6 do 4,0, wykonaj poniższe kroki, w przeciwnym razie przejdź do kroku 13:

    1. Uruchom ponownie wszystkie wymagane usługi w programie Ambari, wybierając pozycję **usługi**  >  **Uruchom ponownie wszystkie wymagane**.
    1. Zatrzymaj usługę HBase.
    1. Użyj protokołu SSH do węzła dozorcy i wykonaj polecenie [zkCli](https://github.com/go-zkcli/zkcli) , `rmr /hbase-unsecure` Aby usunąć HBase główny znode z dozorcy.
    1. Uruchom ponownie HBase.

1. Jeśli uaktualniasz do innej wersji usługi HDInsight niż 4,0, wykonaj następujące kroki:
    1. Zapisz zmiany.
    1. Uruchom ponownie wszystkie wymagane usługi zgodnie z Ambari.

1. Wskaż aplikację nowym klastrem.

    > [!NOTE]  
    > Statyczny serwer DNS dla aplikacji zmienia się podczas uaktualniania. Zamiast napisania kodu w systemie DNS można skonfigurować rekord CNAME w ustawieniach DNS nazwy domeny, który wskazuje na nazwę klastra. Innym rozwiązaniem jest użycie pliku konfiguracji dla aplikacji, który można zaktualizować bez ponownego wdrożenia.

1. Rozpocznij pozyskiwanie, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.

1. Jeśli nowy klaster jest zadowalający, Usuń oryginalny klaster.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o platformie [Apache HBase](https://hbase.apache.org/) i uaktualnianiu klastrów usługi HDInsight, zobacz następujące artykuły:

* [Uaktualnianie klastra usługi HDInsight do nowszej wersji](../hdinsight-upgrade-cluster.md)
* [Monitorowanie usługi Azure HDInsight i zarządzanie nią za pomocą interfejsu użytkownika sieci Web Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop składniki i wersje](../hdinsight-component-versioning.md)
* [Optymalizowanie usługi Apache HBase](../optimize-hbase-ambari.md)
