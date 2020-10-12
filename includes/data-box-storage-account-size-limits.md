---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: acaebcea59e765f5544f1bfbd692c6508f66a84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025320"
---
Poniżej przedstawiono limity rozmiaru danych kopiowanych na konto magazynu. Upewnij się, że przekazane dane są zgodne z tymi limitami. Aby uzyskać najbardziej aktualne informacje dotyczące tych limitów, zobacz [elementy docelowe skalowalności i wydajności dla magazynu obiektów BLOB](../articles/storage/blobs/scalability-targets.md) i [Azure Files skalowalności i wydajności](../articles/storage/files/storage-files-scale-targets.md).

| Rozmiar danych kopiowanych na konto usługi Azure Storage                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokowy obiekt BLOB i stronicowe obiekty blob                                            | Maksymalny limit jest taki sam jak [limit magazynowania zdefiniowany dla subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) i zawiera dane ze wszystkich źródeł, w tym urządzenie Data Box. |
| Azure Files                                                          | Urządzenie Data Box obsługuje duże udziały plików (100TiB), jeśli zostały włączone przed utworzeniem kolejności urządzenie Data Box. <br> Jeśli nie włączono przed utworzeniem zamówienia, maksymalny obsługiwany rozmiar udziału plików to 5 TiB. <br> Udziały plików w warstwie Premium nie są jeszcze obsługiwane.<br> Wszystkie foldery w obszarze *StorageAccount_AzureFiles* muszą być zgodne z tym limitem. <br> Aby uzyskać więcej informacji, zobacz [Włączanie i tworzenie dużych udziałów plików](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
