---
title: Integrowanie aplikacji z usługą Azure Virtual Network
description: Integrowanie aplikacji w Azure App Service z sieciami wirtualnymi platformy Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 42391a073d7cb1d7e6850e298c2be32d550bb813
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832072"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z siecią wirtualną platformy Azure

W tym artykule opisano Azure App Service integracji z siecią wirtualną i sposób jej skonfigurowania przy użyciu aplikacji w [Azure App Service](./overview.md). Dzięki [usłudze Azure Virtual Network][VNETOverview] (VNets) możesz umieścić wiele zasobów platformy Azure w sieci, w ramach których nie można routować Internetu. Funkcja integracji z siecią wirtualną umożliwia aplikacjom uzyskiwanie dostępu do zasobów w sieci wirtualnej lub za jej pośrednictwem. Integracja z siecią wirtualną nie umożliwia prywatnego dostępu do aplikacji.

Azure App Service ma dwie odmiany funkcji integracji z siecią wirtualną:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Włączanie integracji z siecią wirtualną

1. Przejdź do **interfejsu użytkownika** sieci w App Service portal. W **obszarze Integracja z siecią** wirtualną wybierz pozycję Kliknij **tutaj, aby skonfigurować .**

1. Wybierz **pozycję Dodaj sieć wirtualną.**

   ![Wybieranie integracji z siecią wirtualną][1]

1. Lista rozwijana zawiera wszystkie sieci wirtualne Azure Resource Manager subskrypcji w tym samym regionie. Poniżej znajduje się lista wszystkich Resource Manager wirtualnych we wszystkich innych regionach. Wybierz sieć wirtualną, z którą chcesz zintegrować.

   ![Wybierz sieć wirtualną][2]

   * Jeśli sieć wirtualna znajduje się w tym samym regionie, utwórz nową podsieć lub wybierz pustą wcześniejistą podsieć.
   * Aby wybrać sieć wirtualną w innym regionie, musisz mieć aprowizowana bramę sieci wirtualnej z włączoną obsługą połączenia punkt-lokacja.
   * Aby zintegrować z klasyczną siecią  wirtualną, zamiast wybierać listę rozwijaną Virtual Network, wybierz pozycję Kliknij tutaj, aby nawiązać połączenie **z klasyczną siecią wirtualną.** Wybierz klasyczną sieć wirtualną. Docelowa sieć wirtualna musi już mieć bramę Virtual Network aprowizowana z włączoną obsługą połączenia punkt-lokacja.

    ![Wybieranie klasycznej sieci wirtualnej][3]

Podczas integracji aplikacja jest uruchamiana ponownie. Po zakończeniu integracji zobaczysz szczegóły dotyczące zintegrowanej sieci wirtualnej.

## <a name="regional-vnet-integration"></a>Regionalna integracja z siecią wirtualną

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Jak działa regionalna integracja z siecią wirtualną

Aplikacje w App Service są hostowane w rolach procesu roboczego. Plany cenowe w chmurze Podstawowa i wyższa to dedykowane plany hostingu, w których nie ma obciążeń innych klientów uruchomionych na tych samych pracownikach. Regionalna integracja z siecią wirtualną działa przez instalowanie interfejsów wirtualnych z adresami w podsieci delegowane. Ponieważ adres od znajduje się w sieci wirtualnej, może ona uzyskać dostęp do większości rzeczy w sieci wirtualnej lub za jej pośrednictwem, tak jak maszyna wirtualna w sieci wirtualnej. Implementacja sieci różni się od uruchamiania maszyny wirtualnej w sieci wirtualnej. Dlatego niektóre funkcje sieciowe nie są jeszcze dostępne dla tej funkcji.

![Jak działa regionalna integracja z siecią wirtualną][5]

