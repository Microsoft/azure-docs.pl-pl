---
title: Wyszukaj w Azure Data Lake Storage Gen2 (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować zawartość i metadane w Azure Data Lake Storage Gen2. Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 51c32501015a16819ba9316272ed0d522e1083d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545568"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indeksowanie dokumentów w Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Obsługa Azure Data Lake Storage Gen2 jest obecnie dostępna w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Możesz zażądać dostępu do wersji zapoznawczych, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). [Interfejs API REST w wersji 2020-06-30 — wersja zapoznawcza](search-api-preview.md) i Portal zapewniają tę funkcję. Obecnie nie ma obsługi zestawu SDK platformy .NET.


Podczas konfigurowania konta usługi Azure Storage można włączyć [hierarchiczną przestrzeń nazw](../storage/blobs/data-lake-storage-namespace.md). Dzięki temu zbieranie zawartości na koncie będzie zorganizowane w hierarchii katalogów i podkatalogów zagnieżdżonych. Włączenie hierarchicznej przestrzeni nazw umożliwia włączenie [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

W tym artykule opisano, jak rozpocząć pracę z dokumentami indeksowanymi, które znajdują się w Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Konfigurowanie indeksatora Azure Data Lake Storage Gen2

Istnieje kilka kroków, które należy wykonać, aby zindeksować zawartość z Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Krok 1. Rejestracja w celu uzyskania podglądu

Utwórz konto w wersji zapoznawczej indeksatora Data Lake Storage Gen2, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). Po zaakceptowaniu w wersji zapoznawczej otrzymasz wiadomość e-mail z potwierdzeniem.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Krok 2. wykonanie kroków konfiguracyjnych indeksowania magazynu obiektów blob platformy Azure

Po otrzymaniu potwierdzenia, że rejestrowanie w wersji zapoznawczej zakończyło się pomyślnie, możesz utworzyć potok indeksowania.

Zawartość i metadane można indeksować z Data Lake Storage Gen2 przy użyciu [interfejsu API REST w wersji 2020-06-30-Preview](search-api-preview.md) lub portalu. W tej chwili nie ma obsługi zestawu SDK platformy .NET.

Indeksowanie zawartości w Data Lake Storage Gen2 jest takie samo jak indeksowanie zawartości w usłudze Azure Blob Storage. Aby zrozumieć, jak skonfigurować Data Lake Storage Gen2 źródło danych, indeks i indeksator, zapoznaj się z [tematem jak indeksować dokumenty w usłudze azure BLOB Storage za pomocą usługi azure wyszukiwanie poznawcze](search-howto-indexing-azure-blob-storage.md). Artykuł magazynu obiektów BLOB zawiera również informacje o obsługiwanych formatach dokumentów, właściwości metadanych obiektów blob, które są wyodrębniane, indeksowanie przyrostowe i inne. Te informacje będą takie same dla Data Lake Storage Gen2.

## <a name="access-control"></a>Kontrola dostępu

Azure Data Lake Storage Gen2 implementuje [model kontroli dostępu](../storage/blobs/data-lake-storage-access-control.md) , który obsługuje zarówno kontrolę dostępu opartą na rolach (Azure RBAC), jak i oparte na systemie POSIX listy kontroli dostępu (ACL). Podczas indeksowania zawartości z Data Lake Storage Gen2 usługa Azure Wyszukiwanie poznawcze nie wyodrębni z zawartości informacji RBAC i listy ACL platformy Azure. W związku z tym te informacje nie zostaną uwzględnione w indeksie usługi Azure Wyszukiwanie poznawcze.

Jeśli zachowanie kontroli dostępu dla każdego dokumentu w indeksie jest ważne, to Deweloper aplikacji może wdrożyć [przycinanie zabezpieczeń](./search-security-trimming-for-azure-search.md).

## <a name="change-detection"></a>Wykrywanie zmian

Indeksator Data Lake Storage Gen2 obsługuje wykrywanie zmian. Oznacza to, że gdy indeksator uruchamia program, tylko ponownie indeksuje zmienione obiekty blob zgodnie z `LastModified` sygnaturą czasową obiektu BLOB.

> [!NOTE] 
> Data Lake Storage Gen2 zezwala na zmianę nazwy katalogów. Po zmianie nazwy katalogu sygnatury czasowe obiektów BLOB w tym katalogu nie zostaną zaktualizowane. W związku z tym indeksator nie będzie ponownie indeksować tych obiektów BLOB. Jeśli obiekty blob w katalogu mają być ponownie indeksowane po zmianie nazwy katalogu, ponieważ teraz mają nowe adresy URL, należy zaktualizować `LastModified` sygnaturę czasową dla wszystkich obiektów BLOB w katalogu, aby indeksator mógł je ponownie indeksować w przyszłości.