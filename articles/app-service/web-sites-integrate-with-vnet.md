---
title: Integracja aplikacji z usługą Azure Virtual Network
description: Zintegruj aplikację w usłudze Azure App Service z sieciami wirtualnymi platformy Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 78b49b8b7e17f12d49825390a302e28a61e10d16
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770835"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integracja aplikacji z siecią wirtualną platformy Azure

W tym artykule opisano funkcję integracji sieci wirtualnej usługi Azure App Service i jak skonfigurować ją z aplikacjami w [usłudze Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714) Za [pomocą sieci wirtualnej platformy Azure][VNETOverview] (sieci wirtualne) można umieścić wiele zasobów platformy Azure w sieci nierozerwalnie dostępnej dla Internetu.

Usługa Azure App Service ma dwie odmiany:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Włącz integrację sieci wirtualnej

> [!NOTE]
> Jeśli blok "Sieć" jest wyłączony (wyszarzony) w menu aplikacji systemu Linux, oznacza to, że funkcja jest obecnie niedostępna.
>

1. Przejdź do interfejsu użytkownika **sieci** w portalu usługi app service. W obszarze **Integracja sieci wirtualnej**wybierz pozycję **Kliknij tutaj, aby skonfigurować**.

1. Wybierz **pozycję Dodaj wirtualną sieci .**

   ![Wybierz integrację sieci wirtualnej][1]

1. Lista rozwijana zawiera wszystkie sieci wirtualne usługi Azure Resource Manager w ramach subskrypcji w tym samym regionie. Poniżej znajduje się lista sieci wirtualnych Menedżera zasobów we wszystkich innych regionach. Wybierz w sieci wirtualnej, z którą chcesz zintegrować.

   ![Wybierz vnet][2]

   * Jeśli sieć wirtualna znajduje się w tym samym regionie, utwórz nową podsieć lub wybierz pustą istniejącą wcześniej podsieć.
   * Aby wybrać sieci wirtualnej w innym regionie, musisz mieć bramę sieci wirtualnej aprowizowana z włączoną włączoną funkcją point to site.
   * Aby zintegrować się z klasyczną siecią wirtualną, zamiast **wybierać** listę rozwijaną sieci wirtualnej, wybierz pozycję **Kliknij tutaj, aby połączyć się z klasyczną siecią wirtualną**. Wybierz klasyczną sieć wirtualną, którą chcesz. Docelowa sieć wirtualna musi mieć już aprowizowana brama sieci wirtualnej z włączoną obsługą punktu do lokacji.

    ![Wybierz klasyczną wirtualną sieci][3]

Podczas integracji aplikacja zostanie ponownie uruchomiona. Po zakończeniu integracji zobaczysz szczegóły w sieci wirtualnej, z którą jesteś zintegrowany.

## <a name="regional-vnet-integration"></a>Regionalna integracja sieci wirtualnej

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Jak działa regionalna integracja sieci wirtualnych

Aplikacje w usłudze App Service są hostowane w rolach procesu roboczego. Podstawowe i wyższe plany cenowe są dedykowanymi planami hostingowymi, w których nie ma innych obciążeń klientów działających na tych samych pracownikach. Regionalna integracja sieci wirtualnej polega na instalowaniu interfejsów wirtualnych z adresami w delegowanej podsieci. Ponieważ z adresu znajduje się w sieci wirtualnej, można uzyskać dostęp do większości rzeczy w lub za pośrednictwem sieci wirtualnej, jak maszyny Wirtualnej w sieci wirtualnej będzie. Implementacja sieci jest inna niż uruchamianie maszyny Wirtualnej w sieci wirtualnej. Dlatego niektóre funkcje sieciowe nie są jeszcze dostępne dla tej funkcji.

![Jak działa regionalna integracja sieci wirtualnych][5]

Gdy regionalna integracja sieci wirtualnej jest włączona, aplikacja wykonuje wychodzące wywołania do Internetu za pośrednictwem tych samych kanałów, jak zwykle. Adresy wychodzące wymienione w portalu właściwości aplikacji są adresami nadal używanymi przez aplikację. Jakie zmiany w aplikacji są wywołania usług zabezpieczonych punktu końcowego usługi lub RFC 1918 adresy przejść do sieci wirtualnej. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, cały ruch wychodzący można wysłać do sieci wirtualnej.

