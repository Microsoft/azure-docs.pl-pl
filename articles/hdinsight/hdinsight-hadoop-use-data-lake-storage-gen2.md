---
title: Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight
description: Dowiedz się, jak używać Azure Data Lake Storage Gen2 z Azure HDInsight klastrami.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ea9dc2627d5a6498f69ca8c61a9cac8089816886
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378588"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) to usługa magazynu w chmurze przeznaczona do analizy danych big data, zbudowana w oparciu o [usługę Azure Blob Storage.](../storage/blobs/storage-blobs-introduction.md) Data Lake Storage Gen2 łączy możliwości usługi Azure Blob Storage i Azure Data Lake Storage Gen1. Wynikowa usługa oferuje funkcje od Azure Data Lake Storage Gen1, takie jak semantyka systemu plików, zabezpieczenia na poziomie katalogu i na poziomie plików oraz możliwość dostosowywania. Oprócz tańszego magazynu warstwowego, wysokiej dostępności i możliwości odzyskiwania po awarii z usługi Azure Blob Storage.

Aby uzyskać pełne porównanie opcji tworzenia klastra przy użyciu Data Lake Storage Gen2, zobacz Porównanie opcji magazynu do użycia z klastrami [Azure HDInsight klastrach.](hdinsight-hadoop-compare-storage-options.md)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 dostępności

Data Lake Storage Gen2 jest dostępna jako opcja magazynu dla prawie wszystkich typów Azure HDInsight jako domyślne i dodatkowe konto magazynu. Baza HBase może jednak mieć tylko jedno konto z Data Lake Storage Gen2.

> [!Note]  
> Po wybraniu Data Lake Storage Gen2 **jako** podstawowego typu magazynu nie można wybrać magazynu Data Lake Storage Gen1 jako magazynu dodatkowego.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Tworzenie klastrów usługi HDInsight przy użyciu Data Lake Storage Gen2

Skorzystaj z poniższych linków, aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen2.

* [Korzystanie z portalu](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* Program PowerShell nie jest obecnie obsługiwany w przypadku tworzenia klastra usługi HDInsight z Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Kontrola dostępu do Data Lake Storage Gen2 w umacie HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jakiego rodzaju uprawnienia są Data Lake Storage Gen2?

Data Lake Storage Gen2 korzysta z modelu kontroli dostępu, który obsługuje zarówno listę kontroli dostępu na podstawie ról (RBAC) platformy Azure, jak i listy kontroli dostępu (ACL) podobne do modelu POSIX. Data Lake Storage Gen1 obsługuje listy kontroli dostępu tylko w celu kontrolowania dostępu do danych.

Rola RBAC platformy Azure używa przypisań ról w celu skutecznego stosowania zestawów uprawnień do użytkowników, grup i jednostki usługi dla zasobów platformy Azure. Zazwyczaj te zasoby platformy Azure są ograniczone do zasobów najwyższego poziomu (na przykład kont usługi Azure Blob Storage). W przypadku usługi Azure Blob Storage, Data Lake Storage Gen2 także dla tego Data Lake Storage Gen2, ten mechanizm został rozszerzony na zasób systemu plików.

Aby uzyskać więcej informacji na temat uprawnień do plików przy użyciu kontroli dostępu opartej na rolach platformy Azure, zobacz [Azure role-based access control (Azure RBAC) (Kontrola](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)dostępu oparta na rolach na platformie Azure).

Aby uzyskać więcej informacji na temat uprawnień do plików z listami kontroli dostępu, zobacz [Access control lists on files and directories (Listy](../storage/blobs/data-lake-storage-access-control.md)kontroli dostępu do plików i katalogów).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Jak mogę kontrolować dostęp do moich danych w Data Lake Storage Gen2?

Możliwość uzyskiwania dostępu do plików w klastrze usługi HDInsight w Data Lake Storage Gen2 jest kontrolowana za pośrednictwem tożsamości zarządzanych. Tożsamość zarządzana to tożsamość zarejestrowana w usłudze Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. W przypadku tożsamości zarządzanych nie trzeba rejestrować jednostki usługi w usłudze Azure AD. Lub obsługa poświadczeń, takich jak certyfikaty.

Usługi platformy Azure mają dwa typy tożsamości zarządzanych: przypisane przez system i przypisane przez użytkownika. Do uzyskiwania dostępu do usługi HDInsight są używane tożsamości zarządzane przypisane przez Data Lake Storage Gen2. A `user-assigned managed identity` jest tworzony jako autonomiczny zasób platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure.

Cykl życiowy tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życiowego wystąpień usługi platformy Azure, do których została przypisana. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Jak mogę ustawić uprawnienia dla użytkowników usługi Azure AD do wykonywania zapytań o dane w usłudze Data Lake Storage Gen2 przy użyciu programu Hive lub innych usług?

Aby ustawić uprawnienia dla użytkowników do wykonywania zapytań o dane, użyj grup zabezpieczeń usługi Azure AD jako przypisanego podmiotu zabezpieczeń w listy ACL. Nie przypisuj bezpośrednio uprawnień dostępu do plików poszczególnym użytkownikom lub jednostkom usługi. Dzięki grupom zabezpieczeń usługi Azure AD w celu kontrolowania przepływu uprawnień możesz dodawać i usuwać użytkowników lub jednostki usługi bez ponownego użycia listy ACL do całej struktury katalogów. Wystarczy dodać lub usunąć użytkowników z odpowiedniej grupy zabezpieczeń usługi Azure AD. Listy ACL nie są dziedziczone, dlatego ponowne zastosowania list ACL wymagają zaktualizowania listy ACL w każdym pliku i podkatalogu.

## <a name="access-files-from-the-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w u Data Lake Storage Gen2 z klastra usługi HDInsight.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Przy użyciu skróconego formatu ścieżki**. To podejście umożliwia zastąpienie ścieżki do katalogu głównego klastra:

    ```
    abfs:///<file.path>/
    ```

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Przykłady dostępu do danych

Przykłady są oparte na [połączeniu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) z węzłem głównym klastra. W przykładach używa się wszystkich trzech schematów URI. Zastąp `CONTAINERNAME` wartości i odpowiednimi `STORAGEACCOUNT` wartościami

#### <a name="a-few-hdfs-commands"></a>Kilka poleceń hdfs

1. Utwórz plik w magazynie lokalnym.

    ```bash
    touch testFile.txt
    ```

1. Tworzenie katalogów w magazynie klastra.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiowanie danych z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Tworzenie tabeli hive

W celach ilustrujących przedstawiono trzy lokalizacje plików. W przypadku rzeczywistego wykonania użyj tylko jednego `LOCATION` z wpisów.

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
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Następne kroki

* [Azure HDInsight integracji z Data Lake Storage Gen2 wersji zapoznawczej — listy ACL i aktualizacja zabezpieczeń](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Interactive Query w Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
