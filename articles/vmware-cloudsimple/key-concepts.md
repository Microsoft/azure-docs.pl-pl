---
title: Kluczowe pojęcia związane z administrowaniem rozwiązaniem VMware platformy Azure przez CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje kluczowe pojęcia związane z administrowaniem rozwiązaniami VMware platformy Azure według CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5544ef7725855d28e20d39ff345db6bb07671a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895329"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Kluczowe pojęcia związane z administracją rozwiązań VMware platformy Azure według CloudSimple

Administrowanie rozwiązaniami VMware platformy Azure przez CloudSimple wymaga znajomości następujących pojęć:

* Usługa CloudSimple, która jest wyświetlana jako rozwiązanie platformy Azure VMware według CloudSimple — usługa
* Węzeł CloudSimple, który jest wyświetlany jako rozwiązanie platformy Azure VMware według CloudSimple — węzeł
* CloudSimple chmurę prywatną
* Sieć usług
* CloudSimple maszynę wirtualną, która jest wyświetlana jako rozwiązań VMware platformy Azure przez CloudSimple — maszyna wirtualna

## <a name="cloudsimple-service"></a>Usługa CloudSimple

Usługa CloudSimple umożliwia tworzenie wszystkich zasobów skojarzonych z rozwiązaniami VMware i zarządzanie nimi za pomocą CloudSimple z Azure Portal. Utwórz zasób usługi w każdym regionie, w którym zamierzasz korzystać z usługi.

Dowiedz się więcej o [usłudze CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Węzeł CloudSimple

Węzeł CloudSimple to dedykowany Host obliczeniowy i magazyn, do którego wdrożono funkcję hypervisor VMware ESXi. Ten węzeł jest następnie dołączany do platform VMware vSphere, vCenter, sieci vSAN i NSX. Usługi sieciowe CloudSimple i usługi sieci brzegowej również są włączone. Każdy węzeł służy jako jednostka pojemności obliczeniowej i magazynu, którą można udostępnić do tworzenia [chmur prywatnych CloudSimple](cloudsimple-private-cloud.md). W regionie, w którym jest dostępna usługa CloudSimple, można zainicjować obsługę administracyjną lub zarezerwować węzły.

Dowiedz się więcej o [węzłach CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple chmurę prywatną

Chmura prywatna CloudSimple to izolowane środowisko stosu VMware zarządzane przez serwer vCenter we własnej domenie zarządzania. Stos VMware obejmuje hosty ESXi, vSphere, vCenter, sieci vSAN i NSX. Stos działa na dedykowanych węzłach (dedykowany i izolowany sprzęt bez systemu operacyjnego) i jest używany przez użytkowników za pomocą natywnych narzędzi VMware, które obejmują program vCenter i NSX Manager. Węzły dedykowane są wdrażane w lokalizacjach platformy Azure i są zarządzane przez platformę Azure. Każdą chmurę prywatną można podzielić na segmenty i zabezpieczyć przy użyciu usług sieciowych, takich jak sieci VLAN i podsieci oraz tabele zapory. Połączenia ze środowiskiem lokalnym i siecią platformy Azure są tworzone przy użyciu bezpiecznych, prywatnych sieci VPN i połączeń usługi Azure ExpressRoute.

Dowiedz się więcej o [chmurze prywatnej CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Sieć usług

Usługa CloudSimple zapewnia sieć na region, w którym wdrożono usługę CloudSimple. Sieć jest pojedynczą przestrzenią adresową TCP warstwy 3 z włączoną funkcją routingu. Wszystkie chmury prywatne i podsieci utworzone w tym regionie komunikują się ze sobą bez żadnej dodatkowej konfiguracji. Na serwerze vCenter tworzysz rozproszone grupy portów przy użyciu sieci VLAN. Aby skonfigurować i zabezpieczyć zasoby obciążeń w chmurze prywatnej, można użyć następujących funkcji sieciowych:

* [Sieci VLAN i podsieci](cloudsimple-vlans-subnets.md)
* [Tabele zapory](cloudsimple-firewall-tables.md)
* [Bramy sieci VPN](cloudsimple-vpn-gateways.md)
* [Publiczny adres IP](cloudsimple-public-ip-address.md)
* [Połączenie z siecią platformy Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple maszynę wirtualną

Za pomocą usługi CloudSimple można zarządzać maszynami wirtualnymi VMware z poziomu Azure Portal. Co najmniej jeden klaster lub pule zasobów ze środowiska vSphere można zamapować na subskrypcję, w której jest tworzona usługa.

Dowiedz się więcej na następujące tematy:

* [CloudSimple maszyn wirtualnych](cloudsimple-virtual-machines.md)
* [Mapowanie subskrypcji platformy Azure](./azure-subscription-mapping.md)
