---
title: Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0
description: Dowiedz się, jak migrować obciążenia Apache Hive w usłudze HDInsight 3,6 do usługi HDInsight 4,0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 43d616bc82c608918f5e7ee51481a393dd55a284
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566074"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0

Usługa HDInsight 4,0 ma kilka korzyści w stosunku do usługi HDInsight 3,6. Oto [Omówienie nowości w usłudze HDInsight 4,0](../hdinsight-version-release.md).

W tym artykule opisano kroki migracji obciążeń programu Hive z usługi HDInsight 3,6 do 4,0, w tym

* magazyn metadanych Hive kopiowanie i uaktualnianie schematu
* Bezpieczna migracja pod kątem zgodności z KWASem
* Zachowywanie zasad zabezpieczeń programu Hive

Nowe i stare klastry usługi HDInsight muszą mieć dostęp do tych samych kont magazynu.

Migracja tabel programu Hive do nowego konta magazynu musi odbywać się w osobnym kroku. Zobacz [migracja Hive między kontami magazynu](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia

### <a name="1-prepare-the-data"></a>1. Przygotuj dane

* Usługa HDInsight 3,6 domyślnie nie obsługuje tabel KWASów. Jeśli są obecne tabele KWASów, należy jednak uruchomić na nich kompaktowanie "główne". Szczegółowe informacje na temat kompaktowania można znaleźć w [podręczniku języka Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

* W przypadku używania [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)lokalizacje tabeli Hive mogą być zależne od konfiguracji systemu plików HDFS klastra. Uruchom poniższą akcję skryptu, aby zapewnić, że te lokalizacje są przenośne do innych klastrów. Zobacz [Akcja skryptu w uruchomionym klastrze](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Właściwość | Wartość |
    |---|---|
    |Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Typy węzłów|Head|
    |Parametry||

### <a name="2-copy-the-sql-database"></a>2. Kopiowanie bazy danych SQL

* Jeśli klaster używa domyślnego magazyn metadanych Hive, postępuj zgodnie z tym [przewodnikiem](./hive-default-metastore-export-import.md) , aby wyeksportować metadane do zewnętrznego magazynu metadanych. Następnie utwórz kopię zewnętrznego magazynu metadanych do uaktualnienia.

* Jeśli klaster używa zewnętrznego magazyn metadanych Hive, utwórz jego kopię. Opcje obejmują [eksport/import](../../azure-sql/database/database-export.md) oraz [przywracanie do punktu w czasie](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore).

### <a name="3-upgrade-the-metastore-schema"></a>3. Uaktualnij schemat magazynu metadanych

Ten krok powoduje użycie [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) z usługi HDInsight 4,0 do uaktualnienia schematu magazynu metadanych.

> [!Warning]
> Ten krok nie jest odwracalny. Uruchom to tylko na kopii magazynu metadanych.

1. Utwórz tymczasowy klaster HDInsight 4,0, aby uzyskać dostęp do gałęzi 4,0 `schematool` . Dla tego kroku możesz użyć [domyślnej magazyn metadanych Hive](../hdinsight-use-external-metadata-stores.md#default-metastore) .

1. W klastrze usługi HDInsight 4,0 wykonaj polecenie, `schematool` Aby uaktualnić docelowy magazyn metadanych usługi hdinsight 3,6:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > To narzędzie używa klienta `beeline` do wykonywania skryptów SQL w programie `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > Składnia SQL w tych skryptach nie musi być zgodna z innymi narzędziami klienckimi. Na przykład program [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) i [Edytor zapytań w witrynie Azure Portal](../../azure-sql/database/connect-query-portal.md) wymagają słowa kluczowego `GO` po każdym poleceniu.
    >
    > Jeśli dowolny skrypt zakończy się niepowodzeniem ze względu na wydajność zasobów lub limity czasu transakcji, Przeskaluj w górę SQL Database.

1. Sprawdź wersję ostateczną z zapytaniem `select schema_version from dbo.version` .

    Dane wyjściowe powinny być zgodne z następującym poleceniem bash z klastra HDInsight 4,0.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Usuń tymczasowy klaster HDInsight 4,0.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Wdróż nowy klaster usługi HDInsight 4,0

Utwórz nowy klaster usługi HDInsight 4,0, [wybierając uaktualnione magazyn metadanych Hive](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) i te same konta magazynu.

* Nowy klaster nie wymaga posiadania tego samego domyślnego systemu plików.

* Jeśli magazyn metadanych zawiera tabele znajdujące się na wielu kontach magazynu, należy dodać te konta magazynu do nowego klastra, aby uzyskać dostęp do tych tabel. Zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md).

* Jeśli zadania Hive nie powiodły się z powodu niedostępności magazynu, sprawdź, czy lokalizacja tabeli znajduje się na koncie magazynu dodanym do klastra.

    Użyj następującego polecenia Hive, aby zidentyfikować lokalizację tabeli:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. Konwertuj tabele pod kątem zgodności z KWASem

Tabele zarządzane muszą być zgodne ze standardem KWAŚNe w usłudze HDInsight 4,0. Uruchom `strictmanagedmigration` w usłudze HDInsight 4,0, aby skonwertować wszystkie tabele zarządzane NIEkwasowo do tabel zewnętrznych z właściwością `'external.table.purge'='true'` . Wykonaj z węzła głównego:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpieczanie usługi Hive w wersjach usługi HDInsight

Usługa HDInsight opcjonalnie integruje się z Azure Active Directory przy użyciu pakiet Enterprise Security usługi HDInsight (ESP). ESP używa protokołów Kerberos i Apache Ranger do zarządzania uprawnieniami określonych zasobów w klastrze. Zasady Ranger wdrożone w usłudze Hive w usłudze HDInsight 3,6 można migrować do usługi HDInsight 4,0, wykonując następujące czynności:

1. Przejdź do panelu Ranger Service Manager w klastrze usługi HDInsight 3,6.
2. Przejdź do zasad o nazwie **Hive** i wyeksportuj zasady do pliku JSON.
3. Upewnij się, że wszyscy użytkownicy określeni w wyeksportowanym pliku JSON zasad istnieją w nowym klastrze. Jeśli użytkownik jest określony w pliku JSON zasad, ale nie istnieje w nowym klastrze, należy dodać użytkownika do nowego klastra lub usunąć odwołanie z zasad.
4. Przejdź do panelu **Ranger Service Manager** w klastrze usługi HDInsight 4,0.
5. Przejdź do zasad o nazwie **Hive** i zaimportuj kod JSON zasad Ranger z kroku 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Zmiany w usłudze Hive w usłudze HDInsight 4,0, które mogą wymagać zmian aplikacji

* Zapoznaj się z [dodatkową konfiguracją przy użyciu łącznika magazynu Hive](./apache-hive-warehouse-connector.md) do udostępniania magazynu metadanych między platformami Spark i Hive w przypadku tabel kwasowych.

* Usługa HDInsight 4,0 korzysta z [autoryzacji na podstawie magazynu](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Jeśli modyfikujesz uprawnienia do pliku lub utworzysz foldery jako inny użytkownik niż Hive, prawdopodobnie wystąpią błędy programu Hive na podstawie uprawnień magazynu. Aby rozwiązać ten problem, udziel `rw-` dostępu użytkownikowi. Zobacz [Przewodnik po uprawnieniach](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)systemu plików HDFS.

* `HiveCLI` jest zastępowany przez `Beeline` .

Dodatkowe zmiany można znaleźć w [ogłoszeniu usługi HDInsight 4,0](../hdinsight-version-release.md) .

## <a name="further-reading"></a>Dodatkowe informacje

* [Anons usługi HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0 głębokie szczegółowe](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabele KWASów Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)