---
title: Skonfiguruj operacje vRealize dla rozwiązania VMware platformy Azure
description: Dowiedz się, jak skonfigurować operacje vRealize dla chmury prywatnej rozwiązania Azure VMware.
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 729ee5c64776d7d04f702af62451175f7c53421b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750405"
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

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operacje vRealize uruchomione w rozwiązaniu VMware platformy Azure" border="false":::

Po wdrożeniu wystąpienia rozwiązania Azure VMware dla operacji vRealize można skonfigurować operacje vRealize w celu zbierania danych z programu vCenter, ESXi, NSX-T, sieci vSAN i HCX. 

> [!TIP]
> Zapoznaj się z [dokumentacją programu VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby zapoznać się z przewodnikiem krok po kroku dotyczący instalowania vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




