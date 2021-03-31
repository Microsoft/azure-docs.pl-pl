---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80334756"
---
**Niezarządzane dyski maszyny wirtualnej w warstwie Premium: limity dla kont**

| Zasób | Limit |
| --- | --- |
| Całkowita pojemność dysku na konto |35 TB |
| Całkowita pojemność migawki na konto |10 TB |
| Maksymalna przepustowość na konto (ruch przychodzący i wychodzący)<sup>1</sup> |<= 50 Gb/s |

<sup>1</sup>przychodzący *odnosi się* do wszystkich danych z żądań wysyłanych do konta magazynu. Ruch wychodzący *odnosi się* do wszystkich danych z odpowiedzi odbieranych z konta magazynu.

**Niezarządzane dyski maszyny wirtualnej w warstwie Premium: limity poszczególnych dysków**

| Typ dysku magazynu Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Rozmiar dysku |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| Maksymalna liczba operacji we/wy na dysk |500 |2300 |5000 |7500 |7500 |
| Maksymalna przepływność na dysk |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Maksymalna liczba dysków na konto magazynu |280 |70 |35 | 17 | 8 |

**Niezarządzane dyski maszyny wirtualnej w warstwie Premium: limity dla maszyn wirtualnych**

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba operacji we/wy na maszynę wirtualną |80 000 operacji we/wy z maszyną wirtualną GS5 |
| Maksymalna przepływność na maszynę wirtualną |2 000 MB/s z maszyną wirtualną GS5 |