Funkcja obsługuje tylko jeden interfejs wirtualny na proces roboczy. Jeden interfejs wirtualny na proces roboczy oznacza jedną regionalną integrację sieci wirtualnej na plan usługi aplikacji. Wszystkie aplikacje w tym samym planie usługi App Service można użyć tej samej integracji sieci wirtualnej. Jeśli potrzebujesz aplikacji, aby połączyć się z dodatkową siecią wirtualną, musisz utworzyć inny plan usługi app service. Używany interfejs wirtualny nie jest zasobem, do którego klienci mają bezpośredni dostęp.

Ze względu na charakter działania tej technologii ruch używany z integracją sieci wirtualnej nie jest pokazywany w dziennikach przepływu usługi Azure Network Watcher lub NSG.

## <a name="gateway-required-vnet-integration"></a>Integracja sieci wirtualnej wymaganej przez bramę

Integracja sieci wirtualnej wymaganej przez bramę obsługuje łączenie się z siecią wirtualną w innym regionie lub z klasyczną siecią wirtualną. Integracja sieci wirtualnej wymaganej przez bramę:

* Umożliwia aplikacji łączenie się tylko z jedną siecią wirtualną naraz.
* Umożliwia zintegrowanie maksymalnie pięciu sieci wirtualnych w ramach planu usługi app service.
* Umożliwia tej samej sieci wirtualnej do użycia przez wiele aplikacji w planie usługi app service bez wpływu na całkowitą liczbę, która może być używana przez plan usługi aplikacji. Jeśli masz sześć aplikacji przy użyciu tej samej sieci wirtualnej w tym samym planie usługi app service, która liczy się jako jedna sieci wirtualnej używane.
* Obsługuje 99,9% umowy SLA z powodu umowy SLA na bramie.
* Umożliwia aplikacjom korzystanie z systemu DNS skonfigurowany przez witrynę wirtualną.
* Wymaga bramy opartej na trasie sieci wirtualnej skonfigurowanej z siecią VPN typu punkt-lokacja SSTP, zanim będzie można ją połączyć z aplikacją.

Nie można używać integracji sieci wirtualnej wymaganej przez bramę:

* Z aplikacjami Linux.
* Z siecią wirtualną połączoną z usługą Azure ExpressRoute.
* Aby uzyskać dostęp do zasobów zabezpieczonych punktu końcowego usługi.
* Z bramą współistnienia, która obsługuje zarówno usługi ExpressRoute, jak i sieci VPN typu lokacja-lokacja lub lokacja.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Konfigurowanie bramy w sieci wirtualnej platformy Azure ###

Aby utworzyć bramę:

1. [Tworzenie podsieci bramy][creategatewaysubnet] w sieci wirtualnej.  

1. [Utwórz bramę sieci VPN][creategateway]. Wybierz typ sieci VPN oparty na trasie.

1. [Ustaw adresy typu punkt-lokacja][setp2saddresses]. Jeśli bramy nie ma podstawowej jednostki SKU, należy wyłączyć protokół IKEV2 w konfiguracji typu punkt-lokacja i należy wybrać protokół SSTP. Przestrzeń adresowa typu "punkt-lokacja" musi znajdować się w blokach adresowych RFC 1918 10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16.

Jeśli tworzysz bramę do użytku z integracją sieci wirtualnej usługi App Service, nie musisz przekazywać certyfikatu. Utworzenie bramy może potrwać 30 minut. Nie będzie można zintegrować aplikacji z siecią wirtualną, dopóki brama nie zostanie zainicjowana.

### <a name="how-gateway-required-vnet-integration-works"></a>Jak działa integracja sieci wirtualnej wymaganej przez bramę

Integracja sieci wirtualnej wymaganej przez bramę jest oparta na technologii sieci VPN typu punkt-lokacja. Sieci VPN typu punkt-lokacja ograniczają dostęp sieciowy do maszyny wirtualnej, na którym znajduje się aplikacja. Aplikacje są ograniczone do wysyłania ruchu do Internetu tylko za pośrednictwem połączeń hybrydowych lub za pośrednictwem integracji sieci wirtualnej. Gdy aplikacja jest skonfigurowana z portalem do używania integracji sieci wirtualnej wymaganej przez bramę, w Twoim imieniu zarządzana jest złożona negocjacja w celu utworzenia i przypisania certyfikatów po stronie bramy i aplikacji. W rezultacie pracownicy użyti do hosta aplikacji mogą bezpośrednio łączyć się z bramą sieci wirtualnej w wybranej sieci wirtualnej.

![Jak działa integracja sieci wirtualnej wymaganej przez bramę][6]

