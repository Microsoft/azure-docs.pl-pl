---
title: Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight
description: Zawiera omówienie typów magazynu i sposobu ich współdziałania z usługą Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ed93ba937a843618f36bac6e88b15ff77355ca75
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610704"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight

Podczas tworzenia klastrów usługi HDInsight można wybrać kilka różnych usług magazynu platformy Azure:

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Usługa Azure Data Lake Storage 1. generacji](./overview-data-lake-storage-gen1.md)

Ten artykuł zawiera omówienie tych typów magazynu i ich unikatowych funkcji.

## <a name="storage-types-and-features"></a>Typy i funkcje magazynu

Poniższa tabela zawiera podsumowanie usług Azure Storage, które są obsługiwane przez różne wersje usługi HDInsight:

| Usługa magazynu | Typ konta | Typ przestrzeni nazw | Obsługiwane usługi | Obsługiwane warstwy wydajności | Obsługiwane warstwy dostępu | Wersja usługi HDInsight | Typ klastra |
|---|---|---|---|---|---|---|---|
|Usługa Azure Data Lake Storage 2. generacji| Ogólnego przeznaczenia w wersji 2 | Hierarchiczny (system plików) | Obiekt blob | Standardowa | Gorąca, chłodna, archiwalna | 3.6 + | Wszystkie z wyjątkiem platformy Spark 2,1 i 2,2|
|Azure Storage| Ogólnego przeznaczenia w wersji 2 | Obiekt | Obiekt blob | Standardowa | Gorąca, chłodna, archiwalna | 3.6 + | Wszyscy |
|Azure Storage| Ogólnego przeznaczenia w wersji 1 | Obiekt | Obiekt blob | Standardowa | Brak | Wszyscy | Wszyscy |
|Azure Storage| Blob Storage * * | Obiekt | Blokowy obiekt BLOB | Standardowa | Gorąca, chłodna, archiwalna | Wszyscy | Wszyscy |
|Usługa Azure Data Lake Storage 1. generacji| Brak | Hierarchiczny (system plików) | Brak | Brak | Brak | tylko 3,6 | Wszystkie z wyjątkiem HBase |

* * W przypadku klastrów usługi HDInsight tylko konta magazynu pomocniczego mogą być typu BlobStorage, a obiekt BLOB nie jest obsługiwaną opcją magazynu.

Aby uzyskać więcej informacji na temat typów kont usługi Azure Storage, zobacz temat [konto usługi Azure Storage — Omówienie](../storage/common/storage-account-overview.md)

Aby uzyskać więcej informacji na temat warstw dostępu do usługi Azure Storage, zobacz [warstwy magazynu usługi Azure Blob Storage: Premium (wersja zapoznawcza), gorąca, chłodna i archiwalna.](../storage/blobs/storage-blob-storage-tiers.md)

Można tworzyć klastry przy użyciu kombinacji usług dla podstawowego i opcjonalnego magazynu pomocniczego. Poniższa tabela zawiera podsumowanie konfiguracji magazynu klastra, które są obecnie obsługiwane w usłudze HDInsight:

| Wersja usługi HDInsight | Magazyn podstawowy | Magazyn pomocniczy | Obsługiwane |
|---|---|---|---|
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Usługa Data Lake Storage 2. generacji | Nie |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Usługa Data Lake Storage 2. generacji | Tak |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Usługa Data Lake Storage 2. generacji | Usługa Data Lake Storage 1. generacji | Nie |
| 3,6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 1. generacji | Tak |
| 3,6 | Usługa Data Lake Storage 1. generacji | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 | Usługa Data Lake Storage 1. generacji | Usługa Data Lake Storage 2. generacji | Nie |
| 4.0 | Usługa Data Lake Storage 1. generacji | Dowolne | Nie |
| 4.0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Usługa Data Lake Storage 1. generacji | Nie |

* = Może to być jedno lub wiele kont Data Lake Storage Gen2, o ile są one skonfigurowane do używania tej samej tożsamości zarządzanej na potrzeby dostępu do klastra.

> [!NOTE]
> Magazyn podstawowy Data Lake Storage Gen2 nie jest obsługiwany w przypadku klastrów Spark 2,1 lub 2,2.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Storage](./overview-azure-storage.md)
* [Omówienie usługi Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Omówienie usługi Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Wprowadzenie do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
