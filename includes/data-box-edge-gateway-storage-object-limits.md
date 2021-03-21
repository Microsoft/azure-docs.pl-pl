---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183684"
---
Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie przekazane pliki są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit przekazywania                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt BLOB        | ~ 4,75 TB                                                 |
| Obiekt BLOB strony         | 1 TB <br> Każdy plik przekazany w formacie obiektu BLOB musi być 512 bajtami wyrównanymi (wiele całkowitej), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> Dysk VHD i VHDX są wyrównane do 512 bajtów. |
| Azure Files         | 1 TB <br> Każdy plik przekazany w formacie obiektu BLOB musi być 512 bajtami wyrównanymi (wiele całkowitej), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> Dysk VHD i VHDX są wyrównane do 512 bajtów. |

> [!IMPORTANT]
> Tworzenie plików (niezależnie od typu magazynu) jest dozwolone do 5 TB. Jeśli jednak utworzysz plik, którego rozmiar jest większy niż limit przekazywania zdefiniowany w poprzedniej tabeli, plik nie zostanie przekazany. Należy ręcznie usunąć plik w celu Odbierz miejsce.