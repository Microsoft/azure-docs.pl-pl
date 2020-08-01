---
title: Konfigurowanie operacji vRealize dla rozwiązania Azure VMware (Automatyczna synchronizacja)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476072"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Konfigurowanie operacji vRealize dla rozwiązania Azure VMware (Automatyczna synchronizacja)


vRealize Operations Manager to platforma zarządzania operacjami, która umożliwia administratorom infrastruktury VMware monitorowanie zasobów systemowych. Te zasoby systemowe mogą być obiektami poziomu aplikacji lub infrastruktury (zarówno fizycznymi, jak i wirtualnymi). W przeszłości większość administratorów VMware użyła operacji vRealize do monitorowania składników chmur prywatnych VMware i zarządzania nimi — vCenter, ESXi, NSX-T, sieci vSAN i hybrydowego rozszerzenia chmury (HCX). Każda chmura prywatna w wersji zabudowy została zainicjowana przy użyciu dedykowanego wdrożenia vCenter, NSX-T, sieci vSAN i HCX. 

Uważnie zapoznaj [się z wcześniejszym](#before-you-begin) rozpoczęciem i [wymaganiami wstępnymi](#prerequisites) . Następnie przeprowadzimy Cię przez dwa typowe topologie wdrażania dla vRealize Operations Manager z funkcją automatycznej synchronizacji:

> [!div class="checklist"]
> * [Lokalne operacje vRealize zarządzające wdrożeniem automatycznej synchronizacji](#on-premises-vrealize-operations-managing-avs-deployment)
> * [Operacje vRealize uruchomione w ramach wdrożenia automatycznej synchronizacji](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Zapoznaj się z [dokumentacją produktu vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby dowiedzieć się więcej o wdrażaniu vRealize operacji. 
* Zapoznaj się z [serią samouczka](tutorial-network-checklist.md)podstawowa definicja oprogramowania do automatycznej synchronizacji (SDDC).
* Opcjonalnie zapoznaj się z dokumentacją programu [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) , aby zapoznać się z opcją wdrożenia vRealize Operations Manager. 



## <a name="prerequisites"></a>Wymagania wstępne
* Sieć VPN lub usługa Azure ExpressRoute należy skonfigurować między środowiskiem lokalnym i SDDC.
* Na platformie Azure wdrożono chmurę prywatną do automatycznej synchronizacji.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Lokalne operacje vRealize zarządzające wdrożeniem automatycznej synchronizacji
Większość klientów ma istniejące lokalne wdrożenie operacji vRealize służące do zarządzania jedną lub wieloma lokalnymi domenami vCenter. Gdy klienci inicjują nową chmurę prywatną na platformie Azure, zazwyczaj łączą swoje środowisko lokalne z funkcją automatycznej synchronizacji przy użyciu usługi Azure ExpressRoute lub z użyciem rozwiązania sieci VPN warstwy 3, jak pokazano poniżej.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Lokalne operacje vRealize zarządzające wdrożeniem automatycznej synchronizacji"  border="false":::

Aby zwiększyć możliwości operacji vRealize do chmury prywatnej automatycznej synchronizacji, należy utworzyć[ wystąpienie karty dla zasobów chmury prywatnej do automatycznej synchronizacji](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) , aby zebrać dane z chmury prywatnej automatycznej synchronizacji i przenieść je do lokalnych operacji vRealize. Lokalne wystąpienie Operations Manager vRealize może bezpośrednio łączyć się z menedżerem vCenter i NSX-T na potrzeby automatycznej synchronizacji lub opcjonalnie wdrożyć zdalny moduł zbierający operacje vRealize w chmurze prywatnej automatycznej synchronizacji. Zdalny moduł zbierający operacje vRealize kompresuje i szyfruje dane zbierane z chmury prywatnej automatycznej synchronizacji przed ich wysłaniem przez sieć ExpressRoute lub sieci VPN do vRealize Operations Manager działającego lokalnie. 

> [!TIP]
> Zapoznaj się z [dokumentacją programu VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby zapoznać się z przewodnikiem krok po kroku dotyczący instalowania vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-avs-deployment"></a>Operacje vRealize uruchomione w ramach wdrożenia automatycznej synchronizacji

Inną opcją wdrożenia jest wdrożenie wystąpienia vRealize Operations Manager na jednym z klastrów vSphere w chmurze prywatnej automatycznej synchronizacji, jak pokazano poniżej. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operacje vRealize uruchomione przy automatycznej synchronizacji" border="false":::

Po wdrożeniu wystąpienia automatycznej synchronizacji operacji vRealize można skonfigurować operacje vRealize w celu zbierania danych z programu vCenter, ESXi, NSX-T, sieci vSAN i HCX. 

> [!TIP]
> Zapoznaj się z [dokumentacją programu VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , aby zapoznać się z przewodnikiem krok po kroku dotyczący instalowania vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




