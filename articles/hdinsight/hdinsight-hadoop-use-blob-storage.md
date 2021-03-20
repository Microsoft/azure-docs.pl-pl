---
title: Wykonywanie zapytań dla danych z usługi Azure Storage zgodnej z systemem plików HDFS — Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania dotyczące danych z usługi Azure Storage i Azure Data Lake Storage, aby przechowywać wyniki analizy.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: cedc0ff1b3c2aa64f32445eabc800748a753981d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945427"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight

Dane można przechowywać w [usłudze Azure Blob Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)lub [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Lub kombinację tych opcji. Te opcje magazynu umożliwiają bezpieczne usuwanie klastrów usługi HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. Podczas procesu tworzenia klastra usługi HDInsight można określić kontener obiektów BLOB w usłudze Azure Storage jako domyślny system plików. Lub w usłudze HDInsight 3,6 można wybrać usługę Azure Blob Storage lub Azure Data Lake Storage Gen1/Azure Data Lake Storage Gen2 jako domyślny system plików z kilkoma wyjątkami. Aby uzyskać pomoc techniczną dotyczącą używania Data Lake Storage Gen1 jako domyślnego i połączonego magazynu, zobacz [dostępność klastra usługi HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters).

W tym artykule omówiono współdziałanie usługi Azure Storage z klastrami usługi HDInsight. 
* Aby dowiedzieć się, jak Data Lake Storage Gen1 współpracuje z klastrami usługi HDInsight, zobacz [Korzystanie z Azure Data Lake Storage Gen1 z klastrami Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen1.md).
* Aby dowiedzieć się, jak Data Lake Storage Gen2 współpracuje z klastrami usługi HDInsight, zobacz [Korzystanie z Azure Data Lake Storage Gen2 z klastrami Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen2.md).
* Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Rodzaj konta magazynu **BlobStorage** może być używany tylko jako magazyn pomocniczy dla klastrów usługi HDInsight.

| Rodzaj konta magazynu | Obsługiwane usługi | Obsługiwane warstwy wydajności |Nieobsługiwane warstwy wydajności| Obsługiwane warstwy dostępu |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (ogólnego przeznaczenia wersja 2)  | Obiekt blob     | Standardowa (Standard)                    |Premium| Gorąca, chłodna, archiwalna\*   |
| Magazyn (ogólnego przeznaczenia w wersji 1)   | Obiekt blob     | Standardowa (Standard)                    |Premium| Nie dotyczy                    |
| BlobStorage                    | Obiekt blob     | Standardowa (Standard)                    |Premium| Gorąca, chłodna, archiwalna\*   |

Nie zaleca się używania domyślnego kontenera obiektów BLOB do przechowywania danych firmowych. Dobrym rozwiązaniem jest usunięcie domyślnego kontenera obiektów blob po każdym użyciu, aby obniżyć koszty magazynowania. Kontener domyślny zawiera Dzienniki aplikacji i systemu. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie jednego kontenera obiektów BLOB jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

> [!NOTE]  
> Warstwa dostępu archiwalna jest warstwą offline, która ma kilka opóźnień pobierania i nie jest zalecana do użycia z usługą HDInsight. Aby uzyskać więcej informacji, zobacz [Archiwizowanie warstwy dostępu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w Data Lake Storage z klastra usługi HDInsight. Schemat URI zapewnia nieszyfrowany dostęp (z prefiksem *wasb:* ) i dostęp szyfrowany przy użyciu protokołu TLS (z *wasbs*). Zalecamy używanie prefiksu *wasbs* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, które znajdują się wewnątrz tego samego regionu w systemie Azure.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Przy użyciu skróconego formatu ścieżki**. To podejście zastępuje ścieżkę do katalogu głównego klastra przy użyciu:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Przykłady dostępu do danych

Przykłady są oparte na [połączeniu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) z węzłem głównym klastra. W przykładach użyto wszystkich trzech schematów URI. Zamień `CONTAINERNAME` i `STORAGEACCOUNT` z odpowiednimi wartościami

#### <a name="a-few-hdfs-commands"></a>Kilka poleceń systemu plików HDFS

1. Utwórz plik w magazynie lokalnym.

    ```bash
    touch testFile.txt
    ```

1. Utwórz katalogi w magazynie klastra.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Skopiuj dane z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Wyświetlanie zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Podczas pracy z obiektami blob poza usługą HDInsight, większość narzędzi nie rozpoznaje formatu WASB i zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Tworzenie tabeli programu Hive

Trzy lokalizacje plików są pokazane w celach ilustracyjnych. W przypadku rzeczywistego wykonywania należy użyć tylko jednego z `LOCATION` wpisów.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Dostęp do plików z klastra zewnętrznego

Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Witryna Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Zidentyfikuj ścieżkę magazynu z Ambari

* Aby zidentyfikować pełną ścieżkę do skonfigurowanego domyślnego magazynu, przejdź do:

    System plików **HDFS**  >  **Configs** i ENTER `fs.defaultFS` w polu wejściowym filtru.

* Aby sprawdzić, czy magazyn wasb jest skonfigurowany jako magazyn pomocniczy, przejdź do:

    System plików **HDFS**  >  **Configs** i ENTER `blob.core.windows.net` w polu wejściowym filtru.

Aby uzyskać ścieżkę przy użyciu interfejsu API REST Ambari, zobacz temat [pobieranie magazynu domyślnego](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Kontenery obiektów blob

Aby użyć obiektów blob, należy najpierw utworzyć [konto usługi Azure Storage](../storage/common/storage-account-create.md). W ramach tego kroku należy określić region świadczenia usługi Azure, w którym zostanie utworzone konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Baza danych magazyn metadanych Hive SQL Server Database i Apache Oozie baza danych SQL Server musi znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do kontenera na koncie usługi Azure Storage. Ten kontener może być istniejącym obiektem BLOB utworzonym poza usługą HDInsight. Lub może być kontenerem utworzonym dla klastra usługi HDInsight.

Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Nie należy współużytkować domyślnego kontenera obiektów blob dla wielu klastrów usługi HDInsight. Ta akcja może spowodować uszkodzenie historii zadań. Zalecane jest używanie innego kontenera dla każdego klastra. Dane udostępnione należy umieścić na połączonym koncie magazynu określonym dla wszystkich odpowiednich klastrów, a nie na domyślnym koncie magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz artykuł [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Jednak po usunięciu oryginalnego klastra usługi HDInsight można ponownie użyć domyślnego kontenera magazynu. W przypadku klastrów HBase można zachować schemat tabeli HBase i dane przez utworzenie nowego klastra HBase przy użyciu domyślnego kontenera obiektów blob, który jest używany przez usunięty klaster HBase

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Używanie dodatkowych kont magazynu

Podczas tworzenia klastra usługi HDInsight należy wskazać konto usługi Azure Storage, które ma zostać skojarzone z tym klastrem. Ponadto można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia lub po utworzeniu klastra. Aby uzyskać instrukcje dotyczące dodawania dodatkowych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z magazynu Azure zgodnego z systemem plików HDFS w połączeniu z usługą HDInsight. Ten magazyn umożliwia tworzenie dostosowywalnych, długoterminowych rozwiązań do pozyskiwania danych i korzystanie z usługi HDInsight w celu odblokowania informacji w przechowywanych strukturach i danych bez struktury.

Aby uzyskać więcej informacji, zobacz:

* [Szybki Start: Tworzenie klastra Apache Hadoop](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Samouczek: tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md) (Używanie sygnatur dostępu współdzielonego do usługi Azure Storage, aby ograniczyć dostęp do danych za pomocą usługi HDInsight)