---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98225351"
---
Poniżej przedstawiono limity rozmiaru danych kopiowanych na konto magazynu. Upewnij się, że przekazane dane są zgodne z tymi limitami. Aby uzyskać najbardziej aktualne informacje dotyczące tych limitów, zobacz [elementy docelowe skalowalności i wydajności dla magazynu obiektów BLOB](../articles/storage/blobs/scalability-targets.md) i [Azure Files skalowalności i wydajności](../articles/storage/files/storage-files-scale-targets.md).

| Rozmiar danych kopiowanych na konto usługi Azure Storage                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokowy obiekt BLOB i stronicowe obiekty blob                                            | Maksymalny limit jest taki sam jak [limit magazynowania zdefiniowany dla subskrypcji platformy Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) i zawiera dane ze wszystkich źródeł, w tym urządzenie Data Box.   |
| Azure Files                                                          | Urządzenie Data Box obsługuje duże udziały plików (100 TiB), jeśli zostały włączone przed utworzeniem kolejności urządzenie Data Box. <br> Jeśli nie włączono przed utworzeniem zamówienia, maksymalny obsługiwany rozmiar udziału plików to 5 TiB. <br> Urządzenie Data Box obsługuje udziały plików platformy Azure w warstwie Premium, które umożliwiają całkowite 100 TiB wszystkich udziałów na koncie magazynu. <br> Maksymalna użyteczna pojemność jest nieco mniejsza ze względu na miejsce, w którym są używane kopie dzienników i dzienników inspekcji. Minimalny 100 GiB każdy jest zarezerwowany dla dziennika kopiowania i dziennika inspekcji. Aby uzyskać więcej informacji, zobacz [dzienniki inspekcji dla Azure Data Box, Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md). <br> Wszystkie foldery w obszarze *StorageAccount_AzureFiles* muszą być zgodne z tym limitem. <br> Aby uzyskać więcej informacji, zobacz [Włączanie i tworzenie dużych udziałów plików](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
