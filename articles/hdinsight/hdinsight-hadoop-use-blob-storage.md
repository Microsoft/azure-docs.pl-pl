---
title: Wykonywanie zapytań dla danych z usługi Azure Storage zgodnej z systemem plików HDFS — Azure HDInsight
description: Dowiedz się, jak wysyłać zapytania do danych z usługi Azure Storage i usługi Azure Data Lake Storage w celu przechowywania wyników analizy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: cd6ba50cf81b93da887134e89d75313acb6bd936
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869861"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight

Dane można przechowywać w [usłudze Azure Storage](../storage/common/storage-introduction.md), [usłudze Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)lub [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Lub połączenie tych opcji. Te opcje magazynu umożliwiają bezpieczne usuwanie klastrów HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. Podczas procesu tworzenia klastra HDInsight można określić kontener obiektów blob w usłudze Azure Storage jako domyślny system plików. W usłudze HDInsight 3.6 można wybrać usługę Azure Storage lub Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 jako domyślny system plików z kilkoma wyjątkami. Aby uzyskać możliwość obsługi używania magazynu usługi Data Lake Storage Gen 1 jako magazynu domyślnego i połączonego, zobacz [Dostępność klastra HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

W tym artykule omówiono współdziałanie usługi Azure Storage z klastrami usługi HDInsight. Aby dowiedzieć się, jak usługa Data Lake Storage Gen 1 współpracuje z klastrami HDInsight, zobacz [Korzystanie z usługi Azure Data Lake Storage z klastrami usługi Azure HDInsight.](hdinsight-hadoop-use-data-lake-store.md) Aby uzyskać więcej informacji na temat tworzenia klastra HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w pliku HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Obiekt **BlobStorage** typu konta magazynu może być używany tylko jako magazyn pomocniczy dla klastrów HDInsight.

| Rodzaj konta magazynu | Obsługiwane usługi | Obsługiwane warstwy wydajności |Nie obsługiwane warstwy wydajności| Obsługiwane warstwy dostępu |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (ogólnego przeznaczenia wersja 2)  | Obiekt blob     | Standardowa                    |Premium| Gorący, Chłodny, Archiwum\*   |
| Magazyn (ogólnego przeznaczenia w wersji 1)   | Obiekt blob     | Standardowa                    |Premium| Nie dotyczy                    |
| BlobStorage (BlobStorage)                    | Obiekt blob     | Standardowa                    |Premium| Gorący, Chłodny, Archiwum\*   |

Nie zaleca się używania domyślnego kontenera obiektów blob do przechowywania danych biznesowych. Dobrym rozwiązaniem jest usunięcie domyślnego kontenera obiektów blob po każdym użyciu, aby obniżyć koszty magazynowania. Kontener domyślny zawiera dzienniki aplikacji i systemu. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie jednego kontenera obiektów blob jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

> [!NOTE]  
> Warstwa dostępu Archiwum to warstwa offline, która ma kilkugodzinne opóźnienie pobierania i nie jest zalecana do użytku z programem HDInsight. Aby uzyskać więcej informacji, zobacz [Archiwizowanie warstwy dostępu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Dostęp do plików z poziomu klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w magazynie usługi Data Lake z klastra HDInsight. Schemat URI zapewnia dostęp niezaszyfrowany (z prefiksem *wasb:* i dostępem szyfrowanym TLS (z *osami).* Zalecamy używanie prefiksu *wasbs* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, które znajdują się wewnątrz tego samego regionu w systemie Azure.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Przy użyciu skróconego formatu ścieżki**. Za pomocą tego podejścia można zastąpić ścieżkę do katalogu głównego klastra:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Przykłady dostępu do danych

Przykłady są oparte na [połączeniu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) z węzłem głównym klastra. W przykładach użyto wszystkich trzech schematów URI. Zastąp `CONTAINERNAME` i `STORAGEACCOUNT` odpowiednie wartości

#### <a name="a-few-hdfs-commands"></a>Kilka poleceń hdfs

1. Tworzenie pliku w magazynie lokalnym.

    ```bash
    touch testFile.txt
    ```

1. Tworzenie katalogów w magazynie klastra.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiowanie danych z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Podczas pracy z obiektami blob poza usługą HDInsight, większość narzędzi nie rozpoznaje formatu WASB i zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Tworzenie tabeli gałęzi

Trzy lokalizacje plików są wyświetlane w celach ilustracyjnych. W przypadku rzeczywistego wykonania należy `LOCATION` użyć tylko jednego z wpisów.

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
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy (Polski)](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identyfikowanie ścieżki pamięci masowej z ambari

* Aby zidentyfikować pełną ścieżkę do skonfigurowanych magazynu domyślnego, przejdź do:

    **Konfiguruje się HDFS** > **Configs** i wprowadź `fs.defaultFS` w polu wprowadzania filtru.

* Aby sprawdzić, czy magazyn wasb jest skonfigurowany jako magazyn pomocniczy, przejdź do:

    **Konfiguruje się HDFS** > **Configs** i wprowadź `blob.core.windows.net` w polu wprowadzania filtru.

Aby uzyskać ścieżkę przy użyciu interfejsu API Ambari REST, zobacz [Uzyskiwanie domyślnego magazynu](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Kontenery obiektów blob

Aby użyć obiektów blob, należy najpierw utworzyć [konto usługi Azure Storage](../storage/common/storage-create-storage-account.md). W ramach tego kroku należy określić region platformy Azure, w którym jest tworzone konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Baza danych programu SQL Server w magazynie hive i baza danych apache Oozie metastore SQL Server muszą znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do kontenera na koncie usługi Azure Storage. Ten kontener może być istniejącym obiektem blob utworzonym poza programem HDInsight. Lub może to być kontener, który jest tworzony dla klastra HDInsight.

Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Nie należy współużytkować domyślnego kontenera obiektów blob dla wielu klastrów usługi HDInsight. Ta akcja może uszkodzić historię zadań. Zaleca się użycie innego kontenera dla każdego klastra. Umieść udostępnione dane na połączonym koncie magazynu określonym dla wszystkich odpowiednich klastrów, a nie domyślnego konta magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz artykuł [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Jednak po usunięciu oryginalnego klastra usługi HDInsight można ponownie użyć domyślnego kontenera magazynu. W przypadku klastrów HBase można faktycznie zachować schemat tabeli HBase i dane, tworząc nowy klaster HBase przy użyciu domyślnego kontenera obiektów blob, który jest używany przez usunięty klaster HBase

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Używanie dodatkowych kont magazynu

Podczas tworzenia klastra usługi HDInsight należy wskazać konto usługi Azure Storage, które ma zostać skojarzone z tym klastrem. Ponadto można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia. Lub po utworzeniu klastra. Aby uzyskać instrukcje dotyczące dodawania dodatkowych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z magazynu Azure zgodnego z systemem plików HDFS w połączeniu z usługą HDInsight. Ta pamięć masowa umożliwia tworzenie elastycznych, długoterminowych, archiwizujących rozwiązań do pozyskiwania danych i wykorzystanie funkcji HDInsight do odblokowywania informacji wewnątrz przechowywanych danych strukturalnych i nieustrukturyzowanych.

Aby uzyskać więcej informacji, zobacz:

* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Wprowadzenie do usługi Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md) (Używanie sygnatur dostępu współdzielonego do usługi Azure Storage, aby ograniczyć dostęp do danych za pomocą usługi HDInsight)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interaktywnej kwerendy w usłudze Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
