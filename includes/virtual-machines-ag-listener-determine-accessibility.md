---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183184"
---
Należy pamiętać, że istnieją dwa sposoby konfigurowania odbiornika grupy dostępności na platformie Azure. Metody te różnią się w zależności od typu modułu równoważenia obciążenia platformy Azure, który jest używany podczas tworzenia odbiornika. W poniższej tabeli opisano różnice:

| Typ modułu równoważenia obciążenia | Implementacja | Zastosowania: |
| --- | --- | --- |
| **Zewnętrznych** |Używa *publicznego wirtualnego adresu IP* usługi w chmurze, która hostuje maszyny wirtualne. |Musisz uzyskać dostęp do odbiornika spoza sieci wirtualnej, w tym z Internetu. |
| **Wewnętrz** |Używa *wewnętrznego modułu równoważenia obciążenia* z prywatnym adresem odbiornika. |Dostęp do odbiornika można uzyskać tylko w ramach tej samej sieci wirtualnej. Ten dostęp obejmuje sieci VPN typu lokacja-lokacja w scenariuszach hybrydowych. |

> [!IMPORTANT]
> W przypadku odbiornika korzystającego z publicznego adresu VIP usługi w chmurze (zewnętrznego modułu równoważenia obciążenia), o ile klient, odbiornik i bazy danych znajdują się w tym samym regionie świadczenia usługi Azure, nie zostaną naliczone opłaty za ruch wychodzący. W przeciwnym razie wszelkie dane zwrócone przez odbiornik są uznawane za wychodzące i jest naliczana według normalnych stawek za transfer danych. 
> 
> 

ILB można skonfigurować tylko w sieciach wirtualnych z zakresem regionalnym. Istniejące sieci wirtualne, które zostały skonfigurowane dla grupy koligacji, nie mogą używać elementu ILB. Aby uzyskać więcej informacji, zobacz temat [Omówienie wewnętrznego modułu równoważenia obciążenia](../articles/load-balancer/load-balancer-internal-overview.md).

