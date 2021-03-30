---
title: Konfigurowanie usługi Azure ExpressRoute w wersji 1
description: Konfiguracja sieci dla App Service Environment aplikacji PowerApps z usługą Azure ExpressRoute. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961826"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Szczegóły konfiguracji sieci dla App Service Environment dla usługi PowerApps z usługą Azure ExpressRoute

Klienci mogą połączyć obwód [usługi Azure ExpressRoute][ExpressRoute] z infrastrukturą sieci wirtualnej, aby zwiększyć możliwości sieci lokalnej na platformę Azure. App Service Environment jest tworzony w podsieci infrastruktury [sieci wirtualnej][virtualnetwork] . Aplikacje działające na App Service Environment ustanawiają bezpieczne połączenia z zasobami zaplecza, które są dostępne tylko za pośrednictwem połączenia ExpressRoute.  

App Service Environment można utworzyć w następujących scenariuszach:
- Azure Resource Manager sieci wirtualnych.
- Sieci wirtualne klasycznego modelu wdrażania.
- Sieci wirtualne korzystające z zakresów adresów publicznych lub przestrzeni adresów RFC1918 (czyli adresów prywatnych). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Wymagana łączność sieciowa

App Service Environment ma wymagania dotyczące łączności sieciowej, które początkowo mogą nie zostać spełnione w sieci wirtualnej, która jest połączona z usługą ExpressRoute.

App Service Environment wymaga poprawnego działania następujących ustawień łączności sieciowej:

* Wychodząca łączność sieciowa z punktami końcowymi usługi Azure Storage na całym świecie na portach 80 i 443. Punkty końcowe znajdują się w tym samym regionie co App Service Environment, a także w innych regionach świadczenia usługi Azure. Punkty końcowe usługi Azure Storage są rozwiązywane w następujących domenach DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net i file.core.windows.net.  

* Wychodząca łączność sieciowa z usługą Azure Files na porcie 445.

* Wychodząca łączność sieciowa z punktami końcowymi Azure SQL Database, które znajdują się w tym samym regionie co App Service Environment. SQL Database punkty końcowe są rozwiązywane w domenie database.windows.net, która wymaga otwartego dostępu do portów 1433, 11000-11999 i 14000-14999. Aby uzyskać szczegółowe informacje na temat użycia portów SQL Database V12, zobacz [porty powyżej 1433 dla ADO.NET 4,5](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md).

* Wychodząca łączność sieciowa z punktami końcowymi płaszczyzny zarządzania platformy Azure (klasycznym modelem wdrażania platformy Azure i Azure Resource Manager punktami końcowymi). Łączność z tymi punktami końcowymi obejmuje domeny management.core.windows.net i management.azure.com. 

* Wychodząca łączność sieciowa z domenami ocsp.msocsp.com, mscrl.microsoft.com i crl.microsoft.com. Łączność z tymi domenami jest wymagana do obsługi funkcji TLS.

* Konfiguracja systemu DNS dla sieci wirtualnej musi być w stanie rozpoznać wszystkie punkty końcowe i domeny wymienione w tym artykule. Jeśli nie można rozpoznać punktów końcowych, tworzenie App Service Environment nie powiedzie się. Wszystkie istniejące App Service Environment są oznaczone jako w złej kondycji.

* Dostęp wychodzący na porcie 53 jest wymagany do komunikacji z serwerami DNS.

* Jeśli na drugim końcu bramy sieci VPN istnieje niestandardowy serwer DNS, serwer DNS musi być dostępny z podsieci zawierającej App Service Environment. 

* Wychodząca ścieżka sieciowa nie może przechodzić przez wewnętrzne serwery proxy firmy i nie może być wymuszana w środowisku lokalnym. Te akcje zmieniają obowiązujący adres NAT ruchu sieciowego wychodzącego z App Service Environment. Zmiany w adresie NAT App Service Environment wychodzącego ruchu sieciowego powodują błędy łączności z wieloma punktami końcowymi. Tworzenie App Service Environment nie powiedzie się. Wszystkie istniejące App Service Environment są oznaczone jako w złej kondycji.

* Przychodzący dostęp sieciowy do wymaganych portów dla App Service Environment musi być dozwolony. Aby uzyskać szczegółowe informacje, zobacz [jak kontrolować ruch przychodzący do App Service Environment][requiredports].

