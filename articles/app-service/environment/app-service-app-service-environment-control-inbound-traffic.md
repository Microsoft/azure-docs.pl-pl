---
title: Sterowanie ruchem przychodzącym w wersji 1
description: Dowiedz się porady kontrolować ruch przychodzący do App Service Environment. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804405"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Jak kontrolować ruch przychodzący do App Service Environment
## <a name="overview"></a>Omówienie
App Service Environment można **utworzyć w sieci** wirtualnej Azure Resource Manager **lub** w sieci [wirtualnej][virtualnetwork]klasycznego modelu wdrażania.  W momencie tworzenia App Service Environment można zdefiniować nową sieć wirtualną i nową podsieć.  Alternatywnie można utworzyć App Service Environment w istniejącej sieci wirtualnej i istniejącej podsieci.  Ze zmianami wprowadzonymi w czerwcu 2016 środowisk ASE można także wdrożyć w sieciach wirtualnych korzystających z zakresów adresów publicznych lub przestrzeni adresów RFC1918 (np. adresów prywatnych).  Więcej informacji na temat tworzenia App Service Environment można znaleźć w temacie [How to Create a App Service Environment][HowToCreateAnAppServiceEnvironment].

Należy zawsze utworzyć App Service Environment w podsieci, ponieważ podsieć zapewnia granicę sieci, która może służyć do blokowania ruchu przychodzącego za pośrednictwem urządzeń i usług nadrzędnych, takich jak ruch HTTP i HTTPS są akceptowane tylko z określonych nadrzędnych adresów IP.

Ruch sieciowy ruchu przychodzącego i wychodzącego w podsieci jest kontrolowany przy użyciu [sieciowej grupy zabezpieczeń][NetworkSecurityGroups]. Sterowanie ruchem przychodzącym wymaga tworzenia reguł zabezpieczeń sieci w sieciowej grupie zabezpieczeń, a następnie przypisywania grupy zabezpieczeń sieci podsieci zawierającej App Service Environment.

Po przypisaniu sieciowej grupy zabezpieczeń do podsieci ruch przychodzący do aplikacji w App Service Environment jest dozwolony/blokowany na podstawie reguł zezwalania i odmowy zdefiniowanych w sieciowej grupie zabezpieczeń.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Przychodzące porty sieciowe używane w App Service Environment
Przed zablokowaniem przychodzącego ruchu sieciowego z grupą zabezpieczeń sieci należy znać zestaw wymaganych i opcjonalnych portów sieciowych używanych przez App Service Environment.  Przypadkowe zamknięcie ruchu na niektórych portach może spowodować utratę funkcjonalności w App Service Environment.

Poniżej znajduje się lista portów używanych przez App Service Environment. Wszystkie porty są **TCP**, chyba że wyraźnie wskazano inaczej:

