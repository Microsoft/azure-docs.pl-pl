---
title: Omówienie usługi Azure Data Lake Storage Gen2 w usłudze HDInsight
description: Omówienie usługi Data Lake Storage Gen2 w hdinsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873344"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Omówienie usługi Azure Data Lake Storage Gen2 w usłudze HDInsight

Usługa Azure Data Lake Storage Gen2 pobiera podstawowe funkcje z usługi Azure Data Lake Storage Gen1 i integruje je z magazynem obiektów blob platformy Azure. Funkcje te obejmują system plików, który jest zgodny z Hadoop, Azure Active Directory (Azure AD) i posix oparte na listach kontroli dostępu (Listy KONTROLI DOSTĘPU). Ta kombinacja umożliwia korzystanie z wydajności usługi Azure Data Lake Storage Gen1. Podczas gdy również przy użyciu warstw i zarządzania cyklem życia danych magazynu obiektów Blob.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen2, zobacz [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Podstawowa funkcjonalność usługi Azure Data Lake Storage Gen2

* **Dostęp zgodny z Hadoop:** W usłudze Azure Data Lake Storage Gen2 można zarządzać i uzyskiwać dostęp do danych tak samo, jak w przypadku rozproszonego systemu plików Hadoop (HDFS). Sterownik systemu plików obiektów Blob (Azure Blob File System) (ABFS) jest dostępny we wszystkich środowiskach Apache Hadoop, w tym usługi Azure HDInsight i Azure Databricks. Użyj ABFS, aby uzyskać dostęp do danych przechowywanych w data lake storage gen2.

* **Nadzbiór uprawnień POSIX:** Model zabezpieczeń dla usługi Data Lake Gen2 obsługuje uprawnienia ACL i POSIX wraz z kilkoma dodatkowymi szczegółowościami specyficznymi dla usługi Data Lake Storage Gen2. Ustawienia można konfigurować za pomocą narzędzi administracyjnych lub struktur, takich jak Apache Hive i Apache Spark.

* **Opłacalność:** Data Lake Storage Gen2 oferuje tanią pojemność pamięci masowej i transakcje. Cykle życia magazynu obiektów Blob platformy Azure pomagają obniżyć koszty, dostosowując stawki rozliczeń w miarę przechodzenia danych przez cały cykl życia.

* **Zgodność z narzędziami magazynu obiektów Blob, strukturami i aplikacjami:** Data Lake Storage Gen2 kontynuuje pracę z szeroką gamą narzędzi, struktur i aplikacji dla magazynu obiektów Blob.

* **Zoptymalizowany sterownik:** Sterownik ABFS jest zoptymalizowany specjalnie pod kątem analizy dużych zbiorów danych. Odpowiednie interfejsy API REST są rzutowane za pośrednictwem punktu końcowego rozproszonego systemu plików (DFS), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co nowego w usłudze Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Tożsamości zarządzane dla bezpiecznego dostępu do plików

Usługa Azure HDInsight używa tożsamości zarządzanych do bezpiecznego dostępu klastra do plików w usłudze Azure Data Lake Storage Gen2. Tożsamości zarządzane są funkcją usługi Azure Active Directory, która zapewnia usługom platformy Azure zestaw automatycznie zarządzanych poświadczeń. Te poświadczenia mogą służyć do uwierzytelniania w dowolnej usłudze obsługującej uwierzytelnianie usługi Active Directory. Przy użyciu tożsamości zarządzanych nie wymaga przechowywania poświadczeń w plikach kodu lub konfiguracji.

Aby uzyskać więcej informacji, zobacz [Zarządzane tożsamości zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów Blob platformy Azure

Apache Hadoop aplikacje natywnie oczekiwać odczytu i zapisu danych z magazynu dysków lokalnych. Sterownik systemu plików Hadoop, taki jak ABFS, umożliwia aplikacjom Hadoop pracę z pamięcią masową w chmurze. Działa poprzez emulację regularnych operacji systemu plików Hadoop. Sterownik konwertuje te polecenia odebrane z aplikacji na operacje, które rozumie rzeczywista platforma magazynu w chmurze.

Wcześniej sterownik systemu plików Hadoop konwertował wszystkie operacje systemu plików na wywołania interfejsu API rest usługi Azure Storage po stronie klienta. A następnie wywołał interfejs API REST. Ta konwersja po stronie klienta spowodowała jednak wiele wywołań interfejsu API REST dla operacji pojedynczego systemu plików, takich jak zmiana nazwy pliku. ABFS przeniósł logikę systemu plików Hadoop od strony klienta do strony serwera. Interfejs API usługi Azure Data Lake Storage Gen2 działa teraz równolegle z interfejsem API obiektów Blob. Ta migracja zwiększa wydajność, ponieważ teraz typowe operacje systemu plików Hadoop mogą być wykonywane za pomocą jednego wywołania interfejsu API REST.

Aby uzyskać więcej informacji, zobacz [sterownik systemu plików obiektów Blob azure (ABFS): dedykowany sterownik usługi Azure Storage dla usługi Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schemat identyfikatorów URI dla usługi Azure Data Lake Storage Gen 2

Usługa Azure Data Lake Storage Gen2 używa nowego schematu identyfikatorów URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schemat identyfikatorów URI zapewnia dostęp zaszyfrowany za szyfrowany ssl.

`<FILE_SYSTEM_NAME>`identyfikuje ścieżkę systemu plików Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identyfikuje nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

`<PATH>`jest nazwą ścieżki pliku lub katalogu HDFS.

Jeśli wartości `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` dla i nie są określone, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików użyj ścieżki względnej lub ścieżki bezwzględnej. Na przykład `hadoop-mapreduce-examples.jar` plik dostarczany z klastrami HDInsight można odwoływać się przy użyciu jednej z następujących ścieżek:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Nazwa pliku `hadoop-examples.jar` znajduje się w klastrach HDInsight w wersjach 2.1 i 1.6. Podczas pracy z plikami spoza hdinsight, przekonasz się, że większość narzędzi nie rozpoznaje formatu ABFS, `example/jars/hadoop-mapreduce-examples.jar`ale zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak .

Aby uzyskać więcej informacji, zobacz [Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
* [Omówienie usługi Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)