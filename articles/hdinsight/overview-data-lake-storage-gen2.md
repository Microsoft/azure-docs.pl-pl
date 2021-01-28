---
title: Przegląd Azure Data Lake Storage Gen2 w usłudze HDInsight
description: Omówienie Data Lake Storage Gen2 w usłudze HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e69838f18efc08d0f64dd9ea904f502617073a8f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938856"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Przegląd Azure Data Lake Storage Gen2 w usłudze HDInsight

Azure Data Lake Storage Gen2 pobiera podstawowe funkcje z Azure Data Lake Storage Gen1 i integruje je z usługą Azure Blob Storage. Te funkcje obejmują system plików, który jest zgodny z usługą Hadoop, Azure Active Directory (Azure AD) i list kontroli dostępu (ACL) oparty na standardzie POSIX. Ta kombinacja umożliwia korzystanie z wydajności Azure Data Lake Storage Gen1. Ponadto przy użyciu zarządzania warstwami i cyklem życia danych usługi BLOB Storage.

Aby uzyskać więcej informacji na temat Azure Data Lake Storage Gen2, zobacz [wprowadzenie do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Podstawowe funkcje programu Azure Data Lake Storage Gen2

* **Dostęp zgodny z usługą Hadoop:** W Azure Data Lake Storage Gen2 można zarządzać danymi i uzyskiwać do nich dostęp tak samo jak w przypadku rozproszony system plików Hadoop (HDFS). Sterownik systemu plików obiektów blob platformy Azure (ABFS) jest dostępny w ramach wszystkich środowisk Apache Hadoop, w tym usługi Azure HDInsight i Azure Databricks. Użyj ABFS, aby uzyskać dostęp do danych przechowywanych w Data Lake Storage Gen2.

* **Nadzbiór uprawnień systemu POSIX:** Model zabezpieczeń dla Data Lake Gen2 obsługuje uprawnienia list ACL i POSIX oraz niezbędny stopień szczegółowości Data Lake Storage Gen2. Ustawienia można skonfigurować za poorednictwem narzędzi administracyjnych lub platform, takich jak Apache Hive i Apache Spark.

* **Efektywność kosztów:** Data Lake Storage Gen2 oferuje niską wydajność magazynu i transakcji. Cykle życia usługi Azure Blob Storage obniżają koszty przez dostosowanie stawek rozliczeniowych, ponieważ dane są przenoszone przez cykl życia.

* **Zgodność z narzędziami, strukturami i aplikacjami magazynu obiektów blob:** Data Lake Storage Gen2 nadal pracują z szeroką gamę narzędzi, struktur i aplikacji dla usługi BLOB Storage.

* **Zoptymalizowany sterownik:** Sterownik ABFS jest zoptymalizowany pod kątem analizy danych Big Data. Odpowiednie interfejsy API REST są nadawane za pomocą punktu końcowego rozproszonego systemu plików (DFS) dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co nowego w Azure Data Lake Storage generacji 2

### <a name="managed-identities-for-secure-file-access"></a>Zarządzane tożsamości na potrzeby bezpiecznego dostępu do plików

Usługa Azure HDInsight używa zarządzanych tożsamości do zabezpieczania dostępu do klastrów do plików w Azure Data Lake Storage Gen2. Tożsamości zarządzane są funkcją Azure Active Directory, która zapewnia usługi platformy Azure z zestawem automatycznie zarządzanych poświadczeń. Przy użyciu tych poświadczeń można uwierzytelniać się w dowolnej usłudze, która obsługuje uwierzytelnianie Active Directory. Korzystanie z tożsamości zarządzanych nie wymaga przechowywania poświadczeń w plikach kodu lub konfiguracji.

Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów blob platformy Azure

Apache Hadoop aplikacje natywnie oczekują odczytu i zapisu danych z magazynu na dysku lokalnym. Sterownik systemu plików Hadoop, taki jak ABFS, umożliwia aplikacjom Hadoop współpracują z magazynem w chmurze. Działa przez emulowanie zwykłych operacji systemu plików usługi Hadoop. Sterownik konwertuje te polecenia otrzymane z aplikacji na operacje, które są zrozumiałe dla rzeczywistej platformy magazynu w chmurze.

Wcześniej sterownik systemu plików Hadoop przeprzekonwertował wszystkie operacje systemu plików na wywołania interfejsu API REST usługi Azure Storage po stronie klienta. A następnie wywołano interfejs API REST. Ta konwersja po stronie klienta, jednak wywołała wiele wywołań interfejsu API REST dla operacji systemu pojedynczego pliku, takich jak zmiana nazwy pliku. ABFS przeniósł logikę systemu plików Hadoop z po stronie klienta po stronie serwera. Interfejs API Azure Data Lake Storage Gen2 jest teraz uruchamiany równolegle z interfejsem API usługi BLOB. Ta migracja poprawia wydajność, ponieważ teraz Typowe operacje systemu plików usługi Hadoop można wykonać przy użyciu jednego wywołania interfejsu API REST.

Aby uzyskać więcej informacji, zobacz [Sterownik systemu plików obiektów blob platformy Azure (ABFS): dedykowany sterownik magazynu platformy Azure dla usługi Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schemat identyfikatora URI dla Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen2 używa nowego schematu identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schemat URI zapewnia dostęp szyfrowany przy użyciu protokołu SSL.

`<FILE_SYSTEM_NAME>` Identyfikuje ścieżkę do systemu plików Data Lake Storage Gen2.

`<ACCOUNT_NAME>` Określa nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

`<PATH>` jest nazwą ścieżki systemu plików HDFS pliku lub katalogu.

Jeśli wartości dla `<FILE_SYSTEM_NAME>` i `<ACCOUNT_NAME>` nie są określone, używany jest domyślny system plików. Dla plików w domyślnym systemie plików użyj ścieżki względnej lub ścieżki bezwzględnej. Na przykład `hadoop-mapreduce-examples.jar` plik, który jest dostarczany z klastrami usługi HDInsight, może być określony przy użyciu jednej z następujących ścieżek:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Nazwa pliku jest `hadoop-examples.jar` w klastrach usługi HDInsight w wersji 2,1 i 1,6. Podczas pracy z plikami spoza usługi HDInsight okaże się, że większość narzędzi nie rozpoznaje formatu ABFS, ale zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar` .

Aby uzyskać więcej informacji, zobacz [Korzystanie z identyfikatora URI Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
* [Omówienie usługi Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)