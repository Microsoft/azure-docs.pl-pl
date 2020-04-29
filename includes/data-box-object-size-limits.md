---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66244654"
---
Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie przekazane pliki są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 4,75 TiB                                                 |
| Stronicowy obiekt blob         | 8 TiB <br> Każdy plik przekazany w formacie obiektu BLOB musi być 512 bajtami wyrównanymi (wiele całkowitej), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> Pliki VHD i VHDX są wyrównane do 512 bajtów. |
| Azure Files        | 1 TiB                                                      |
| Dyski zarządzane     | 4 TiB <br> Aby uzyskać więcej informacji na temat rozmiaru i limitów, zobacz: <li>[Cele skalowalności standardowego dysków SSD](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Cele skalowalności w warstwie Premium dysków SSD](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Cele skalowalności standardowego HDD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Cennik i rozliczenia dysków zarządzanych](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
