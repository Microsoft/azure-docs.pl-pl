---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 25c2ea04cd062554a975c63aae9b97846e646d68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025649"
---
Poniżej znajduje się lista obsługiwanych kont magazynu i typów magazynów dla urządzenia urządzenie Data Box. Aby zapoznać się z pełną listą różnych typów kont magazynu i ich pełnych możliwości, zobacz [typy kont magazynu](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

W przypadku zamówień importu Poniższa tabela zawiera listę obsługiwanych kont magazynu.

| **Konto magazynu/obsługiwane typy magazynów** | **Blokowy obiekt blob** |**Obiekt BLOB strony** _ |_ *Plików platformy Azure** |**Uwagi**|
| --- | --- | -- | -- | -- |
| Standard klasyczny | T | T | T |
| Standard do ogólnego przeznaczenia w wersji 1  | T | T | T | Obsługiwane są zarówno gorąca, jak i chłodna.|
| Ogólnego przeznaczenia w wersji 1 Premium  |  | T| | |
| Standard ogólnego przeznaczenia w wersji 2  | T | T | T | Obsługiwane są zarówno gorąca, jak i chłodna.|
| Ogólnego przeznaczenia w wersji 2 Premium  |  |T | | |
| BLOB Storage Standard |T | | |Obsługiwane są zarówno gorąca, jak i chłodna. |

\**— Dane przekazane do stronicowych obiektów BLOB muszą być 512 bajtami wyrównanymi, takimi jak wirtualne dyski twarde.*

W przypadku zamówień eksportu Poniższa tabela zawiera listę obsługiwanych kont magazynu.

| **Konto magazynu/obsługiwane typy magazynów** | **Blokowy obiekt blob** |**Obiekt BLOB strony** _ |_ *Plików platformy Azure** |**Obsługiwane warstwy dostępu**|
| --- | --- | -- | -- | -- |
| Standard klasyczny | T | T | T | |
| Standard do ogólnego przeznaczenia w wersji 1  | T | T | T | Gorąca, chłodna|
| Ogólnego przeznaczenia w wersji 1 Premium  |  | T| | |
| Standard ogólnego przeznaczenia w wersji 2  | T | T | T | Gorąca, chłodna|
| Ogólnego przeznaczenia w wersji 2 Premium  |  |T | | |
| BLOB Storage Standard |T | | |Gorąca, chłodna |
| Blokowe obiekty blob Storage Premium |T | | |Gorąca, chłodna |
| Usługa Page BLOB Storage Premium | |T | | |

> [!IMPORTANT]
> - W przypadku kont ogólnego przeznaczenia urządzenie Data Box nie obsługuje typów magazynu kolejek, tabel i dysków dla zamówień importu. W przypadku zamówień eksportu urządzenie Data Box nie obsługuje typów magazynu kolejek, tabel, dysków i Azure Data Lake generacji 2 dla kont ogólnego przeznaczenia.
> - Urządzenie Data Box nie obsługuje dołączania obiektów BLOB dla Blob Storage i blokowania kont Blob Storage.
> - Urządzenie Data Box nie obsługuje kont File Storage Premium.
> - Dane przekazane do stronicowych obiektów BLOB muszą być 512 bajtami wyrównanymi, takimi jak wirtualne dyski twarde.
> - Można wyeksportować maksymalnie 80 TB.
> - Historia plików i migawki obiektów BLOB nie zostały wyeksportowane.