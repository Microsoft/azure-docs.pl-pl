---
title: Skonfiguruj operacje vRealize dla rozwiązania VMware platformy Azure
description: Dowiedz się, jak skonfigurować operacje vRealize dla chmury prywatnej rozwiązania Azure VMware.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580399"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Skonfiguruj operacje vRealize dla rozwiązania VMware platformy Azure


vRealize Operations Manager to platforma zarządzania operacjami, która umożliwia administratorom infrastruktury VMware monitorowanie zasobów systemowych. Te zasoby systemowe mogą być obiektami poziomu aplikacji lub infrastruktury (zarówno fizycznymi, jak i wirtualnymi). W przeszłości większość administratorów VMware użyła operacji vRealize do monitorowania składników chmur prywatnych VMware i zarządzania nimi — vCenter, ESXi, NSX-T, sieci vSAN i hybrydowego rozszerzenia chmury (HCX). Każdy chmurę prywatną rozwiązania VMware platformy Azure jest inicjowana przy użyciu dedykowanego wdrożenia vCenter, NSX-T, sieci vSAN i HCX. 

Uważnie zapoznaj [się z wcześniejszym](#before-you-begin) rozpoczęciem i [wymaganiami wstępnymi](#prerequisites) . Następnie przeprowadzimy Cię przez dwa typowe topologie wdrażania dla vRealize Operations Manager z rozwiązaniem VMware platformy Azure:

> [!div class="checklist"]
> * [Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Operacje vRealize uruchomione w ramach wdrożenia rozwiązania VMware firmy Azure](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Zapoznaj się z [dokumentacją produktu vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby dowiedzieć się więcej o wdrażaniu vRealize operacji. 
* Zapoznaj się z podstawową [serią samouczków](tutorial-network-checklist.md)dotyczących oprogramowania VMware (SDDC).
* Opcjonalnie zapoznaj się z dokumentacją dotyczącą [zdalnego kontrolera usług VRealize Operations](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) Manager, aby uzyskać dostęp do lokalnych operacji vRealize zarządzanie wdrożeniem rozwiązań VMware platformy Azure. 



## <a name="prerequisites"></a>Wymagania wstępne
* Sieć VPN lub usługa Azure ExpressRoute należy skonfigurować między środowiskiem lokalnym i rozwiązaniem Azure VMware SDDC.
* Chmura prywatna rozwiązania Azure VMware została wdrożona na platformie Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure
Większość klientów ma istniejące lokalne wdrożenie operacji vRealize służące do zarządzania jedną lub wieloma lokalnymi domenami vCenter. Gdy klienci inicjują nową chmurę prywatną rozwiązania VMware platformy Azure na platformie Azure, zazwyczaj łączą swoje środowisko lokalne z rozwiązaniem VMware platformy Azure przy użyciu usługi Azure ExpressRoute lub z użyciem rozwiązania sieci VPN warstwy 3, jak pokazano poniżej.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure"  border="false":::

Aby zwiększyć możliwości operacji vRealize do chmury prywatnej rozwiązań VMware platformy Azure, należy utworzyć [wystąpienie karty dla zasobów chmury prywatnej rozwiązania Azure VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) , aby zebrać dane z chmury prywatnej rozwiązania Azure VMware i przenieść je do lokalnych operacji vRealize. Lokalne wystąpienie Operations Manager vRealize może bezpośrednio nawiązać połączenie z menedżerem vCenter i NSX-T w rozwiązaniu VMware firmy Azure lub opcjonalnie można wdrożyć zdalny moduł zbierający operacje vRealize na platformie Azure VMware Private Cloud. Zdalny moduł zbierający operacje vRealize kompresuje i szyfruje dane zbierane z chmury prywatnej rozwiązań VMware platformy Azure przed ich wysłaniem przez sieć ExpressRoute lub sieci VPN do vRealize Operations Manager działającego lokalnie. 

> [!TIP]
> Zapoznaj się z [dokumentacją programu VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby zapoznać się z przewodnikiem krok po kroku dotyczący instalowania vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Operacje vRealize uruchomione w ramach wdrożenia rozwiązania VMware firmy Azure

Inną opcją wdrożenia jest wdrożenie wystąpienia vRealize Operations Manager na jednym z klastrów vSphere w chmurze prywatnej rozwiązania platformy Azure VMware, jak pokazano poniżej. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure" border="false":::

Po wdrożeniu wystąpienia rozwiązania Azure VMware dla operacji vRealize można skonfigurować operacje vRealize w celu zbierania danych z programu vCenter, ESXi, NSX-T, sieci vSAN i HCX. 

> [!TIP]
> Zapoznaj się z [dokumentacją programu VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby zapoznać się z przewodnikiem krok po kroku dotyczący instalowania vRealize Operations Manager.


## <a name="known-limitations"></a>Znane ograniczenia

- **cloudadmin@vsphere.local**Użytkownik w rozwiązaniu Azure VMware ma [ograniczone uprawnienia](concepts-role-based-access-control.md). Kolekcja pamięci gościa przy użyciu narzędzi VMware nie jest obsługiwana w przypadku maszyn wirtualnych w rozwiązaniu VMware platformy Azure. Aktywne i wykorzystane użycie pamięci nadal działa w tym przypadku.
- Optymalizacja obciążenia dla zamiaru biznesowego opartego na hoście nie działa, ponieważ rozwiązania VMware platformy Azure zarządzają konfiguracjami klastra, w tym ustawieniami DRS.
- Optymalizacja obciążeń związanych z rozmieszczeniem między klastrami w ramach SDDC przy użyciu zamiaru biznesowego opartego na klastrze jest w pełni obsługiwana za pomocą vRealize Operations Manager 8,0 i do wewnątrz. Jednak Optymalizacja obciążenia nie rozpoznaje pul zasobów i umieszcza maszyny wirtualne na poziomie klastra. Użytkownik może ręcznie rozwiązać ten problem w vCenter Server interfejsie na platformie Azure VMware.
- Nie można zalogować się do usługi vRealize Operations Manager przy użyciu poświadczeń vCenter Server Azure VMware. 
- Rozwiązanie VMware firmy Azure nie obsługuje wtyczki vRealize Operations Manager.

Podczas nawiązywania połączenia z usługą Azure VMware Solution vCenter do vRealize Operations Manager przy użyciu konta w chmurze vCenter Server zostanie wyświetlone następujące ostrzeżenie:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure":::

Ostrzeżenie występuje, ponieważ **cloudadmin@vsphere.local** użytkownik w rozwiązaniu VMware platformy Azure nie ma wystarczających uprawnień, aby wykonać wszystkie czynności vCenter Server wymagane do rejestracji. Jednak uprawnienia są wystarczające dla wystąpienia karty do zbierania danych, jak pokazano poniżej:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Lokalne operacje vRealize zarządzania wdrożeniem rozwiązań VMware platformy Azure":::

Aby uzyskać więcej informacji, zobacz [uprawnienia wymagane do konfigurowania wystąpienia adaptera vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