### <a name="access-on-premises-resources"></a>Dostęp do zasobów lokalnych

Aplikacje mogą uzyskiwać dostęp do zasobów lokalnych, integrując się z sieciami wirtualnymi, które mają połączenia lokacja-lokacja. Jeśli używasz integracji sieci wirtualnej wymaganej przez bramę, zaktualizuj lokalne trasy bramy sieci VPN za pomocą bloków adresów typu punkt-lokacja. Po pierwszej konfiguracji sieci VPN między lokacjami skrypty używane do jej konfigurowania powinny poprawnie skonfigurować trasy. Jeśli po utworzeniu sieci VPN między lokacjami zostanie dodane adresy typu "punkt-lokacja", należy ręcznie zaktualizować trasy. Szczegółowe informacje na temat tego, jak to zrobić, różnią się w zależności od bramy i nie są opisane tutaj. Nie można skonfigurować protokołu BGP z połączeniem sieci VPN lokacja-lokacja.

Nie jest wymagana żadna dodatkowa konfiguracja dla regionalnej funkcji integracji sieci wirtualnej, aby dotrzeć za pośrednictwem sieci wirtualnej do zasobów lokalnych. Wystarczy połączyć sieć wirtualną z zasobami lokalnymi przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja lokacja.

> [!NOTE]
> Funkcja integracji sieci wirtualnej wymaganej przez bramę nie integruje aplikacji z siecią wirtualną z bramą usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest skonfigurowana w [trybie współistnienia,][VPNERCoex]integracja sieci wirtualnej nie działa. Jeśli chcesz uzyskać dostęp do zasobów za pośrednictwem połączenia usługi ExpressRoute, użyj regionalnej funkcji integracji sieci wirtualnej lub [środowiska usługi app service,][ASE]która jest uruchamiana w sieci wirtualnej.
> 
> 

### <a name="peering"></a>Komunikacja równorzędna

Jeśli używasz komunikacji równorzędnej z regionalnej integracji sieci wirtualnej, nie trzeba wykonywać żadnych dodatkowych konfiguracji.

Jeśli używasz integracji sieci wirtualnej wymaganej przez bramę z komunikacją równorzędną, należy skonfigurować kilka dodatkowych elementów. Aby skonfigurować komunikację równorzędnej do pracy z aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, z którymi łączy się aplikacja. Po dodaniu połączenia równorzędnego włącz opcję **Zezwalaj na dostęp do sieci wirtualnej** i wybierz pozycję **Zezwalaj na ruch przesyłany dalej** i **Zezwalaj na przesyłanie bramy**.
1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, która jest równorzędna z siecią wirtualną, z którą masz połączenie. Po dodaniu połączenia równorzędnego w docelowej sieci wirtualnej włącz opcję Zezwalaj na **dostęp do sieci wirtualnej** i wybierz pozycję **Zezwalaj na ruch przesyłany dalej** i Zezwalaj na bramy **zdalne**.
1. Przejdź do **planu** > usługi app service sieci sieci**wirtualnej** > **integracji interfejsu użytkownika** w portalu. Wybierz sieci wirtualnej, z którymi łączy się aplikacja. W sekcji routingu dodaj zakres adresów sieci wirtualnej, która jest równorzędna z siecią wirtualną, z którą jest połączona aplikacja.

## <a name="manage-vnet-integration"></a>Zarządzanie integracją sieci wirtualnej

Łączenie i rozłączanie za pomocą sieci wirtualnej jest na poziomie aplikacji. Operacje, które mogą mieć wpływ na integrację sieci wirtualnej w wielu aplikacjach są na poziomie planu usługi app service. W portalu integracji sieci > **wirtualnej** > sieci **wirtualnej sieci sieciowej**aplikacji można uzyskać szczegółowe informacje w sieci wirtualnej. Podobne informacje można zobaczyć na poziomie planu usługi app service w portalu > **integracji sieci wirtualnej** sieci sieci > **sieciowej** **usługi aplikacji.**

Jedyną operacją, którą można wykonać w widoku aplikacji wystąpienia integracji sieci wirtualnej jest odłączenie aplikacji od sieci wirtualnej, z którą jest obecnie połączony. Aby odłączyć aplikację od sieci wirtualnej, wybierz pozycję **Odłącz**. Aplikacja zostanie ponownie uruchomiona po rozłączeniu z siecią wirtualną. Rozłączanie nie zmienia sieci wirtualnej. Podsieć lub brama nie zostanie usunięta. Jeśli następnie chcesz usunąć sieci wirtualnej, najpierw odłącz aplikację od sieci wirtualnej i usuń zasoby w niej, takie jak bramy.

