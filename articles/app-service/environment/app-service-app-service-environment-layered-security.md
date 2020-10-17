---
title: Zabezpieczenia warstwowe v1
description: Dowiedz się, jak zaimplementować warstwową architekturę zabezpieczeń w środowisku App Service. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: ab26ad08947a4a9929ae0d41b669160b3d159224
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150235"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementowanie warstwowej architektury zabezpieczeń w środowiskach App Service
Ponieważ środowiska App Service udostępniają izolowane środowisko uruchomieniowe wdrożone w sieci wirtualnej, deweloperzy mogą tworzyć warstwowe architektury zabezpieczeń zapewniające różne poziomy dostępu do sieci dla każdej warstwy fizycznej aplikacji.

Typowym pragnieniem jest ukrycie zaplecza interfejsu API z ogólnego dostępu do Internetu i Zezwalanie na wywoływanie interfejsów API przez nadrzędne aplikacje sieci Web.  [Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)][NetworkSecurityGroups] mogą być używane w podsieciach zawierających App Service środowiska w celu ograniczenia publicznego dostępu do aplikacji interfejsu API.

Na poniższym diagramie przedstawiono przykładową architekturę z aplikacją opartą na WebAPI wdrożoną na App Service Environment.  Trzy oddzielne wystąpienia aplikacji sieci Web, wdrożone w trzech oddzielnych środowiskach App Service, wykonują wywołania zaplecza do tej samej aplikacji WebAPI.

![Architektura koncepcyjna][ConceptualArchitecture] 

Zielony znak plus wskazuje, że sieciowa Grupa zabezpieczeń w podsieci zawierającej "apiase" zezwala na wywołania przychodzące z nadrzędnych aplikacji sieci Web, jak również wywołania z samego siebie.  Jednak ta sama sieciowa Grupa zabezpieczeń jawnie nie zezwala na dostęp do ogólnego ruchu przychodzącego z Internetu. 

W dalszej części tego artykułu przedstawiono kroki niezbędne do skonfigurowania sieciowej grupy zabezpieczeń w podsieci zawierającej "apiase".

## <a name="determining-the-network-behavior"></a>Określanie zachowania sieci
Aby dowiedzieć się, jakie reguły zabezpieczeń sieci są potrzebne, należy określić, którzy klienci sieci będą mogli dotrzeć do App Service Environment zawierającego aplikację interfejsu API, a klienci zostaną zablokowani.

Ze względu na to, że [sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)][NetworkSecurityGroups] są stosowane do podsieci, a środowiska App Service są wdrażane w podsieciach, reguły zawarte w sieciowej grupy zabezpieczeń stosują się do **wszystkich** aplikacji uruchomionych na App Service Environment.  Korzystając z przykładowej architektury dla tego artykułu, po zastosowaniu sieciowej grupy zabezpieczeń do podsieci zawierającej "apiase" wszystkie aplikacje działające w App Service Environment "apiase" będą chronione przez ten sam zestaw reguł zabezpieczeń. 

* **Określ wychodzący adres IP wywołujących obiektów nadrzędnych:**  Jaki jest adres IP lub adresy obiektów wywołujących w strumieniu nadrzędnym?  Te adresy muszą mieć jawnie dozwolony dostęp w sieciowej grupy zabezpieczeń.  Ponieważ wywołania między środowiskami App Service są traktowane jako wywołania "internetowe", wychodzący adres IP przypisany do każdego z trzech środowisk nadrzędnych App Service musi mieć dozwolony dostęp w sieciowej grupy zabezpieczeń dla podsieci "apiase".   Aby uzyskać więcej informacji na temat określania wychodzącego adresu IP dla aplikacji działających w App Service Environment, zobacz artykuł Omówienie [architektury sieci][NetworkArchitecture] .
* **Czy aplikacja interfejsu API zaplecza musi być wywoływana sama?**  Czasami przebiegać i delikatny punkt jest scenariuszem, w którym aplikacja zaplecza musi sam wywołać.  Jeśli aplikacja interfejsu API zaplecza na App Service Environment musi wywołać sam siebie, jest również traktowana jako wywołanie "Internet".  W przykładowej architekturze wymaga to również zezwolenia na dostęp z wychodzącego adresu IP "apiase App Service Environment".

## <a name="setting-up-the-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń
Po znalezieniu zestawu wychodzących adresów IP następnym krokiem jest utworzenie sieciowej grupy zabezpieczeń.  Sieciowe grupy zabezpieczeń można tworzyć zarówno dla sieci wirtualnych opartych na Menedżer zasobów, jak i klasycznych sieci wirtualnych.  W poniższych przykładach pokazano, jak utworzyć i skonfigurować sieciowej grupy zabezpieczeń w klasycznej sieci wirtualnej przy użyciu programu PowerShell.

W przypadku architektury przykładowej środowiska znajdują się w Południowo-środkowe stany USA, więc w tym regionie jest tworzona pusta sieciowej grupy zabezpieczeń:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

Najpierw dodawana jest jawna reguła zezwalania dla infrastruktury zarządzania systemu Azure, która została zapisana w artykule dotyczącym [ruchu przychodzącego][InboundTraffic] dla środowisk App Serviceych.

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Następnie są dodawane dwie reguły zezwalające na wywołania HTTP i HTTPS z pierwszego nadrzędnego App Service Environment ("fe1ase").

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Przepłukać i powtórz dla drugiego i trzeciego środowiska App Service nadrzędnego ("fe2ase" i "fe3ase").

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Na koniec Udziel dostępu do wychodzącego adresu IP App Service Environment interfejsu API zaplecza, tak aby można było wywołać go ponownie.

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Nie są wymagane żadne inne reguły zabezpieczeń sieci, ponieważ każdy sieciowej grupy zabezpieczeń ma zestaw reguł domyślnych, które domyślnie blokują dostęp do ruchu przychodzącego z Internetu.

Poniżej przedstawiono pełną listę reguł w sieciowej grupie zabezpieczeń.  Zwróć uwagę na to, jak Ostatnia reguła, która jest wyróżniona, blokuje dostęp przychodzący ze wszystkich wywołujących, innych niż wywołujący, którym udzielono jawnie dostępu.

![Konfiguracja sieciowej grupy zabezpieczeń][NSGConfiguration] 

Ostatnim krokiem jest zastosowanie sieciowej grupy zabezpieczeń do podsieci zawierającej App Service Environment "apiase".

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

Po zastosowaniu sieciowej grupy zabezpieczeń do podsieci, tylko trzy środowiska App Service nadrzędnego i App Service Environment zawierające zaplecza interfejsu API, mogą wywołać środowisko "apiase".

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Informacje o [sieciowych grupach zabezpieczeń](../../virtual-network/network-security-groups-overview.md).

Informacje o [wychodzących adresach IP][NetworkArchitecture] i środowiskach App Service.

[Porty sieciowe][InboundTraffic] używane przez środowiska App Service.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png