Aby spełnić wymagania systemu DNS, upewnij się, że skonfigurowano prawidłową infrastrukturę DNS i jest ona utrzymywana dla sieci wirtualnej. Jeśli konfiguracja DNS zostanie zmieniona po utworzeniu App Service Environment, deweloperzy mogą wymusić App Service Environment, aby pobrać nową konfigurację DNS. Stopniowe ponowne uruchomienie środowiska można wyzwolić, korzystając z ikony **ponownego uruchamiania** w obszarze App Service Environment zarządzanie w [Azure Portal][NewPortal]. Ponowne uruchomienie powoduje, że środowisko pobiera nową konfigurację DNS.

Aby spełnić wymagania dotyczące dostępu do sieci przychodzącej, należy skonfigurować [sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)][NetworkSecurityGroups] w podsieci App Service Environment. SIECIOWEJ grupy zabezpieczeń umożliwia wymagany dostęp [do kontroli ruchu przychodzącego do App Service Environment][requiredports].

## <a name="outbound-network-connectivity"></a>Wychodząca łączność sieciowa

Domyślnie nowo utworzony obwód usługi ExpressRoute anonsuje trasę domyślną, która umożliwia wychodzące połączenie z Internetem. App Service Environment może użyć tej konfiguracji do nawiązania połączenia z innymi punktami końcowymi platformy Azure.

Typową konfiguracją klienta jest zdefiniowanie własnej trasy domyślnej (0.0.0.0/0), która wymusza ruch z Internetu do przepływu lokalnego. Ten przepływ ruchu niezmiennie przerwań w App Service Environment. Ruch wychodzący jest blokowany lokalnie lub NAT na nierozpoznawalny zbiór adresów, które nie działają już z różnymi punktami końcowymi platformy Azure.

Rozwiązanie polega na zdefiniowaniu co najmniej jednej trasy zdefiniowanej przez użytkownika (UDR) w podsieci zawierającej App Service Environment. UDR definiuje trasy specyficzne dla podsieci, które są honorowane zamiast trasy domyślnej.

Jeśli to możliwe, należy użyć następującej konfiguracji:

* Konfiguracja ExpressRoute anonsuje 0.0.0.0/0. Domyślnie tunel wymusza cały ruch wychodzący w środowisku lokalnym.
* UDR zastosowana do podsieci, która zawiera App Service Environment definiuje 0.0.0.0/0 z typem następnego przeskoku Internet. Przykład tej konfiguracji przedstawiono w dalszej części tego artykułu.

Połączony efekt tej konfiguracji polega na tym, że UDR poziomu podsieci ma pierwszeństwo przed tunelowaniem wymuszonym ExpressRoute. Zapewnianie wychodzącego dostępu do Internetu z App Service Environment jest gwarantowane.

> [!IMPORTANT]
> Trasy zdefiniowane w UDR muszą być wystarczająco odpowiednie, aby mieć pierwszeństwo przed wszelkimi trasami anonsowanymi przez konfigurację ExpressRoute. W przykładzie opisanym w następnej sekcji jest stosowany szeroki zakres adresów 0.0.0.0/0. Ten zakres może być przypadkowo przesłonięty przez anonse trasy, które używają bardziej szczegółowych zakresów adresów.
> 
> App Service Environment nie jest obsługiwana w przypadku konfiguracji ExpressRoute, które przesyłają krzyżowo trasy z publicznej ścieżki komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej. Konfiguracje ExpressRoute, które mają skonfigurowaną publiczną komunikację równorzędną, odbierają Anonsy tras od firmy Microsoft w ramach dużego zestawu Microsoft Azure zakresów adresów IP. Jeśli te zakresy adresów są anonsowane krzyżowo w ścieżce prywatnej komunikacji równorzędnej, wszystkie pakiety sieciowe wychodzące z podsieci App Service Environment są Wymuszone tunelowanie do lokalnej infrastruktury sieciowej klienta. Ten przepływ sieciowy nie jest obecnie obsługiwany w App Service Environment. Jednym z rozwiązań jest zatrzymanie tras krzyżowego anonsowania od publicznej ścieżki komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej.
> 
> 

