---
title: 'Wirtualna sieć WAN platformy Azure: informacje o urządzeniu wirtualnym w centrum'
description: Ten artykuł zawiera informacje o urządzeniach wirtualnych sieci w wirtualnym koncentratorze sieci WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 365ed60e73be9bb2098022fa767f4ae54b93c37c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028084"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Informacje o urządzeniu wirtualnym sieci w centrum sieci wirtualnej platformy Azure (wersja zapoznawcza)

Wirtualna sieć WAN platformy Azure pracowała z partnerami sieciowymi, aby utworzyć automatyzację, która ułatwia łączenie urządzeń lokalnych klienta (CPE) z bramą sieci VPN platformy Azure w koncentratorze wirtualnym. Platforma Azure współpracuje z wybieraniem partnerów sieciowych w celu umożliwienia klientom wdrażania zewnętrznego urządzenia sieciowego (urządzenie WUS) innej firmy bezpośrednio w koncentratorze wirtualnym. Pozwala to klientom, którzy chcą połączyć swoją gałąź CPE z tą samą marką urządzenie WUS w koncentratorze wirtualnym, dzięki czemu mogą oni korzystać z zastrzeżonych możliwości kompleksowej klasy SD-WAN.

Sieci Barracuda i firmy Cisco są pierwszymi partnerami, aby zapewnić urządzeń WUS, które mogą być wdrażane bezpośrednio w koncentratorze sieci wirtualnej.  Zapoznaj się z dokumentacją produktu [Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan) i [Cisco Cloud onrampy dla wielochmurowego](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) . Platforma Azure współpracuje z większą liczbą partnerów, dlatego należy zobaczyć inne oferty.

> [!NOTE]
> Tylko oferty urządzenie WUS dostępne do wdrożenia w wirtualnym koncentratorze sieci WAN można wdrożyć w wirtualnym koncentratorze sieci WAN. Nie można ich wdrożyć w ramach dowolnej sieci wirtualnej na platformie Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Jak to działa?

Urządzeń WUS, które są dostępne do wdrożenia bezpośrednio w wirtualnym Centrum sieci WAN platformy Azure, są przeznaczone do użycia w koncentratorze wirtualnym. Oferta urządzenie WUS jest publikowana w witrynie Azure Marketplace jako aplikacja zarządzana, a klienci mogą wdrożyć ofertę bezpośrednio z witryny Azure Marketplace lub wdrożyć ofertę z poziomu koncentratora wirtualnego za pośrednictwem Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Przegląd procesu":::

Oferta urządzenie WUS każdego partnera będzie miała nieco inne środowisko i funkcjonalność w oparciu o ich wymagania dotyczące wdrażania. Istnieje jednak kilka rzeczy, które są wspólne dla wszystkich ofert partnerskich dla urządzenie WUS w wirtualnym koncentratorze sieci WAN.

* Środowisko zarządzanej aplikacji oferowane w witrynie Azure Marketplace.
* Wydajność i rozliczenia oparte na jednostce infrastruktury urządzenie WUS.
* Metryki kondycji wyznaczone przez Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Aplikacja zarządzana

Wszystkie oferty urządzenie WUS, które są dostępne do wdrożenia w wirtualnego centrum sieci WAN, będą mieć **zarządzaną aplikację** , która jest dostępna w portalu Azure Marketplace. Aplikacje zarządzane umożliwiają partnerom wykonywanie następujących czynności:

* Utwórz niestandardowe środowisko wdrażania dla ich urządzenie WUS.
* Podaj wyspecjalizowany szablon Menedżer zasobów, który umożliwia im tworzenie urządzenie WUS bezpośrednio w koncentratorze sieci wirtualnej.
* Wylicz koszty licencjonowania oprogramowania bezpośrednio lub za pomocą witryny Azure Marketplace.
* Uwidacznianie właściwości niestandardowych i liczników zasobów.

