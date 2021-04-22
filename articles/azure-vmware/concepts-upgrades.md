---
title: Pojęcia — aktualizacje i uaktualnienia chmury prywatnej
description: Dowiedz się więcej na temat procesów i funkcji uaktualniania kluczy w Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ced5832a6d994f6cbc7e659d44ce4f6ac88681d0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876818"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware Solution aktualizacji i uaktualnień chmury prywatnej

Jedną z zalet Azure VMware Solution chmur prywatnych jest to, że platforma jest utrzymywana dla Ciebie. Konserwacja obejmuje automatyczne aktualizacje pakietu oprogramowania zweryfikowanego przez oprogramowanie VMware, aby upewnić się, że używasz najnowszej wersji oprogramowania Azure VMware Solution w chmurze prywatnej.

W szczególności chmura Azure VMware Solution prywatna obejmuje:

- Dedykowane węzły serwera bez systemu plików aprowizowane VMware ESXi funkcji hypervisor 
- Serwer vCenter do zarządzania sieciami ESXi i vSAN 
- Sieć zdefiniowana programowo VMware NSX-T dla maszyn wirtualnych obciążeń vSphere  
- Magazyn danych VMware vSAN dla maszyn wirtualnych obciążeń vSphere  
- VMware HCX na potrzeby mobilności obciążeń  

Chmura Azure VMware Solution prywatna obejmuje również zasoby w ramach underlay platformy Azure wymagane do łączności i obsługi chmury prywatnej. Azure VMware Solution stale monitoruje kondycję składników oprogramowania VMware i składników oprogramowania VMware. Gdy Azure VMware Solution wykryje awarię, podejmuje działania w celu naprawienia uszkodzonych składników. 

## <a name="what-components-get-updated"></a>Jakie składniki są aktualizowane?   

Azure VMware Solution aktualizuje następujące składniki VMware: 

- vCenter Server i ESXi uruchomione na węzłach serwera bez systemu plików 
- Vsan 
- NSX-T 

Azure VMware Solution także oprogramowanie w ramach oprogramowania, takie jak sterowniki, oprogramowanie w przełącznikach sieciowych i oprogramowanie układowe w węzłach bez systemu operacyjnego. 

## <a name="types-of-updates"></a>Typy aktualizacji

Azure VMware Solution stosuje następujące typy aktualizacji składników VMware:

- Poprawki: poprawki zabezpieczeń i poprawki błędów wydane przez oprogramowanie VMware. 
- Aktualizacje: aktualizacje wersji pomocniczej jednego lub większej liczby składników VMware. 
- Uaktualnienia: aktualizacje wersji głównych jednego lub większej liczby składników VMware.

Otrzymasz powiadomienie przed zastosowaniem poprawek do chmur prywatnych i po ich zastosowaniu. Będziemy również współpracować z Tobem, aby zaplanować okno obsługi przed zastosowaniem aktualizacji lub uaktualnień do chmury prywatnej. 

## <a name="vmware-appliance-backup"></a>Kopia zapasowa urządzenia VMware 

Azure VMware Solution również kopii zapasowej konfiguracji następujących składników VMware:

- Program vCenter Server 
- NSX-T Manager 

W czasie awarii program Azure VMware Solution przywrócić te składniki z kopii zapasowej konfiguracji. 

## <a name="vmware-software-versions"></a>Wersje oprogramowania VMware
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały już uwzględnione kluczowe procesy uaktualniania i funkcje w programie Azure VMware Solution, możesz dowiedzieć się więcej na temat:

- [Jak utworzyć chmurę prywatną](tutorial-create-private-cloud.md)
- [Jak włączyć Azure VMware Solution zasobów](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