* 454: **wymagany port** używany przez infrastrukturę platformy Azure do zarządzania i konserwowania środowisk App Service przy użyciu protokołu TLS.  Nie blokuj ruchu do tego portu.  Ten port jest zawsze powiązany z publicznym adresem VIP środowiska ASE.
* 455: **wymagany port** używany przez infrastrukturę platformy Azure do zarządzania i konserwowania środowisk App Service przy użyciu protokołu TLS.  Nie blokuj ruchu do tego portu.  Ten port jest zawsze powiązany z publicznym adresem VIP środowiska ASE.
* 80: domyślny port ruchu HTTP dla ruchu przychodzącego do aplikacji uruchamianych w planach App Service w App Service Environment.  W środowisku ASE z obsługą ILB ten port jest powiązany z adresem ILB środowiska ASE.
* 443: domyślny port dla ruchu przychodzącego TLS do aplikacji uruchamianych w planach App Service w App Service Environment.  W środowisku ASE z obsługą ILB ten port jest powiązany z adresem ILB środowiska ASE.
* 21: Kanał kontrolny dla usługi FTP.  Ten port może być bezpiecznie zablokowany, jeśli nie jest używany protokół FTP.  W środowisku ASE z obsługą ILB ten port może być powiązany z adresem ILB dla środowiska ASE.
* 990: Kanał kontrolny dla FTPS.  Ten port może być bezpiecznie zablokowany, jeśli FTPS nie jest używany.  W środowisku ASE z obsługą ILB ten port może być powiązany z adresem ILB dla środowiska ASE.
* 10001-10020: kanały danych dla FTP.  Podobnie jak w przypadku kanału kontrolnego, te porty można bezpiecznie zablokować, jeśli usługa FTP nie jest używana.  W środowisku ASE z obsługą ILB ten port może być powiązany z adresem ILB środowiska ASE.
* 4016: służy do zdalnego debugowania w programie Visual Studio 2012.  Ten port może być bezpiecznie zablokowany, jeśli funkcja nie jest używana.  W środowisku ASE z obsługą ILB ten port jest powiązany z adresem ILB środowiska ASE.
* 4018: służy do zdalnego debugowania z Visual Studio 2013.  Ten port może być bezpiecznie zablokowany, jeśli funkcja nie jest używana.  W środowisku ASE z obsługą ILB ten port jest powiązany z adresem ILB środowiska ASE.
* 4020: służy do zdalnego debugowania w programie Visual Studio 2015.  Ten port może być bezpiecznie zablokowany, jeśli funkcja nie jest używana.  W środowisku ASE z obsługą ILB ten port jest powiązany z adresem ILB środowiska ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Outbound Connectivity and DNS Requirements (Wymagania dotyczące łączności wychodzącej i systemu DNS)
Aby App Service Environment działała prawidłowo, wymagany jest również dostęp wychodzący do różnych punktów końcowych. Pełna lista zewnętrznych punktów końcowych używanych przez środowisko ASE znajduje się w sekcji "wymagana łączność sieciowa" w artykule [Konfiguracja sieci dla ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Środowiska App Service wymagają prawidłowej infrastruktury DNS skonfigurowanej dla sieci wirtualnej.  Jeśli z jakiegoś powodu Konfiguracja DNS została zmieniona po utworzeniu App Service Environment, deweloperzy mogą wymusić App Service Environment do pobrania nowej konfiguracji DNS.  Wyzwalanie stopniowego ponownego uruchomienia środowiska przy użyciu ikony "restart" znajdującej się w górnej części bloku zarządzanie App Service Environment w [Azure Portal][NewPortal] spowodują pobranie nowej konfiguracji DNS przez środowisko.

Zaleca się również, aby wszystkie niestandardowe serwery DNS w sieci wirtualnej były skonfigurowane przed upływem czasu przed utworzeniem App Service Environment.  Jeśli konfiguracja DNS sieci wirtualnej zostanie zmieniona podczas tworzenia App Service Environment, spowoduje to niepowodzenie procesu tworzenia App Service Environment.  W podobnej kombinacji, jeśli niestandardowy serwer DNS istnieje na drugim końcu bramy sieci VPN, a serwer DNS jest nieosiągalny lub niedostępny, proces tworzenia App Service Environment również zakończy się niepowodzeniem.

## <a name="creating-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Aby uzyskać szczegółowe informacje na temat działania grup zabezpieczeń sieci, zobacz poniższe [informacje][NetworkSecurityGroups].  Poniższy przykład zarządzania usługami platformy Azure dotyka wyróżnionych grup zabezpieczeń sieci, a jednocześnie koncentruje się na konfigurowaniu i stosowaniu sieciowej grupy zabezpieczeń do podsieci zawierającej App Service Environment.

**Uwaga:** Sieciowe grupy zabezpieczeń można skonfigurować graficznie przy użyciu witryny [Azure Portal](https://portal.azure.com) lub za pośrednictwem Azure PowerShell.

Sieciowe grupy zabezpieczeń są najpierw tworzone jako jednostka autonomiczna skojarzona z subskrypcją. Ze względu na to, że sieciowe grupy zabezpieczeń są tworzone w regionie świadczenia usługi Azure, upewnij się, że sieciowa Grupa zabezpieczeń jest tworzona w tym samym regionie co App Service Environment.

Poniżej przedstawiono tworzenie sieciowej grupy zabezpieczeń:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po utworzeniu sieciowej grupy zabezpieczeń są do niej dodawane co najmniej jedna reguła zabezpieczeń sieci.  Ponieważ zestaw reguł może ulec zmianie z upływem czasu, zaleca się przekroczenie schematu numeracji używanego dla priorytetów reguł, aby ułatwić Wstawianie dodatkowych reguł w czasie.

W poniższym przykładzie przedstawiono regułę, która jawnie udziela dostępu do portów zarządzania wymaganych przez infrastrukturę platformy Azure do zarządzania App Service Environmentami i ich obsługi.  Należy zauważyć, że cały ruch związany z zarządzaniem odbywa się za pośrednictwem protokołu TLS i jest zabezpieczony przez certyfikaty klienta, więc mimo że otwarte porty są niedostępne dla każdej jednostki innej niż infrastruktura zarządzania platformy Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Po zablokowaniu dostępu do portów 80 i 443 w celu "ukrycia" App Service Environment za urządzeniem lub usługami nadrzędnymi, należy znać nadrzędny adres IP.  Na przykład jeśli używasz zapory aplikacji sieci Web (WAF), WAF będzie mieć własny adres IP (lub adresy), który jest używany podczas przesyłania ruchu proxy do App Service Environment podrzędnego.  Musisz użyć tego adresu IP w parametrze *DestinationPortRange i SourceAddressPrefix* reguły zabezpieczeń sieci.

W poniższym przykładzie ruch przychodzący z określonego nadrzędnego adresu IP jest jawnie dozwolony.  Adres *1.2.3.4* jest używany jako symbol zastępczy dla adresu IP nadrzędnego WAF.  Zmień wartość tak, aby była zgodna z adresem używanym przez urządzenie lub usługę nadrzędną.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Jeśli jest wymagana obsługa FTP, następujące reguły mogą służyć jako szablon do udzielania dostępu do portów sterowania FTP i portu kanału danych.  Ponieważ FTP jest protokołem stanowym, może nie być możliwe kierowanie ruchu FTP za pośrednictwem tradycyjnej zapory HTTP/HTTPS lub urządzenia serwera proxy.  W takim przypadku należy ustawić *DestinationPortRange i SourceAddressPrefix* na inną wartość — na przykład zakres adresów IP maszyn dewelopera lub wdrożenia, na których działają Klienci FTP. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Uwaga:** zakres portów kanału danych może ulec zmianie w okresie zapoznawczym).

Jeśli jest używane zdalne debugowanie z programem Visual Studio, następujące zasady pokazują, jak udzielić dostępu.  Istnieje oddzielna reguła dla każdej obsługiwanej wersji programu Visual Studio, ponieważ każda wersja używa innego portu do zdalnego debugowania.  Podobnie jak w przypadku dostępu za pomocą protokołu FTP, ruch zdalny może nie przepływać prawidłowo za pomocą tradycyjnego urządzenia WAF lub serwera proxy.  *DestinationPortRange i SourceAddressPrefix* zamiast tego można ustawić na zakres adresów IP maszyn deweloperskich z uruchomionym programem Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Przypisywanie sieciowej grupy zabezpieczeń do podsieci
Sieciowa Grupa zabezpieczeń ma domyślną regułę zabezpieczeń, która nie zezwala na dostęp do całego ruchu zewnętrznego.  Wynikiem łączenia reguł zabezpieczeń sieci opisanych powyżej, a domyślną regułą zabezpieczeń blokującą ruch przychodzący jest tylko ruch z zakresów adresów źródłowych skojarzonych z akcją *Zezwalaj* , będzie mógł wysyłać ruch do aplikacji uruchomionych w App Service Environment.

Po wypełnieniu grupy zabezpieczeń sieci z regułami zabezpieczeń należy przypisać ją do podsieci zawierającej App Service Environment.  Polecenie przypisania odwołuje się zarówno do nazwy sieci wirtualnej, w której znajduje się App Service Environment, jak i nazwy podsieci, w której utworzono App Service Environment.  

W poniższym przykładzie przedstawiono grupę zabezpieczeń sieci przypisaną do podsieci i sieci wirtualnej:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Po pomyślnym przypisaniu sieciowej grupy zabezpieczeń (przypisanie to długotrwałe operacje, które może potrwać kilka minut), tylko ruch przychodzący zgodny z regułami *Zezwalaj* będzie mógł uzyskiwać dostęp do aplikacji w App Service Environment.

W poniższym przykładzie pokazano, jak usunąć i w ten sposób utworzyć grupę zabezpieczeń sieci z podsieci:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Specjalne zagadnienia dotyczące jawnego protokołu IP-SSL
Jeśli aplikacja jest skonfigurowana za pomocą jawnego adresu IP-SSL (dotyczy *tylko* środowisk ASE z publicznym adresem VIP), a nie przy użyciu domyślnego adresu IP App Service Environment, ruch HTTP i HTTPS jest przenoszony do podsieci za pośrednictwem innego zestawu portów innego niż porty 80 i 443.

Poszczególne pary portów używane przez poszczególne adresy IP-SSL można znaleźć w interfejsie użytkownika portalu z bloku szczegóły App Service Environment.  Wybierz pozycję "wszystkie ustawienia" — > "adresy IP".  Blok "adresy IP" przedstawia tabelę wszystkich jawnie skonfigurowanych adresów IP-SSL dla App Service Environment oraz specjalną parę portów, która jest używana do kierowania ruchu HTTP i HTTPS skojarzonego z każdym adresem IP-SSL.  Jest to para portów, która musi być używana dla parametrów DestinationPortRange podczas konfigurowania reguł w sieciowej grupie zabezpieczeń.

Gdy aplikacja w środowisku ASE jest skonfigurowana do używania protokołu IP-SSL, klienci zewnętrzni nie będą widzieć i nie muszą martwić się o specjalne mapowanie pary portów.  Ruch do aplikacji będzie przepływać zwykle do skonfigurowanego adresu IP-SSL.  Tłumaczenie na specjalną parę portów jest automatycznie wykonywane wewnętrznie w końcowym etapie przesyłania ruchu sieciowego do podsieci zawierającej środowisko ASE. 

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service środowiskami, zobacz [wprowadzenie do App Service Environment][IntroToAppServiceEnvironment]

Aby uzyskać szczegółowe informacje dotyczące bezpiecznego łączenia się z zasobem zaplecza z App Service Environment, zobacz [bezpieczne nawiązywanie połączenia z zasobami zaplecza z poziomu App Service Environment][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

