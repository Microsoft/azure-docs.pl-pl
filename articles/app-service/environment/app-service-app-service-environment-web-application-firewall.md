---
title: Konfigurowanie aplikacji sieci Szkieletowej aplikacji sieciOwej
description: Dowiedz się, jak skonfigurować zaporę aplikacji internetowej (WAF) przed zaporą App Service Environment, za pomocą Azure Application Gateway lub zapory aplikacji internetowej innej firmy.
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 56d931f2346e5a0b615d3f11dce3b06396e586b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588721"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Konfigurowanie zapory aplikacji internetowych za pomocą środowiska App Service Environment
## <a name="overview"></a>Omówienie

Zapory aplikacji internetowych pomagają w zabezpieczaniu aplikacji internetowych, sprawdzając przychodzący ruch internetowy w celu blokowania wstrzyknięcia kodu SQL, działania skryptów między witrynami, operacji pobierania złośliwego oprogramowania oraz ataków DDoS na aplikacje i innych ataków. Badają one również odpowiedzi z internetowych serwerów zaplecza na potrzeby zapobiegania utracie danych. W połączeniu z izolacją i dodatkowym skalowaniem oferowanym przez środowisko App Service Environment ta funkcja oferuje idealne środowisko hostowania biznesowych aplikacji internetowych o krytycznym znaczeniu, które muszą radzić sobie ze złośliwymi żądaniami i intensywnym ruchem. Platforma Azure oferuje możliwości zapory aplikacji internetowych dzięki usłudze [Application Gateway](../../application-gateway/overview.md).  Aby dowiedzieć się, jak zintegrować środowisko App Service Environment z usługą Application Gateway, zapoznaj się z dokumentem [Integrate your ILB ASE with an Application Gateway (Integrowanie środowiska ASE wewnętrznego modułu równoważenia obciążenia z usługą Application Gateway)](./integrate-with-application-gateway.md).

