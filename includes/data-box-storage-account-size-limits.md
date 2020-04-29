---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75467942"
---
Poniżej przedstawiono limity rozmiaru danych kopiowanych na konto magazynu. Upewnij się, że przekazane dane są zgodne z tymi limitami. Aby uzyskać najbardziej aktualne informacje dotyczące tych limitów, zobacz [elementy docelowe skalowalności i wydajności dla magazynu obiektów BLOB](../articles/storage/blobs/scalability-targets.md) i [Azure Files skalowalności i wydajności](../articles/storage/files/storage-files-scale-targets.md).

| Rozmiar danych kopiowanych na konto usługi Azure Storage                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokowy obiekt BLOB i stronicowe obiekty blob                                            | 2 PB dla Stanów Zjednoczonych i Europy.<br>500 TB dla wszystkich innych regionów, które obejmują Zjednoczone Królestwo.  <br> Obejmuje to dane ze wszystkich źródeł, w tym urządzenie Data Box.|
| Azure Files                                                          | 5 TB na udział.<br> Wszystkie foldery w obszarze *StorageAccount_AzureFiles* muszą być zgodne z tym limitem.       |
