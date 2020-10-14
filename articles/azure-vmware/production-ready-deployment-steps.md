---
title: Planowanie wdrożenia rozwiązań VMware na platformie Azure
description: W tym artykule opisano przepływ pracy wdrażania rozwiązań VMware platformy Azure.  Wynik końcowy to środowisko gotowe do tworzenia i migracji maszyn wirtualnych.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: fe1396c8f256ba8374208febad2afe03a453e5f5
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055090"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planowanie wdrożenia rozwiązań VMware na platformie Azure

W tym artykule udostępniamy proces planowania służący do identyfikowania i zbierania danych używanych podczas wdrażania. [Lista kontrolna przed wdrożeniem służy](pre-deployment-checklist.md) do dokumentowania informacji oraz do łatwego odwoływania się podczas wdrażania.  

Procesy tego przewodnika Szybki Start umożliwiają tworzenie maszyn wirtualnych i migracji w środowisku gotowym do produkcji. 

>[!IMPORTANT]
>Przed utworzeniem zasobu rozwiązania Azure VMware należy przesłać bilet pomocy technicznej w celu przydzielenia węzłów. Gdy zespół pomocy technicznej otrzyma Twoje żądanie, zajmie do pięciu dni roboczych, aby potwierdzić żądanie i przydzielić węzły. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i chcesz uzyskać więcej węzłów, przejdziesz do tego samego procesu. Aby uzyskać więcej informacji, zobacz [How to enable Azure VMware Solution Resource](enable-azure-vmware-solution.md). 

## <a name="subscription"></a>Subskrypcja

Określ subskrypcję, która ma zostać użyta do wdrożenia rozwiązania VMware platformy Azure.  Można utworzyć nową subskrypcję lub ponownie użyć istniejącej.

>[!NOTE]
>Subskrypcja musi być skojarzona z Umowa Enterpriseem firmy Microsoft.

## <a name="resource-group"></a>Grupa zasobów

Zidentyfikuj grupę zasobów, której chcesz użyć dla rozwiązania VMware platformy Azure.  Ogólnie rzecz biorąc, Grupa zasobów jest tworzona dla rozwiązań VMware platformy Azure, ale można użyć istniejącej grupy zasobów.

## <a name="region"></a>Region

