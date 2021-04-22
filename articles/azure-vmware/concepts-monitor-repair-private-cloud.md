---
title: Pojęcia — monitorowanie i naprawianie Azure VMware Solution chmur prywatnych
description: Dowiedz się, Azure VMware Solution monitoruje i naprawia serwery VMware ESXi w chmurze Azure VMware Solution prywatnej.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 9fa94d6093e03432f20672ecf5c0160ce479e175
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871673"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorowanie i naprawianie Azure VMware Solution chmur prywatnych

Azure VMware Solution stale monitoruje serwery VMware ESXi w Azure VMware Solution prywatnej. 

## <a name="what-azure-vmware-solution-monitors"></a>Co Azure VMware Solution monitory

Azure VMware Solution monitoruje następujące warunki na hoście:  

- Stan procesora 
- Stan pamięci 
- Stan połączenia i zasilania 
- Stan wentylatora sprzętu 
- Utrata łączności sieciowej 
- Stan tablicy systemowej sprzętu 
- Wystąpiły błędy na dyskach hosta vSAN 
- Napięcie sprzętu 
- Stan temperatury sprzętu 
- Stan zasilania sprzętu 
- Stan magazynu 
- Błąd połączenia 

> [!NOTE]
> Azure VMware Solution administratorzy dzierżawy nie mogą edytować ani usuwać powyższych zdefiniowanych alarmów programu VMware vCenter, ponieważ są one zarządzane przez płaszczyznę sterowania Azure VMware Solution w programie vCenter. Te alarmy są używane przez Azure VMware Solution do wyzwalania Azure VMware Solution procesu korygowania hosta.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware Solution korygowania hosta  

Gdy Azure VMware Solution wykryje spadek lub awarię w węźle Azure VMware Solution, wyzwala proces korygowania hosta. Korygowanie hosta obejmuje zastąpienie uszkodzonego węzła nowym węzłem w dobrej kondycji.  

Korygowanie hosta rozpoczyna się od dodania nowego węzła w dobrej kondycji w klastrze. Następnie, gdy jest to możliwe, uszkodzony host jest umieszczany w trybie VMware vSphere konserwacji. Program VMware vMotion przenosi maszyny wirtualne z uszkodzonego hosta na inne dostępne serwery w klastrze, co potencjalnie pozwala na brak przestojów w migracji obciążeń na żywo. Jeśli uszkodzony host nie może zostać umieszczony w trybie konserwacji, zostanie usunięty z klastra.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak Azure VMware Solution i naprawia chmury prywatne, możesz dowiedzieć się więcej na temat:

- [Azure VMware Solution chmury prywatnej](concepts-upgrades.md)
- [Jak włączyć Azure VMware Solution zasobów](enable-azure-vmware-solution.md)
