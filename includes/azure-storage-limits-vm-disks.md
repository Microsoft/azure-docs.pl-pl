---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334962"
---
Do maszyny wirtualnej platformy Azure można dołączyć wiele dysków z danymi. Na podstawie celów skalowalności i wydajności dla dysków danych maszyny wirtualnej można określić liczbę i typ dysku, który trzeba spełnić wymagania dotyczące wydajności i pojemności.

> [!IMPORTANT]
> Aby zapewnić optymalną wydajność, należy ograniczyć liczbę dysków o wysokiej dostępności podłączonych do maszyny wirtualnej, aby uniknąć możliwego ograniczenia przepustowości. Jeśli wszystkie dołączone dyski nie są wysoce wykorzystywane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

**W przypadku usługi Azure Managed disks:**

W poniższej tabeli przedstawiono domyślne i maksymalne limity liczby zasobów na region na subskrypcję. Nie ma żadnego limitu liczby Managed Disks, migawek i obrazów na grupę zasobów.  

> | Zasób | Limit |
> | --- | --- |
> | Dyski zarządzane w warstwie Standardowa | 50 000 |
> | SSD w warstwie Standardowa Managed disks | 50 000 |
> | Dyski zarządzane w warstwie Premium | 50 000 |
> | Migawki Standard_LRS | 50 000 |
> | Migawki Standard_ZRS | 50 000 |
> | Obraz zarządzany | 50 000 |

* **W przypadku kont magazynu w warstwie Standardowa:** Maksymalna łączna liczba żądań w ramach konta magazynu w warstwie Standardowa wynosi 20 000 operacji we/wy na sekundę. Łączna liczba operacji we/wy na wszystkich dyskach maszyny wirtualnej na koncie magazynu w warstwie Standardowa nie powinna przekraczać tego limitu.
  
    Można w przybliżeniu obliczyć liczbę dysków o wysokiej dostępności obsługiwanych przez pojedyncze konto magazynu w warstwie Standardowa na podstawie limitu liczby żądań. Na przykład w przypadku maszyny wirtualnej warstwy Podstawowa Maksymalna liczba dysków o wysokiej dostępności wynosi około 66, czyli 20000/300 operacji we/wy na dysku. Maksymalna liczba dysków o wysokiej dostępności dla maszyny wirtualnej warstwy Standardowa to około 40, czyli 20000/500 operacji we/wy na dysku. 

* **Dla kont usługi Premium Storage:** Konto magazynu w warstwie Premium ma maksymalną łączną stawkę przepływności wynoszącą 50 GB/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

