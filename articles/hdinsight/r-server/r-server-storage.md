---
title: Rozwiązania Azure Storage dla usług ML w usłudze HDInsight — Azure
description: Dowiedz się więcej o różnych opcjach magazynu dostępnych w usłudze ML w usłudze HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943901"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Rozwiązania usługi Azure Storage dla usług ML w usłudze Azure HDInsight

Usługi ML w usłudze HDInsight mogą używać różnych rozwiązań do przechowywania danych, kodu lub obiektów, które zawierają wyniki analizy. Te rozwiązania obejmują następujące opcje:

- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Usługa Azure Data Lake Storage 1. generacji](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

Istnieje również możliwość uzyskiwania dostępu do wielu kontenerów lub kont magazynu platformy Azure z klastrem usługi HDInsight. Usługa Azure File Storage to wygodna opcja magazynu danych do użycia w węźle brzegowym, który umożliwia zainstalowanie udziału plików usługi Azure Storage w systemie, na przykład systemu plików Linux. Jednak udziały plików platformy Azure mogą być instalowane i używane przez dowolny system, który ma obsługiwany system operacyjny, taki jak Windows lub Linux.

Podczas tworzenia klastra Apache Hadoop w usłudze HDInsight należy określić konto **usługi Azure Blob Storage** lub **Data Lake Storage Gen1**. Określony kontener magazynu z tego konta zawiera system plików dla tworzonego klastra (na przykład rozproszony system plików Hadoop). Aby uzyskać więcej informacji i wskazówki, zobacz:

- [Używanie usługi Azure Blob Storage z usługą HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Korzystanie z kont usługi Azure Blob Storage z klastrem usług ML

Jeśli podczas tworzenia klastra usług ML określono więcej niż jedno konto magazynu, poniższe instrukcje wyjaśniają, jak używać konta pomocniczego do uzyskiwania dostępu do danych i operacji w klastrze usługi ML. Przyjęto założenie następujących kont magazynu i kontenera: **storage1** i domyślnego kontenera o nazwie **container1**, a **storage2** z **container2**.

> [!WARNING]  
> Ze względu na wydajność klaster usługi HDInsight jest tworzony w tym samym centrum danych co określone przez Ciebie podstawowe konto magazynu. Użycie konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Korzystanie z domyślnego magazynu za pomocą usługi ML w usłudze HDInsight

1. Za pomocą klienta SSH Połącz się z węzłem brzegowym klastra. Aby uzyskać informacje na temat używania protokołu SSH z klastrami usługi HDInsight, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Skopiuj przykładowy plik mysamplefile.csv do katalogu/Share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Przejdź do programu R Studio lub innej konsoli języka R i napisz kod R, aby ustawić **domyślny** węzeł nazwy i lokalizację pliku, do którego chcesz uzyskać dostęp.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Wszystkie odwołania do katalogu i pliku wskazują konto magazynu `wasbs://container1@storage1.blob.core.windows.net` . Jest to **domyślne konto magazynu** skojarzone z klastrem usługi HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Korzystanie z dodatkowego magazynu za pomocą usługi ML w usłudze HDInsight

Teraz Załóżmy, że chcesz przetworzyć plik o nazwie mysamplefile1.csv znajdujący się w katalogu/Private **container2** w **storage2**.

W kodzie R wskaż węzeł nazwa odwołanie do konta magazynu **storage2** .

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Wszystkie katalogi i pliki odwołują się teraz do konta magazynu `wasbs://container2@storage2.blob.core.windows.net` . Jest to określony **węzeł nazwy** .

Skonfiguruj `/user/RevoShare/<SSH username>` katalog na **storage2** w następujący sposób:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Używanie Azure Data Lake Storage Gen1 z klastrem usługi ML

Aby można było używać Data Lake Storage Gen1 z klastrem usługi HDInsight, należy nadać klastrowi dostęp do każdego Azure Data Lake Storage Gen1, którego chcesz użyć. Aby uzyskać instrukcje dotyczące sposobu używania Azure Portal do tworzenia klastra usługi HDInsight z Azure Data Lake Storage Gen1 jako magazyn domyślny lub jako dodatkowy magazyn, zobacz [Tworzenie klastra usługi HDInsight za pomocą Data Lake Storage Gen1 przy użyciu Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Następnie należy użyć magazynu w skrypcie języka R, podobnie jak w przypadku konta pomocniczego usługi Azure Storage, zgodnie z opisem w poprzedniej procedurze.

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Dodawanie dostępu do klastra do Azure Data Lake Storage Gen1

Dostęp do Data Lake Storage Gen1 przy użyciu jednostki usługi Azure Active Directory (Azure AD) skojarzonej z klastrem HDInsight.

1. Podczas tworzenia klastra usługi HDInsight wybierz pozycję **klaster Azure AD tożsamość** na karcie **Źródło danych** .

2. W oknie dialogowym **tożsamość klastra Azure AD** w obszarze **Wybierz jednostkę usługi AD** wybierz pozycję **Utwórz nową**.

Po podaniu nazwy głównej usługi i utworzeniu dla niej hasła kliknij pozycję **Zarządzaj dostępem ADLS** , aby skojarzyć jednostkę usługi z Data Lake Storage.

Możliwe jest również dodanie dostępu klastra do co najmniej jednego Data Lake kont Gen1 magazynu po utworzeniu klastra. Otwórz wpis Azure Portal dla Data Lake Storage Gen1 i przejdź do **Eksplorator danych > Access > Dodaj**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Jak uzyskać dostęp do Data Lake Storage Gen1 z usług ML w usłudze HDInsight

Po uzyskaniu dostępu do Data Lake Storage Gen1 można korzystać z magazynu w klastrze usługi ML w usłudze HDInsight w taki sposób, jak w przypadku dodatkowego konta magazynu platformy Azure. Jedyną różnicą jest to, że prefiks **wasbs://** zmienia się na **ADL://** w następujący sposób:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Następujące polecenia służą do konfigurowania Data Lake Storage Gen1 przy użyciu katalogu RevoShare i dodawania przykładowego pliku CSV z poprzedniego przykładu:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Korzystanie z usługi Azure File Storage z usługą ML w usłudze HDInsight

Dostępna jest również wygodna opcja przechowywania danych do użycia w węźle brzegowym o nazwie [Azure Files](https://azure.microsoft.com/services/storage/files/). Umożliwia ona zainstalowanie udziału plików usługi Azure Storage w systemie plików Linux. Ta opcja może być przydatna do przechowywania plików danych, skryptów języka R i obiektów wynikowych, które mogą być potrzebne później, szczególnie w przypadku, gdy warto używać natywnego systemu plików w węźle brzegowym, a nie w systemie HDFS.

Główną zaletą Azure Files jest to, że udziały plików można instalować i używać w dowolnym systemie, który ma obsługiwane systemy operacyjne, takie jak Windows lub Linux. Na przykład może być używany przez inny klaster usługi HDInsight, który jest lub ktoś w Twoim zespole, przez maszynę wirtualną platformy Azure, a nawet przez system lokalny. Aby uzyskać więcej informacji, zobacz:

- [Jak używać usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak korzystać z usługi Azure File Storage w systemie Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Następne kroki

- [Omówienie klastra usług ML w usłudze HDInsight](r-server-overview.md)
- [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
