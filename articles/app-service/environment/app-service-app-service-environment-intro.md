---
title: Wprowadzenie do środowiska ASE v1
description: Dowiedz się więcej o funkcjach App Service Environment w wersji 1. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008632"
---
# <a name="introduction-to-app-service-environment-v1"></a>Wprowadzenie do App Service Environment v1

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v1.  Istnieje nowsza wersja App Service Environment ułatwiająca korzystanie z bardziej wydajną infrastrukturą. Aby dowiedzieć się więcej o nowej wersji, Zacznij od [wprowadzenia do App Service Environment](intro.md).

## <a name="overview"></a>Omówienie

App Service Environment to opcja planu usługi [Premium][PremiumTier] systemu [Azure App Service](../overview.md) , która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji Azure App Service na dużą skalę, w tym Web Apps, Mobile Apps i API Apps.  

Środowiska App Service doskonale sprawdzają się w przypadku obciążeń aplikacji wymagających:

* Bardzo wysoka Skala
* Izolacja i bezpieczny dostęp do sieci

Klienci mogą tworzyć wiele środowisk App Service w ramach jednego regionu świadczenia usługi Azure, a także w wielu regionach świadczenia usługi Azure.  Dzięki temu środowiska App Service idealnie sprawdzają się w przypadku skalowania w poziomie bez mniejszej ilości aplikacji w obsłudze dużych obciążeń RPS pliku.

Środowiska App Service są izolowane do uruchamiania tylko aplikacji jednego klienta i są zawsze wdrażane w sieci wirtualnej.  Klienci mają szczegółową kontrolę nad ruchem przychodzącym i wychodzącym ruchu sieciowego aplikacji, a aplikacje mogą nawiązywać bezpieczne połączenia za pośrednictwem sieci wirtualnych z lokalnymi zasobami firmowymi.

Aby dowiedzieć się, jak środowiska App Service zapewniają wysoką skalowalność i bezpieczny dostęp do sieci, zobacz [AzureCon głębokie szczegółowe][AzureConDeepDive] w środowiskach App Service!

Aby uzyskać głębokie szczegółowe skalowanie w poziomie przy użyciu wielu środowisk App Service, zobacz artykuł dotyczący konfigurowania [geograficznie rozproszonej aplikacji][GeodistributedAppFootprint].

Aby dowiedzieć się, jak została skonfigurowana Architektura zabezpieczeń pokazana w AzureCon głębokiej szczegółowe, zapoznaj się z artykułem dotyczącym implementowania [warstwowej architektury zabezpieczeń](app-service-app-service-environment-layered-security.md) w środowiskach App Service.

Aplikacje działające w środowiskach App Service mogą mieć dostęp warunkowy przez urządzenia nadrzędne, takie jak zapory aplikacji sieci Web (WAF).  Artykuł dotyczący [KONFIGUROWANIA WAF środowiska App Service](app-service-app-service-environment-web-application-firewall.md) obejmuje ten scenariusz.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedykowane zasoby obliczeniowe

Wszystkie zasoby obliczeniowe w App Service Environment są przeznaczone wyłącznie dla jednej subskrypcji, a App Service Environment można skonfigurować przy użyciu do 50 (50) zasobów obliczeniowych do wyłącznego użytku przez pojedynczą aplikację.

App Service Environment składa się z puli zasobów obliczeniowych frontonu, a także od jednej do trzech pul zasobów obliczeniowych procesu roboczego.

Pula frontonu zawiera zasoby obliczeniowe odpowiedzialne za zakończenie protokołu TLS, jak również automatyczne równoważenie obciążenia żądań aplikacji w ramach App Service Environment.

Każda pula procesów roboczych zawiera zasoby obliczeniowe przydzieloną do [planów App Service][AppServicePlan], które z kolei zawierają jedną lub więcej aplikacji Azure App Service.  Ponieważ w App Service Environment może istnieć maksymalnie trzy różne pule procesów roboczych, można wybrać różne zasoby obliczeniowe dla każdej puli procesów roboczych.  

Na przykład umożliwia utworzenie jednej puli procesów roboczych z mniej zaawansowanymi zasobami obliczeniowymi dla planów App Service przeznaczonych do tworzenia i testowania aplikacji.  Druga Pula procesów roboczych (lub nawet trzecia) może wykorzystywać bardziej zaawansowane zasoby obliczeniowe przeznaczone do App Service planów uruchamiających aplikacje produkcyjne.

Aby uzyskać więcej informacji na temat liczby zasobów obliczeniowych dostępnych dla pul frontonu i procesów roboczych, zobacz [jak skonfigurować App Service Environment][HowToConfigureanAppServiceEnvironment].  

Aby uzyskać szczegółowe informacje o dostępnych rozmiarach zasobów obliczeniowych obsługiwanych w App Service Environment, zapoznaj się z [cennikiem App Service][AppServicePricing] i zapoznaj się z dostępnymi opcjami dotyczącymi środowisk App Service w warstwie cenowej Premium.

## <a name="virtual-network-support"></a>Obsługa Virtual Network

App Service Environment można **utworzyć w sieci** wirtualnej Azure Resource Manager **lub** w sieci wirtualnej klasycznego modelu wdrażania ([więcej informacji na temat sieci wirtualnych][MoreInfoOnVirtualNetworks]).  Ponieważ App Service Environment zawsze istnieje w sieci wirtualnej i dokładniej w podsieci sieci wirtualnej, można użyć funkcji zabezpieczeń sieci wirtualnych do kontrolowania komunikacji przychodzącej i wychodzącej.  

App Service Environment może być połączona z Internetem za pomocą publicznego adresu IP lub wewnętrznego dołączona tylko do wewnętrznego Load Balancer platformy Azure (ILB).

[Grupy zabezpieczeń sieci][NetworkSecurityGroups] mogą służyć do ograniczania przychodzącej komunikacji sieciowej do podsieci, w której znajduje się App Service Environment.  Dzięki temu można uruchamiać aplikacje znajdujące się za nadrzędnymi urządzeniami i usługami, takimi jak zapory aplikacji internetowych i dostawcy usługi Network SaaS.

Aplikacje często muszą również uzyskiwać dostęp do zasobów firmy, takich jak wewnętrzne bazy danych i usługi internetowe.  Typowym podejściem jest udostępnienie tych punktów końcowych tylko do ruchu w sieci wewnętrznej w sieci wirtualnej platformy Azure.  Gdy App Service Environment jest przyłączona do tej samej sieci wirtualnej co wewnętrzne usługi, aplikacje działające w środowisku mogą uzyskiwać do nich dostęp, w tym punkty końcowe dostępne za pośrednictwem połączeń [między lokacjami][SiteToSite] i [usługi Azure ExpressRoute][ExpressRoute] .

Aby uzyskać więcej informacji o tym, jak środowiska App Service współpracują z sieciami wirtualnymi i sieciami lokalnymi, zapoznaj się z następującymi artykułami dotyczącymi [architektury sieci][NetworkArchitectureOverview], [kontrolowania ruchu przychodzącego][ControllingInboundTraffic]i [bezpiecznego łączenia się z][SecurelyConnectingToBackends]zadziałami. 

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć pracę z App Service środowiskami, zobacz [jak utworzyć App Service Environment][HowToCreateAnAppServiceEnvironment]

Aby zapoznać się z omówieniem architektury sieci App Service Environment, zobacz artykuł [Omówienie architektury sieci][NetworkArchitectureOverview] .

Aby uzyskać szczegółowe informacje na temat korzystania z App Service Environment z ExpressRoute, zobacz następujący artykuł w witrynie [Express Route i App Service][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->