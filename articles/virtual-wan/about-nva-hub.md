---
title: 'Azure Virtual WAN: informacje o wirtualnym urządzeniu sieciowym w koncentratorze'
description: Ten artykuł zawiera informacje o wirtualnych urządzeniach sieciowych w centrum Virtual WAN wirtualnego.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: 7c3ae14cd409e7bfc9be77c1a593964b73a12ddc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791053"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Informacje o wirtualnym urządzeniu sieciowym w centrum Azure Virtual WAN (wersja zapoznawcza)

Azure Virtual WAN współpracuje z partnerami sieci w celu tworzenia automatyzacji, która ułatwia łączenie sprzętu lokalnego klienta z bramą sieci VPN platformy Azure w koncentratorze wirtualnym. Platforma Azure współpracuje z wybranych partnerów sieciowych, aby umożliwić klientom wdrażanie wirtualnego urządzenia sieciowego (WUS) innej firmy bezpośrednio w koncentratorze wirtualnym. Dzięki temu klienci, którzy chcą połączyć swoje oddziałowe urządzenie CPE z tym samym urządzeniem WUS tej samej marki w koncentratorze wirtualnym, dzięki czemu mogą korzystać z zastrzeżonych, end-to-end możliwości sieci SD-WAN.

Barracuda Networks i Cisco Systems są pierwszymi partnerami, którzy dostarczają urządzenia WUS, które można wdrożyć bezpośrednio w centrum Virtual WAN wirtualnego.  Zobacz Barracuda CloudGen WAN, [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) and VMware SD-WAN (Rozwiązanie [Barracuda CloudGen WAN,](https://www.barracuda.com/products/cloudgenwan)Cisco Cloud OnRamp for Multi-Cloud i [VMware SD-WAN),](https://kb.vmware.com/s/article/82746) aby uzyskać odpowiednią dokumentację produktu. Platforma Azure współpracuje z większą liczby partnerów, więc należy spodziewać się kolejnych ofert.

> [!NOTE]
> Tylko oferty urządzeń WUS dostępne do wdrożenia w centrum Virtual WAN można wdrożyć w centrum Virtual WAN sieci. Nie można ich wdrożyć w dowolnej sieci wirtualnej na platformie Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Jak to działa?

Urządzenia WUS dostępne do wdrożenia bezpośrednio w centrum Azure Virtual WAN zostały zaprojektowane specjalnie do pracy w koncentratorze wirtualnym. Oferta urządzenia WUS jest publikowana w użytce Azure Marketplace jako aplikacja zarządzana, a klienci mogą wdrożyć ofertę bezpośrednio z usługi Azure Marketplace lub wdrożyć ofertę z koncentratora wirtualnego za pośrednictwem Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Omówienie procesu":::

Oferta urządzenia WUS każdego partnera będzie mieć nieco inne środowisko i funkcjonalność w zależności od wymagań dotyczących wdrażania. Istnieją jednak pewne rzeczy, które są wspólne dla wszystkich ofert partnerskich dla urządzenia WUS w centrum Virtual WAN sieci.

* Środowisko aplikacji zarządzanej oferowane za pośrednictwem Azure Marketplace.
* Pojemność i rozliczenia oparte na jednostce infrastruktury urządzenia WUS.
* Metryki kondycji dostępne za pośrednictwem Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Aplikacja zarządzana

Wszystkie oferty urządzeń WUS dostępne do wdrożenia w centrum Virtual WAN będą  mieć aplikację zarządzaną dostępną w Azure Marketplace. Aplikacje zarządzane umożliwiają partnerom:

* Tworzenie niestandardowego rozwiązania wdrażania dla urządzenia WUS.
* Udostępnij wyspecjalizowany Resource Manager, który umożliwia utworzenie urządzenia WUS bezpośrednio w centrum Virtual WAN sieci.
* Rozliczaj koszty licencjonowania oprogramowania bezpośrednio lub za pośrednictwem Azure Marketplace.
* Uwidocznij właściwości niestandardowe i mierniki zasobów.

Partnerzy urządzenia WUS mogą tworzyć różne zasoby w zależności od potrzeb dotyczących wdrażania urządzeń, licencjonowania konfiguracji i zarządzania. Gdy klient tworzy urządzenie WUS w centrum Virtual WAN, tak jak wszystkie aplikacje zarządzane, w jego subskrypcji zostaną utworzone dwie grupy zasobów.

* **Grupa zasobów klienta** — będzie zawierać symbol zastępczy aplikacji dla aplikacji zarządzanej. Partnerzy mogą użyć tej funkcji, aby uwidocznić wszelkie właściwości klientów, które wybiorą w tym miejscu.
* **Zarządzana grupa zasobów** — klienci nie mogą bezpośrednio konfigurować ani zmieniać zasobów w tej grupie zasobów, ponieważ jest to kontrolowane przez wydawcę aplikacji zarządzanej. Ta grupa zasobów będzie zawierać **zasób NetworkVirtualAppliances.**

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Grupy zasobów aplikacji zarządzanej":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Jednostki infrastruktury urządzenia WUS

Podczas tworzenia urządzenia WUS w centrum Virtual WAN należy wybrać liczbę jednostek infrastruktury urządzenia WUS, za pomocą których ma ono zostać wdrożone. Jednostka **infrastruktury urządzenia WUS** jest jednostką zagregowanej pojemności przepustowości dla urządzenia WUS w centrum Virtual WAN sieci. Jednostka **infrastruktury urządzenia WUS** jest podobna do jednostki [skalowania](pricing-concepts.md#scale-unit) sieci VPN pod względem sposobu myślenia o pojemności i wielkości.

* 1 Jednostka infrastruktury urządzenia WUS reprezentuje 500 Mb/s zagregowanej przepustowości dla wszystkich połączeń lokacji oddziałów pochodzących z tego urządzenia WUS, kosztem 0,25 USD/godzinę.
* Platforma Azure obsługuje od 1 do 80 jednostek infrastruktury urządzenia WUS dla danego wdrożenia koncentratora wirtualnego urządzenia WUS.
* Każdy partner może oferować różne pakiety jednostek infrastruktury urządzenia WUS, które są podzbiorem wszystkich obsługiwanych konfiguracji jednostki infrastruktury urządzenia WUS.

Podobnie jak w przypadku jednostek skalowania sieci VPN, jeśli wybierzemy 1 jednostkę infrastruktury urządzenia *WUS = 500 Mb/s,* oznacza to, że zostaną utworzone dwa wystąpienia nadmiarowości, z których każde ma maksymalną przepływność 500 Mb/s. Jeśli na przykład masz pięć gałęzi, z których każdy robi 10 Mb/s w gałęzi, na końcu będzie potrzebna agregacja 50 Mb/s. Planowanie zagregowanej pojemności urządzenia WUS należy wykonać po ocenie pojemności wymaganej do obsługi liczby gałęzi w koncentratorze.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Proces konfiguracji wirtualnego urządzenia sieciowego

Partnerzy pracowali nad zapewnieniem doświadczenia, które automatycznie konfiguruje urządzenie WUS w ramach procesu wdrażania. Po zaaprowizowanych urządzenia WUS w koncentratorze wirtualnym wszelkie dodatkowe konfiguracje, które mogą być wymagane dla urządzenia WUS, muszą być wykonywane za pośrednictwem portalu partnerów urządzenia WUS lub aplikacji do zarządzania. Bezpośredni dostęp do urządzenia WUS nie jest dostępny.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Zasoby lokacji i połączenia z urządzeniami WUS

W przeciwieństwie do konfiguracji usługi Azure VPN Gateway  nie trzeba tworzyć zasobów lokacji, zasobów połączenia **lokacja-lokacja** ani zasobów połączenia **punkt-lokacja,** aby połączyć lokacje oddziałów z urządzeniem WUS w centrum Virtual WAN. Wszystko to jest zarządzane za pośrednictwem partnera urządzenia WUS.

Nadal musisz utworzyć połączenia typu piasta-sieć wirtualna, aby połączyć centrum Virtual WAN z sieciami wirtualnymi platformy Azure.

## <a name="supported-regions"></a><a name="regions"></a>Obsługiwane regiony

Urządzenie WUS w koncentratorze wirtualnym jest dostępne w wersji zapoznawczej w następujących regionach:

|Region geopolityczny | Regiony świadczenia usługi Azure|
|---|---|
| Ameryka Północna| Kanada Środkowa, Kanada Wschodnia, Środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Północno-środkowe stany USA, Zachodnio-środkowe stany USA, Zachodnie stany USA 2 |
| Ameryka Południowa | Brazylia Południowa, Brazylia Południowo-Wschodnia |
| Europa | Francja Środkowa, Francja Południowa, Niemcy Północne, Niemcy Zachodnio-środkowe, Europa Północna, Eizja Wschodnia, Azja Zachodnia, Holandia Północna, Holandia Zachodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Europa Zachodnia|
|  Bliski Wschód | Północne Zjednoczone Emiraty Zjednoczone |
| Azja |  Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa, Azja Południowo-Wschodnia | 
| Australia | Australia Południowo-Wschodnia, Australia Wschodnia, Australia Środkowa, Australia Środkowa 2|
| Afryka | Północna Republika Południowej Afryki |
| Indie | Indie Południowe, Indie Zachodnie, Indie Środkowe | 
## <a name="faq"></a>Często zadawane pytania

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Jestem partnerem urządzenia sieciowego i chcę uzyskać urządzenie WUS w koncentratorze.  Czy mogę dołączyć do tego programu partnerskiego?

Niestety, w tej chwili nie mamy możliwości do wesłania żadnych nowych ofert partnerów. Wróć do nas w listopadzie!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Czy można wdrożyć dowolne urządzenie WUS z Azure Marketplace do Virtual WAN sieciowego?

Obecnie do wdrożenia w centrum danych są dostępne tylko aplikacje [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer)[Cisco Cloud vWAN Application](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) i [VMware Sd-W Virtual WAN AN.](https://aka.ms/vmwareMarketplaceLink)  

### <a name="what-is-the-cost-of-the-nva"></a>Jaki jest koszt urządzenia WUS?

Należy zakupić licencję urządzenia WUS od dostawcy urządzenia WUS.  Aby uzyskać licencję na urządzenie WAN barracuda CloudGen WAN od firmy Barracuda, zobacz stronę [sieci WAN CloudGen firmy Barracuda.](https://www.barracuda.com/products/cloudgenwan) Firma Cisco obecnie oferuje tylko model licencjonowania BYOL (Bring Your Own License), który należy uzyskać bezpośrednio od firmy Cisco. Ponadto zostaną naliczone opłaty od firmy Microsoft za używane jednostki infrastruktury urządzenia WUS i inne używane zasoby. Aby uzyskać więcej informacji, zobacz [Pricing Concepts (Pojęcia dotyczące cen).](pricing-concepts.md)

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Czy mogę wdrożyć urządzenie WUS w podstawowym koncentratorze?

Nie. Jeśli chcesz wdrożyć urządzenie WUS, musisz użyć koncentratora w standardowych urządzeniach.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Czy mogę wdrożyć urządzenie WUS w bezpiecznym koncentratorze?

Tak. Urządzenia WUS partnera można wdrożyć w koncentratorze za pomocą Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Czy mogę podłączyć dowolne urządzenie CPE w moim oddziale do urządzenia WAN Barracuda CloudGen w centrum?

Nie. Sieć WAN Barracuda CloudGen jest zgodna tylko z urządzeniami Barracuda CPE. Aby dowiedzieć się więcej na temat wymagań usługi CloudGen WAN, zobacz stronę [usługi CloudGen WAN firmy Barracuda.](https://www.barracuda.com/products/cloudgenwan) W przypadku firmy Cisco istnieje kilka urządzeń SD-WAN CPE, które można compatable. Zobacz Cisco Cloud OnRamp for Multi-Cloud documenation for compatable CPE (Cisco [Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) documenation for compatable CPE).

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Jakie scenariusze routingu są obsługiwane przez urządzenie WUS w koncentratorze?

Wszystkie scenariusze routingu obsługiwane Virtual WAN są obsługiwane przez urządzenia WUS w koncentratorze.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Virtual WAN, zobacz Virtual WAN [Omówienie.](virtual-wan-about.md)
