---
title: Scenariusze korzystania z sieci wirtualnej
description: Scenariusze, zasoby i ograniczenia dotyczące wdrażania grup kontenerów w sieci wirtualnej platformy Azure.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 6de99c68c3f05e4734dd46a579d28a6f1a3b824e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763779"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scenariusze i zasoby dotyczące sieci wirtualnej

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną, prywatną sieć dla platformy Azure i zasobów lokalnych. Wdrażając grupy kontenerów w sieci wirtualnej platformy Azure, kontenery mogą bezpiecznie komunikować się z innymi zasobami w sieci wirtualnej. 

Ten artykuł zawiera podstawowe informacje na temat scenariuszy, ograniczeń i zasobów sieci wirtualnej. Aby uzyskać przykłady wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz Deploy container instances into an Azure virtual network (Wdrażanie wystąpień [kontenerów w sieci wirtualnej platformy Azure).](container-instances-vnet.md)

> [!IMPORTANT]
> Wdrażanie grupy kontenerów w sieci wirtualnej jest ogólnie dostępne dla kontenerów systemu Linux w większości regionów, Azure Container Instances jest dostępna. Aby uzyskać szczegółowe informacje, [zobacz Regions and resource availability (Regiony i dostępność zasobów).](container-instances-region-availability.md) 

## <a name="scenarios"></a>Scenariusze

Grupy kontenerów wdrożone w sieci wirtualnej platformy Azure umożliwiają scenariusze, takie jak:

