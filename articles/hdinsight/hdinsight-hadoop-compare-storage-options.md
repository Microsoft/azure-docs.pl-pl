---
title: Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight
description: Zawiera omówienie typów magazynu i sposobu ich współdziałania z usługą Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b6dd0fd95280a65615d38ab11a2f9814f58586f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945857"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight

Podczas tworzenia klastrów usługi HDInsight można wybrać kilka różnych usług magazynu platformy Azure:

* [Magazyn obiektów blob platformy Azure z usługą HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2 z usługą HDInsight](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1 z usługą HDInsight](./overview-data-lake-storage-gen1.md)

Ten artykuł zawiera omówienie tych typów magazynu i ich unikatowych funkcji.

## <a name="storage-types-and-features"></a>Typy i funkcje magazynu

Poniższa tabela zawiera podsumowanie usług Azure Storage, które są obsługiwane przez różne wersje usługi HDInsight:

| Usługa magazynu | Typ konta | Typ przestrzeni nazw | Obsługiwane usługi | Obsługiwane warstwy wydajności | Obsługiwane warstwy dostępu | Wersja usługi HDInsight | Typ klastra |
|---|---|---|---|---|---|---|---|
|Usługa Azure Data Lake Storage 2. generacji| Ogólnego przeznaczenia w wersji 2 | Hierarchiczny (system plików) | Obiekt blob | Standardowa (Standard) | Gorąca, chłodna, archiwalna | 3.6 + | Wszystkie z wyjątkiem platformy Spark 2,1 i 2,2|
|Azure Storage| Ogólnego przeznaczenia w wersji 2 | Obiekt | Obiekt blob | Standardowa (Standard) | Gorąca, chłodna, archiwalna | 3.6 + | Wszystko |
|Azure Storage| Ogólnego przeznaczenia w wersji 1 | Obiekt | Obiekt blob | Standardowa (Standard) | Brak | Wszystko | Wszystko |
|Azure Storage| Blob Storage * * | Obiekt | Blokowy obiekt BLOB | Standardowa (Standard) | Gorąca, chłodna, archiwalna | Wszystko | Wszystko |
|Usługa Azure Data Lake Storage 1. generacji| Nie dotyczy | Hierarchiczny (system plików) | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | tylko 3,6 | Wszystkie z wyjątkiem HBase |
|Azure Storage| Blokowy obiekt BLOB| Obiekt | Blokowy obiekt BLOB | Premium | Nie dotyczy| 3.6 + | Tylko HBase z przyspieszonymi zapisami|
|Usługa Azure Data Lake Storage 2. generacji| Blokowy obiekt BLOB| Hierarchiczny (system plików) | Blokowy obiekt BLOB | Premium | Nie dotyczy| 3.6 + | Tylko HBase z przyspieszonymi zapisami|

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
| 4,0 | Data Lake Storage Gen1 | Dowolne | Nie |
| 4,0 | Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 | Data Lake Storage Gen1 | Nie |

* = Może to być jeden lub wiele Data Lake Storage Gen2, pod warunkiem, że są one skonfigurowane do używania tej samej tożsamości zarządzanej na potrzeby dostępu do klastra.

> [!NOTE]
> Magazyn podstawowy Data Lake Storage Gen2 nie jest obsługiwany w przypadku klastrów Spark 2,1 lub 2,2.

## <a name="data-replication"></a>Replikacja danych

Usługa Azure HDInsight nie przechowuje danych klienta. Podstawowym środkiem magazynu dla klastra są skojarzone konta magazynu. Klaster można dołączyć do istniejącego konta magazynu lub utworzyć nowe konto magazynu podczas procesu tworzenia klastra. Jeśli zostanie utworzone nowe konto, zostanie ono utworzone jako konto magazynu lokalnie nadmiarowy (LRS) i będzie spełniać wymagania dotyczące miejsca zamieszkania danych w regionie, w tym te określone w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net).

Możesz sprawdzić, czy Usługa HDInsight została prawidłowo skonfigurowana do przechowywania danych w jednym regionie, upewniając się, że konto magazynu skojarzone z usługą HDInsight to LRS lub inną opcję magazynu podaną w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net).
 
## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Storage w usłudze HDInsight](./overview-azure-storage.md)
* [Przegląd Azure Data Lake Storage Gen1 w usłudze HDInsight](./overview-data-lake-storage-gen1.md)
* [Przegląd Azure Data Lake Storage Gen2 w usłudze HDInsight](./overview-data-lake-storage-gen2.md)
* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
