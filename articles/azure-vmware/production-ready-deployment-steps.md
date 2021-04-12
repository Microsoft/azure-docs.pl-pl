---
title: Planowanie wdrożenia rozwiązań VMware na platformie Azure
description: W tym artykule opisano przepływ pracy wdrażania rozwiązań VMware platformy Azure.  Wynik końcowy to środowisko gotowe do tworzenia i migracji maszyn wirtualnych.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 60e0a4083c0253d322b2e10472d0df7496722c10
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107248"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planowanie wdrożenia rozwiązań VMware na platformie Azure

W tym artykule przedstawiono proces planowania, który umożliwia identyfikowanie i zbieranie informacji, które będą używane podczas wdrażania. Podczas planowania wdrożenia, pamiętaj o udokumentowaniu zbieranych informacji w celu łatwego odwoływania się podczas wdrażania.

Kroki opisane w tym przewodniku szybki start zapewniają środowisko gotowe do tworzenia maszyn wirtualnych i migracji. 

Aby śledzić zbierane dane, Pobierz [listę kontrolną planowania HCX](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/).

> [!IMPORTANT]
> Ważne jest, aby przede wszystkim zażądać limitu przydziału hosta podczas przygotowywania do utworzenia zasobu rozwiązania VMware platformy Azure. Możesz teraz zażądać limitu przydziału hosta, więc po zakończeniu procesu planowania możesz wdrożyć chmurę prywatną rozwiązania VMware platformy Azure. Gdy zespół pomocy technicznej otrzyma żądanie dotyczące przydziału hosta, może potrwać do pięciu dni roboczych, aby potwierdzić żądanie i przydzielić hosty. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i potrzebujesz większej liczby hostów, wykonaj ten sam proces. Aby uzyskać więcej informacji, zobacz następujące linki, w zależności od typu posiadanej subskrypcji:
> - [Klienci z umowami EA](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
> - [Klienci dostawcy CSP](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="subscription"></a>Subskrypcja

Określ subskrypcję, która ma zostać użyta do wdrożenia rozwiązania VMware platformy Azure.  Można utworzyć nową subskrypcję lub ponownie użyć istniejącej.

>[!NOTE]
>Subskrypcja musi być skojarzona z programem Microsoft Enterprise Agreement lub planem platformy Azure dla dostawcy rozwiązań w chmurze. Aby uzyskać więcej informacji, zobacz [How to enable Azure VMware Solution Resource](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Grupa zasobów

Zidentyfikuj grupę zasobów, której chcesz użyć dla rozwiązania VMware platformy Azure.  Ogólnie rzecz biorąc, Grupa zasobów jest tworzona dla rozwiązań VMware platformy Azure, ale można użyć istniejącej grupy zasobów.

## <a name="region"></a>Region

Określ region, dla którego ma zostać wdrożone rozwiązanie Azure VMware.  Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem dotyczącym produktów platformy Azure dostępnych według regionów](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Nazwa zasobu

Zdefiniuj nazwę zasobu, która będzie używana podczas wdrażania.  Nazwa zasobu jest przyjazną i opisową nazwą, w której jest określana Chmura prywatna rozwiązania Azure VMware.

>[!IMPORTANT]
>Długość nazwy nie może przekraczać 40 znaków. Jeśli nazwa przekroczy ten limit, nie będzie można tworzyć publicznych adresów IP do użycia z chmurą prywatną. 

## <a name="size-hosts"></a>Rozmiar hostów

Określ rozmiar hostów, które mają być używane podczas wdrażania rozwiązania Azure VMware.  Aby uzyskać pełną listę, zobacz dokumentację [chmur prywatnych i klastrów rozwiązań VMware platformy Azure](concepts-private-clouds-clusters.md#hosts) .

## <a name="number-of-clusters-and-hosts"></a>Liczba klastrów i hostów

Pierwsze wdrożenie rozwiązania Azure VMware będzie zawierać chmurę prywatną zawierającą pojedynczy klaster. W przypadku wdrożenia należy zdefiniować liczbę hostów, które mają zostać wdrożone w pierwszym klastrze.

>[!NOTE]
>Minimalna liczba hostów na klaster to trzy, a maksymalna to 16. Maksymalna liczba klastrów na chmurę prywatną to cztery. 

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [chmury i klastrów prywatnych rozwiązania Azure VMware](concepts-private-clouds-clusters.md#clusters) .

>[!TIP]
>Można zawsze rozłożyć klaster i później dodać kolejne klastry, jeśli trzeba przekroczyć początkowy numer wdrożenia.

## <a name="ip-address-segment-for-private-cloud-management"></a>Segment adresów IP do zarządzania chmurą prywatną

Pierwszym krokiem w procesie planowania wdrożenia jest zaplanowanie segmentacji adresów IP. Rozwiązanie VMware platformy Azure wymaga sieci CIDR/22. Ta przestrzeń adresowa jest używać miejsca do mniejszych segmentów sieci (podsieci) i używanych przez segmenty zarządzania rozwiązaniami VMware platformy Azure, w tym vCenter, VMware HCX, NSX-T i vMotion. Wizualizacja poniżej podświetla, gdzie będzie używany ten segment.

Ten blok adresów sieciowych CIDR/22 nie powinien nakładać się na istniejący segment sieci, który jest już lokalnie lub na platformie Azure.

**Przykład:** 10.0.0.0/22

Szczegółowe informacje o tym, jak sieć CIDR/22 jest podzielona na [listę kontrolek planowania sieci](tutorial-network-checklist.md#routing-and-subnet-considerations)w chmurze prywatnej.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identyfikowanie — segment adresów IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segment adresów IP dla obciążeń maszyny wirtualnej

Podobnie jak w przypadku dowolnego środowiska VMware, maszyny wirtualne muszą łączyć się z segmentem sieci. W rozwiązaniu VMware platformy Azure istnieją dwa typy segmentów, rozszerzone segmenty L2 (omówione dalej) i segmenty sieci NSX-T. Po rozwinięciu wdrożenia produkcyjnego rozwiązania VMware na platformie Azure występuje częsta kombinacja rozszerzonych segmentów L2 z lokalnych segmentów sieci NSX-T. Aby zaplanować wstępne wdrożenie, w rozwiązaniu VMware platformy Azure Zidentyfikuj pojedynczy segment sieci (sieć IP). Ta sieć nie może pokrywać się z żadnymi segmentami sieci lokalnie ani w ramach reszty platformy Azure i nie może znajdować się w ramach zdefiniowanego wcześniej segmentu sieci/22.

Ten segment sieci jest używany głównie do celów testowych podczas wstępnego wdrażania.

>[!NOTE]
>Ta sieć lub sieci nie będą potrzebowały podczas wdrażania. Są one tworzone jako krok po wdrożeniu.
  
**Przykład:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identyfikowanie segmentu adresów IP dla obciążeń maszyny wirtualnej" border="false":::     

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Dołącz Virtual Network platformy Azure do rozwiązania VMware platformy Azure

Aby zapewnić łączność z rozwiązaniem VMware platformy Azure, ExpressRoute jest tworzona z chmury prywatnej rozwiązań VMware platformy Azure do bramy sieci wirtualnej ExpressRoute.

Możesz użyć *istniejącej* lub *nowej* bramy sieci wirtualnej ExpressRoute.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Tożsamość — Virtual Network platformy Azure, aby dołączyć rozwiązanie VMware dla platformy Azure" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Użyj istniejącej bramy sieci wirtualnej ExpressRoute

Jeśli planujesz użycie *istniejącej* bramy sieci wirtualnej ExpressRoute, obwód usługi Azure VMware ExpressRoute jest ustanawiany jako krok po wdrożeniu. W takim przypadku pozostaw pole **Virtual Network** puste.

Zgodnie z ogólnym zaleceniem można używać istniejącej bramy sieci wirtualnej ExpressRoute. W celu zaplanowania należy zanotować, która ExpressRoute Brama sieci wirtualnej, która będzie używana, a następnie przejść do [następnego kroku](#vmware-hcx-network-segments).

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Tworzenie nowej bramy sieci wirtualnej ExpressRoute

Podczas tworzenia *nowej* bramy sieci wirtualnej ExpressRoute można użyć istniejącej usługi Azure Virtual Network lub utworzyć nową.  

- W przypadku istniejącej sieci wirtualnej platformy Azure:
   1. Zidentyfikuj sieć wirtualną platformy Azure, w której nie ma już istniejących bram sieci wirtualnej ExpressRoute.
   2. Przed wdrożeniem Utwórz [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) w usłudze Azure Virtual Network.

- W przypadku nowej bramy usługi Azure Virtual Network i sieci wirtualnej, która zostanie utworzona podczas wdrażania, wybierz link **Utwórz nowy** poniżej listy **Virtual Network** .  Ważne jest, aby wcześniej definiować przestrzeń adresową i podsieci przed wdrożeniem, dzięki czemu możesz wprowadzać te informacje po zakończeniu kroków wdrożenia.

Na poniższej ilustracji przedstawiono ekran **Tworzenie wdrożenia chmury prywatnej** z wyróżnionym polem **Virtual Network** .

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Zrzut ekranu przedstawiający ekran wdrożenia rozwiązania VMware platformy Azure z wyróżnionym polem Virtual Network.":::

> [!NOTE]
> Każda sieć wirtualna, która ma być używana lub utworzona, może być widoczna w środowisku lokalnym i w rozwiązaniu Azure VMware, dlatego należy się upewnić, że jakikolwiek segment IP używany w tej sieci wirtualnej i podsieci nie nakładają się na siebie.

## <a name="vmware-hcx-network-segments"></a>Segmenty sieci VMware HCX

VMware HCX to technologia, która jest instalowana z rozwiązaniem VMware platformy Azure. Podstawowym przypadkiem użycia programu VMware HCX są migracje obciążeń i odzyskiwanie po awarii. Jeśli planujesz wykonać te czynności, najlepiej zaplanuj sieć teraz. W przeciwnym razie możesz pominąć i przejść do następnego kroku.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="optional-extend-your-networks"></a>Obowiązkowe Zwiększanie sieci

Segmenty sieci można rozciągnąć z rozwiązań lokalnych do platformy Azure VMware. W przypadku rozszerania segmentów sieci należy zidentyfikować te sieci teraz.  

Oto kilka czynników, które należy wziąć pod uwagę:

- Jeśli planujesz rozszerzoną sieć ze środowiska lokalnego, te sieci muszą łączyć się z [przełącznikiem rozproszonym vSphere (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) w lokalnym środowisku VMware.  
- Nie można rozszerzyć sieci, które znajdują się na [przełączniku standardowym vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) .

>[!NOTE]
>Te sieci są rozszerzane jako ostatni etap konfiguracji, a nie w trakcie wdrażania.
>
## <a name="next-steps"></a>Następne kroki
Teraz, po zebraniu i udokumentowaniu potrzebnych informacji, przejdź do następnej sekcji, aby utworzyć chmurę prywatną rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure VMware Solution](deploy-azure-vmware-solution.md)
> 