Oprócz usługi Azure Application Gateway istnieje wiele innych opcji, takich jak [zapora aplikacji internetowych Barracuda dla platformy Azure](https://www.barracuda.com/programs/azure), które są dostępne w witrynie [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice). W pozostałej części tego dokumentu skupiono się na sposobie integrowania środowiska App Service Environment z urządzeniem zapory aplikacji internetowych Barracuda.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Konfiguracja
W przypadku tego dokumentu konfigurujemy środowisko App Service Environment za wieloma wystąpieniami zapory aplikacji internetowych Barracuda ze zrównoważonym obciążeniem tak, aby tylko ruch z zapory aplikacji internetowych mógł docierać do środowiska App Service Environment i był niedostępny od sieci obwodowej. Mamy również usługę Azure Traffic Manager przed wystąpieniami zapory aplikacji internetowych Barracuda w celu zrównoważenia obciążenia w centrach danych i regionach platformy Azure. Diagram konfiguracji wysokiego poziomu będzie wyglądać podobnie jak na poniższej ilustracji:

![Diagram przedstawia opcjonalny Azure Traffic Manager z wystąpieniami usługi Web Application Firewall, łączący się z siecią A C L, aby zezwolić tylko na ruch z zapory w sieci App Service Environment zawierającej sieć Web, interfejs P I i aplikację mobilną dla dwóch regionów.][Architecture] 

> [!NOTE]
> Dzięki wprowadzeniu [obsługi wewnętrznego modułu równoważenia obciążenia dla środowiska App Service Environment](app-service-environment-with-internal-load-balancer.md) można skonfigurować ASE niedostępny od sieci obwodowej i udostępniane wyłącznie do sieci prywatnej. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Konfigurowanie środowiska App Service Environment
Aby skonfigurować środowisko App Service Environment, zapoznaj się z [naszą dokumentacją](app-service-web-how-to-create-an-app-service-environment.md) dotyczącą tego tematu. Po utworzeniu środowiska App Service Environment można utworzyć w nim aplikacje internetowe, aplikacje interfejsu API i [aplikacje mobilne](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop). Będą one chronione za skonfigurowaną w następnej sekcji zaporą aplikacji internetowych.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Konfigurowanie usługi w chmurze zapory aplikacji internetowych Barracuda
Można zapoznać się ze [szczegółowym artykułem](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) na temat wdrażania zapory aplikacji internetowych usługi Barracuda na maszynie wirtualnej na platformie Azure. Jednak ponieważ potrzebujemy nadmiarowości i nie chcemy wprowadzać ani jednego punktu występowania awarii, warto wdrożyć co najmniej dwie maszyny wirtualne wystąpienia zapory aplikacji internetowych w jednej usłudze w chmurze, wykonując poniższe instrukcje.

### <a name="adding-endpoints-to-cloud-service"></a>Dodawanie punktów końcowych do usługi w chmurze
Gdy masz już co najmniej 2 wystąpienia maszyny wirtualnej zapory aplikacji internetowych w usłudze w chmurze, możesz użyć witryny [Azure Portal](https://portal.azure.com/) do dodania punktów końcowych HTTP i HTTPS używanych przez aplikację, jak pokazano na poniższej ilustracji:

![Konfigurowanie punktu końcowego][ConfigureEndpoint]

Jeśli aplikacje korzystają z innych punktów końcowych, pamiętaj również o dodaniu ich do tej listy. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Konfigurowanie zapory aplikacji internetowych Barracuda za pomocą portalu zarządzania
Zapora aplikacji internetowych Barracuda używa portu 8000 w obrębie protokołu TCP na potrzeby konfiguracji ustawianej w portalu zarządzania. Jeśli istnieje wiele wystąpień maszyn wirtualnych zapory aplikacji internetowych, należy powtórzyć kroki opisane w tym miejscu dla każdego wystąpienia maszyny wirtualnej. 

> [!NOTE]
> Po zakończeniu konfiguracji zapory aplikacji sieci internetowych należy usunąć punkt końcowy protokołu TCP/8000 ze wszystkich maszyn wirtualnych zapory aplikacji internetowych w celu zapewnienia bezpieczeństwa tej zapory.
> 
> 

Dodaj punkt końcowy zarządzania, jak pokazano na poniższej ilustracji, aby skonfigurować zaporę aplikacji internetowych Barracuda.

![Dodawanie punktu końcowego zarządzania][AddManagementEndpoint]

Aby przejść do punktu końcowego zarządzania w usłudze w chmurze, należy użyć przeglądarki. Jeśli usługa w chmurze ma nazwę test.cloudapp.net, dostęp do tego punktu końcowego można uzyskać, przechodząc do adresu `http://test.cloudapp.net:8000`. Powinna zostać wyświetlona strona logowania, taka jak na poniższej ilustracji. Można się na niej zalogować się przy użyciu poświadczeń zdefiniowanych w fazie konfiguracji maszyny wirtualnej zapory aplikacji internetowych.

![Zarządzanie — strona logowania][ManagementLoginPage]

Gdy się zalogujesz, powinien zostać wyświetlony pulpit nawigacyjny, taki jak na poniższej ilustracji. Będzie on zawierać podstawowe dane statystyczne dotyczące ochrony przy użyciu zapory aplikacji internetowych.

![Zarządzanie — pulpit nawigacyjny][ManagementDashboard]

Kliknięcie karty **Usługi** umożliwia skonfigurowanie zapory aplikacji internetowych dla chronionych usług. Więcej informacji na temat konfigurowania zapory aplikacji internetowych Barracuda można znaleźć w odpowiedniej [dokumentacji](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259884/configure-the-barracuda-web-application-firewall-from-the-web-interface/). W poniższym przykładzie skonfigurowano aplikację usługi App Service, która obsługuje ruch w obrębie protokołów HTTP i HTTPS.

![Zarządzanie — dodawanie usług][ManagementAddServices]

> [!NOTE]
> W zależności od sposobu konfigurowania aplikacji i funkcji używanych w programie App Service Environment należy przesyłać dalej ruch dla portów TCP innych niż 80 i 443, na przykład jeśli masz skonfigurowany protokół TLS ip dla aplikacji App Service. Listę portów używanych w środowiskach App Service Environment można znaleźć w sekcji dotyczącej portów sieciowych w [dokumentacji funkcji sterowania ruchem przychodzącym](app-service-app-service-environment-control-inbound-traffic.md).
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Konfigurowanie usługi Microsoft Azure Traffic Manager (OPCJONALNIE)
Jeśli aplikacja jest dostępna w wielu regionach, można równoważyć ich obciążenie za pomocą usługi [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). W tym celu można dodać punkt końcowy w witrynie [Azure Portal](https://portal.azure.com) przy użyciu nazwy usługi w chmurze dla zapory aplikacji internetowych w profilu usługi Traffic Manager, jak pokazano na poniższej ilustracji. 

![Punkt końcowy usługi Traffic Manager][TrafficManagerEndpoint]

Jeśli aplikacja wymaga uwierzytelniania, upewnij się, że masz zasób, który nie wymaga uwierzytelniania w usłudze Traffic Manager w przypadku sprawdzania dostępności aplikacji przy użyciu polecenia ping. Adres URL można skonfigurować na stronie **Konfiguracja** witryny [Azure Portal](https://portal.azure.com), jak pokazano na poniższej ilustracji:

![Konfigurowanie usługi Traffic Manager][ConfigureTrafficManager]

Aby przekazywać dalej polecenia ping usługi Traffic Manager z zapory aplikacji internetowych do aplikacji, należy skonfigurować tłumaczenia witryn internetowych w obrębie zapory aplikacji internetowych Barracuda. Umożliwi to przekazywanie ruchu do aplikacji, tak jak w poniższym przykładzie:

![Tłumaczenia witryn internetowych][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Zabezpieczanie ruchu do środowiska App Service Environment przy użyciu sieciowych grup zabezpieczeń
Szczegółowe informacje na temat ograniczania ruchu do środowiska App Service Environment tylko z zapory aplikacji internetowych przy użyciu adresu VIP usługi w chmurze można znaleźć w [dokumentacji dotyczącej sterowania ruchem przychodzącym](app-service-app-service-environment-control-inbound-traffic.md). Oto przykładowe polecenie programu PowerShell służące do wykonywania tego zadania na porcie TCP 80.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
```

Zastąp element SourceAddressPrefix wirtualnym adresem IP (VIP) usługi w chmurze zapory aplikacji internetowych.

> [!NOTE]
> Adres VIP usługi w chmurze zmieni się po usunięciu i ponownym utworzeniu usługi w chmurze. Pamiętaj o zaktualizowaniu adresu IP w grupie zasobów sieciowych po wykonaniu tej czynności. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
