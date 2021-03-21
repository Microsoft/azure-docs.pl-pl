---
title: Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight
description: Dowiedz się, jak przekazywać i uzyskiwać dostęp do danych Apache Hadoop zadań w usłudze HDInsight. Użyj klasycznego interfejsu wiersza polecenia platformy Azure, Eksplorator usługi Azure Storage, Azure PowerShell, wiersza poleceń Hadoop lub Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931336"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight

Usługa HDInsight udostępnia rozproszony system plików Hadoop (HDFS) za pośrednictwem usługi Azure Storage, a Azure Data Lake Storage. Ten magazyn obejmuje Gen1 i Gen2. Usługi Azure Storage i Data Lake Storage Gen1 i Gen2 są przeznaczone do rozszerzeń systemu plików HDFS. Umożliwiają one bezpośrednie działanie pełnego zestawu składników w środowisku Hadoop w danych, którymi zarządza. Usługi Azure Storage, Data Lake Storage Gen1 i Gen2 to różne systemy plików. Systemy są zoptymalizowane pod kątem przechowywania danych i obliczeń na tych danych. Aby uzyskać informacje na temat korzyści z używania usługi Azure Storage, zobacz [Korzystanie z usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md). Zobacz również [Korzystanie z Data Lake Storage Gen1 z usługą HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)i [Używanie Data Lake Storage Gen2 z usługą HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy zwrócić uwagę na następujące wymagania:

* Klaster usługi Azure HDInsight. Aby uzyskać instrukcje, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Znajomość następujących artykułów:
    * [Korzystanie z usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Korzystanie z Data Lake Storage Gen1 z usługą HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Korzystanie z Data Lake Storage Gen2 z usługą HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Przekazywanie danych do usługi Azure Storage

### <a name="utilities"></a>Narzędzia

Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Witryna Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop — polecenie](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Polecenie Hadoop jest dostępne tylko w klastrze usługi HDInsight. Polecenie umożliwia tylko ładowanie danych z lokalnego systemu plików do usługi Azure Storage.  

### <a name="hadoop-command-line"></a>Wiersz polecenia usługi Hadoop

Wiersz polecenia usługi Hadoop jest przydatny tylko w przypadku przechowywania danych w usłudze Azure Storage BLOB, gdy dane znajdują się już w węźle głównym klastra.

Aby użyć polecenia Hadoop, należy najpierw nawiązać połączenie z usługą węzła głównego przy użyciu protokołu [SSH lub](hdinsight-hadoop-linux-use-ssh-unix.md)wyciągania.

Po nawiązaniu połączenia możesz użyć następującej składni, aby przekazać plik do magazynu.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Na przykład `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Ponieważ domyślny system plików dla usługi HDInsight znajduje się w usłudze Azure Storage,/example/Data/data.txt jest w rzeczywistości w usłudze Azure Storage. Można również odwołać się do pliku jako:

`wasbs:///example/data/data.txt`

lub

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Aby zapoznać się z listą innych poleceń usługi Hadoop, które pracują z plikami, zobacz [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> W przypadku klastrów Apache HBase domyślny rozmiar bloku używany podczas pisania danych to 256 KB. Chociaż jest to dobre rozwiązanie w przypadku używania interfejsów API HBase lub interfejsów API REST, użycie `hadoop` `hdfs dfs` poleceń lub do zapisu danych o rozmiarze większym niż ~ 12 GB powoduje wystąpienie błędu. Aby uzyskać więcej informacji, zobacz [wyjątek magazynu do zapisu w obiekcie blob](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Klienci graficzną

Istnieje również kilka aplikacji, które udostępniają interfejs graficzny służący do pracy z usługą Azure Storage. Poniższa tabela zawiera listę kilku z następujących aplikacji:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Narzędzia Microsoft Visual Studio dla usługi HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Eksplorator usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer dla Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Instalowanie usługi Azure Storage jako dysku lokalnego

Zobacz [Instalowanie usługi Azure Storage jako dysku lokalnego](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Przekaż za pomocą usług

### <a name="azure-data-factory"></a>Azure Data Factory

Usługa Azure Data Factory to w pełni zarządzana usługa umożliwiająca tworzenie danych: magazynu, przetwarzania i usług przenoszenia w ulepszonych, dostosowywalnych i niezawodnych potokach produkcyjnych danych.

|Typ magazynu|Dokumentacja|
|----|----|
|Azure Blob Storage|[Kopiowanie danych do lub z usługi Azure Blob Storage za pomocą usługi Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Usługa Azure Data Lake Storage 1. generacji|[Kopiowanie danych do lub z Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Usługa Azure Data Lake Storage 2. generacji |[Załaduj dane do Azure Data Lake Storage Gen2 z Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop to narzędzie przeznaczone do przesyłania danych między usługą Hadoop a relacyjnymi bazami danych. Służy do importowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS), takiego jak SQL Server, MySQL lub Oracle. Następnie do rozproszonego systemu plików Hadoop (HDFS). Przekształć dane w usłudze Hadoop za pomocą MapReduce lub Hive, a następnie wyeksportuj dane z powrotem do RDBMS.

Aby uzyskać więcej informacji, zobacz [Korzystanie z Sqoop z usługą HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Zestawy SDK programistyczne

Dostęp do usługi Azure Storage można również uzyskać przy użyciu zestawu Azure SDK z następujących języków programowania:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Aby uzyskać więcej informacji na temat instalowania zestawów Azure SDK, zobacz [Azure downloads](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Następne kroki

Teraz, po zrozumieniu sposobu pobierania danych do usługi HDInsight, przeczytaj następujące artykuły, aby dowiedzieć się, jak to zrobić:

* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Programowe przesyłanie Apache Hadoop zadań](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)