Aby uzyskać ogólne informacje dotyczące tras zdefiniowanych przez użytkownika, zobacz [routing ruchu w sieci wirtualnej][UDROverview].  

Aby dowiedzieć się, jak tworzyć i konfigurować trasy zdefiniowane przez użytkownika, zobacz temat [kierowanie ruchu sieciowego za pomocą tabeli tras przy użyciu programu PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Konfiguracja UDR

W tej sekcji przedstawiono przykładową konfigurację UDR dla App Service Environment.

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj Azure PowerShell ze [strony plików do pobrania platformy Azure][AzureDownloads]. Wybierz pobieranie z datą 2015 czerwca lub nowszą. W obszarze **narzędzia wiersza polecenia**  >  **programu Windows PowerShell** wybierz pozycję **Zainstaluj** , aby zainstalować najnowsze polecenia cmdlet programu PowerShell.

* Utwórz unikatową podsieć do wyłącznego użytku przez App Service Environment. Unikatowa podsieć zapewnia, że UDR zastosowana do podsieci otwiera ruch wychodzący tylko dla App Service Environment.

> [!IMPORTANT]
> Wdrażaj App Service Environment tylko po wykonaniu czynności konfiguracyjnych. Przed podjęciem próby wdrożenia App Service Environment upewnij się, że jest dostępna wychodząca łączność sieciowa.

### <a name="step-1-create-a-route-table"></a>Krok 1. Tworzenie tabeli tras

Utwórz tabelę tras o nazwie **DirectInternetRouteTable** w regionie platformy Azure zachodnie stany USA, jak pokazano w tym fragmencie kodu:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Krok 2. Tworzenie tras w tabeli

Dodaj trasy do tabeli tras, aby włączyć wychodzący dostęp do Internetu.  

Skonfiguruj dostęp wychodzący do Internetu. Zdefiniuj trasę dla 0.0.0.0/0, jak pokazano w tym fragmencie kodu:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 jest szerokim zakresem adresów. Zakres jest przesłonięty przez zakresy adresów anonsowane przez ExpressRoute, które są bardziej szczegółowe. UDR z trasą 0.0.0.0/0 należy używać w połączeniu z konfiguracją ExpressRoute, która anonsuje tylko 0.0.0.0/0. 

Alternatywnie można pobrać bieżącą, kompleksową listę zakresów CIDR używanych przez platformę Azure. Plik XML dla wszystkich zakresów adresów IP platformy Azure jest dostępny w [Centrum pobierania Microsoft][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Zakresy adresów IP platformy Azure zmieniają się z upływem czasu. Trasy zdefiniowane przez użytkownika wymagają okresowego ręcznego aktualizowania, aby zachować synchronizację.
>
> Pojedynczy UDR ma domyślny górny limit 100 tras. Należy "podsumować" zakresy adresów IP platformy Azure, aby mieściły się w limicie 100-trasie. Trasy zdefiniowane przez UDR muszą być bardziej specyficzne niż trasy anonsowane przez połączenie ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Krok 3. Kojarzenie tabeli z podsiecią

Skojarz tabelę tras z podsiecią, w której zamierzasz wdrożyć App Service Environment. To polecenie kojarzy tabelę **DirectInternetRouteTable** z podsiecią **ASESubnet** , która będzie zawierać App Service Environment.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Krok 4. testowanie i potwierdzenie trasy

Po powiązaniu tabeli tras z podsiecią Przetestuj i Potwierdź trasę.

Wdróż maszynę wirtualną w podsieci i Potwierdź następujące warunki:

* Ruch wychodzący do punktów końcowych platformy Azure i spoza platformy Azure opisany w tym artykule **nie** przepływa w dół obwodu usługi ExpressRoute. Jeśli ruch wychodzący z podsieci jest wymuszany w środowisku lokalnym, tworzenie App Service Environment zawsze kończy się niepowodzeniem.
* Wyszukiwania DNS dla punktów końcowych opisanych w tym artykule wszystkie rozwiązują się prawidłowo. 

Po wykonaniu kroków konfiguracji i zatwierdzeniu trasy Usuń maszynę wirtualną. Po utworzeniu App Service Environment podsieć musi być pusta.

Teraz wszystko jest gotowe do wdrożenia App Service Environment!

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą App Service Environment dla usługi PowerApps, zobacz [wprowadzenie do App Service Environment][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->