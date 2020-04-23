---
title: Porównywanie opcji magazynowania używanych z klastrami usługi Azure HDInsight
description: Zawiera omówienie typów magazynu i sposobu pracy z usługą Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869838"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porównywanie opcji magazynowania używanych z klastrami usługi Azure HDInsight

Podczas tworzenia klastrów usługi HDInsight można wybrać jedną z kilku różnych usług magazynu platformy Azure:

* [Azure Storage](./overview-azure-storage.md)
* [Usługa Azure Data Lake Storage 2. generacji](./overview-data-lake-storage-gen1.md)
* [Usługa Azure Data Lake Storage 1. generacji](./overview-data-lake-storage-gen2.md)

Ten artykuł zawiera omówienie tych typów magazynu i ich unikatowych funkcji.

## <a name="storage-types-and-features"></a>Typy i funkcje pamięci masowej

W poniższej tabeli podsumowano usługi usługi Azure Storage obsługiwane w różnych wersjach usługi HDInsight:

| Usługa magazynowania | Typ konta | Typ obszaru nazw | Obsługiwane usługi | Obsługiwane warstwy wydajności | Obsługiwane warstwy dostępu | Wersja HDInsight | Typ klastra |
|---|---|---|---|---|---|---|---|
|Usługa Azure Data Lake Storage 2. generacji| Uniwersalny V2 | Hierarchiczny (system plików) | Obiekt blob | Standardowa | Gorący, Chłodny, Archiwum | 3,6+ | Wszystkie z wyjątkiem Spark 2.1 i 2.2|
|Azure Storage| Uniwersalny V2 | Obiekt | Obiekt blob | Standardowa | Gorący, Chłodny, Archiwum | 3,6+ | Wszystkie |
|Azure Storage| Uniwersalny V1 | Obiekt | Obiekt blob | Standardowa | Nie dotyczy | Wszystkie | Wszystkie |
|Azure Storage| Przechowywanie obiektów blob** | Obiekt | Blok blob | Standardowa | Gorący, Chłodny, Archiwum | Wszystkie | Wszystkie |
|Usługa Azure Data Lake Storage 1. generacji| Nie dotyczy | Hierarchiczny (system plików) | Nie dotyczy | Nie dotyczy | Nie dotyczy | 3.6 Tylko | Wszystkie z wyjątkiem HBase |

**W przypadku klastrów HDInsight tylko konta magazynu pomocniczego mogą być typu BlobStorage, a obiekt blob strony nie jest obsługiwaną opcją magazynu.

Aby uzyskać więcej informacji na temat typów kont usługi Azure Storage, zobacz [omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md)

Aby uzyskać więcej informacji na temat warstw dostępu usługi Azure Storage, zobacz [usługi Azure Blob storage: Premium (wersja zapoznawcza), Warstwy magazynu gorąca, chłodna i archiwum](../storage/blobs/storage-blob-storage-tiers.md)

Klastry można tworzyć przy użyciu kombinacji usług dla magazynu pomocniczego podstawowego i opcjonalnego. W poniższej tabeli podsumowano konfiguracje magazynu klastra, które są obecnie obsługiwane w programie HDInsight:

| Wersja HDInsight | Magazyn podstawowy | Pamięć dodatkowa | Obsługiwane |
|---|---|---|---|
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, ogólnego przeznaczenia V2 | Ogólnego przeznaczenia V1, ogólnego przeznaczenia V2, BlobStorage (blokowe obiekty blob) | Yes |
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, ogólnego przeznaczenia V2 | Usługa Data Lake Storage 2. generacji | Nie |
| 3,6 & 4,0 | Pamięć masowa Data Lake Gen2* | Usługa Data Lake Storage 2. generacji | Yes |
| 3,6 & 4,0 | Pamięć masowa Data Lake Gen2* | Ogólnego przeznaczenia V1, ogólnego przeznaczenia V2, BlobStorage (blokowe obiekty blob) | Yes |
| 3,6 & 4,0 | Usługa Data Lake Storage 2. generacji | Usługa Data Lake Storage 1. generacji | Nie |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 1. generacji | Yes |
| 3.6 | Usługa Data Lake Storage 1. generacji | Ogólnego przeznaczenia V1, ogólnego przeznaczenia V2, BlobStorage (blokowe obiekty blob) | Yes |
| 3.6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 2. generacji | Nie |
| 4.0 | Usługa Data Lake Storage 1. generacji | Dowolne | Nie |
| 4.0 | Ogólnego przeznaczenia V1, ogólnego przeznaczenia V2 | Usługa Data Lake Storage 1. generacji | Nie |

*=Może to być jedno lub wiele kont Data Lake Storage Gen2, o ile wszystkie są skonfigurowane do używania tej samej tożsamości zarządzanej do dostępu do klastra.

> [!NOTE]
> Magazyn podstawowy magazynu usługi Data Lake Storage Gen2 nie jest obsługiwany dla klastrów platformy Spark 2.1 lub 2.2.

## <a name="next-steps"></a>Następne kroki

* [Usługa Azure Storage — omówienie](./overview-azure-storage.md)
* [Omówienie usługi Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Omówienie usługi Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