Interfejs użytkownika integracji sieci wirtualnej usługi app service pokazuje wszystkie integracje sieci wirtualnej używane przez aplikacje w planie usługi App Service. Aby wyświetlić szczegółowe informacje na temat każdej sieci wirtualnej, wybierz sieci wirtualnej, która Cię interesuje. Istnieją dwie akcje, które można wykonać w tym miejscu dla integracji sieci wirtualnej wymaganej przez bramę:

* **Sieć synchronizacji:** Operacja sieci synchronizacji jest używana tylko dla funkcji integracji sieci wirtualnej zależnej od bramy. Wykonanie operacji sieci synchronizacji gwarantuje, że certyfikaty i informacje o sieci są zsynchronizowane. Jeśli dodasz lub zmienisz dns sieci wirtualnej, wykonaj operację sieci synchronizacji. Ta operacja powoduje ponowne uruchomienie wszystkich aplikacji korzystających z tej sieci wirtualnej.
* **Dodawanie tras:** Dodawanie tras napędza ruch wychodzący do sieci wirtualnej.

### <a name="gateway-required-vnet-integration-routing"></a>Routing integracji sieci wirtualnej wymaganej przez bramę
Trasy, które są zdefiniowane w sieci wirtualnej są używane do kierowania ruchu do sieci wirtualnej z aplikacji. Aby wysłać dodatkowy ruch wychodzący do sieci wirtualnej, dodaj te bloki adresów w tym miejscu. Ta funkcja działa tylko z integracją sieci wirtualnej wymaganej przez bramę. Tabele tras nie wpływają na ruch aplikacji podczas korzystania z integracji sieci wirtualnej wymaganej przez bramę w taki sposób, jak robią to z regionalną integracją sieci wirtualnej.

### <a name="gateway-required-vnet-integration-certificates"></a>Certyfikaty integracji sieci wirtualnej wymagane przez bramę
Gdy integracja sieci wirtualnej wymaganej przez bramę jest włączona, istnieje wymagana wymiana certyfikatów w celu zapewnienia bezpieczeństwa połączenia. Wraz z certyfikatami są konfiguracja DNS, trasy i inne podobne rzeczy, które opisują sieć.

Jeśli certyfikaty lub informacje o sieci są zmieniane, wybierz pozycję **Synchronizuj sieć**. Po **wybraniu opcji Synchronizuj sieć**spowoduje krótką awarię łączności między aplikacją a siecią wirtualną. Gdy aplikacja nie zostanie ponownie uruchomiona, utrata łączności może spowodować, że witryna nie będzie działać poprawnie.

## <a name="pricing-details"></a>Szczegóły cennika
Funkcja regionalnej integracji sieci wirtualnej nie ma żadnych dodatkowych opłat za korzystanie poza opłatami za warstwę cenową planu usługi app service.

Trzy opłaty są związane z korzystaniem z funkcji integracji sieci wirtualnej wymaganej przez bramę:

* **Opłaty za warstwę cenową planu usługi App Service:** Aplikacje muszą być w planie usługi aplikacji Standard, Premium lub PremiumV2. Aby uzyskać więcej informacji na temat tych kosztów, zobacz [Ceny usługi App Service][ASPricing].
* **Koszty transferu danych:** Jest pobierana opłata za wyjście danych, nawet jeśli sieć wirtualna znajduje się w tym samym centrum danych. Opłaty te są opisane w [szczegóły cen transferu danych][DataPricing].
* **Koszty bramy sieci VPN:** Brama sieci wirtualnej jest wymagana dla sieci VPN typu punkt-lokacja. Aby uzyskać więcej informacji, zobacz [cennik bramy sieci VPN][VNETPricing].

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatyzacja

Obsługa interfejsu wiersza polecenia jest dostępna dla regionalnej integracji sieci wirtualnej. Aby uzyskać dostęp do następujących poleceń, [zainstaluj narzędzie Azure CLI][installCLI].

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

W przypadku integracji sieci wirtualnej wymaganej przez bramę można zintegrować usługę App Service z siecią wirtualną platformy Azure przy użyciu programu PowerShell. Aby uzyskać gotowy do uruchomienia skrypt, zobacz [Łączenie aplikacji w usłudze Azure App Service z siecią wirtualną platformy Azure.](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
[privateendpoints]: networking/private-endpoint.md
