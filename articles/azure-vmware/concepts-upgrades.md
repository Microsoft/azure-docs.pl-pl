---
title: Koncepcje — aktualizacje i uaktualnienia w chmurze prywatnej
description: Dowiedz się więcej na temat podstawowych procesów uaktualniania i funkcji w rozwiązaniu VMware platformy Azure.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9810de40944f70a4efb7ec81d17868ffdf256c7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586152"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Aktualizacje i uaktualnienia chmury prywatnej rozwiązania VMware platformy Azure

Jedną z korzyści związanych z chmurą prywatną rozwiązań Azure VMware jest to, że platforma jest obsługiwana. Konserwacja obejmuje zautomatyzowane aktualizacje pakietu oprogramowania VMware zweryfikowanego w celu zapewnienia, że korzystasz z najnowszej wersji oprogramowania Azure VMware w chmurze prywatnej.

W przypadku chmury prywatnej Azure VMware rozwiązanie to obejmuje:

- Dedykowane węzły serwera bez systemu operacyjnego obsługiwane przy użyciu funkcji hypervisor VMware ESXi 
- serwer vCenter do zarządzania ESXi i sieci vSAN 
- Oprogramowanie VMware NSX-T zdefiniowane w sieci dla maszyn wirtualnych obciążeń vSphere  
- Magazyn danych VMware sieci vSAN dla maszyn wirtualnych obciążeń vSphere  
- VMware HCX na potrzeby mobilności obciążeń  

Chmura prywatna rozwiązania Azure VMware obejmuje również zasoby w usłudze Azure underlay, które są wymagane do łączności i obsługują chmurę prywatną. Rozwiązanie VMware firmy Azure stale monitoruje kondycję zarówno underlay, jak i składników programu VMware. Gdy rozwiązanie VMware firmy Azure wykryje błąd, podejmuje działania w celu naprawy składników zakończonych niepowodzeniem. 

## <a name="what-components-get-updated"></a>Jakie składniki są aktualizowane?   

Rozwiązanie VMware platformy Azure aktualizuje następujące składniki VMware: 

- vCenter Server i ESXi uruchomione w węzłach serwera bez systemu operacyjnego 
- Sieci vSAN 
- NSX-T 

Rozwiązanie VMware firmy Azure aktualizuje także oprogramowanie w underlay, takie jak sterowniki, oprogramowanie w przełącznikach sieciowych i oprogramowanie układowe w węzłach bez systemu operacyjnego. 

## <a name="types-of-updates"></a>Typy aktualizacji

Rozwiązanie VMware firmy Azure stosuje następujące typy aktualizacji składników oprogramowania VMware:

- Poprawki: poprawki zabezpieczeń i poprawki błędów wydane przez oprogramowanie VMware. 
- Aktualizacje: aktualizacje wersji pomocniczej co najmniej jednego składnika VMware. 
- Uaktualnienia: główne aktualizacje wersji co najmniej jednego składnika VMware.

Otrzymasz powiadomienie przed zastosowaniem poprawek do chmur prywatnych. Pomożemy również zaplanować przedział czasu obsługi przed zastosowaniem aktualizacji lub uaktualnień do chmury prywatnej. 

## <a name="vmware-appliance-backup"></a>Kopia zapasowa urządzenia VMware 

Rozwiązanie VMware firmy Azure pobiera również kopię zapasową konfiguracji następujących składników programu VMware:

- Program vCenter Server 
- Menedżer NSX-T 

W razie awarii rozwiązanie VMware firmy Azure może przywrócić te składniki z kopii zapasowej konfiguracji. 

## <a name="vmware-software-versions"></a>Wersje oprogramowania VMware
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały omówione podstawowe procesy uaktualniania i funkcje w rozwiązaniu VMware platformy Azure, warto zapoznać się z tematem:

- [Jak utworzyć chmurę prywatną](tutorial-create-private-cloud.md).
- [Jak włączyć zasób rozwiązania VMware dla platformy Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