Gdy włączono regionalną integrację z siecią wirtualną, aplikacja wykonuje połączenia wychodzące do Internetu za pośrednictwem tych samych kanałów, co zwykle. Adresy wychodzące, które są wyświetlane w portalu właściwości aplikacji, są adresami nadal używanymi przez aplikację. Zmiany dotyczące aplikacji to wywołania usług zabezpieczonych punktem końcowym usługi lub adresy RFC 1918 trafiają do sieci wirtualnej. Jeśli WEBSITE_VNET_ROUTE_ALL ustawiono wartość 1, cały ruch wychodzący może być wysyłany do sieci wirtualnej.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` Usługa nie jest obecnie obsługiwana w kontenerach systemu Windows.
> 

Funkcja obsługuje tylko jeden interfejs wirtualny na proces roboczy. Jeden interfejs wirtualny na proces roboczy oznacza jedną regionalną integrację z siecią wirtualną na App Service wirtualnej. Wszystkie aplikacje w tym samym planie App Service mogą korzystać z tej samej integracji z siecią wirtualną. Jeśli potrzebujesz aplikacji do nawiązania połączenia z dodatkową siecią wirtualną, musisz utworzyć inny plan App Service wirtualnej. Używany interfejs wirtualny nie jest zasobem, do których klienci mają bezpośredni dostęp.

Ze względu na charakter działania tej technologii ruch używany z integracją z siecią wirtualną nie jest pokazywany w dziennikach przepływu usługi Azure Network Watcher ani sieciowej organizacji zabezpieczeń.

## <a name="gateway-required-vnet-integration"></a>Integracja z siecią wirtualną wymaganą przez bramę

Integracja z siecią wirtualną wymaganą przez bramę obsługuje nawiązywanie połączenia z siecią wirtualną w innym regionie lub z klasyczną siecią wirtualną. Integracja z siecią wirtualną wymaganą przez bramę:

* Umożliwia aplikacji łączenie się tylko z jedną siecią wirtualną jednocześnie.
* Umożliwia integrację maksymalnie pięciu sieci wirtualnych w ramach App Service wirtualnej.
* Umożliwia korzystanie z tej samej sieci wirtualnej przez wiele aplikacji w planie App Service bez wpływu na łączną liczbę, która może być używana przez App Service planu. Jeśli masz sześć aplikacji korzystających z tej samej sieci wirtualnej w tym samym planie App Service, będzie to oznaczać, że używana jest jedna sieć wirtualna.
* Obsługuje umowy SLA na poziomie 99,9% z powodu umowy SLA bramy.
* Umożliwia aplikacjom korzystanie z systemu DNS skonfigurowanego w sieci wirtualnej.
* Wymaga Virtual Network bramy opartej na trasach skonfigurowanej przy użyciu sieci VPN typu punkt-lokacja protokołu SSTP, zanim będzie można ją połączyć z aplikacją.

Nie można używać integracji z siecią wirtualną wymaganą przez bramę:

* Z siecią wirtualną połączeni z Azure ExpressRoute.
* Z aplikacji systemu Linux.
* Z kontenera [systemu Windows](quickstart-custom-container.md).
* Aby uzyskać dostęp do zasobów zabezpieczonych przez punkt końcowy usługi.
* W przypadku współistnienia bramy obsługującej zarówno usługę ExpressRoute, jak i sieci VPN typu punkt-lokacja lub lokacja-lokacja.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Konfigurowanie bramy w sieci wirtualnej platformy Azure ###

Aby utworzyć bramę:

1. [Utwórz podsieć bramy][creategatewaysubnet] w sieci wirtualnej.

1. [Utwórz bramę sieci VPN.][creategateway] Wybierz typ sieci VPN opartej na trasach.

1. [Ustaw adresy typu punkt-lokacja.][setp2saddresses] Jeśli brama nie znajduje się w podstawowej wersji SKU, należy wyłączyć IKEV2 w konfiguracji punkt-lokacja i wybrać SSTP. Przestrzeń adresowa punkt-lokacja musi znajdować się w blokach adresów RFC 1918 10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16.

Jeśli tworzysz bramę do użycia z App Service integracji z siecią wirtualną, nie musisz przekazywać certyfikatu. Tworzenie bramy może potrwać 30 minut. Nie będzie można zintegrować aplikacji z siecią wirtualną, dopóki brama nie zostanie aprowizowana.

### <a name="how-gateway-required-vnet-integration-works"></a>Jak działa integracja z siecią wirtualną wymaganą przez bramę

Integracja z siecią wirtualną wymaganą przez bramę jest zbudowana na podstawie technologii sieci VPN typu punkt-lokacja. Sieci VPN typu punkt-lokacja ograniczają dostęp sieciowy do maszyny wirtualnej, która hostuje aplikację. Aplikacje są ograniczone do wysyłania ruchu do Internetu tylko za pośrednictwem połączeń hybrydowych lub integracji z siecią wirtualną. Gdy aplikacja jest skonfigurowana przy użyciu portalu do korzystania z integracji z siecią wirtualną wymaganą przez bramę, w Twoim imieniu jest zarządzane złożone negocjacje w celu utworzenia i przypisania certyfikatów po stronie bramy i aplikacji. W efekcie pracownicy, którzy hostują aplikacje, mogą bezpośrednio połączyć się z bramą sieci wirtualnej w wybranej sieci wirtualnej.

![Jak działa integracja z siecią wirtualną wymaganą przez bramę][6]

### <a name="access-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych

Aplikacje mogą uzyskać dostęp do zasobów lokalnych przez integrację z sieciami wirtualnmi, które mają połączenia lokacja-lokacja. Jeśli używasz integracji z siecią wirtualną wymaganą przez bramę, zaktualizuj trasy lokalnej bramy sieci VPN blokami adresów typu punkt-lokacja. Podczas pierwszej konfiguracji sieci VPN typu lokacja-lokacja skrypty używane do jej konfigurowania powinny prawidłowo konfigurować trasy. W przypadku dodawania adresów punkt-lokacja po utworzeniu sieci VPN typu lokacja-lokacja należy ręcznie zaktualizować trasy. Szczegółowe informacje na temat tego, jak to zrobić, różnią się w zależności od bramy i nie zostały tutaj opisane. Nie można skonfigurować protokołu BGP za pomocą połączenia sieci VPN typu lokacja-lokacja.

Nie jest wymagana żadna dodatkowa konfiguracja, aby funkcja integracji regionalnej sieci wirtualnej docierała za pośrednictwem sieci wirtualnej do zasobów lokalnych. Wystarczy połączyć sieć wirtualną z zasobami lokalnymi przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja-lokacja.

> [!NOTE]
> Funkcja integracji z siecią wirtualną wymaganą przez bramę nie integruje aplikacji z siecią wirtualną, która ma bramę usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest skonfigurowana w trybie [współistnienia,][VPNERCoex]integracja z siecią wirtualną nie działa. Jeśli musisz uzyskać dostęp do zasobów za pośrednictwem połączenia expressRoute, użyj regionalnej funkcji integracji z siecią wirtualną lub App Service Environment [,][ASE]która działa w Twojej sieci wirtualnej.
>
>

### <a name="peering"></a>Komunikacja równorzędna

Jeśli używasz komunikacji równorzędnej z regionalną integracją z siecią wirtualną, nie musisz robić żadnych dodatkowych konfiguracji.

Jeśli używasz integracji sieci wirtualnej wymaganej przez bramę z użyciem komunikacji równorzędnej, musisz skonfigurować kilka dodatkowych elementów. Aby skonfigurować komunikacja równorzędna do pracy z aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, z która łączy się aplikacja. Po dodaniu połączenia komunikacji równorzędnej włącz opcję Zezwalaj na dostęp do sieci **wirtualnej** i wybierz pozycję **Zezwalaj** na ruch przesyłany dalej i **Zezwalaj na tranzyt bramy.**
1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, która jest połączona równorzędnie z siecią wirtualną, z która jest połączona. Po dodaniu połączenia komunikacji równorzędnej w  docelowej sieci wirtualnej włącz opcję Zezwalaj na dostęp do sieci wirtualnej i wybierz pozycję Zezwalaj na ruch przesyłany dalej i **Zezwalaj na bramy zdalne.** 
1. Przejdź do planu **App Service**  >  **interfejsu użytkownika**  >  **integracji sieci wirtualnej** w portalu. Wybierz sieć wirtualną, z która łączy się aplikacja. W sekcji routingu dodaj zakres adresów sieci wirtualnej połączonej za pomocą komunikacji równorzędnej z siecią wirtualną, z która jest połączona aplikacja.

## <a name="manage-vnet-integration"></a>Zarządzanie integracją sieci wirtualnej

Nawiązywanie połączenia z siecią wirtualną i rozłączanie z siecią wirtualną jest na poziomie aplikacji. Operacje, które mogą mieć wpływ na integrację z siecią wirtualną w wielu aplikacjach, App Service poziomie planu. W portalu integracji > **sieci** wirtualnych można uzyskać szczegółowe informacje  >   dotyczące sieci wirtualnej. Podobne informacje można znaleźć na poziomie planu App Service w portalu **integracji** sieci App Service  >  **sieci**  >   wirtualnych.

Jedyną operacją, która można podjąć w widoku aplikacji wystąpienia integracji z siecią wirtualną, jest rozłączenie aplikacji z siecią wirtualną, z która jest aktualnie połączona. Aby odłączyć aplikację od sieci wirtualnej, wybierz pozycję **Rozłącz.** Aplikacja zostanie ponownie uruchomiona po rozłączeniu z siecią wirtualną. Rozłączenie nie zmienia sieci wirtualnej. Podsieć lub brama nie jest usuwana. Jeśli następnie chcesz usunąć sieć wirtualną, najpierw odłącz aplikację od sieci wirtualnej i usuń jej zasoby, takie jak bramy.

Interfejs App Service integracji z siecią wirtualną planu pokazuje wszystkie integracje sieci wirtualnej używane przez aplikacje w App Service wirtualnej. Aby wyświetlić szczegółowe informacje dotyczące każdej sieci wirtualnej, wybierz sieć wirtualną, która Cię interesuje. W przypadku integracji z siecią wirtualną wymaganą przez bramę można wykonać dwie akcje:

* **Sieć synchronizacji:** operacja synchronizacji sieci jest używana tylko w przypadku funkcji integracji sieci wirtualnej zależnej od bramy. Wykonanie operacji synchronizacji sieci gwarantuje, że certyfikaty i informacje o sieci są zsynchronizowane. Jeśli dodasz lub zmienisz system DNS sieci wirtualnej, wykonaj operację synchronizacji sieci. Ta operacja powoduje ponowne uruchomienie wszystkich aplikacji, które korzystają z tej sieci wirtualnej. Ta operacja nie będzie działać, jeśli używasz aplikacji i sieci wirtualnej należącej do różnych subskrypcji.
* **Dodawanie tras:** dodanie tras kieruje ruchem wychodzącym do sieci wirtualnej.

Prywatny adres IP przypisany do wystąpienia jest udostępniane za pośrednictwem zmiennej środowiskowej **WEBSITE_PRIVATE_IP**. Interfejs użytkownika konsoli Kudu zawiera również listę zmiennych środowiskowych dostępnych dla aplikacji internetowej. Ten adres IP jest przypisywany z zakresu adresów zintegrowanej podsieci. W przypadku regionalnej integracji z siecią wirtualną wartość WEBSITE_PRIVATE_IP to adres IP z zakresu adresów delegowanej podsieci, a w przypadku integracji z siecią wirtualną wymaganą przez bramę wartość jest adresem IP z zakresu adresów puli adresów punkt-lokacja skonfigurowanej w bramie usługi Virtual Network Gateway. Jest to adres IP, który będzie używany przez aplikację internetową do łączenia się z zasobami za pośrednictwem Virtual Network. 

> [!NOTE]
> Wartość właściwości WEBSITE_PRIVATE_IP jest powiązana ze zmianą. Będzie to jednak adres IP w zakresie adresów podsieci integracji lub zakres adresów punkt-lokacja, dlatego należy zezwolić na dostęp z całego zakresu adresów.
>

### <a name="gateway-required-vnet-integration-routing"></a>Routing integracji sieci wirtualnej wymagany przez bramę
Trasy zdefiniowane w sieci wirtualnej są używane do kierowania ruchu do sieci wirtualnej z aplikacji. Aby wysłać dodatkowy ruch wychodzący do sieci wirtualnej, dodaj te bloki adresów tutaj. Ta funkcja działa tylko w przypadku integracji z siecią wirtualną wymaganą przez bramę. Tabele tras nie wpływają na ruch aplikacji w przypadku korzystania z integracji z siecią wirtualną wymaganą przez bramę w taki sposób, jak w przypadku regionalnej integracji z siecią wirtualną.

### <a name="gateway-required-vnet-integration-certificates"></a>Certyfikaty integracji sieci wirtualnej wymagane przez bramę
Po włączeniu integracji z siecią wirtualną wymaganą przez bramę istnieje wymagana wymiana certyfikatów w celu zapewnienia bezpieczeństwa połączenia. Oprócz certyfikatów są to konfiguracja dns, trasy i inne podobne rzeczy, które opisują sieć.

Jeśli certyfikaty lub informacje o sieci zostaną zmienione, wybierz pozycję **Synchronizuj sieć**. Wybranie opcji **Synchronizuj sieć** powoduje krótką przerwę w łączności między aplikacją a siecią wirtualną. Gdy aplikacja nie zostanie ponownie uruchomiona, utrata łączności może spowodować, że witryna nie będzie działać prawidłowo.

## <a name="pricing-details"></a>Szczegóły cennika
Regionalna funkcja integracji z siecią wirtualną nie ma dodatkowych opłat za użycie wykraczające poza App Service warstwy cenowej.

Trzy opłaty są związane z użyciem funkcji integracji z siecią wirtualną wymaganą przez bramę:

* **App Service warstwy cenowej:** Twoje aplikacje muszą być w planie premium, premium, premiumv2 lub PremiumV3 App Service planie. Aby uzyskać więcej informacji na temat tych kosztów, [zobacz App Service cennik.][ASPricing]
* **Koszty transferu danych:** za ruch wychodzący danych naliczana jest opłata, nawet jeśli sieć wirtualna znajduje się w tym samym centrum danych. Te opłaty są opisane w [te tematach szczegóły cennika usługi Data Transfer.][DataPricing]
* **Koszty bramy sieci VPN:** istnieje koszt bramy sieci wirtualnej, który jest wymagany dla sieci VPN typu punkt-lokacja. Aby uzyskać więcej informacji, zobacz [Cennik bramy sieci VPN.][VNETPricing]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

> [!NOTE]
> Integracja z siecią wirtualną nie jest obsługiwana w Docker Compose scenariuszach App Service.
> Azure Functions dostępu są ignorowane, jeśli istnieje prywatny punkt końcowy.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Obsługa interfejsu wiersza polecenia jest dostępna dla regionalnej integracji z siecią wirtualną. Aby uzyskać dostęp do poniższych poleceń, [zainstaluj interfejs wiersza polecenia platformy Azure.][installCLI]

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

Dostępna jest również obsługa regionalnej integracji z siecią wirtualną w programie PowerShell, ale należy utworzyć zasób ogólny z tablicą właściwości resourceID podsieci

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


W przypadku integracji z siecią wirtualną wymaganą przez bramę można zintegrować App Service z siecią wirtualną platformy Azure przy użyciu programu PowerShell. Aby uzyskać gotowy do uruchomienia skrypt, zobacz Connect an app in Azure App Service to an Azure virtual network (Łączenie aplikacji w [usłudze Azure App Service z siecią wirtualną platformy Azure).](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)


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
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
