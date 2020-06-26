---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378519"
---
Poniżej przedstawiono limity rozmiaru danych kopiowanych na konto magazynu. Upewnij się, że przekazane dane są zgodne z tymi limitami. Aby uzyskać najbardziej aktualne informacje dotyczące tych limitów, zobacz [elementy docelowe skalowalności i wydajności dla magazynu obiektów BLOB](../articles/storage/blobs/scalability-targets.md) i [Azure Files skalowalności i wydajności](../articles/storage/files/storage-files-scale-targets.md).

| Rozmiar danych kopiowanych na konto usługi Azure Storage                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokowy obiekt BLOB i stronicowe obiekty blob                                            | Maksymalny limit jest taki sam jak [limit magazynowania zdefiniowany dla subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) i zawiera dane ze wszystkich źródeł, w tym urządzenie Data Box.|
| Azure Files                                                          | Maksymalny rozmiar standardowych udziałów plików to 5 TB <br> Maksymalny rozmiar udziałów plików w warstwie Premium to 100TiB na udział.<br> Wszystkie foldery w obszarze *StorageAccount_AzureFiles* muszą być zgodne z tym limitem.       |
