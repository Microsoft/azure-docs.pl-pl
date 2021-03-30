---
title: Łączenie z zapleczem w wersji 1
description: Dowiedz się, jak bezpiecznie łączyć się z zasobami zaplecza z poziomu App Service Environment. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961809"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Bezpieczne nawiązywanie połączenia z zasobami zaplecza ze środowiska App Service
Ponieważ App Service Environment jest zawsze **tworzona w sieci** wirtualnej Azure Resource Manager **lub** w klasycznej [sieci wirtualnej][virtualnetwork]modelu wdrożenia, połączenia wychodzące z App Service Environment do innych zasobów zaplecza mogą przepływać wyłącznie za pośrednictwem sieci wirtualnej. Od czerwca 2016 środowisk ASE można również wdrożyć do sieci wirtualnych, które korzystają z zakresów adresów publicznych lub przestrzeni adresów RFC1918 (adresów prywatnych).  

Na przykład może istnieć SQL Server uruchomiony w klastrze maszyn wirtualnych z zablokowanym portem 1433.  Punkt końcowy może być ACLd, aby zezwalać na dostęp tylko z innych zasobów w tej samej sieci wirtualnej.  

Innym przykładem, poufne punkty końcowe mogą działać lokalnie i być połączone z platformą Azure za pośrednictwem połączeń [między lokacjami lub][SiteToSite] [Azure ExpressRoute][ExpressRoute] .  W związku z tym tylko zasoby w sieciach wirtualnych podłączonych do tuneli typu lokacja-lokacja lub ExpressRoute mogą uzyskiwać dostęp do lokalnych punktów końcowych.

We wszystkich tych scenariuszach aplikacje działające na App Service Environment mogą bezpiecznie łączyć się z różnymi serwerami i zasobami. Jeśli ruch wychodzący z aplikacji jest uruchamiany w App Service Environment do prywatnych punktów końcowych w tej samej sieci wirtualnej lub połączony z tą samą siecią wirtualną, będzie przepływać tylko w sieci wirtualnej.  Ruch wychodzący do prywatnych punktów końcowych nie będzie przepływać przez publiczny Internet.

Jeden problem dotyczy ruchu wychodzącego z App Service Environment do punktów końcowych w ramach sieci wirtualnej. Środowiska App Service nie mogą uzyskać dostępu do punktów końcowych maszyn wirtualnych znajdujących się w **tej samej** podsieci co App Service Environment. To ograniczenie zwykle nie powinno być problemem, jeśli środowiska App Service są wdrażane w podsieci zarezerwowanej do użytku na wyłączność przez App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Outbound Connectivity and DNS Requirements (Wymagania dotyczące łączności wychodzącej i systemu DNS)
Aby App Service Environment działała prawidłowo, wymaga dostępu wychodzącego do różnych punktów końcowych. Pełna lista zewnętrznych punktów końcowych używanych przez środowisko ASE znajduje się w sekcji "wymagana łączność sieciowa" w artykule [Konfiguracja sieci dla ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Środowiska App Service wymagają prawidłowej infrastruktury DNS skonfigurowanej dla sieci wirtualnej.  Jeśli konfiguracja DNS zostanie zmieniona po utworzeniu App Service Environment, deweloperzy mogą wymusić App Service Environment do pobrania nowej konfiguracji DNS. W górnej części bloku zarządzanie App Service Environment w portalu wybierz ikonę **ponownego uruchomienia** , aby wyzwolić stopniowe ponowne uruchomienie środowiska, co spowoduje pobranie nowej konfiguracji DNS.

Zaleca się również, aby wszystkie niestandardowe serwery DNS w sieci wirtualnej były konfigurowane przed utworzeniem App Service Environment.  Jeśli konfiguracja DNS sieci wirtualnej zostanie zmieniona podczas tworzenia App Service Environment, spowoduje to niepowodzenie procesu tworzenia App Service Environment. Na drugiej stronie bramy sieci VPN, jeśli istnieje niestandardowy serwer DNS, który jest nieosiągalny lub niedostępny, proces tworzenia App Service Environment również zakończy się niepowodzeniem.

## <a name="connecting-to-a-sql-server"></a>Nawiązywanie połączenia z SQL Server
Wspólna konfiguracja SQL Server ma punkt końcowy nasłuchuje na porcie 1433:

![SQL Server punkt końcowy][SqlServerEndpoint]

Istnieją dwa podejścia do ograniczania ruchu do tego punktu końcowego:

* [Listy Access Control sieci][NetworkAccessControlLists] (listy ACL sieci)
* [Grupy zabezpieczeń sieci][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Ograniczanie dostępu za pomocą listy ACL sieci
Port 1433 może być zabezpieczony przy użyciu listy kontroli dostępu do sieci.  Poniższy przykład dodaje do uprawnień do przypisywania adresy klientów pochodzące z sieci wirtualnej i blokuje dostęp do wszystkich innych klientów.

![Przykład listy Access Control sieciowych][NetworkAccessControlListExample]

Wszystkie aplikacje, które działają w App Service Environment, w tej samej sieci wirtualnej co SQL Server, mogą łączyć się z wystąpieniem SQL Server. Użyj **wewnętrznego** adresu IP dla maszyny wirtualnej SQL Server.  

Przykładowe parametry połączenia poniżej odwołują się do SQL Server przy użyciu swojego prywatnego adresu IP.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Mimo że maszyna wirtualna ma także publiczny punkt końcowy, próba połączenia z użyciem publicznego adresu IP zostanie odrzucona ze względu na listę kontroli dostępu do sieci. 

## <a name="restricting-access-with-a-network-security-group"></a>Ograniczanie dostępu z sieciową grupą zabezpieczeń
Alternatywnym podejściem do zabezpieczania dostępu jest Grupa zabezpieczeń sieci.  Sieciowe grupy zabezpieczeń można stosować do poszczególnych maszyn wirtualnych lub do podsieci zawierającej maszyny wirtualne.

Najpierw należy utworzyć sieciową grupę zabezpieczeń:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Ograniczanie dostępu do tylko wewnętrznego ruchu sieciowego jest proste z sieciową grupą zabezpieczeń.  Reguły domyślne w sieciowej grupie zabezpieczeń zezwalają na dostęp tylko z innych klientów sieciowych w tej samej sieci wirtualnej.

W efekcie zablokowanie dostępu do SQL Server jest proste. Po prostu Zastosuj grupę zabezpieczeń sieci z jej domyślnymi regułami do maszyn wirtualnych z systemem SQL Server lub podsieci zawierającej maszyny wirtualne.

W poniższym przykładzie zastosowano grupę zabezpieczeń sieci do podsieci zawierającej:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

Końcowy wynik to zestaw reguł zabezpieczeń, które blokują dostęp zewnętrzny, jednocześnie zapewniając dostęp wewnętrzny do sieci wirtualnej:

![Domyślne reguły zabezpieczeń sieci][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service środowiskami, zobacz [wprowadzenie do App Service Environment][IntroToAppServiceEnvironment]

Aby uzyskać szczegółowe informacje na temat kontrolowania ruchu przychodzącego do App Service Environment, zobacz [sterowanie ruchem przychodzącym do App Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png