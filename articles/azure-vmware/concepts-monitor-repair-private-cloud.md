---
title: Pojęcia — monitorowanie i naprawa prywatnych chmur rozwiązań platformy Azure VMware
description: Dowiedz się, jak rozwiązanie VMware firmy Azure monitoruje i naprawia serwery VMware ESXi w chmurze prywatnej rozwiązania Azure VMware.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 11a3c53bff7ce7b67b677977eddb9829f336672d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684699"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorowanie i naprawa chmur prywatnych rozwiązania Azure VMware

Rozwiązanie VMware firmy Azure stale monitoruje VMware ESXi serwerów w chmurze prywatnej rozwiązania Azure VMware. 

## <a name="what-azure-vmware-solution-monitors"></a>Co to są monitory rozwiązań VMware platformy Azure

Rozwiązanie VMware firmy Azure monitoruje następujące kwestie dotyczące niepowodzeń na hoście:  

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

Gdy rozwiązanie VMware firmy Azure wykryje spadek wydajności lub niepowodzenie w węźle rozwiązania Azure VMware w chmurze prywatnej dzierżawcy, wyzwala proces korygowania hosta. Korygowanie hosta obejmuje zastąpienie uszkodzonego węzła nowym węzłem w dobrej kondycji.  

Proces korygowania hosta jest uruchamiany przez dodanie nowego węzła w dobrej kondycji w klastrze. Gdy jest to możliwe, wadliwy host zostanie umieszczony w trybie konserwacji VMware vSphere. Program VMware vMotion służy do przenoszenia maszyn wirtualnych na uszkodzonym hoście do innych dostępnych serwerów w klastrze, co może spowodować brak przestojów migracji na żywo obciążeń. W scenariuszach, w których nie można umieścić działającego hosta w trybie konserwacji, host jest usuwany z klastra.

## <a name="next-steps"></a>Następne kroki

Oto kilka tematów, dla których warto dowiedzieć się więcej na temat:

- [Uaktualnienia chmury prywatnej rozwiązań VMware platformy Azure](concepts-upgrades.md)
- [Zarządzanie cyklem życia maszyn wirtualnych rozwiązań VMware platformy Azure](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Ochrona maszyn wirtualnych rozwiązań VMware platformy Azure z integracją Azure Security Center](azure-security-integration.md)
- [Tworzenie kopii zapasowych maszyn wirtualnych rozwiązań VMware platformy Azure przy użyciu Azure Backup Server](backup-azure-vmware-solution-virtual-machines.md)
- [Ukończ odzyskiwanie po awarii maszyn wirtualnych przy użyciu rozwiązania Azure VMware](disaster-recovery-for-virtual-machines.md)
