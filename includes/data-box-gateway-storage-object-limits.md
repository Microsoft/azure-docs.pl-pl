---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582642"
---
Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie przekazane pliki są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit przekazywania                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt BLOB        | ~ 4,75 TB                                                 |
| Obiekt BLOB strony         | 1 TB <br> Każdy plik przekazany w formacie obiektu BLOB musi być 512 bajtami wyrównanymi (wiele całkowitej), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> Dysk VHD i VHDX są wyrównane do 512 bajtów. |
| Azure Files         | 1 TB <br> Każdy plik przekazany w formacie obiektu BLOB musi być 512 bajtami wyrównanymi (wiele całkowitej), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> Dysk VHD i VHDX są wyrównane do 512 bajtów. |

> [!IMPORTANT]
> Tworzenie plików (niezależnie od typu magazynu) jest dozwolone do 5 TB. Jeśli jednak utworzysz plik, którego rozmiar jest większy niż limit przekazywania zdefiniowany w poprzedniej tabeli, plik nie zostanie przekazany. Należy ręcznie usunąć plik w celu Odbierz miejsce.