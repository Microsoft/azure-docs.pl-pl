---
title: Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight
description: Dowiedz się, jak używać Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: cc17cd23ae197db25fed440eb249f2cf069d4859
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744594"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) to usługa magazynu w chmurze przeznaczona dla analizy danych Big Data, oparta na [usłudze Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 łączy możliwości usługi Azure Blob Storage i Azure Data Lake Storage Gen1. Usługa będąca wynikiem oferty oferuje funkcje z Azure Data Lake Storage Gen1, w tym: semantyki systemu plików, poziom katalogów i zabezpieczenia na poziomie plików oraz możliwość adaptacji. Wraz z niskim kosztem magazynowania warstwowego, wysokiej dostępności i odzyskiwania po awarii z usługi Azure Blob Storage.

Aby zapoznać się z pełnym porównaniem opcji tworzenia klastra przy użyciu Data Lake Storage Gen2, zobacz [porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Dostępność Data Lake Storage Gen2

Data Lake Storage Gen2 jest dostępna jako opcja magazynu dla niemal wszystkich typów klastrów usługi Azure HDInsight zarówno jako domyślnego, jak i dodatkowego konta magazynu. HBase jednak może mieć tylko jedno konto z Data Lake Storage Gen2.

> [!Note]  
> Po wybraniu Data Lake Storage Gen2 jako **podstawowego typu magazynu** nie można wybrać Data Lake Storage Gen1 jako dodatkowego magazynu.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Tworzenie klastrów usługi HDInsight przy użyciu Data Lake Storage Gen2

Skorzystaj z poniższych linków, aby uzyskać szczegółowe instrukcje dotyczące tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen2.

* [Korzystanie z portalu](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* Program PowerShell nie jest obecnie obsługiwany na potrzeby tworzenia klastra usługi HDInsight z Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Kontrola dostępu do Data Lake Storage Gen2 w usłudze HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jakie rodzaje uprawnień Data Lake Storage Gen2 obsługiwać?

Data Lake Storage Gen2 używa modelu kontroli dostępu, który obsługuje zarówno kontrolę dostępu opartą na rolach (Azure RBAC), jak i oparte na systemie POSIX listy kontroli dostępu (ACL). Data Lake Storage Gen1 obsługuje listy kontroli dostępu tylko w celu kontrolowania dostępu do danych.

Funkcja RBAC platformy Azure korzysta z przypisań ról w celu efektywnego stosowania zestawów uprawnień dla użytkowników, grup i jednostek usługi dla zasobów platformy Azure. Zazwyczaj te zasoby platformy Azure są ograniczone do zasobów najwyższego poziomu (na przykład kont usługi Azure Blob Storage). W przypadku usługi Azure Blob Storage, a także Data Lake Storage Gen2, ten mechanizm został rozszerzony do zasobu systemu plików.

Aby uzyskać więcej informacji na temat uprawnień do plików za pomocą usługi Azure RBAC, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Aby uzyskać więcej informacji o uprawnieniach plików z listami ACL, zobacz [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Jak mogę kontrolować dostęp do danych w Data Lake Storage Gen2?

Możliwość dostępu do plików w Data Lake Storage Gen2 przez klaster usługi HDInsight jest kontrolowana przez zarządzane tożsamości. Tożsamość zarządzana to tożsamość zarejestrowana w Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. Przy użyciu tożsamości zarządzanych nie trzeba rejestrować nazw głównych usług w usłudze Azure AD. Lub Zachowaj poświadczenia, takie jak certyfikaty.

Usługi platformy Azure mają dwa typy zarządzanych tożsamości: przypisane do systemu i przypisane przez użytkownika. Usługa HDInsight używa zarządzanych tożsamości przypisanych przez użytkownika w celu uzyskania dostępu do Data Lake Storage Gen2. `user-assigned managed identity`Jest tworzony jako autonomiczny zasób platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure.

Cykl życiowy tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życiowego wystąpień usługi platformy Azure, do których została przypisana. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Jak mogę ustawić uprawnienia dla użytkowników usługi Azure AD do wykonywania zapytań dotyczących danych w Data Lake Storage Gen2 przy użyciu programu Hive lub innych usług?

Aby ustawić uprawnienia dla użytkowników do wykonywania zapytań dotyczących danych, należy użyć grup zabezpieczeń usługi Azure AD jako przypisanego podmiotu zabezpieczeń na listach ACL. Nie należy bezpośrednio przypisywać uprawnień dostępu do plików do poszczególnych użytkowników lub podmiotów usługi. Grupy zabezpieczeń usługi Azure AD w celu kontrolowania przepływu uprawnień można dodawać i usuwać użytkowników lub jednostki usługi bez ponownego zastosowania list kontroli dostępu do całej struktury katalogów. Wystarczy dodać lub usunąć użytkowników z odpowiedniej grupy zabezpieczeń usługi Azure AD. Listy ACL nie są dziedziczone, dlatego ponowne zastosowanie list kontroli dostępu wymaga aktualizacji listy ACL dla każdego pliku i podkatalogu.

## <a name="access-files-from-the-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w Data Lake Storage Gen2 z klastra usługi HDInsight.

* **Przy użyciu w pełni kwalifikowanej nazwy** . W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Przy użyciu skróconego formatu ścieżki** . To podejście zastępuje ścieżkę do katalogu głównego klastra przy użyciu:

    ```
    abfs:///<file.path>/
    ```

* **Przy użyciu ścieżki względnej** . W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

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
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Skopiuj dane z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Wyświetlanie zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

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
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Następne kroki

* [Integracja z usługą Azure HDInsight z usługą Data Lake Storage Gen2 Preview — lista ACL i aktualizacja zabezpieczeń](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
