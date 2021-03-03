---
title: Migrowanie domyślnych magazyn metadanych Hive do zewnętrznego magazynu metadanych w usłudze Azure HDInsight
description: Migrowanie domyślnych magazyn metadanych Hive do zewnętrznego magazynu metadanych w usłudze Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 825204fe40125a65e8e6f27c6973417813700a9e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745348"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Migruj domyślną bazę danych magazyn metadanych Hive DB do zewnętrznej bazy danych magazynu metadanych

W tym artykule pokazano, jak migrować metadane z [domyślnej bazy danych magazynu](../hdinsight-use-external-metadata-stores.md#default-metastore) metadanych dla programu Hive do zewnętrznego SQL Database w usłudze HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Dlaczego należy przeprowadzić migrację do zewnętrznej bazy danych magazynu metadanych

* Domyślna baza danych magazynu metadanych jest ograniczona do podstawowej jednostki SKU i nie może obsługiwać obciążeń skalowania produkcyjnego.

* Zewnętrzna baza danych magazynu metadanych umożliwia klientowi skalowanie zasobów obliczeniowych Hive w poziomie przez dodanie nowych klastrów usługi HDInsight, które współużytkują tę samą bazę danych magazynu metadanych.

* W przypadku migracji do programu HDInsight 3,6 do 4,0 należy przeprowadzić migrację metadanych do zewnętrznej bazy danych magazynu Metadata przed uaktualnieniem wersji schematu Hive. Zobacz [Migrowanie obciążeń z usługi hdinsight 3,6 do usługi hdinsight 4,0](./apache-hive-migrate-workloads.md).

Ze względu na to, że domyślna baza danych magazynu metadanych ma ograniczoną pojemność obliczeniową, zalecamy niskie użycie z innych zadań w klastrze podczas migrowania metadanych.

Baz danych źródłowy i docelowy muszą używać tej samej wersji usługi HDInsight i tych samych kont magazynu. Jeśli uaktualniasz wersje usługi HDInsight z 3,6 do 4,0, najpierw wykonaj kroki opisane w tym artykule. Następnie postępuj zgodnie z oficjalnymi [krokami](./apache-hive-migrate-workloads.md)uaktualniania.

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku używania [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)lokalizacje tabeli programu Hive mogą być zależne od konfiguracji systemu plików HDFS klastra dla Azure Data Lake Storage Gen1. Uruchom poniższą akcję skryptu, aby zapewnić, że te lokalizacje są przenośne do innych klastrów. Zobacz [Akcja skryptu w uruchomionym klastrze](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Akcja jest podobna do zastępowania linków symbolicznych z ich pełną ścieżką.

|Właściwość | Wartość |
|---|---|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Typy węzłów|Head|
|Parametry|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migruj z eksportem/importem przy użyciu sqlpackage

Klaster HDInsight An utworzony dopiero po 2020-10-15 obsługuje eksport/import języka SQL dla domyślnej bazy danych magazynu metadanych Hive przy użyciu programu `sqlpackage` .

1. Zainstaluj [pakiet sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) w klastrze.

2. Wyeksportuj domyślną bazę danych magazynu metadanych do pliku BACPAC, wykonując następujące polecenie.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Zapisz plik BACPAC. Poniżej znajduje się opcja.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Zaimportuj plik BACPAC do nowej bazy danych z krokami wymienionymi [tutaj](../../azure-sql/database/database-import.md).

5. Nowa baza danych jest gotowa do [skonfigurowania jako zewnętrzna baza danych magazynu metadanych w nowym klastrze usługi HDInsight](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migrowanie przy użyciu skryptu Hive

Klastry utworzone przed 2020-10-15 nie obsługują eksportowania/importowania domyślnej bazy danych magazynu metadanych.

W przypadku takich klastrów postępuj zgodnie z przewodnikiem [Kopiuj tabele Hive między kontami magazynu](./hive-migration-across-storage-accounts.md), używając drugiego klastra z [zewnętrzną bazą danych magazyn metadanych Hive](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Drugi klaster może korzystać z tego samego konta magazynu, ale musi korzystać z nowego domyślnego systemu plików.

### <a name="option-to-shallow-copy"></a>Opcja kopiowania na płytki
Użycie magazynu będzie podwójne, gdy tabele są "głębokie" skopiowane za pomocą powyższego przewodnika. Należy ręcznie wyczyścić dane w źródłowym kontenerze magazynu.
Zamiast tego, "płytki" kopiuje tabele, jeśli są one nietransakcyjne. Wszystkie tabele Hive w usłudze HDInsight 3,6 są domyślnie nietransakcyjne, ale tylko tabele zewnętrzne nie są transakcyjne w usłudze HDInsight 4,0. Tabele transakcyjne muszą być głęboko skopiowane. Wykonaj następujące kroki, aby uzyskać skróconą kopię tabel nietransakcyjnych:

1. Wykonaj polecenie Script [Hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) na podstawowym węzła głównego klastra źródłowego, aby wygenerować kod DDL dla każdej tabeli programu Hive.
2. KOD DDL jest zapisywana w lokalnym skrypcie Hive o nazwie `/tmp/hdi_hive_ddls.hql` . Wykonaj to w klastrze docelowym, który używa zewnętrznej bazy danych magazyn metadanych Hive.

## <a name="verify-that-all-hive-tables-are-imported"></a>Sprawdź, czy wszystkie tabele Hive zostały zaimportowane

Następujące polecenie używa zapytania SQL w bazie danych magazynu metadanych do drukowania wszystkich tabel programu Hive i ich lokalizacji danych. Porównaj dane wyjściowe między nowym i starym klastrem, aby sprawdzić, czy w nowej bazie danych magazynu metadanych brakuje żadnych tabel.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Dodatkowe informacje

* [Migrowanie obciążeń z usługi HDInsight 3,6 do 4,0](./apache-hive-migrate-workloads.md)
* [Migracja obciążenia Hive między kontami magazynu](./hive-migration-across-storage-accounts.md)
* [Nawiązywanie połączenia z usługą Z usługi Beeline w usłudze HDInsight](../hadoop/connect-install-beeline.md)
* [Rozwiązywanie problemów z uprawnieniami do tworzenia tabeli](./interactive-query-troubleshoot-permission-error-create-table.md)
