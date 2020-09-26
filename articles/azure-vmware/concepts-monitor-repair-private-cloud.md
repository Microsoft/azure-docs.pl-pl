---
title: Pojęcia — monitorowanie i naprawa prywatnych chmur rozwiązań platformy Azure VMware
description: Dowiedz się, jak rozwiązanie VMware firmy Azure monitoruje i naprawia serwery VMware ESXi w chmurze prywatnej rozwiązania Azure VMware.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: bee08304cd14f2aeec6995203638b5a37d9a861c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346235"
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

Dowiedz się więcej na temat [uaktualnień chmur prywatnych w usłudze Azure VMware](concepts-upgrades.md).  
