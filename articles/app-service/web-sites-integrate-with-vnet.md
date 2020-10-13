---
title: Integrowanie aplikacji z usługą Azure Virtual Network
description: Integruj aplikację w Azure App Service z sieciami wirtualnymi platformy Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 141649f7620063b58134caaa878162f3a7e767e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739917"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z usługą Azure Virtual Network

W tym artykule opisano funkcję integracji sieci wirtualnej Azure App Service i sposób konfigurowania jej przy użyciu aplikacji w programie [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Korzystając z [usługi azure Virtual Network][VNETOverview] (sieci wirtualnych), możesz umieścić wiele zasobów platformy Azure w sieci bez obsługi Internetu. Funkcja integracji sieci wirtualnej umożliwia aplikacjom dostęp do zasobów w sieci wirtualnej lub przez sieć wirtualną. Integracja z siecią wirtualną nie umożliwia prywatnym dostępu do Twoich aplikacji.

Azure App Service ma dwie odmiany funkcji integracji sieci wirtualnej:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Włącz integrację sieci wirtualnej

1. Przejdź do interfejsu użytkownika **sieci** w portalu App Service. W obszarze integracja z siecią **wirtualną**wybierz **pozycję kliknij tutaj, aby ją skonfigurować**.

1. Wybierz pozycję **Dodaj sieć wirtualną**.

   ![Wybierz integrację sieci wirtualnej][1]

1. Lista rozwijana zawiera wszystkie Azure Resource Manager sieci wirtualnych w ramach subskrypcji w tym samym regionie. Poniżej znajduje się lista Menedżer zasobów sieci wirtualnych we wszystkich innych regionach. Wybierz sieć wirtualną, z którą chcesz przeprowadzić integrację.

   ![Wybierz sieć wirtualną][2]

   * Jeśli sieć wirtualna znajduje się w tym samym regionie, Utwórz nową podsieć lub Wybierz pustą istniejącą podsieć.
   * Aby można było wybrać sieć wirtualną w innym regionie, trzeba mieć zainicjowaną bramę sieci wirtualnej z włączoną opcją punkt do lokacji.
   * Aby przeprowadzić integrację z klasyczną siecią wirtualną, zamiast wybierać listę rozwijaną **Virtual Network** wybierz **pozycję kliknij tutaj, aby nawiązać połączenie z klasyczną siecią wirtualną**. Wybierz wybraną klasyczną sieć wirtualną. Docelowa sieć wirtualna musi mieć już zainicjowaną bramę Virtual Network z włączoną opcją punkt-lokacja.

    ![Wybieranie klasycznej sieci wirtualnej][3]

W trakcie integracji aplikacja zostanie ponownie uruchomiona. Po zakończeniu integracji zobaczysz szczegóły dotyczące sieci wirtualnej, z którą masz integrację.

## <a name="regional-vnet-integration"></a>Integracja z regionalną siecią wirtualną

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Sposób działania integracji regionalnej sieci wirtualnej

Aplikacje w App Service są hostowane na rolach procesów roboczych. Plany cenowe w warstwach Podstawowa i wyższa są dedykowanymi planami hostingu, w których nie ma innych obciążeń klientów działających w tych samych procesach roboczych. Integracja regionalnej sieci wirtualnej działa przez zainstalowanie interfejsów wirtualnych z adresami w podsieci delegowanej. Ponieważ adres od jest w sieci wirtualnej, może uzyskać dostęp do większości rzeczy w lub za pośrednictwem sieci wirtualnej, jak w przypadku maszyny wirtualnej w sieci wirtualnej. Implementacja sieci jest inna niż w przypadku uruchamiania maszyny wirtualnej w sieci wirtualnej. Dlatego niektóre funkcje sieciowe nie są jeszcze dostępne dla tej funkcji.

![Sposób działania integracji regionalnej sieci wirtualnej][5]

Gdy włączona jest regionalna Integracja sieci wirtualnej, aplikacja wykonuje wywołania wychodzące do Internetu za pomocą tych samych kanałów co normalny. Adresy wychodzące, które są wyświetlane w portalu właściwości aplikacji, to adresy nadal używane przez aplikację. Jakie zmiany w aplikacji są wywołaniami zabezpieczonych usług punktu końcowego usługi lub adresy RFC 1918, przejdź do sieci wirtualnej. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, cały ruch wychodzący może być wysyłany do sieci wirtualnej.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` nie jest obecnie obsługiwane w kontenerach systemu Windows.
> 

Funkcja obsługuje tylko jeden interfejs wirtualny na proces roboczy. Jeden wirtualny interfejs na proces roboczy oznacza jedną regionalną integrację sieci wirtualnej na App Service plan. Wszystkie aplikacje w tym samym planie App Service mogą korzystać z tej samej integracji sieci wirtualnej. Jeśli potrzebujesz aplikacji do łączenia się z dodatkową siecią wirtualną, musisz utworzyć kolejny plan App Service. Używany interfejs wirtualny nie jest zasobem, do którego klienci mają bezpośredni dostęp.

Ze względu na to, jak działa ta technologia, ruch używany z integracją sieci wirtualnej nie jest wyświetlany w usłudze Azure Network Watcher ani w dziennikach przepływów sieciowej grupy zabezpieczeń.

## <a name="gateway-required-vnet-integration"></a>Integracja z siecią wirtualną wymagana przez bramę

Integracja sieci wirtualnej wymagana przez bramę obsługuje łączenie z siecią wirtualną w innym regionie lub w klasycznej sieci wirtualnej. Integracja z siecią wirtualną wymagana przez bramę:

* Umożliwia aplikacji łączenie tylko z jedną siecią wirtualną jednocześnie.
* Umożliwia zintegrowanie maksymalnie pięciu sieci wirtualnych w ramach planu App Service.
* Umożliwia korzystanie z tej samej sieci wirtualnej przez wiele aplikacji w planie App Service bez wpływu na łączną liczbę, która może być używana przez plan App Service. Jeśli masz sześć aplikacji korzystających z tej samej sieci wirtualnej w ramach tego samego planu App Service, który jest liczony jako jedna sieć wirtualna.
* Program obsługuje umowę SLA na 99,9% ze względu na umowę SLA na bramie.
* Umożliwia aplikacjom używanie systemu DNS, z którym jest konfigurowana Sieć wirtualna.
* Wymaga Virtual Network bramy opartej na trasach skonfigurowanej z siecią VPN typu punkt-lokacja, zanim będzie można połączyć się z aplikacją.

Nie można używać integracji sieci wirtualnej wymaganej przez bramę:

* Z siecią wirtualną połączoną z usługą Azure ExpressRoute.
* Z poziomu aplikacji systemu Linux.
* Z [kontenera systemu Windows](quickstart-custom-container.md).
* Aby uzyskać dostęp do zabezpieczonych zasobów punktu końcowego usługi.
* Z bramą współistnienia obsługującą zarówno ExpressRoute, jak i sieci VPN typu lokacja-lokacja.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Konfigurowanie bramy w sieci wirtualnej platformy Azure ###

Aby utworzyć bramę:

1. [Utwórz podsieć bramy][creategatewaysubnet] w sieci wirtualnej.

1. [Utwórz bramę sieci VPN][creategateway]. Wybierz typ sieci VPN opartej na trasach.

1. [Ustaw adresy punkt-lokacja][setp2saddresses]. Jeśli Brama nie znajduje się w podstawowej jednostce SKU, protokół IKEV2 musi być wyłączony w konfiguracji punkt-lokacja, a protokół SSTP musi być wybrany. Przestrzeń adresowa punktu do lokacji musi znajdować się w blokach adresów RFC 1918 10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16.

Jeśli tworzysz bramę do użycia z integracją App Service sieci wirtualnej, nie musisz przekazywać certyfikatu. Tworzenie bramy może potrwać 30 minut. Nie będzie można zintegrować aplikacji z siecią wirtualną do momentu aprowizacji bramy.

### <a name="how-gateway-required-vnet-integration-works"></a>Jak działa integracja z siecią wirtualną wymaganą przez bramę

Integracja z siecią wirtualną wymagana przez bramę jest oparta na technologii sieci VPN typu punkt-lokacja. Sieci VPN typu punkt-lokacja ograniczają dostęp sieciowy do maszyny wirtualnej, która hostuje aplikację. Aplikacje są ograniczone do wysyłania ruchu do Internetu tylko za pomocą Połączenia hybrydowe lub przez integrację sieci wirtualnej. Gdy aplikacja zostanie skonfigurowana przy użyciu portalu do korzystania z integracji z siecią wirtualną wymaganą przez bramę, w Twoim imieniu zarządza się złożoną negocjacją w celu utworzenia i przypisania certyfikatów na bramie i stronie aplikacji. W wyniku tego pracownicy używający do hostowania aplikacji mogą bezpośrednio łączyć się z bramą sieci wirtualnej w wybrane sieci wirtualnej.

![Jak działa integracja z siecią wirtualną wymaganą przez bramę][6]

### <a name="access-on-premises-resources"></a>Dostęp do zasobów lokalnych

Aplikacje mogą uzyskiwać dostęp do zasobów lokalnych przez integrację z usługą sieci wirtualnych, która ma połączenia lokacja-lokacja. W przypadku używania integracji sieci wirtualnej wymaganej przez bramę należy zaktualizować trasy lokalnej bramy sieci VPN do bloków adresów punkt-lokacja. Po pierwszym skonfigurowaniu sieci VPN typu lokacja-lokacja, skrypty używane do skonfigurowania należy prawidłowo skonfigurować trasy. W przypadku dodania adresów punkt-lokacja po utworzeniu sieci VPN typu lokacja-lokacja należy ręcznie zaktualizować trasy. Szczegółowe informacje o tym, jak to zrobić, różnią się w zależności od bramy i nie zostały opisane tutaj. Nie można skonfigurować protokołu BGP z połączeniem sieci VPN typu lokacja-lokacja.

Żadna dodatkowa konfiguracja nie jest wymagana do uzyskania dostępu do zasobów lokalnych w ramach funkcji integracji regionalnej sieci wirtualnej. Wystarczy podłączyć sieć wirtualną do zasobów lokalnych przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja-lokacja.

> [!NOTE]
> Funkcja integracji sieci wirtualnej wymagana przez bramę nie integruje aplikacji z siecią wirtualną, która ma bramę ExpressRoute. Nawet jeśli Brama ExpressRoute jest skonfigurowana w [trybie współistnienia][VPNERCoex], integracja sieci wirtualnej nie działa. Jeśli musisz uzyskać dostęp do zasobów za pomocą połączenia ExpressRoute, użyj funkcji integracji regionalnej sieci wirtualnej lub [App Service Environment][ASE], która działa w sieci wirtualnej.
>
>

### <a name="peering"></a>Komunikacja równorzędna

Jeśli używasz komunikacji równorzędnej z integracją regionalnej sieci wirtualnej, nie musisz wykonywać żadnych dodatkowych czynności konfiguracyjnych.

W przypadku korzystania z integracji sieci wirtualnej wymaganej przez bramę z usługą komunikacji równorzędnej należy skonfigurować kilka dodatkowych elementów. Aby skonfigurować komunikację równorzędną do pracy z aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, z którą łączy się aplikacja. Po dodaniu połączenia komunikacji równorzędnej Włącz **dostęp do sieci wirtualnej** i wybierz opcję **Zezwalaj na przekazywanie dalej ruchu** i **Zezwalaj na tranzyt bramy**.
1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, która jest połączona z siecią wirtualną, z którą nawiązano połączenie. Po dodaniu połączenia komunikacji równorzędnej w docelowej sieci wirtualnej Włącz **dostęp do usługi Virtual Network** i wybierz opcję **Zezwalaj na przekazywanie dalej ruchu** i **Zezwalaj na bramy zdalne**.
1. Przejdź do **App Service planowanie**  >  **Networking**  >  interfejsu użytkownika**integracji sieci wirtualnej** w portalu. Wybierz sieć wirtualną, z którą aplikacja nawiązuje połączenie. W sekcji Routing Dodaj zakres adresów sieci wirtualnej, która jest połączona z siecią wirtualną.

## <a name="manage-vnet-integration"></a>Zarządzanie integracją sieci wirtualnej

Łączenie i rozłączanie z siecią wirtualną jest na poziomie aplikacji. Operacje, które mogą mieć wpływ na integrację sieci wirtualnej w wielu aplikacjach, są na poziomie planu App Service. W portalu integracji sieci wirtualnej z aplikacją > **Networking**  >  **VNet Integration** możesz uzyskać szczegółowe informacje dotyczące sieci wirtualnej. Podobne informacje można wyświetlić na poziomie planu App Service w **App Service plan**  >  **Networking**  >  portalu**integracji sieci wirtualnej** App Service plan.

Jedyną operacją, którą można wykonać w widoku aplikacji wystąpienia integracji sieci wirtualnej, jest rozłączenie aplikacji z sieci wirtualnej, z którą jest ona aktualnie połączona. Aby odłączyć aplikację od sieci wirtualnej, wybierz pozycję **Rozłącz**. Aplikacja zostanie uruchomiona ponownie po rozłączeniu z siecią wirtualną. Rozłączanie nie zmienia sieci wirtualnej. Podsieć lub Brama nie jest usuwana. Jeśli chcesz usunąć sieć wirtualną, najpierw Odłącz aplikację od sieci wirtualnej i Usuń z niej zasoby, takie jak bramy.

W interfejsie użytkownika integracji z siecią wirtualną App Service są wyświetlane wszystkie integracje sieci wirtualnej używane przez aplikacje w planie App Service. Aby wyświetlić szczegółowe informacje o każdej sieci wirtualnej, wybierz sieć wirtualną, która Cię interesuje. Istnieją dwie akcje, które można wykonać w tym miejscu dla integracji z siecią wirtualną wymagana przez bramę:

* **Synchronizacja sieci**: operacja synchronizacji sieci jest używana tylko w przypadku funkcji integracji wirtualnej zależnej od bramy. Wykonanie operacji synchronizacji sieci gwarantuje, że certyfikaty i informacje o sieci są zsynchronizowane. W przypadku dodania lub zmiany serwera DNS sieci wirtualnej wykonaj operację synchronizacji sieci. Ta operacja uruchamia ponownie wszystkie aplikacje używające tej sieci wirtualnej. Ta operacja nie będzie działała, jeśli używasz aplikacji i sieci wirtualnej należącej do różnych subskrypcji.
* **Dodawanie tras**: Dodawanie tras dysków ruch wychodzący do sieci wirtualnej.

### <a name="gateway-required-vnet-integration-routing"></a>Routing integracji sieci wirtualnej wymagane przez bramę
Trasy, które są zdefiniowane w sieci wirtualnej, są używane do kierowania ruchu do sieci wirtualnej z aplikacji. Aby wysłać dodatkowy ruch wychodzący do sieci wirtualnej, Dodaj te bloki adresów tutaj. Ta funkcja działa tylko z integracją sieci wirtualnej wymaganą przez bramę. Tabele tras nie wpływają na ruch aplikacji, gdy używasz integracji sieci wirtualnej wymagane przez bramę w taki sposób, aby korzystała z regionalnej integracji sieci wirtualnej.

### <a name="gateway-required-vnet-integration-certificates"></a>Certyfikaty integracji sieci wirtualnej wymagane przez bramę
Po włączeniu integracji sieci wirtualnej wymaganej przez bramę wymagana jest wymiana certyfikatów w celu zapewnienia bezpieczeństwa połączenia. Wraz z certyfikatami są Konfiguracja DNS, trasy i inne podobne elementy opisujące sieć.

Jeśli zostaną zmienione informacje o certyfikatach lub sieci, wybierz pozycję **Synchronizuj sieć**. Po wybraniu opcji **Synchronizuj sieć**spowoduje to krótką awarię połączenia między aplikacją i siecią wirtualną. Gdy aplikacja nie zostanie uruchomiona ponownie, utrata łączności może spowodować, że lokacja nie działa prawidłowo.

## <a name="pricing-details"></a>Szczegóły cennika
Funkcja zintegrowanej integracji sieci wirtualnej nie ma dodatkowej opłaty za użycie poza opłatami za warstwę cenową planu App Service.

Trzy opłaty są związane z korzystaniem z funkcji integracji sieci wirtualnej wymaganej przez bramę:

* **App Service plan cenowy planu opłaty**: aplikacje muszą być w planie App Service w warstwie Standardowa, Premium, PremiumV2 lub PremiumV3. Aby uzyskać więcej informacji na temat tych kosztów, zobacz [Cennik usługi App Service][ASPricing].
* **Koszty transferu danych**: naliczane są opłaty za ruch wychodzący, nawet jeśli sieć wirtualna znajduje się w tym samym centrum danych. Te opłaty są opisane w [transfer danych szczegóły cennika][DataPricing].
* **Koszty bramy sieci VPN**: Istnieje koszt bramy sieci wirtualnej, która jest wymagana dla sieci VPN typu punkt-lokacja. Aby uzyskać więcej informacji, zobacz [Cennik usługi VPN Gateway][VNETPricing].

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatyzacja

Obsługa interfejsu wiersza polecenia jest dostępna dla integracji regionalnej sieci wirtualnej. Aby uzyskać dostęp do poniższych poleceń, [Zainstaluj interfejs wiersza polecenia platformy Azure][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

Obsługa programu PowerShell dla integracji regionalnej sieci wirtualnej jest również dostępna, ale należy utworzyć zasób generyczny z tablicą właściwości resourceID

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


W przypadku integracji sieci wirtualnej wymagane przez bramę można zintegrować App Service z siecią wirtualną platformy Azure za pomocą programu PowerShell. Aby uzyskać gotowy do uruchomienia skrypt, zobacz [łączenie aplikacji w Azure App Service z siecią wirtualną platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli?view=azure-cli-latest%2f
[privateendpoints]: networking/private-endpoint.md