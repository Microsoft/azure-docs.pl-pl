---
title: Skonfiguruj operacje vRealize dla rozwiązania VMware platformy Azure
description: Dowiedz się, jak skonfigurować operacje vRealize dla chmury prywatnej rozwiązania Azure VMware.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: c2470ecde0874b46da1236ca6e99e6b0b3eb990d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880695"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Skonfiguruj operacje vRealize dla rozwiązania VMware platformy Azure


vRealize Operations Manager to platforma zarządzania operacjami, która umożliwia administratorom infrastruktury VMware monitorowanie zasobów systemowych. Te zasoby systemowe mogą być obiektami poziomu aplikacji lub infrastruktury (zarówno fizycznymi, jak i wirtualnymi). Większość administratorów VMware użyła operacji vRealize do monitorowania składników chmur prywatnych VMware i zarządzania nimi — vCenter, ESXi, NSX-T, sieci vSAN i VMware HCX.  Każda obsługiwana Chmura prywatna rozwiązania Azure VMware obejmuje dedykowane wdrożenie vCenter, NSX-T, sieci vSAN i HCX. 

Uważnie zapoznaj [się z wcześniejszym](#before-you-begin) rozpoczęciem i [wymaganiami wstępnymi](#prerequisites) . Następnie przeprowadzimy Cię przez dwa typowe topologie wdrażania:

> [!div class="checklist"]
> * [Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Operacje vRealize uruchomione w ramach wdrożenia rozwiązania VMware firmy Azure](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Zapoznaj się z [dokumentacją produktu vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby dowiedzieć się więcej o wdrażaniu operacji vRealize. 
* Zapoznaj się z podstawową [serią samouczka](tutorial-network-checklist.md)usługi Azure VMware Software-Defined Datacenter (SDDC).
* Opcjonalnie zapoznaj się z dokumentacją dotyczącą [zdalnego kontrolera usług VRealize Operations](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) Manager, aby uzyskać dostęp do lokalnych operacji vRealize zarządzanie wdrożeniem rozwiązań VMware platformy Azure. 


## <a name="prerequisites"></a>Wymagania wstępne
* [vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .
* Sieć VPN lub usługa Azure ExpressRoute skonfigurowana między środowiskiem lokalnym i rozwiązaniem Azure VMware SDDC.
* Chmura prywatna rozwiązania Azure VMware została wdrożona na platformie Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure
Większość klientów ma istniejące lokalne wdrożenie operacji vRealize do zarządzania jedną lub wieloma lokalnymi domenami vCenter. Gdy udostępniają chmurę prywatną rozwiązania Azure VMware, nawiązują połączenie ze środowiskiem lokalnym z ich chmurą prywatną przy użyciu rozwiązania Azure ExpressRoute lub sieci VPN w warstwie 3.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure"  border="false":::

Aby zwiększyć możliwości operacji vRealize do chmury prywatnej rozwiązania Azure VMware, należy utworzyć [wystąpienie karty dla zasobów chmury prywatnej](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Zbiera dane z chmury prywatnej rozwiązania VMware platformy Azure i przenosi je do lokalnych operacji vRealize. Lokalne wystąpienie Operations Manager vRealize może bezpośrednio łączyć się z menedżerem vCenter i NSX-T w rozwiązaniu VMware platformy Azure. Opcjonalnie można wdrożyć zdalny moduł zbierający operacje vRealize w chmurze prywatnej rozwiązania Azure VMware. Moduł zbierający kompresuje i szyfruje dane zbierane z chmury prywatnej przed ich wysłaniem przez sieć ExpressRoute lub sieci VPN do vRealize Operations Manager działających lokalnie. 

> [!TIP]
> Zapoznaj się z [dokumentacją programu VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby zapoznać się z przewodnikiem krok po kroku dotyczący instalowania vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Operacje vRealize uruchomione w ramach wdrożenia rozwiązania VMware firmy Azure

Innym rozwiązaniem jest wdrożenie wystąpienia vRealize Operations Manager w klastrze vSphere w chmurze prywatnej. 

>[!IMPORTANT]
>Ta opcja nie jest obecnie obsługiwana przez oprogramowanie VMware.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operacje vRealize uruchomione w rozwiązaniu VMware platformy Azure" border="false":::

Po wdrożeniu wystąpienia można skonfigurować operacje vRealize do zbierania danych z programu vCenter, ESXi, NSX-T, sieci vSAN i HCX. 



## <a name="known-limitations"></a>Znane ograniczenia

- **Cloudadmin \@ vSphere. Local** User in Azure VMware ma [ograniczone uprawnienia](concepts-role-based-access-control.md).  Maszyny wirtualne na platformie Azure VMware nie obsługują zbierania pamięci gościa przy użyciu narzędzi VMware.  Użycie aktywnego i zużytej pamięci w tym przypadku nadal działa.
- Optymalizacja obciążenia dla zamiaru biznesowego opartego na hoście nie działa, ponieważ rozwiązania VMware platformy Azure zarządzają konfiguracjami klastra, w tym ustawieniami DRS.
- Optymalizacja obciążeń dla rozmieszczenia między klastrami w ramach SDDC przy użyciu zamiaru biznesowego opartego na klastrze jest w pełni obsługiwana w przypadku vRealize Operations Manager 8,0 i do wewnątrz. Jednak Optymalizacja obciążenia nie ma informacji o pulach zasobów i umieszcza maszyny wirtualne na poziomie klastra. Użytkownik może ręcznie rozwiązać ten problem w vCenter Server interfejsie na platformie Azure VMware.
- Nie można zalogować się do usługi vRealize Operations Manager przy użyciu poświadczeń vCenter Server Azure VMware. 
- Rozwiązanie VMware firmy Azure nie obsługuje wtyczki vRealize Operations Manager.

Po nawiązaniu połączenia z usługą Azure VMware Solution vCenter do vRealize Operations Manager przy użyciu konta w chmurze vCenter Server zostanie wyświetlone ostrzeżenie:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Tworzenie wystąpienia karty ostrzeżeń powiodło się":::

Ostrzeżenie występuje, ponieważ **cloudadmin \@ vSphere. Local** User in Azure VMware rozwiązanie nie ma wystarczających uprawnień, aby wykonać wszystkie czynności vCenter Server wymagane do rejestracji. Jednak uprawnienia są wystarczające dla wystąpienia karty do zbierania danych, jak pokazano poniżej:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Wystąpienie karty do przeprowadzenia zbierania danych":::

Aby uzyskać więcej informacji, zobacz [uprawnienia wymagane do konfigurowania wystąpienia adaptera vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




