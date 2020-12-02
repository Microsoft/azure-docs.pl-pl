---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 7098f23e9b5b6f56fbbe761335afc65375aea680
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509180"
---
**Azure Data Lake Storage Gen2** nie jest dedykowanym typem konta usługi lub magazynu. Jest to Najnowsza wersja możliwości, która jest przeznaczona do analizy danych Big Data.  Te funkcje są dostępne w ramach konta magazynu ogólnego przeznaczenia w wersji 2 lub BlockBlobStorage i można je uzyskać, włączając funkcję **hierarchicznej przestrzeni nazw** konta. W przypadku elementów docelowych skalowania Zobacz te artykuły. 

- [Skalowanie obiektów docelowych dla magazynu obiektów BLOB](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage).
- [Skalowanie obiektów docelowych dla kont magazynu w warstwie Standardowa](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage Gen1** to dedykowana usługa. Jest to całe korporacyjne repozytorium skalowania na potrzeby obciążeń analitycznych danych Big Data. Data Lake Storage Gen1 służy do przechwytywania danych dowolnego rozmiaru, typu i szybkości pozyskiwania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych. Nie ma limitu ilości danych, które można przechowywać na koncie Data Lake Storage Gen1.

| **Zasób** | **Limit** | **Komentarze** |
| --- | --- | --- |
| Maksymalna liczba kont Data Lake Storage Gen1 na subskrypcję na region |10 | Aby zażądać zwiększenia dla tego limitu, skontaktuj się z pomocą techniczną. |
| Maksymalna liczba list kontroli dostępu dla pliku lub folderu |32 | Jest to sztywny limit. Używanie grup do zarządzania dostępem z mniejszą liczbą wpisów. |
| Maksymalna liczba domyślnych list kontroli dostępu (ACL) na plik lub folder |32 | Jest to sztywny limit. Używanie grup do zarządzania dostępem z mniejszą liczbą wpisów. |