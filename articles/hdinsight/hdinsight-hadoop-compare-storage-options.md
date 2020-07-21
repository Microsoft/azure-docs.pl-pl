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
ms.openlocfilehash: ce1c6bdfb38e37c18a18cf970d2dd08683967da3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536752"
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
|Azure Data Lake Storage Gen2| Ogólnego przeznaczenia w wersji 2 | Hierarchiczny (system plików) | Obiekt blob | Standard | Gorąca, chłodna, archiwalna | 3.6 + | Wszystkie z wyjątkiem platformy Spark 2,1 i 2,2|
|Azure Storage| Ogólnego przeznaczenia w wersji 2 | Obiekt | Obiekt blob | Standard | Gorąca, chłodna, archiwalna | 3.6 + | Wszystko |
|Azure Storage| Ogólnego przeznaczenia w wersji 1 | Obiekt | Obiekt blob | Standard | Nie dotyczy | Wszystko | Wszystko |
|Azure Storage| Blob Storage * * | Obiekt | Blokowy obiekt BLOB | Standard | Gorąca, chłodna, archiwalna | Wszystko | Wszystko |
|Usługa Azure Data Lake Storage 1. generacji| Nie dotyczy | Hierarchiczny (system plików) | Nie dotyczy | Nie dotyczy | Nie dotyczy | tylko 3,6 | Wszystkie z wyjątkiem HBase |

* * W przypadku klastrów usługi HDInsight tylko konta magazynu pomocniczego mogą być typu BlobStorage, a obiekt BLOB nie jest obsługiwaną opcją magazynu.

Aby uzyskać więcej informacji na temat typów kont usługi Azure Storage, zobacz temat [konto usługi Azure Storage — Omówienie](../storage/common/storage-account-overview.md)

Aby uzyskać więcej informacji na temat warstw dostępu do usługi Azure Storage, zobacz [warstwy magazynu usługi Azure Blob Storage: Premium (wersja zapoznawcza), gorąca, chłodna i archiwalna.](../storage/blobs/storage-blob-storage-tiers.md)

Można tworzyć klastry przy użyciu kombinacji usług dla podstawowego i opcjonalnego magazynu pomocniczego. Poniższa tabela zawiera podsumowanie konfiguracji magazynu klastra, które są obecnie obsługiwane w usłudze HDInsight:

| Wersja usługi HDInsight | Magazyn podstawowy | Magazyn pomocniczy | Obsługiwane |
|---|---|---|---|
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Data Lake Storage Gen2 | Nie |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Tak |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nie |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Tak |
| 3,6 | Data Lake Storage Gen1 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2, BlobStorage (blokowe obiekty blob) | Tak |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nie |
| 4.0 | Data Lake Storage Gen1 | Dowolne | Nie |
| 4.0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Data Lake Storage Gen1 | Nie |

* = Może to być jedno lub wiele kont Data Lake Storage Gen2, o ile są one skonfigurowane do używania tej samej tożsamości zarządzanej na potrzeby dostępu do klastra.

> [!NOTE]
> Magazyn podstawowy Data Lake Storage Gen2 nie jest obsługiwany w przypadku klastrów Spark 2,1 lub 2,2.

## <a name="data-replication"></a>Replikacja danych

Usługa Azure HDInsight nie przechowuje danych klienta. Podstawowym środkiem magazynu dla klastra są skojarzone konta magazynu. Klaster można dołączyć do istniejącego konta magazynu lub utworzyć nowe konto magazynu podczas procesu tworzenia klastra. Jeśli zostanie utworzone nowe konto, zostanie ono utworzone jako konto magazynu lokalnie nadmiarowy (LRS) i będzie spełniać wymagania dotyczące miejsca zamieszkania danych w regionie, w tym te określone w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net).

Możesz sprawdzić, czy Usługa HDInsight została prawidłowo skonfigurowana do przechowywania danych w jednym regionie, upewniając się, że konto magazynu skojarzone z usługą HDInsight to LRS lub inną opcję magazynu podaną w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net).
 
## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Storage](./overview-azure-storage.md)
* [Omówienie usługi Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Omówienie usługi Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Wprowadzenie do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
