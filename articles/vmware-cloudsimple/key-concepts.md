---
title: Kluczowe pojęcia dotyczące administrowania rozwiązaniem Azure VMware przez CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: W tym artykule opisano kluczowe pojęcia dotyczące administrowania rozwiązaniami VMware platformy Azure przez CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 410215550f728d6b11b02b3ef108d429481e42a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869026"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Kluczowe pojęcia dotyczące administrowania rozwiązaniami VMware platformy Azure przez CloudSimple

Administrowanie rozwiązaniami VMware platformy Azure przez CloudSimple wymaga zrozumienia następujących pojęć:

* Usługa CloudSimple, która jest wyświetlana jako rozwiązania Azure VMware firmy CloudSimple — usługa
* Węzeł CloudSimple, który jest wyświetlany jako rozwiązania Azure VMware przez CloudSimple - Node
* Chmura prywatna CloudSimple
* Sieci usług
* Maszyna wirtualna CloudSimple, która jest wyświetlana jako rozwiązania Azure VMware firmy CloudSimple — maszyna wirtualna

## <a name="cloudsimple-service"></a>Usługa CloudSimple

Za pomocą usługi CloudSimple można tworzyć wszystkie zasoby skojarzone z rozwiązaniami VMware przez CloudSimple i zarządzać nimi z witryny Azure portal. Utwórz zasób usługi w każdym regionie, w którym zamierzasz korzystać z usługi.

Dowiedz się więcej o [usłudze CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Węzeł CloudSimple

Węzeł CloudSimple to dedykowany, nieotworzony, hiperkonwergentny host obliczeniowy i magazynowy, na którym wdrożono hipernadzorcę VMware ESXi. Ten węzeł jest następnie włączany do platform VMware vSphere, vCenter, vSAN i NSX. Usługi sieciowe CloudSimple i usługi sieciowe brzegowe są również włączone. Każdy węzeł służy jako jednostka mocy obliczeniowej i pojemności pamięci masowej, którą można aprowizować w celu [utworzenia chmur prywatnych CloudSimple.](cloudsimple-private-cloud.md) Aprowizowanie lub rezerwowanie węzłów w regionie, w którym usługa CloudSimple jest dostępna.

Dowiedz się więcej o [węzłach CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Chmura prywatna CloudSimple

CloudSimple private cloud to izolowane środowisko stosu VMware zarządzane przez serwer vCenter we własnej domenie zarządzania. Stos VMware zawiera hosty ESXi, vSphere, vCenter, vSAN i NSX. Stos działa na dedykowanych węzłach (dedykowany i izolowany sprzęt bez systemu metalowego) i jest używany przez użytkowników za pośrednictwem natywnych narzędzi VMware, które obejmują vCenter i NSX Manager. Dedykowane węzły są wdrażane w lokalizacjach platformy Azure i są zarządzane przez platformę Azure. Każda chmura prywatna może być podzielona na segmenty i zabezpieczona za pomocą usług sieciowych, takich jak sieci VPN i podsieci oraz tabele zapór. Połączenia ze środowiskiem lokalnym i siecią platformy Azure są tworzone przy użyciu bezpiecznych, prywatnych połączeń VPN i Usługi Azure ExpressRoute.

Dowiedz się więcej o [chmurze prywatnej CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Sieci usług

Usługa CloudSimple udostępnia sieć dla regionu, w którym wdrożono usługę CloudSimple. Sieć jest pojedynczą przestrzenią adresową warstwy TCP 3 z domyślnie włączoną obsługą routingu. Wszystkie chmury prywatne i podsieci utworzone w tym regionie komunikują się ze sobą bez żadnej dodatkowej konfiguracji. Grupy portów rozproszonych są tworzą w centrum vCenter przy użyciu sieci VLAN. Za pomocą następujących funkcji sieciowych można skonfigurować i zabezpieczyć zasoby obciążenia w chmurze prywatnej:

* [Sieci VLAN i podsieci](cloudsimple-vlans-subnets.md)
* [Tabele zapory](cloudsimple-firewall-tables.md)
* [Bramy sieci VPN](cloudsimple-vpn-gateways.md)
* [Publiczny adres IP](cloudsimple-public-ip-address.md)
* [Połączenie z siecią platformy Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Maszyna wirtualna CloudSimple

Dzięki usłudze CloudSimple możesz zarządzać maszynami wirtualnymi VMware z witryny Azure portal. Co najmniej jeden klastry lub pule zasobów ze środowiska vSphere mogą być mapowane do subskrypcji, na której jest tworzona usługa.

Dowiedz się więcej o usługach:

* [Maszyny wirtualne CloudSimple](cloudsimple-virtual-machines.md)
* [Mapowanie subskrypcji platformy Azure](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-subscription-mapping/)
