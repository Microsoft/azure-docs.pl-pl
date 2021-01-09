---
title: Scenariusze korzystania z sieci wirtualnej
description: Scenariusze, zasoby i ograniczenia dotyczące wdrażania grup kontenerów w sieci wirtualnej platformy Azure.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 65d7fa46ebbb9b072b50731bff68b9b88809075d
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033833"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scenariusze i zasoby sieci wirtualnej

[Usługa azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną i prywatną sieć dla zasobów platformy Azure i lokalnych. Wdrażając grupy kontenerów w sieci wirtualnej platformy Azure, kontenery mogą bezpiecznie komunikować się z innymi zasobami w sieci wirtualnej. 

Ten artykuł zawiera informacje o scenariuszach, ograniczeniach i zasobach sieci wirtualnej. Przykłady wdrażania przy użyciu interfejsu wiersza polecenia platformy Azure znajdują się [w temacie Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure](container-instances-vnet.md).

## <a name="scenarios"></a>Scenariusze

Grupy kontenerów wdrożone w usłudze Azure Virtual Network umożliwiają włączanie scenariuszy, takich jak:

* Bezpośrednia komunikacja między grupami kontenerów w tej samej podsieci
* Wysyłaj dane wyjściowe obciążenia [opartego na zadaniach](container-instances-restart-policy.md) z wystąpień kontenerów do bazy danych w sieci wirtualnej
* Pobieranie zawartości dla wystąpień kontenerów z [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) w sieci wirtualnej
* Włącz komunikację kontenera z zasobami lokalnymi za pośrednictwem [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integracja z [zaporą platformy Azure](../firewall/overview.md) w celu zidentyfikowania ruchu wychodzącego pochodzącego z kontenera 
* Rozpoznawanie nazw za pośrednictwem wewnętrznego Azure DNS na potrzeby komunikacji z zasobami platformy Azure w sieci wirtualnej, takich jak maszyny wirtualne
* Używanie reguł sieciowej grupy zabezpieczeń do kontrolowania dostępu kontenera do podsieci lub innych zasobów sieciowych

## <a name="unsupported-networking-scenarios"></a>Nieobsługiwane scenariusze sieci 

* **Azure Load Balancer** — umieszczenie Azure Load Balancer przed wystąpieniami kontenerów w grupie kontenerów sieciowych nie jest obsługiwane
* **Globalne wirtualne sieci równorzędne** — globalna komunikacja równorzędna (łączenie sieci wirtualnych w regionach platformy Azure) nie jest obsługiwana
* **Public IP lub etykieta DNS** — grupy kontenerów wdrożone w sieci wirtualnej nie obsługują obecnie udostępniania kontenerów bezpośrednio w Internecie za pomocą publicznego adresu IP lub w pełni kwalifikowanej nazwy domeny
* **Virtual Network translator adresów sieciowych** — grupy kontenerów wdrożone w sieci wirtualnej nie są obecnie obsługiwane przy użyciu zasobu bramy translatora adresów sieciowych do wychodzącej łączności z Internetem.

## <a name="other-limitations"></a>Inne ograniczenia

* Obecnie w grupie kontenerów wdrożonej w sieci wirtualnej są obsługiwane tylko kontenery systemu Linux.
* Aby wdrożyć grupy kontenerów w podsieci, podsieć nie może zawierać innych typów zasobów. Usuń wszystkie istniejące zasoby z istniejącej podsieci przed wdrożeniem w niej grup kontenerów lub Utwórz nową podsieć.
* Nie można użyć [tożsamości zarządzanej](container-instances-managed-identity.md) w grupie kontenerów wdrożonej w sieci wirtualnej.
* Nie można włączyć [sondy na żywo](container-instances-liveness-probe.md) lub [sondy gotowości](container-instances-readiness-probe.md) w grupie kontenerów wdrożonej w sieci wirtualnej.
* Ze względu na dodatkowe zasoby sieci, wdrożenia w sieci wirtualnej są zwykle wolniejsze niż wdrożenie standardowego wystąpienia kontenera.
* Jeśli łączysz grupę kontenerów z kontem usługi Azure Storage, musisz dodać [punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) do tego zasobu.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="where-to-deploy"></a>Miejsce wdrożenia

Poniższe regiony i maksymalne zasoby są dostępne do wdrożenia grupy kontenerów w sieci wirtualnej platformy Azure.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="required-network-resources"></a>Wymagane zasoby sieciowe

Do wdrożenia grup kontenerów w sieci wirtualnej są wymagane trzy zasoby Virtual Network platformy Azure: [Sieć wirtualna](#virtual-network) , [podsieć delegowana](#subnet-delegated) w ramach sieci wirtualnej i [profil sieciowy](#network-profile). 

### <a name="virtual-network"></a>Sieć wirtualna

Sieć wirtualna definiuje przestrzeń adresową, w której można utworzyć co najmniej jedną podsieć. Następnie należy wdrożyć zasoby platformy Azure (takie jak grupy kontenerów) w podsieciach w sieci wirtualnej.

### <a name="subnet-delegated"></a>Podsieć (delegowana)

Podsieci umożliwiają segmentację sieci wirtualnej na oddzielne przestrzenie adresowe, które są używane przez zasoby platformy Azure, które znajdują się w nich. Należy utworzyć co najmniej jedną podsieć w ramach sieci wirtualnej.

Podsieć używana dla grup kontenerów może zawierać tylko grupy kontenerów. Podczas pierwszego wdrażania grupy kontenerów w podsieci usługa Azure deleguje tę podsieć do Azure Container Instances. Po delegowaniu ta podsieć może być używana tylko dla grup kontenerów. Jeśli podjęto próbę wdrożenia zasobów innych niż grupy kontenerów w delegowanej podsieci, operacja zakończy się niepowodzeniem.

### <a name="network-profile"></a>Profil sieci

Profil sieci to szablon konfiguracji sieci dla zasobów platformy Azure. Określa pewne właściwości sieci dla zasobu, na przykład podsieć, w której ma zostać wdrożona. Po pierwszym użyciu polecenia [AZ Container Create][az-container-create] do wdrożenia grupy kontenerów w podsieci (i w ten sposób do sieci wirtualnej) platforma Azure tworzy profil sieciowy. Tego profilu sieciowego można następnie użyć do przyszłych wdrożeń w podsieci. 

Aby użyć szablonu Menedżer zasobów, pliku YAML lub metody programowej do wdrożenia grupy kontenerów w podsieci, należy podać pełny Menedżer zasobów identyfikator zasobu profilu sieciowego. Możesz użyć profilu utworzonego wcześniej przy użyciu polecenia [AZ Container Create][az-container-create]lub Create a profile przy użyciu szablonu Menedżer zasobów (zobacz [przykład szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) i [odwołanie](/azure/templates/microsoft.network/networkprofiles)). Aby uzyskać identyfikator utworzonego wcześniej profilu, użyj polecenia [AZ Network profile list][az-network-profile-list] . 

Na poniższym diagramie kilka grup kontenerów zostało wdrożonych w podsieci delegowanej do Azure Container Instances. Po wdrożeniu jednej grupy kontenerów w podsieci można wdrożyć do niej dodatkowe grupy kontenerów, określając ten sam profil sieci.

![Grupy kontenerów w ramach sieci wirtualnej][aci-vnet-01]

## <a name="next-steps"></a>Następne kroki

* Przykłady wdrożenia z interfejsem wiersza polecenia platformy Azure można znaleźć [w temacie Deploying instance Containers to the Azure Virtual Network](container-instances-vnet.md).
* Aby wdrożyć nową sieć wirtualną, podsieć, profil sieci i grupę kontenerów przy użyciu szablonu Menedżer zasobów, zobacz [Tworzenie grupy kontenerów platformy Azure przy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)użyciu sieci wirtualnej.
* Podczas tworzenia wystąpienia kontenera przy użyciu [Azure Portal](container-instances-quickstart-portal.md) można także podać ustawienia dla nowej lub exsting sieci wirtualnej na karcie **Sieć** .


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
