---
title: Grupa zabezpieczeń sieci — jak to działa
titlesuffix: Azure Virtual Network
description: Dowiedz się, w jaki sposób grupy zabezpieczeń sieci pomagają w filtrowaniu ruchu sieciowego między zasobami platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: e60cdfb00d0dc9d446bd52a72e9fd15676acd285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89458199"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Jak sieciowe grupy zabezpieczeń filtrują ruch sieciowy
<a name="network-security-groups"></a>

Za pomocą grupy zabezpieczeń sieci platformy Azure można filtrować ruch sieciowy do i z zasobów platformy Azure w sieci wirtualnej platformy Azure. Grupa zabezpieczeń sieci zawiera [reguły zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules), które zezwalają na lub blokują przychodzący ruch sieciowy lub wychodzący ruch sieciowy dla kilku typów zasobów platformy Azure. Dla każdej reguły można określić źródło i obiekt docelowy, port i protokół.

W sieci wirtualnej platformy Azure można wdrożyć zasoby z kilku usług platformy Azure. Aby uzyskać pełną listę, zobacz [Services that can be deployed into a virtual network](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (Usługi, które można wdrożyć w sieci wirtualnej). Z każdą siecią [podsiecią](virtual-network-manage-subnet.md#change-subnet-settings) i [interfejsem sieciowym](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) sieci wirtualnej na maszynie wirtualnej możesz skojarzyć zero lub jedną grupę zabezpieczeń sieci. Tę samą grupę zabezpieczeń sieci można zastosować do dowolnej liczby interfejsów sieciowych i podsieci.

Poniższa ilustracja przedstawia różne scenariusze dotyczące sposobu wdrażania grup zabezpieczeń sieci w celu zezwolenia na ruch sieciowy do i z Internetu przez port TCP 80:

![Sieciowe grupy zabezpieczeń — przetwarzanie](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Odwołaj się do poprzedniej ilustracji i następującego tekstu, aby zrozumieć, w jaki sposób platforma Azure przetwarza reguły ruchu przychodzącego i wychodzącego dla grup zabezpieczeń sieci:

## <a name="inbound-traffic"></a>Ruch przychodzący

W przypadku ruchu przychodzącego platforma Azure przetwarza najpierw reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią, jeśli taka istnieje, a następnie reguły w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, jeśli taka istnieje.

- **VM1**: przetwarzane są reguły zabezpieczeń w grupie *NSG1*, ponieważ jest ona skojarzona z podsiecią *Subnet1*, a maszyna wirtualna *VM1* znajduje się w podsieci *Subnet1*. O ile nie utworzono reguły zezwalającej na port 80 dla ruchu przychodzącego, ruch jest blokowany przez domyślną regułę zabezpieczeń [DenyAllInbound](https://docs.microsoft.com/azure/virtual-network/security-overview#denyallinbound) i nigdy nie jest oceniany przez grupę *NSG2*, ponieważ grupa *NSG2* jest skojarzona z interfejsem sieciowym. Jeśli grupa *NSG1* zawiera regułę zabezpieczeń, która zezwala na port 80, ruch jest następnie przetwarzany przez grupę *NSG2*. Aby zezwolić na ruch przychodzący na porcie 80 do maszyny wirtualnej, obie grupy *NSG1* i *NSG2* muszą zawierać regułę zezwalającą ruch przychodzący na porcie 80 z Internetu.
- **VM2**: przetwarzane są reguły w grupie *NSG1*, ponieważ maszyna wirtualna *VM2* znajduje się również w podsieci *Subnet1*. Ponieważ maszyna wirtualna *VM2* nie ma grupy zabezpieczeń sieci skojarzonej z jej interfejsem sieciowym, odbiera cały ruch dozwolony przez grupę *NSG1* lub nie odbiera całego ruchu blokowanego przez grupę *NSG1*. Ruch jest dozwolony albo blokowany dla wszystkich zasobów w tej samej podsieci, gdy grupa zabezpieczeń sieci jest skojarzona z podsiecią.
- **VM3**: ponieważ ma żadnej sieciowej grupy zabezpieczeń skojarzonej z podsiecią *Subnet2*, ruch jest dozwolony do tej podsieci i przetwarzany przez grupę *NSG2*, ponieważ grupa *NSG2* jest skojarzona z interfejsem sieciowym dołączonym do maszyny wirtualnej *VM3*.
- **VM4**: ruch jest dozwolony do maszyny wirtualnej *VM4*, ponieważ grupa zabezpieczeń sieci nie jest skojarzona z podsiecią *Subnet3* ani interfejsem sieciowym w tej maszynie wirtualnej. Cały ruch sieciowy przez podsieć i interfejs sieciowy jest dozwolony, jeśli nie mają one skojarzonej żadnej sieciowej grupy zabezpieczeń.

## <a name="outbound-traffic"></a>Ruch wychodzący

W przypadku ruchu wychodzącego platforma Azure przetwarza najpierw reguły w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, jeśli taka istnieje, a następnie reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią, jeśli taka istnieje.

- **Maszyna VM1**: przetwarzane są reguły zabezpieczeń w grupie *NSG2*. O ile nie utworzono reguły zabezpieczeń, która blokuje ruch wychodzący do Internetu na porcie 80, ruch jest dozwolony przez domyślną regułą zabezpieczeń [AllowInternetOutbound](https://docs.microsoft.com/azure/virtual-network/security-overview#allowinternetoutbound) w obu grupach *NSG1* i *NSG2*. Jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która nie zezwala na port 80, ruch jest blokowany i nigdy nie jest oceniany przez grupę *NSG1*. Aby zablokować port 80 na maszynie wirtualnej, jedna lub obie grupy zabezpieczeń sieci muszą zawierać regułę, która blokuje ruch do Internetu na porcie 80.
- **Maszyna VM2**: cały ruch jest wysyłany przez interfejs sieciowy do podsieci, ponieważ interfejs sieciowy dołączony do maszyny wirtualnej *VM2* nie ma skojarzonej grupy zabezpieczeń sieci. Reguły w grupie *NSG1* są przetwarzane.
- **VM3**: jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która blokuje port 80, ruch jest blokowany. Jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która zezwala na użycie portu 80, ruch wychodzący do Internetu przez port 80 jest dozwolony, ponieważ grupa zabezpieczeń sieci nie jest skojarzona z podsiecią *Subnet2*.
- **VM4**: cały ruch sieciowy z maszyny wirtualnej *VM4* jest dozwolony, ponieważ żadna grupa zabezpieczeń sieci nie jest skojarzona z interfejsem sieciowym dołączonym do maszyny wirtualnej ani z podsiecią *Subnet3*.


## <a name="intra-subnet-traffic"></a>Ruch Intra-Subnet

Należy pamiętać, że reguły zabezpieczeń w sieciowej grupy zabezpieczeń skojarzonej z podsiecią mogą mieć wpływ na łączność między MASZYNami wirtualnymi. Na przykład, jeśli reguła zostanie dodana do *NSG1* , która odmówi cały ruch przychodzący i wychodzący, *VM1* i *VM2* nie będą już mogły komunikować się ze sobą. Aby to umożliwić, należy dodać inną regułę. 

Reguły agregowane stosowane do interfejsu sieciowego można łatwo wyświetlić, wyświetlając [obowiązujące reguły zabezpieczeń](virtual-network-network-interface.md#view-effective-security-rules) dla interfejsu sieciowego. Możesz również skorzystać z możliwości [weryfikowania przepływu protokołu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w celu ustalenia, czy komunikacja z lub do interfejsu sieciowego jest dozwolona. Weryfikacja przepływu IP informuje o tym, czy komunikacja jest dozwolona, czy odmowa, oraz która reguła zabezpieczeń sieci zezwala na ruch lub go odmawia.

> [!NOTE]
> Sieciowe grupy zabezpieczeń są skojarzone z podsieciami lub do maszyn wirtualnych i usług w chmurze wdrożonych w klasycznym modelu wdrażania, a także do podsieci lub interfejsów sieciowych w Menedżer zasobów model wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).

> [!TIP]
> O ile nie masz konkretnej przyczyny, zalecamy skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym, ale nie obu jednocześnie. Ponieważ reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią mogą powodować konflikt z regułami w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, możesz napotkać nieoczekiwane problemy z komunikacją, co będzie wymagać rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zasobach platformy Azure, które można wdrożyć w sieci wirtualnej i skojarzyć z nimi grupy zabezpieczeń sieci, zobacz [Integracja z siecią wirtualną dla usług platformy Azure](virtual-network-for-azure-services.md).
* Jeśli nie masz doświadczenia w tworzeniu grup zabezpieczeń sieci, możesz ukończyć szybki [samouczek](tutorial-filter-network-traffic.md), aby zyskać pewne doświadczenie w tym zakresie.
* Jeśli znasz grupy zabezpieczeń sieci i chcesz nimi zarządzać, zobacz [Manage a network security group](manage-network-security-group.md) (Zarządzanie sieciową grupą zabezpieczeń). 
* Jeśli występują problemy z komunikacją i musisz rozwiązać problemy z grupami zabezpieczeń sieci, zobacz [Diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md). 
* Dowiedz się, jak włączyć [dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , aby analizować ruch sieciowy do i z zasobów, które mają skojarzoną grupę zabezpieczeń sieci.