Partnerzy urządzenie WUS mogą tworzyć różne zasoby w zależności od wdrożenia urządzenia, licencjonowania konfiguracji i potrzeb związanych z zarządzaniem. Gdy klient tworzy urządzenie WUS w wirtualnym koncentratorze sieci WAN, podobnie jak wszystkie zarządzane aplikacje, zostaną utworzone dwie grupy zasobów w ramach subskrypcji.

* **Grupa zasobów klienta** — będzie zawierać symbol zastępczy aplikacji zarządzanej. Partnerzy mogą korzystać z tej opcji, aby ujawnić dowolne właściwości klienta, które wybierzemy tutaj.
* **Grupa zasobów zarządzanych** — klienci nie mogą bezpośrednio konfigurować ani zmieniać zasobów w tej grupie zasobów, ponieważ są one kontrolowane przez wydawcę zarządzanej aplikacji. Ta grupa zasobów będzie zawierać zasób **NetworkVirtualAppliances** .

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Grupy zasobów aplikacji zarządzanej":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Jednostki infrastruktury urządzenie WUS

Podczas tworzenia urządzenie WUS w wirtualnym koncentratorze sieci WAN należy wybrać liczbę jednostek infrastruktury urządzenie WUS, z którymi ma zostać wdrożona. **Jednostka infrastruktury urządzenie WUS** jest jednostką zagregowanej pojemności przepustowości dla urządzenie WUS w KONCENTRATORZE sieci WAN. **Jednostka infrastruktury urządzenie WUS** jest podobna do [jednostki skalowania](pricing-concepts.md#scale-unit) sieci VPN w taki sposób, że myślisz o pojemności i wielkości.

* 1 jednostka infrastruktury urządzenie WUS reprezentuje 500 MB/s łącznej przepustowości dla wszystkich połączeń z witryną oddziału, które są używane w tym urządzenie WUS, przy kosztach wynoszących USD/godz.
* Platforma Azure obsługuje jednostki infrastruktury urządzenie WUS 1-80 dla danego wdrożenia wirtualnego centrum urządzenie WUS.
* Każdy partner może oferować różne zbiory jednostek infrastruktury urządzenie WUS, które są podzbiorem wszystkich obsługiwanych konfiguracji jednostek infrastruktury urządzenie WUS.

Podobnie jak w przypadku jednostek skalowania sieci VPN, jeśli wybierzesz *1 jednostkę infrastruktury urządzenie WUS = 500 MB/s*, oznacza to, że zostanie utworzone dwa wystąpienia nadmiarowości, z których każda będzie mieć maksymalną przepływność równą 500 MB/s. Na przykład jeśli masz pięć rozgałęzień, każda z nich wykonuje 10 MB/s w rozgałęzieniu, będzie potrzebna zagregowana liczba 50 MB/s na końcu. Planowanie zagregowanej pojemności urządzenie WUS należy wykonać po ocenie pojemności wymaganej do obsługi liczby gałęzi do centrum.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Proces konfiguracji wirtualnego urządzenia sieciowego

Partnerzy pracowali, aby zapewnić środowisko, które automatycznie konfiguruje urządzenie WUS w ramach procesu wdrażania. Po udostępnieniu urządzenie WUS w koncentratorze wirtualnym każda dodatkowa konfiguracja, która może być wymagana przez urządzenie WUS, musi zostać wykonana za pośrednictwem portalu partnerów urządzenie WUS lub aplikacji do zarządzania. Bezpośredni dostęp do urządzenie WUS jest niedostępny.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Zasoby lokacji i połączeń z usługą urządzeń WUS

W przeciwieństwie do konfiguracji VPN Gateway platformy Azure, nie trzeba tworzyć zasobów **lokacji** , zasobów **połączeń typu lokacja-lokacja** ani zasobów **połączeń punkt-lokacja** , aby połączyć Lokacje oddziałów z urządzenie WUS w wirtualnym koncentratorze sieci WAN. To wszystko jest zarządzane przez partnera urządzenie WUS.

Nadal musisz utworzyć połączenia między koncentratorami, aby połączyć wirtualne koncentrator sieci WAN z sieciami wirtualnymi platformy Azure.

## <a name="supported-regions"></a><a name="regions"></a>Obsługiwane regiony

URZĄDZENIE WUS w koncentratorze wirtualnym jest dostępny w wersji zapoznawczej w następujących regionach:

|Region geopolityczny | Regiony świadczenia usługi Azure|
|---|---|
| Ameryka Północna| Zachodnie stany USA, Południowo-środkowe stany USA, Wschodnie stany USA 2   |
| Ameryka Południowa | Brazylia Południowa |
| Europa | Europa Zachodnia, Południowe Zjednoczone Królestwo|
|  Bliski Wschód | Północne Zjednoczone Emiraty Arabskie |
| Azja | Japonia Wschodnia |
| Australia | Australia Wschodnia |

## <a name="faq"></a>Często zadawane pytania

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Jestem partnerem urządzenia sieciowego i chcesz uzyskać nasze urządzenie WUS w centrum.  Czy mogę dołączyć do tego programu partnerskiego?

Niestety, w tej chwili nie ma możliwości dowolnych nowych ofert partnerskich. Wróć do nas w listopadzie!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Czy mogę wdrożyć dowolne urządzenie WUS z witryny Azure Marketplace w ramach wirtualnego centrum sieci WAN?

W tej chwili tylko aplikacja [Barracuda CloudGen sieci WAN](https://aka.ms/BarracudaMarketPlaceOffer) i [Cisco Cloud vWAN](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) są dostępne do wdrożenia w wirtualnym koncentratorze sieci WAN.

### <a name="what-is-the-cost-of-the-nva"></a>Jaki jest koszt urządzenie WUS?

Musisz zakupić licencję dla urządzenie WUS od dostawcy urządzenie WUS.  W przypadku usługi Barracuda CloudGen WAN urządzenie WUS z licencji Barracuda zobacz [stronę Barracuda WAN CloudGen](https://www.barracuda.com/products/cloudgenwan). Cisco obecnie oferuje tylko BYOLy model licencjonowania, który należy uzyskać bezpośrednio z firmy Cisco. Ponadto naliczane są opłaty od firmy Microsoft dla jednostek infrastruktury urządzenie WUS, które są używane, a także inne zasoby, z których korzystasz. Aby uzyskać więcej informacji, zobacz [pojęcia cenowe](pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Czy mogę wdrożyć urządzenie WUS w centrum Basic?

Nie. Jeśli chcesz wdrożyć urządzenie WUS, musisz użyć standardowego centrum.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Czy mogę wdrożyć urządzenie WUS w bezpiecznym centrum?

Tak. Barracuda CloudGen sieci WAN można wdrożyć w centrum za pomocą zapory platformy Azure.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Czy mogę połączyć każde urządzenie CPE w biurze oddziału, aby Barracuda CloudGen WAN urządzenie WUS w centrum?

Nie. Barracuda CloudGen WAN jest zgodny z urządzeniami Barracuda CPE. Aby dowiedzieć się więcej o wymaganiach dotyczących sieci WAN CloudGen, zobacz [stronę sieci WAN CloudGen Barracuda](https://www.barracuda.com/products/cloudgenwan). W przypadku programu Cisco istnieje kilka urządzeń z systemem SD-WAN CPE, które są zgodne. Zapoznaj się z artykułem [Cisco Cloud onpochylni dla zawiera z obsługą](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) CPEs.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Jakie scenariusze routingu są obsługiwane w usłudze urządzenie WUS w centrum?

Wszystkie scenariusze routingu obsługiwane przez wirtualną sieć WAN są obsługiwane za pomocą urządzeń WUS w centrum.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wirtualnej sieci WAN, zobacz artykuł [Omówienie wirtualnych sieci WAN](virtual-wan-about.md) .
