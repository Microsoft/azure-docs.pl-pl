---
title: Migracja obciążenia Hive do nowego konta w usłudze Azure Storage
description: Migracja obciążenia Hive do nowego konta w usłudze Azure Storage
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747227"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migracja obciążenia Hive do nowego konta w usłudze Azure Storage

Dowiedz się, jak używać akcji skryptu do kopiowania tabel programu Hive między kontami magazynu w usłudze HDInsight. Może to być przydatne w przypadku migrowania do programu [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Aby ręcznie skopiować pojedynczą tabelę programu Hive w usłudze HDInsight 4,0, zobacz [Eksportowanie/Importowanie Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Wymagania wstępne

* Nowy klaster usługi HDInsight z następującymi konfiguracjami:
  * Jego domyślny system plików znajduje się na docelowym koncie magazynu. Zobacz [Korzystanie z usługi Azure Storage z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md).
  * Jego wersja klastra musi być zgodna z klastrem źródłowym.
  * Używa ona nowej zewnętrznej bazy danych magazyn metadanych Hive. Zobacz [Używanie zewnętrznych magazynów metadanych](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Konto magazynu, które jest dostępne zarówno dla oryginalnego, jak i nowego klastra. Zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md) i [typów magazynu oraz funkcji](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) dla dozwolonych typów magazynu pomocniczego.

    Oto kilka opcji:
  * Dodaj docelowe konto magazynu do oryginalnego klastra.
  * Dodaj oryginalne konto magazynu do nowego klastra.
  * Dodaj konto magazynu pośredniczącego zarówno do oryginalnego, jak i nowego klastra.

## <a name="how-it-works"></a>Jak to działa

Zostanie uruchomiona akcja skryptu w celu wyeksportowania tabel programu Hive z oryginalnego klastra do określonego katalogu HDFS. Zobacz [Akcja skryptu w uruchomionym klastrze](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Następnie w nowym klastrze zostanie uruchomiona inna akcja skryptu w celu zaimportowania tabel programu Hive z katalogu HDFS.

Skrypt utworzy ponownie tabele do domyślnego systemu plików nowego klastra. Tabele natywne również skopiują swoje dane do magazynu. Tabele NIENATYWNE zostaną skopiowane tylko przez definicję. Aby uzyskać szczegółowe informacje o tabelach nienatywnych, zobacz [programy obsługi magazynu Hive](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) .

Ścieżka tabel zewnętrznych spoza katalogu magazynu Hive zostanie zachowana. Inne tabele zostaną skopiowane do domyślnej ścieżki Hive klastra docelowego. Zobacz właściwości Hive `hive.metastore.warehouse.external.dir` i `hive.metastore.warehouse.dir` .

Skrypty nie będą zachować niestandardowych uprawnień do plików w klastrze docelowym.

> [!NOTE]
>
> Ten przewodnik obsługuje kopiowanie obiektów metadanych związanych z bazami danych programu Hive, tabelami i partycjami. Inne obiekty metadanych muszą zostać utworzone ręcznie.
>
> * W przypadku programu `Views` Hive obsługuje `SHOW VIEWS` polecenie w ramach programu Hive 2.2.0 w usłudze HDInsight 4,0. Służy `SHOW CREATE TABLE` do wyświetlania definicji. W przypadku wcześniejszych wersji programu Hive wykonaj zapytanie do bazy danych SQL magazynu metadanych, aby wyświetlić widoki.
> * Dla `Materialized Views` , Użyj poleceń `SHOW MATERIALIZED VIEWS` , `DESCRIBE FORMATTED` i `CREATE MATERIALIZED VIEW` . Szczegóły można znaleźć w [widokach z materiałami](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) .
> * W przypadku programu `Constraints` , obsługiwanego w ramach platformy Hive 2.1.0 w usłudze HDInsight 4,0, użyj `DESCRIBE EXTENDED` do listy ograniczeń dla tabeli i Użyj, `ALTER TABLE` Aby dodać ograniczenia. Szczegóły można znaleźć w temacie [ALTER TABLE Constraints](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) .

## <a name="copy-hive-tables"></a>Kopiuj tabele Hive

1. Zastosuj akcję skryptu "Eksportuj" w oryginalnym klastrze z następującymi polami.

    Spowoduje to wygenerowanie i wykonanie pośrednich skryptów Hive. Zostaną one zapisane w określonym `<hdfs-export-path>` .

    Opcjonalnie można użyć, `--run-script=false` Aby dostosować je przed ręcznym wykonaniem.

    |Właściwość | Wartość |
    |---|---|
    |Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Typy węzłów|Head|
    |Parametry|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Po pomyślnym zakończeniu eksportu Zastosuj akcję skryptu "Importuj" w nowym klastrze z następującymi polami.

    |Właściwość | Wartość |
    |---|---|
    |Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Typy węzłów|Head|
    |Parametry|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Weryfikacja

Pobierz i uruchom skrypt jako użytkownik główny [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) w węźle głównym każdego klastra i Porównaj zawartość pliku wyjściowego `/tmp/hive_contents.out` . Zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>Wyczyść dodatkowe użycie magazynu

Po zakończeniu migracji i zweryfikowaniu magazynu można usunąć dane z określonej ścieżki eksportu systemu plików HDFS.

Opcja to użycie systemu plików HDFS `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Opcja: Zmniejsz użycie magazynu

Akcja eksportowania skryptu prawdopodobnie podwaja użycie magazynu ze względu na gałąź Hive. Istnieje jednak możliwość ograniczenia dodatkowego użycia magazynu przez Migrowanie ręcznie, jednej bazy danych lub tabeli w danym momencie.

1. Określ, `--run-script=false` Aby pominąć wykonywanie wygenerowanego skryptu programu Hive. Skrypty eksportu i importu Hive zostaną nadal zapisane w ścieżce eksportu.

2. Wykonaj fragmenty kodu z bazy danych "Eksportowanie i importowanie" skryptów Hive w bazie danych lub tabeli po tabeli, ręcznie czyszcząc ścieżkę eksportu po każdej zmigrowanej bazie danych lub tabeli.

## <a name="next-steps"></a>Następne kroki

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Korzystanie z zewnętrznych magazynów metadanych](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Typy i funkcje magazynu](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Akcja skryptu w uruchomionym klastrze](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
