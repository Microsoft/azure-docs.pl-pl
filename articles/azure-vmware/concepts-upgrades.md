---
title: Koncepcje — aktualizacje i uaktualnienia w chmurze prywatnej
description: Dowiedz się więcej na temat podstawowych procesów uaktualniania i funkcji w rozwiązaniu VMware platformy Azure.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6fb65a0e192fbf1f054857223b3fdaf58fc6a903
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809653"
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

Następnym krokiem jest [utworzenie chmury prywatnej](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
