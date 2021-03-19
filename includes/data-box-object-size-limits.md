---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88655207"
---
Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie przekazane pliki są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 4,75 TiB                                                 |
| Stronicowy obiekt blob         | 8 TiB <br> Każdy plik przekazany w formacie obiektu BLOB musi być 512 bajtami wyrównanymi (wiele całkowitej), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> Pliki VHD i VHDX są wyrównane do 512 bajtów. |
| Azure Files        | 1 TiB                                                      |
| Dyski zarządzane     | 4 TiB <br> Aby uzyskać więcej informacji na temat rozmiaru i limitów, zobacz: <li>[Cele skalowalności standardowego dysków SSD](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Cele skalowalności w warstwie Premium dysków SSD](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[Cele skalowalności standardowego HDD](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[Cennik i rozliczenia dysków zarządzanych](../articles/virtual-machines/disks-types.md#billing)</li>  
