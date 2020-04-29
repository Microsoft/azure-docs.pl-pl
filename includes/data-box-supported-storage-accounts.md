---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66242128"
---
Poniżej znajduje się lista obsługiwanych kont magazynu i typów magazynów dla urządzenia urządzenie Data Box. Aby zapoznać się z pełną listą różnych typów kont magazynu i ich pełnych możliwości, zobacz [typy kont magazynu](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Konto magazynu/obsługiwane typy magazynów** | **Blokowy obiekt blob** |**Obiekt BLOB strony*** |**Pliki platformy Azure** |**Uwagi**|
| --- | --- | -- | -- | -- |
| Standard klasyczny | Tak | Tak | Tak |
| Standard do ogólnego przeznaczenia w wersji 1  | Tak | Tak | Tak | Obsługiwane są zarówno gorąca, jak i chłodna.|
| Ogólnego przeznaczenia w wersji 1 Premium  |  | Tak| | |
| Standard ogólnego przeznaczenia w wersji 2  | Tak | Tak | Tak | Obsługiwane są zarówno gorąca, jak i chłodna.|
| Ogólnego przeznaczenia w wersji 2 Premium  |  |Tak | | |
| BLOB Storage Standard |Tak | | |Obsługiwane są zarówno gorąca, jak i chłodna. |

\**— Dane przekazane do stronicowych obiektów BLOB muszą być 512 bajtami wyrównanymi, takimi jak wirtualne dyski twarde.*

>[!NOTE]
> Konta usługi Azure Data Lake Storage Gen 2 nie są obsługiwane.
