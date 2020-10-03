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
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665621"
---
**Azure Data Lake Storage Gen2** nie jest dedykowanym typem konta usługi lub magazynu. Jest to Najnowsza wersja możliwości, która jest przeznaczona do analizy danych Big Data.  Te funkcje są dostępne w ramach konta magazynu ogólnego przeznaczenia w wersji 2 lub BlockBlobStorage i można je uzyskać, włączając funkcję **hierarchicznej przestrzeni nazw** konta. W przypadku elementów docelowych skalowania Zobacz te artykuły. 

- [Skalowanie obiektów docelowych dla magazynu obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage).
- [Skalowanie obiektów docelowych dla kont magazynu w warstwie Standardowa](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage Gen1** to dedykowana usługa. Jest to całe korporacyjne repozytorium skalowania na potrzeby obciążeń analitycznych danych Big Data. Data Lake Storage Gen1 służy do przechwytywania danych dowolnego rozmiaru, typu i szybkości pozyskiwania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych. Nie ma limitu ilości danych, które można przechowywać na koncie Data Lake Storage Gen1.

| **Zasób** | **Limit** | **Komentarze** |
| --- | --- | --- |
| Maksymalna liczba kont Data Lake Storage Gen1 na subskrypcję na region |10 | Aby zażądać zwiększenia dla tego limitu, skontaktuj się z pomocą techniczną. |
| Maksymalna liczba list kontroli dostępu dla pliku lub folderu |32 | Jest to sztywny limit. Używanie grup do zarządzania dostępem z mniejszą liczbą wpisów. |
| Maksymalna liczba domyślnych list kontroli dostępu (ACL) na plik lub folder |32 | Jest to sztywny limit. Używanie grup do zarządzania dostępem z mniejszą liczbą wpisów. |
