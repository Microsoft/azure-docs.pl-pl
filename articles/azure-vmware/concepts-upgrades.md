---
title: Koncepcje — aktualizacje i uaktualnienia w chmurze prywatnej
description: Dowiedz się więcej na temat podstawowych procesów uaktualniania i funkcji w rozwiązaniu VMware platformy Azure.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8338666d70bd0ca4ea3c6b32bd8f8d205feaf7c0
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509201"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Aktualizacje i uaktualnienia chmury prywatnej rozwiązania VMware platformy Azure

Jedną z najważniejszych zalet chmur prywatnych rozwiązań platformy Azure VMware jest to, że platforma jest utrzymywana. Obsługa platformy obejmuje automatyczne aktualizacje pakietu oprogramowania zweryfikowanego przez oprogramowanie VMware, pomagając w upewnieniu się, że korzystasz z najnowszej wersji zweryfikowanego oprogramowania platformy Azure w chmurze prywatnej.

W przypadku chmury prywatnej Azure VMware rozwiązanie to obejmuje:

- Dedykowane węzły serwera bez systemu operacyjnego obsługiwane przy użyciu funkcji hypervisor VMware ESXi 
- serwer vCenter do zarządzania ESXi i sieci vSAN 
- Oprogramowanie VMware NSX-T zdefiniowane w sieci dla maszyn wirtualnych obciążeń vSphere  
- Magazyn danych VMware sieci vSAN dla maszyn wirtualnych obciążeń vSphere  
- VMware HCX na potrzeby mobilności obciążeń  

Oprócz tych składników Chmura prywatna rozwiązania Azure VMware obejmuje zasoby w usłudze Azure underlay, które są wymagane do łączności i obsługują chmurę prywatną. Rozwiązanie VMware firmy Azure stale monitoruje kondycję zarówno underlay, jak i składników programu VMware. Gdy rozwiązanie VMware firmy Azure wykryje błąd, podejmuje działania w celu naprawy składników zakończonych niepowodzeniem. 

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

Oprócz dokonywania aktualizacji rozwiązanie VMware firmy Azure pobiera kopię zapasową konfiguracji tych składników programu VMware:

- Program vCenter Server 
- Menedżer NSX-T 

W razie awarii rozwiązanie VMware firmy Azure może przywrócić te z kopii zapasowej konfiguracji. 

Aby uzyskać więcej informacji na temat wersji oprogramowania VMware, zapoznaj się z [artykułem dotyczącym chmur prywatnych i klastrów](concepts-private-clouds-clusters.md) oraz [często zadawanych pytań](faq.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały omówione podstawowe procesy uaktualniania i funkcje w rozwiązaniu VMware platformy Azure, warto zapoznać się z tematem:

- [Jak utworzyć chmurę prywatną](tutorial-create-private-cloud.md).
- [Jak włączyć zasób rozwiązania VMware dla platformy Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
