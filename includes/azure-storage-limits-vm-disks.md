---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755628"
---
Do maszyny wirtualnej platformy Azure można dołączyć wiele dysków z danymi. Na podstawie celów skalowalności i wydajności dla dysków danych maszyny wirtualnej można określić liczbę i typ dysku, który trzeba spełnić wymagania dotyczące wydajności i pojemności.

> [!IMPORTANT]
> Aby zapewnić optymalną wydajność, należy ograniczyć liczbę dysków o wysokiej dostępności podłączonych do maszyny wirtualnej, aby uniknąć możliwego ograniczenia przepustowości. Jeśli wszystkie dołączone dyski nie są wysoce wykorzystywane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

**W przypadku usługi Azure Managed disks:**

W poniższej tabeli przedstawiono domyślne i maksymalne limity liczby zasobów na region na subskrypcję. Limity pozostają takie same niezależnie od dysków zaszyfrowanych przy użyciu kluczy zarządzanych przez platformę lub kluczy zarządzanych przez klienta. Nie ma żadnego limitu liczby Managed Disks, migawek i obrazów na grupę zasobów.  

> | Zasób | Limit |
> | --- | --- |
> | Dyski zarządzane w warstwie Standardowa | 50 000 |
> | SSD w warstwie Standardowa Managed disks | 50 000 |
> | Dyski zarządzane w warstwie Premium | 50 000 |
> | Migawki Standard_LRS | 50 000 |
> | Migawki Standard_ZRS | 50 000 |
> | Obraz zarządzany | 50 000 |

**W przypadku kont magazynu w warstwie Standardowa:** Maksymalna łączna liczba żądań w ramach konta magazynu w warstwie Standardowa wynosi 20 000 operacji we/wy na sekundę. Łączna liczba operacji we/wy na wszystkich dyskach maszyny wirtualnej na koncie magazynu w warstwie Standardowa nie powinna przekraczać tego limitu.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Dla kont usługi Premium Storage:** Konto magazynu w warstwie Premium ma maksymalną łączną stawkę przepływności wynoszącą 50 GB/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

