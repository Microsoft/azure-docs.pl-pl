---
title: Przekazywanie danych dla zadań Apache Hadoop w umacie HDInsight
description: Dowiedz się, jak przekazywać dane i uzyskać do nich dostęp w przypadku zadań Apache Hadoop w umacie HDInsight. Użyj klasycznego interfejsu wiersza polecenia platformy Azure, Eksplorator usługi Azure Storage, Azure PowerShell, wiersza polecenia usługi Hadoop lub narzędzia Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020, devx-track-azurecli
ms.date: 04/27/2020
ms.openlocfilehash: d58d194e8dbf011eec949602e4f8cd2e084a0d98
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482113"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Przekazywanie danych dla zadań Apache Hadoop w umacie HDInsight

Usługa HDInsight udostępnia rozproszony system plików Hadoop (HDFS) za pośrednictwem usługi Azure Storage i Azure Data Lake Storage. Ten magazyn obejmuje usługi Gen1 i Gen2. Usługi Azure Storage Data Lake Storage Gen1 i Gen2 zostały zaprojektowane jako rozszerzenia systemu plików HDFS. Umożliwiają one działanie pełnego zestawu składników w środowisku Hadoop bezpośrednio na danych, które zarządza. Usługi Azure Storage, Data Lake Storage Gen1 i Gen2 to odrębne systemy plików. Systemy są zoptymalizowane pod kątem przechowywania danych i obliczeń na tych danych. Aby uzyskać informacje o korzyściach z używania usługi Azure Storage, zobacz [Używanie usługi Azure Storage z usługą HDInsight.](hdinsight-hadoop-use-blob-storage.md) Zobacz też temat [Używanie Data Lake Storage Gen1 z hdInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)i używanie [Data Lake Storage Gen2 z hdInsight.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy pamiętać o następujących wymaganiach:

* Klaster usługi Azure HDInsight. Aby uzyskać instrukcje, [zobacz Wprowadzenie do Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Znajomość następujących artykułów:
    * [Korzystanie z usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Używanie Data Lake Storage Gen1 z hdinsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Używanie Data Lake Storage Gen2 z hdInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Przekazywanie danych do usługi Azure Storage

### <a name="utilities"></a>Narzędzia

Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Witryna Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |â za" |â za" |â za" |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |â za" |â za" |â za" |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |â za" |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |â za" | |â za" |
| [Polecenie hadoop](#hadoop-command-line) |â za" |â za" |â za" |

> [!NOTE]  
> Polecenie Hadoop jest dostępne tylko w klastrze usługi HDInsight. Polecenie zezwala tylko na ładowanie danych z lokalnego systemu plików do usługi Azure Storage.  

### <a name="hadoop-command-line"></a>Wiersz polecenia hadoop

Wiersz polecenia usługi Hadoop jest przydatny tylko do przechowywania danych w obiekcie blob usługi Azure Storage, gdy dane są już obecne w węźle głównym klastra.

Aby użyć polecenia usługi Hadoop, musisz najpierw połączyć się z węzłom headnode przy użyciu [programu SSH lub PuTTY.](hdinsight-hadoop-linux-use-ssh-unix.md)

Po na połączeniu możesz użyć następującej składni, aby przekazać plik do magazynu.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Na przykład `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Ponieważ domyślny system plików dla usługi HDInsight znajduje się w usłudze Azure Storage, /example/data/data.txt w rzeczywistości znajduje się w usłudze Azure Storage. Możesz również odwołać się do pliku jako:

`wasbs:///example/data/data.txt`

lub

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Aby uzyskać listę innych poleceń hadoop, które działają z plikami, zobacz [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> W klastrach Apache HBase domyślny rozmiar bloku używany podczas zapisywania danych to 256 KB. Chociaż działa to prawidłowo w przypadku korzystania z interfejsów API HBase lub interfejsów API REST, użycie poleceń lub do zapisu danych większych niż `hadoop` `hdfs dfs` ~12 GB powoduje błąd. Aby uzyskać więcej informacji, zobacz Storage exception for write on blob (Wyjątek magazynu [dla zapisu w obiekcie blob).](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob)

### <a name="graphical-clients"></a>Klienci graficzni

Istnieje również kilka aplikacji, które zapewniają interfejs graficzny do pracy z usługą Azure Storage. W poniższej tabeli przedstawiono listę kilku z tych aplikacji:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |âââ" |âââ" |âââ" |
| [Eksplorator usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |âââ" |âââ" |âââ" |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |âââ" |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |â za" |
| [CloudBerry Explorer for Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |â za" |
| [Cyberduck](https://cyberduck.io/) | |â za" |â za" |

## <a name="mount-azure-storage-as-local-drive"></a>Zainstaluj usługę Azure Storage jako dysk lokalny

Zobacz Mount Azure Storage as Local Drive (Zainstaluj [usługę Azure Storage jako dysk lokalny).](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive)

## <a name="upload-using-services"></a>Przekazywanie przy użyciu usług

### <a name="azure-data-factory"></a>Azure Data Factory

Usługa Azure Data Factory to w pełni zarządzana usługa do składowania danych: magazynowania, przetwarzania i przemieszczania usług w usprawnione, dostosowywalne i niezawodne potoki produkcyjne danych.

|Typ magazynu|Dokumentacja|
|----|----|
|Azure Blob Storage|[Kopiowanie danych do lub z usługi Azure Blob Storage za pomocą usługi Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Usługa Azure Data Lake Storage 1. generacji|[Kopiowanie danych do lub z Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Usługa Azure Data Lake Storage 2. generacji |[Ładowanie danych do Azure Data Lake Storage Gen2 za pomocą Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop to narzędzie przeznaczone do przesyłania danych między platformą Hadoop i relacyjną bazami danych. Umożliwia importowanie danych z systemu zarządzania relacyjnej bazy danych (RDBMS), takiego jak SQL Server, MySQL lub Oracle. Następnie do rozproszonego systemu plików Hadoop (HDFS). Przekształć dane na hadoop za pomocą mapReduce lub Hive, a następnie wyeksportuj dane z powrotem do programu RDBMS.

Aby uzyskać więcej informacji, zobacz [Use Sqoop with HDInsight (Używanie sqoop z hdinsight).](hadoop/hdinsight-use-sqoop.md)

### <a name="development-sdks"></a>Zestawy SDK development

Dostęp do usługi Azure Storage można również uzyskać przy użyciu zestawu Azure SDK w następujących językach programowania:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Aby uzyskać więcej informacji na temat instalowania zestawów Azure SDK, zobacz [Pliki do pobrania dla platformy Azure](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak pobrać dane do hdinsight, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat analizy:

* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Programowe przesyłanie zadań usługi Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
