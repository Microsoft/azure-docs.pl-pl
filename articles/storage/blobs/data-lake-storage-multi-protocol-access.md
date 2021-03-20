---
title: Dostęp przez wiele protokołów w Azure Data Lake Storage | Microsoft Docs
description: Używaj interfejsów API obiektów blob i aplikacji, które używają interfejsów API obiektów blob z Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77914862"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Dostęp z wieloprotokołem do Azure Data Lake Storage

Interfejsy API obiektów BLOB działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. Powoduje to odblokowanie ekosystemu narzędzi, aplikacji i usług, a także kilku funkcji magazynu obiektów BLOB dla kont, które mają hierarchiczną przestrzeń nazw.

Do tej pory mogło być konieczne przechowywanie oddzielnych rozwiązań magazynu dla magazynu obiektów i magazynu analizy. Dzieje się tak, ponieważ Azure Data Lake Storage Gen2 miała ograniczoną obsługę ekosystemu. Miał także ograniczony dostęp do funkcji Blob service, takich jak rejestrowanie diagnostyczne. Pofragmentowane rozwiązanie magazynu jest trudne do utrzymania, ponieważ trzeba przenieść dane między kontami, aby osiągnąć różne scenariusze. Nie musisz już tego robić.

W przypadku dostępu z użyciem protokołu wieloprotokołowego do Data Lake Storage można korzystać z danych przy użyciu ekosystemu narzędzi, aplikacji i usług. Obejmuje to również narzędzia i aplikacje innych firm. Można wskazać te konta, które mają hierarchiczną przestrzeń nazw bez konieczności ich modyfikacji. Aplikacje te działają *podobnie jak* w przypadku wywoływania interfejsów API obiektów blob, ponieważ interfejsy API obiektów BLOB mogą teraz operować na danych na kontach z hierarchiczną przestrzenią nazw.

Funkcje magazynu obiektów blob, takie jak [Rejestrowanie diagnostyki](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem życia magazynu obiektów BLOB](storage-lifecycle-management-concepts.md) , działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utraty dostępu do tych ważnych funkcji. 

> [!NOTE]
> Dostęp do wieloprotokołowy na Data Lake Storage jest ogólnie dostępny i jest dostępny we wszystkich regionach. Niektóre funkcje usług platformy Azure lub usługi BLOB Storage obsługiwane przez wieloprotokołowy dostęp pozostają w wersji zapoznawczej.  W tych artykułach zestawiono bieżącą obsługę funkcji magazynu obiektów blob i integracji usług platformy Azure. 
>
> [Funkcje Blob Storage dostępne w programie Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak działa dostęp przez wiele protokołów w usłudze Data Lake Storage

Interfejsy API obiektów blob i interfejsy API Data Lake Storage Gen2 mogą działać na tych samych danych na kontach magazynu, które mają hierarchiczną przestrzeń nazw. Data Lake Storage Gen2 kieruje interfejsy API obiektów BLOB za pomocą hierarchicznej przestrzeni nazw, aby można było korzystać z zalet operacji katalogu pierwszej klasy i list kontroli dostępu zgodnych ze standardem POSIX (ACL). 

![Dostęp z wieloprotokołem do Data Lake Storage koncepcyjnych](./media/data-lake-storage-interop/interop-concept.png) 

Istniejące narzędzia i aplikacje korzystające z interfejsu API obiektów BLOB uzyskują te korzyści automatycznie. Deweloperzy nie będą musieli ich modyfikować. Data Lake Storage Gen2 konsekwentnie stosuje katalogi i listy ACL na poziomie plików, niezależnie od protokołu używanego przez narzędzia i aplikacje do uzyskiwania dostępu do danych. 

## <a name="see-also"></a>Zobacz też

- [Funkcje Blob Storage dostępne w programie Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Platformy Open source obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




