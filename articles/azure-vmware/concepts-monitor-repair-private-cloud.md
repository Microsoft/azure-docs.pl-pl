---
title: Pojęcia — monitorowanie i naprawa prywatnych chmur rozwiązań platformy Azure VMware
description: Dowiedz się, jak rozwiązanie VMware firmy Azure monitoruje i naprawia serwery VMware ESXi w chmurze prywatnej rozwiązania Azure VMware.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633142"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorowanie i naprawa chmur prywatnych rozwiązania Azure VMware

Rozwiązanie VMware firmy Azure stale monitoruje VMware ESXi serwerów w chmurze prywatnej rozwiązania Azure VMware. 

## <a name="what-azure-vmware-solution-monitors"></a>Co to są monitory rozwiązań VMware platformy Azure

Rozwiązanie VMware firmy Azure monitoruje następujące warunki na hoście:  

- Stan procesora 
- Stan pamięci 
- Stan połączenia i zasilacza 
- Stan wentylatoru sprzętowego 
- Utrata łączności sieciowej 
- Stan płyty sprzętowej sprzętu 
- Wystąpiły błędy na dyskach hosta sieci vSAN 
- Napięcie sprzętowe 
- Stan temperatury sprzętu 
- Stan zasilacza sprzętowego 
- Stan magazynu 
- Błąd połączenia 

> [!NOTE]
> Administratorzy dzierżawy rozwiązań VMware platformy Azure nie mogą edytować ani usuwać powyższych zdefiniowanych danych alarmowych programu VMware vCenter, ponieważ są one zarządzane przez płaszczyznę kontroli rozwiązań VMware platformy Azure w programie vCenter. Te alarmy są używane przez monitorowanie rozwiązań VMware platformy Azure do wyzwalania procesu korygowania hostów rozwiązań VMware platformy Azure.

## <a name="azure-vmware-solution-host-remediation"></a>Korygowanie hosta rozwiązań VMware platformy Azure  

Gdy rozwiązanie VMware firmy Azure wykryje spadek wydajności lub niepowodzenie w węźle rozwiązania Azure VMware, wyzwala proces korygowania hosta. Korygowanie hosta obejmuje zastąpienie uszkodzonego węzła nowym węzłem w dobrej kondycji.  

Korygowanie hosta zostanie rozpoczęte przez dodanie nowego węzła w dobrej kondycji w klastrze. Gdy jest to możliwe, wadliwy host zostanie umieszczony w trybie konserwacji VMware vSphere. Program VMware vMotion przenosi maszyny wirtualne na uszkodzony Host do innych dostępnych serwerów w klastrze, co może spowodować zero przestoju w przypadku migracji na żywo obciążeń. Jeśli uszkodzony host nie może zostać umieszczony w trybie konserwacji, host zostanie usunięty z klastra.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się ze sposobem, w jaki rozwiązanie VMware firmy Azure monitoruje i naprawia chmurę prywatną, warto zapoznać się z tematem:

- [Uaktualnianie chmury prywatnej rozwiązań VMware platformy Azure](concepts-upgrades.md).
- [Jak włączyć zasób rozwiązania VMware dla platformy Azure](enable-azure-vmware-solution.md).