Określ region, dla którego ma zostać wdrożone rozwiązanie Azure VMware.  Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem dotyczącym produktów platformy Azure dostępnych według regionów](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Nazwa zasobu

Zdefiniuj nazwę zasobu, która będzie używana podczas wdrażania.  Nazwa zasobu jest przyjazną i opisową nazwą, w której jest określana Chmura prywatna rozwiązania Azure VMware.

## <a name="size-nodes"></a>Węzły rozmiaru

Określ węzły rozmiaru, które mają być używane podczas wdrażania rozwiązania Azure VMware.  Aby uzyskać pełną listę, zobacz dokumentację [chmur prywatnych i klastrów rozwiązań VMware platformy Azure](concepts-private-clouds-clusters.md#hosts) .

## <a name="number-of-hosts"></a>Liczba hostów

Zdefiniuj liczbę hostów, które chcesz wdrożyć w chmurze prywatnej rozwiązania Azure VMware.  Minimalna liczba węzłów to trzy, a maksymalna to 16 na klaster.  Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [chmury i klastrów prywatnych rozwiązania Azure VMware](concepts-private-clouds-clusters.md#clusters) .

Klaster można zawsze później zwiększyć, jeśli trzeba przekroczyć początkowy numer wdrożenia.

## <a name="vcenter-admin-password"></a>hasło administratora vCenter
Zdefiniuj hasło administratora vCenter.  Podczas wdrażania utworzysz hasło administratora vCenter. Hasło jest cloudadmin@vsphere.local kontem administratora podczas kompilacji programu vCenter. Będziesz używać go do logowania się do programu vCenter.

## <a name="nsx-t-admin-password"></a>Hasło administratora NSX-T
Zdefiniuj hasło administratora NSX-T.  Podczas wdrażania utworzysz hasło administratora NSX-T. Hasło jest przypisywane do użytkownika administracyjnego na koncie NSX podczas kompilacji NSX. Zostanie ona użyta do zalogowania się do Menedżera NSX-T.

## <a name="ip-address-segment"></a>Segment adresów IP

Pierwszym krokiem w procesie planowania wdrożenia jest zaplanowanie segmentacji adresów IP.  Rozwiązanie Azure VMware pozyskuje podaną sieć/22. Następnie carves go do mniejszych segmentów, a następnie używa tych segmentów IP dla vCenter, VMware HCX, NSX-T i vMotion.

Rozwiązanie Azure VMware nawiązuje połączenie z Microsoft Azure Virtual Network za pośrednictwem wewnętrznego obwodu usługi ExpressRoute. W większości przypadków nawiązuje połączenie z centrum danych za pośrednictwem ExpressRoute Global Reach. 

Rozwiązanie Azure VMware, istniejące środowisko platformy Azure i środowisko lokalne wszystkie trasy wymiany (zazwyczaj). W takim przypadku blok adresów sieciowych CIDR/22 zdefiniowany w tym kroku nie powinien nakładać się na wszystkie elementy, które są już lokalnie lub na platformie Azure.

**Przykład:** 10.0.0.0/22

Aby uzyskać więcej informacji, zobacz [Lista kontrolna planowania sieci](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identyfikowanie — segment adresów IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segment adresów IP dla obciążeń maszyny wirtualnej

Zidentyfikuj segment IP, aby utworzyć pierwszą sieć (segment NSX) w chmurze prywatnej.  Innymi słowy, chcesz utworzyć segment sieci w rozwiązaniu VMware platformy Azure, aby można było wdrożyć maszyny wirtualne w rozwiązaniu VMware platformy Azure.   

Nawet jeśli planujesz tylko rozszerzanie sieci L2, Utwórz segment sieci, który będzie przydatny do sprawdzania poprawności środowiska.

Należy pamiętać, że wszystkie tworzone segmenty IP muszą być unikatowe na platformie Azure i lokalnie.  

**Przykład:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identyfikowanie — segment adresów IP" border="false":::     

## <a name="optional-extend-networks"></a>Obowiązkowe Rozszerzone sieci

Segmenty sieci można rozszerać z rozwiązań lokalnych do platformy Azure VMware, a jeśli to zrobisz, należy zidentyfikować te sieci teraz.  

Należy pamiętać, że:

- Jeśli planujesz rozszerzoną sieć ze środowiska lokalnego, te sieci muszą łączyć się z [przełącznikiem rozproszonym vSphere (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) w lokalnym środowisku VMware.  
- Jeśli sieci, które chcesz rozszerzyć na żywo na [przełączniku standardowym vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), nie można ich rozszerzyć.

## <a name="expressroute-global-reach-peering-network"></a>ExpressRoute Global Reach sieci równorzędnej

Zidentyfikuj `/29` blok adresów sieciowych CIDR, który jest wymagany do ExpressRoute Global REACH komunikacji równorzędnej. Należy pamiętać, że wszystkie tworzone segmenty IP muszą być unikatowe w ramach rozwiązania VMware platformy Azure i lokalnego. Adresy IP w tym segmencie są używane na każdym końcu ExpressRoute Global Reach połączenia w celu połączenia usługi Azure VMware ExpressRoute obwód z lokalnym obwodem ExpressRoute. 

**Przykład:** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="Identyfikowanie — segment adresów IP" border="false":::

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Virtual Network platformy Azure, aby dołączyć rozwiązanie VMware dla platformy Azure

Aby uzyskać dostęp do prywatnej chmury rozwiązania VMware, obwód usługi ExpressRoute, który jest dostarczany z rozwiązaniem VMware platformy Azure, musi zostać dołączony do Virtual Network platformy Azure.  Podczas wdrażania można zdefiniować nową sieć wirtualną lub wybrać istniejącą.

Obwód usługi ExpressRoute z rozwiązania Azure VMware nawiązuje połączenie z bramą ExpressRoute w ramach usługi Azure Virtual Network zdefiniowanej w tym kroku.  

>[!IMPORTANT]
>Możesz użyć istniejącej bramy ExpressRoute, aby nawiązać połączenie z rozwiązaniem VMware platformy Azure, o ile nie przekracza limit czterech obwodów ExpressRoute na sieć wirtualną.  Jednak w celu uzyskania dostępu do rozwiązania VMware platformy Azure ze środowisk lokalnych za pośrednictwem usługi ExpressRoute należy mieć ExpressRoute Global Reach, ponieważ Brama ExpressRoute nie zapewnia routingu przechodniego między podłączonymi do niego obwodami.  

Jeśli chcesz połączyć obwód usługi ExpressRoute z rozwiązania Azure VMware do istniejącej bramy ExpressRoute, możesz to zrobić po wdrożeniu.  

Z tego względu w podsumowaniu czy chcesz połączyć rozwiązanie VMware firmy Azure z istniejącą bramą usługi Express Route?  

* **Tak** = Zidentyfikuj sieć wirtualną, która nie jest używana podczas wdrażania.
* **Nie** = Zidentyfikuj istniejącą sieć wirtualną lub Utwórz nową, podczas wdrażania.

W obu przypadkach należy udokumentować, co chcesz zrobić w tym kroku.

>[!NOTE]
>Ta sieć wirtualna jest postrzegana przez środowisko lokalne i rozwiązanie platformy Azure VMware, dlatego należy się upewnić, że żaden segment IP używany w tej sieci wirtualnej i podsieci nie nakładają się na siebie.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identyfikowanie — segment adresów IP" border="false":::

## <a name="vmware-hcx-network-segments"></a>Segmenty sieci VMware HCX

VMware HCX to technologia dostosowana do rozwiązań VMware platformy Azure. Podstawowym przypadkiem użycia programu VMware HCX są migracje obciążeń i odzyskiwanie po awarii. Jeśli planujesz wykonać te czynności, najlepiej zaplanuj sieć teraz.   W przeciwnym razie możesz pominąć i przejść do następnego kroku.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, po zebraniu i udokumentowaniu wymaganych informacji, przejdź do następnej sekcji, aby utworzyć chmurę prywatną rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure VMware Solution](deploy-azure-vmware-solution.md)
