---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706053"
---
Poniżej znajduje się lista obsługiwanych kont magazynu i typów magazynów dla urządzenia urządzenie Data Box. Aby uzyskać pełną listę wszystkich funkcji dla wszystkich typów kont magazynu, zobacz [typy kont magazynu](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

W przypadku zamówień importu Poniższa tabela zawiera listę obsługiwanych kont magazynu.

| **Konto magazynu/obsługiwane typy magazynów** | **Blokowy obiekt blob** |**Obiekt BLOB strony** _ |_ *Plików platformy Azure** |**Uwagi**|
| --- | --- | -- | -- | -- |
| Standard klasyczny | Y | Y | Y |
| Standard do ogólnego przeznaczenia w wersji 1  | Y | Y | Y | Obsługiwane są zarówno gorąca, jak i chłodna.|
| Ogólnego przeznaczenia w wersji 1 Premium  |  | Y| | |
| Standard ogólnego przeznaczenia w wersji 2  | Y | Y | Y | Obsługiwane są zarówno gorąca, jak i chłodna.|
| Ogólnego przeznaczenia w wersji 2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |  
| BLOB Storage Standard |Y | | |Obsługiwane są zarówno gorąca, jak i chłodna. |

\**— Dane przekazane do stronicowych obiektów BLOB muszą być 512 bajtami wyrównanymi, takimi jak wirtualne dyski twarde.*

W przypadku zamówień eksportu Poniższa tabela zawiera listę obsługiwanych kont magazynu.

| **Konto magazynu/obsługiwane typy magazynów** | **Blokowy obiekt blob** |**Obiekt BLOB strony** _ |_ *Plików platformy Azure** |**Obsługiwane warstwy dostępu**|
| --- | --- | -- | -- | -- |
| Standard klasyczny | Y | Y | Y | |
| Standard do ogólnego przeznaczenia w wersji 1  | Y | Y | Y | Gorąca, chłodna|
| Ogólnego przeznaczenia w wersji 1 Premium  |  | Y| | |
| Standard ogólnego przeznaczenia w wersji 2  | Y | Y | Y | Gorąca, chłodna|
| Ogólnego przeznaczenia w wersji 2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |
| BLOB Storage Standard |Y | | |Gorąca, chłodna |
| Blokowe obiekty blob Storage Premium |Y | | |Gorąca, chłodna |
| Usługa Page BLOB Storage Premium | |Y | | |

> [!IMPORTANT]
> - W przypadku kont ogólnego przeznaczenia urządzenie Data Box nie obsługuje typów magazynu kolejek, tabel i dysków dla zamówień importu. W przypadku zamówień eksportu urządzenie Data Box nie obsługuje typów magazynu kolejek, tabel, dysków i Azure Data Lake generacji 2 dla kont ogólnego przeznaczenia.
> - Urządzenie Data Box nie obsługuje dołączania obiektów BLOB dla Blob Storage i blokowania kont Blob Storage.
> - Obsługa protokołu sieciowego systemu plików (NFS) 3,0 w usłudze Azure Blob Storage nie jest obsługiwana w przypadku urządzenie Data Box.
> - Dane przekazane do stronicowych obiektów BLOB muszą być 512 bajtami wyrównanymi, takimi jak wirtualne dyski twarde.
> - Można wyeksportować maksymalnie 80 TB.
> - Historia plików i migawki obiektów BLOB nie zostały wyeksportowane.