* Bezpośrednia komunikacja między grupami kontenerów w tej samej podsieci
* Wysyłanie [danych wyjściowych obciążenia opartego](container-instances-restart-policy.md) na zadaniach z wystąpień kontenera do bazy danych w sieci wirtualnej
* Pobieranie zawartości dla wystąpień kontenera z punktu [końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) w sieci wirtualnej
* Włączanie komunikacji kontenera z zasobami lokalnymi za pośrednictwem [bramy sieci VPN lub](../vpn-gateway/vpn-gateway-about-vpngateways.md) usługi [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integracja z [Azure Firewall,](../firewall/overview.md) aby identyfikować ruch wychodzący pochodzący z kontenera 
* Rozpoznawania nazw za pośrednictwem wewnętrznego Azure DNS komunikacji z zasobami platformy Azure w sieci wirtualnej, takimi jak maszyny wirtualne
* Kontrolowanie dostępu do kontenerów do podsieci lub innych zasobów sieciowych przy użyciu reguł sieciowej sieciowej sieci

## <a name="unsupported-networking-scenarios"></a>Nieobsługiwane scenariusze sieciowe 

* **Azure Load Balancer** — umieszczanie Azure Load Balancer przed wystąpieniami kontenerów w sieciowej grupie kontenerów nie jest obsługiwane
* **Globalna komunikacja równorzędna sieci wirtualnych** — globalna komunikacja równorzędna (łączenie sieci wirtualnych między regionami platformy Azure) nie jest obsługiwana
* Publiczny adres IP lub **etykieta DNS** — grupy kontenerów wdrożone w sieci wirtualnej nie obsługują obecnie uwyłania kontenerów bezpośrednio w Internecie za pomocą publicznego adresu IP lub w pełni kwalifikowanej nazwy domeny
* **Translator adresów sieciowych usługi Virtual Network** — grupy kontenerów wdrożone w sieci wirtualnej nie obsługują obecnie korzystania z zasobu bramy nat dla wychodzącej łączności z Internetem.

## <a name="other-limitations"></a>Inne ograniczenia

* Obecnie w grupie kontenerów wdrożonej w sieci wirtualnej są obsługiwane tylko kontenery systemu Linux.
* Aby wdrożyć grupy kontenerów w podsieci, podsieć nie może zawierać innych typów zasobów. Usuń wszystkie istniejące zasoby z istniejącej podsieci przed wdrożeniem w nim grup kontenerów lub utwórz nową podsieć.
* Nie można użyć tożsamości [zarządzanej w](container-instances-managed-identity.md) grupie kontenerów wdrożonej w sieci wirtualnej.
* Nie można włączyć [sondy uaktywnienia](container-instances-liveness-probe.md) ani [sondy](container-instances-readiness-probe.md) gotowości w grupie kontenerów wdrożonej w sieci wirtualnej.
* Ze względu na dodatkowe zasoby sieciowe wdrożenia w sieci wirtualnej są zwykle wolniejsze niż wdrażanie standardowego wystąpienia kontenera.
* Jeśli łączysz grupę kontenerów z kontem usługi Azure Storage, musisz dodać punkt [końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) do tego zasobu.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Wymagane zasoby sieciowe

Istnieją trzy zasoby usługi Azure Virtual Network wymagane do wdrożenia grup kontenerów w sieci [](#subnet-delegated) wirtualnej: sama sieć wirtualna, delegowana podsieć w sieci wirtualnej i [profil sieci.](#network-profile) [](#virtual-network) 

### <a name="virtual-network"></a>Sieć wirtualna

Sieć wirtualna definiuje przestrzeń adresową, w której tworzysz co najmniej jedną podsieć. Następnie wdrożysz zasoby platformy Azure (takie jak grupy kontenerów) w podsieciach w sieci wirtualnej.

### <a name="subnet-delegated"></a>Podsieć (delegowana)

Podsieci segmentują sieć wirtualną na oddzielne przestrzenie adresowe, z których mogą korzystać zasoby platformy Azure, które się w nich znajdują. Należy utworzyć jedną lub kilka podsieci w sieci wirtualnej.

Podsieć, która jest dostępna dla grup kontenerów, może zawierać tylko grupy kontenerów. Po pierwszym wdrożeniu grupy kontenerów w podsieci platforma Azure deleguje podsieć do Azure Container Instances. Po delegowaniu podsieć może być używana tylko dla grup kontenerów. Jeśli spróbujemy wdrożyć zasoby inne niż grupy kontenerów w podsieci delegowanej, operacja zakończy się niepowodzeniem.

### <a name="network-profile"></a>Profil sieci

Profil sieciowy to szablon konfiguracji sieci dla zasobów platformy Azure. Określa on pewne właściwości sieci dla zasobu, na przykład podsieć, w której ma zostać wdrożony. Przy pierwszym użyciu [polecenia az container create][az-container-create] w celu wdrożenia grupy kontenerów w podsieci (a tym samym sieci wirtualnej) platforma Azure utworzy dla Ciebie profil sieci. Następnie można użyć tego profilu sieci na użytek przyszłych wdrożeń w podsieci. 

Aby wdrożyć grupę kontenerów w podsieci przy użyciu szablonu Resource Manager, pliku YAML lub metody programowej, należy podać pełny identyfikator zasobu Resource Manager profilu sieciowego. Możesz użyć profilu utworzonego wcześniej przy użyciu narzędzia [az container create][az-container-create]lub utworzyć profil przy użyciu Resource Manager szablonu (zobacz przykład szablonu i [odwołanie](/azure/templates/microsoft.network/networkprofiles)). [](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) Aby uzyskać identyfikator wcześniej utworzonego profilu, użyj [polecenia az network profile list.][az-network-profile-list] 

Na poniższym diagramie wdrożono kilka grup kontenerów w podsieci delegowane do Azure Container Instances. Po wdrożeniu jednej grupy kontenerów w podsieci można wdrożyć w tej podsieci dodatkowe grupy kontenerów, określając ten sam profil sieciowy.

![Grupy kontenerów w sieci wirtualnej][aci-vnet-01]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać przykłady wdrożenia za pomocą interfejsu wiersza polecenia platformy Azure, zobacz Wdrażanie wystąpień [kontenerów w sieci wirtualnej platformy Azure.](container-instances-vnet.md)
* Aby wdrożyć nową sieć wirtualną, podsieć, profil sieci i grupę kontenerów przy użyciu szablonu Resource Manager, zobacz Tworzenie grupy kontenerów platformy Azure za [pomocą sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).
* Podczas tworzenia [Azure Portal](container-instances-quickstart-portal.md) kontenera przy użyciu interfejsu sieciowego można również określić ustawienia nowej lub exsting sieci wirtualnej na **karcie** Sieć.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
