---
title: Publiczne adresy IP na platformie Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat publicznych adresów IP na platformie Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 1e46cf78c76e873bcb78af4942f42a5c4be45391
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955590"
---
# <a name="public-ip-addresses"></a>Publiczne adresy IP

Publiczne adresy IP umożliwiają zasobom internetowym komunikowanie się w ramach ruchu przychodzącego z zasobami platformy Azure. Publiczne adresy IP umożliwiają zasobom platformy Azure komunikowanie się z Internetem i publicznymi usługami platformy Azure. Adres jest przeznaczony dla zasobu, dopóki nie zostanie anulowany przez użytkownika. Zasób bez przypisanego publicznego adresu IP może komunikować się wychodzące. Platforma Azure dynamicznie przypisuje dostępny adres IP, który nie jest przeznaczony dla zasobu. Aby uzyskać więcej informacji na temat połączeń wychodzących na platformie Azure, zobacz [Understanding outbound connections (Opis połączeń wychodzących)](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W usłudze Azure Resource Manager [publiczny adres IP](virtual-network-public-ip-address.md) jest zasobem, który ma własne właściwości. Do niektórych zasobów można skojarzyć zasób publicznego adresu IP z:

* Interfejsy sieciowe maszyny wirtualnej
* Moduły równoważenia obciążenia dostępne z Internetu
* Bramy sieci VPN
* Bramy aplikacji
* Azure Firewall

## <a name="ip-address-version"></a>Wersja adresu IP

Publiczne adresy IP są tworzone przy użyciu adresu IPv4 lub IPv6. 

## <a name="sku"></a>Jednostka SKU

Aby dowiedzieć się więcej o uaktualnieniu jednostki SKU, zapoznaj się z tematem [Public IP upgrade](../virtual-network/virtual-network-public-ip-address-upgrade.md).

Publiczne adresy IP są tworzone przy użyciu jednej z następujących jednostek SKU:

>[!IMPORTANT]
> W przypadku zasobów modułu równoważenia obciążenia i publicznych adresów IP wymagane są zgodne jednostki SKU. Nie można mieć kombinacji podstawowych i standardowych zasobów SKU. Nie można dołączyć autonomicznych maszyn wirtualnych, maszyn wirtualnych w zasobie zestawu dostępności lub zasobów zestawu skalowania maszyn wirtualnych jednocześnie do obu jednostek SKU.  W nowych projektach należy rozważyć użycie standardowych zasobów SKU.  Zapoznaj się z tematem [Usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), aby uzyskać szczegółowe informacje.

### <a name="standard"></a>Standardowa

Publiczne adresy IP jednostki SKU:

- Zawsze używają metody alokacji statycznej.
- Ich dostosowywalny limit czasu bezczynności dla przepływu opartego na ruchu przychodzącym wynosi od 4 do 30 minut przy domyślnej wartości 4 minut, a stały limit czasu bezczynności dla przepływu opartego na ruchu wychodzącym wynosi 4 minuty.
- Zabezpiecz domyślnie i zamknięto w ruchu przychodzącym. Zezwalaj na wyświetlanie listy ruchu przychodzącego z [sieciową grupą zabezpieczeń](security-overview.md#network-security-groups).
- Przypisane do interfejsów sieciowych, standardowych publicznych modułów równoważenia obciążenia lub bram aplikacji. Aby uzyskać więcej informacji na temat usługi równoważenia obciążenia w warstwie Standardowa, zobacz [Azure usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Mogą być nadmiarowe strefowo (pochodzące ze wszystkich 3 stref) lub zona (można utworzyć strefę i zagwarantować w określonej strefie dostępności). Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Omówienie stref dostępności) oraz [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Nadmiarowe adresy IP stref można tworzyć tylko w regionach, w których znajdują się [3 strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-region) .** Adresy IP utworzone przed na żywo nie będą strefowo nadmiarowe.
- Mogą być używane jako adresy IP frontonu dla [międzyregionowych modułów równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) (funkcji wersji zapoznawczej).
 
> [!NOTE]
> Próba obsługi komunikacji przychodzącej za pomocą standardowego zasobu SKU będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups), a następnie jawnego zezwolenia na żądany ruch przychodzący.

> [!NOTE]
> W przypadku korzystania z [usługi metadanych wystąpienia IMDS](../virtual-machines/windows/instance-metadata-service.md)są dostępne tylko publiczne adresy IP z podstawową jednostką SKU. Standardowa jednostka SKU nie jest obsługiwana.

### <a name="basic"></a>Podstawowe

Wszystkie publiczne adresy IP utworzone przed wprowadzeniem jednostek SKU są publicznymi adresami IP opartymi na podstawowej jednostce SKU. 

Wraz z wprowadzeniem jednostek SKU Określ, która jednostka SKU ma być publicznym adresem IP. 

Podstawowe adresy jednostki SKU:

- Są przypisywane przy użyciu metody alokacji statycznej lub dynamicznej.
- Ich dostosowywalny limit czasu bezczynności dla przepływu opartego na ruchu przychodzącym wynosi od 4 do 30 minut przy domyślnej wartości 4 minut, a stały limit czasu bezczynności dla przepływu opartego na ruchu wychodzącym wynosi 4 minuty.
- Są domyślnie otwarte.  Użycie sieciowych grup zabezpieczeń do ograniczania ruchu przychodzącego lub wychodzącego jest zalecane, ale opcjonalne.
- Przypisane do dowolnego zasobu platformy Azure, do którego można przypisać publiczny adres IP, na przykład:
    * Interfejsy sieciowe
    * Bramy sieci VPN
    * Bramy aplikacji
    * Publiczne usługi równoważenia obciążenia
- Nie obsługują scenariuszy strefy dostępności. Używaj standardowego publicznego adresu IP jednostki SKU dla scenariuszy strefy dostępności. Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Omówienie stref dostępności) oraz [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Metoda alokacji

Prywatne i standardowe publiczne adresy IP obsługują przypisanie **statyczne** .  Zasób ma przypisany adres IP w momencie jego utworzenia. Adres IP jest wydawany, gdy zasób zostanie usunięty.

Publiczne adresy IP jednostki SKU obsługują przypisanie **dynamiczne** . Dynamiczna jest domyślną metodą przypisywania. Adres IP **nie jest** przydzielany do zasobu w momencie tworzenia podczas wybierania dynamicznego.

Adres IP jest przypisywany po skojarzeniu zasobu publicznego adresu IP z:

* Maszyna wirtualna 
* Pierwsza maszyna wirtualna jest skojarzona z pulą zaplecza modułu równoważenia obciążenia.

Adres IP jest zwalniany, gdy zatrzymasz (lub usuniesz) zasób.  

Na przykład zasób publicznego adresu IP jest publikowany z zasobu o nazwie **Resource a**. **Zasób A** odbiera inny adres IP po rozpoczęciu, jeśli zasób publicznego adresu IP jest ponownie przypisywany.

Adres IP jest wydawany, gdy metoda alokacji jest zmieniana z **statyczne** na **dynamiczny**. Aby zapewnić, że adres IP skojarzonego zasobu pozostaje taki sam, ustaw metodę alokacji jawnie na **statyczną**. Statyczny adres IP jest przypisywany natychmiast.

> [!NOTE]
> Nawet gdy ustawisz metodę alokacji jako **statyczną**, nie możesz określić rzeczywistego adresu IP przypisanego do publicznego zasobu adresu IP. Platforma Azure przypisuje adres IP z puli adresów IP dostępnych w lokalizacji platformy Azure, w której zasób jest tworzony.
>

Statyczne publiczne adresy IP są powszechnie używane w następujących scenariuszach:

* Gdy musisz aktualizować reguły zapory, aby komunikować się z zasobami platformy Azure.
* Rozpoznawanie nazw DNS, gdzie zmiana adresu IP wymaga aktualizacji rekordów A.
* Zasoby platformy Azure komunikują się z innymi aplikacjami lub usługami, które korzystają z modelu zabezpieczeń opartych na adres IP.
* Używane są certyfikaty TLS/SSL połączone z adresem IP.

> [!NOTE]
> Platforma Azure przydziela publiczne adresy IP z zakresu unikatowego dla każdego regionu w każdej chmurze platformy Azure. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).
>

## <a name="dns-hostname-resolution"></a>Rozpoznawanie nazw hostów DNS

Wybierz opcję, aby określić nazwę domeny DNS dla zasobu publicznego adresu IP. 

To zaznaczenie powoduje utworzenie mapowania dla **wartość domainnamelabel**. **Location**. cloudapp.Azure.com do publicznego adresu IP w systemie DNS zarządzanym przez platformę Azure. 

Na przykład tworzenie publicznego adresu IP przy użyciu:

* **contoso** jako **wartość domainnamelabel**
* **Zachodnie stany USA** **Lokalizacja** platformy Azure

W pełni kwalifikowana nazwa domeny (FQDN) **contoso.westus.cloudapp.Azure.com** jest rozpoznawana jako publiczny adres IP zasobu.

> [!IMPORTANT]
> Każda utworzona etykieta nazwy domeny musi być unikatowa w swojej lokalizacji na platformie Azure.  
>

## <a name="dns-recommendations"></a>Zalecenia dotyczące systemu DNS

W przypadku konieczności przeniesienia regionu nie można migrować nazwy FQDN publicznego adresu IP. Użyj nazwy FQDN, aby utworzyć niestandardowy rekord CNAME wskazujący na publiczny adres IP. 

Jeśli jest wymagany przechodzenie do innego publicznego adresu IP, należy zaktualizować rekord CNAME zamiast aktualizować nazwę FQDN.

Dla rekordu DNS można użyć [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) lub zewnętrznego dostawcy DNS. 

## <a name="virtual-machines"></a>Maszyny wirtualne

Możesz skojarzyć publiczny adres IP z maszyną wirtualną systemu [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), przypisując go do **interfejsu sieciowego**. 

Wybierz **dynamiczny** lub **statyczny** dla publicznego adresu IP. Dowiedz się więcej o [przypisywaniu adresów IP do interfejsów sieciowych](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Moduły równoważenia obciążenia dostępne z Internetu

Możesz skojarzyć publiczny adres IP każdej [jednostki SKU](#sku) z [Azure Load Balancer](../load-balancer/load-balancer-overview.md), przypisując go do konfiguracji **frontonu** modułu równoważenia obciążenia. Publiczny adres IP służy jako adres IP ze zrównoważonym obciążeniem. 

Do frontonu modułu równoważenia obciążenia możesz przypisać dynamiczny lub statyczny publiczny adres IP. Do frontonu modułu równoważenia obciążenia można przypisać wiele publicznych adresów IP. Ta konfiguracja umożliwia scenariusze z [wieloma adresami IP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , takie jak środowisko wielodostępne z witrynami sieci Web opartymi na protokole TLS. 

Aby uzyskać więcej informacji na temat jednostek SKU usługi Azure Load Balancer, zobacz [Azure load balancer standard SKU (Usługa Azure Load Balancer — standardowa jednostka SKU)](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="vpn-gateways"></a>Bramy sieci VPN

[Usługa azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nawiązuje połączenie z usługą Azure Virtual Network, aby:

* Sieci wirtualne platformy Azure
* Sieci lokalne. 

Publiczny adres IP jest przypisywany do VPN Gateway, aby umożliwić komunikację z siecią zdalną. Do bramy sieci VPN możesz przypisać tylko *dynamiczny* podstawowy publiczny adres IP.

## <a name="application-gateways"></a>Bramy aplikacji

Publiczny adres IP możesz skojarzyć z usługą [Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure, przypisując go do konfiguracji **frontonu** bramy. 

* Przypisz **dynamiczny** podstawowy adres IP do konfiguracji frontonu bramy aplikacji w wersji 1. 
* Przypisz **statyczny** standardowy adres jednostki SKU do konfiguracji frontonu w wersji 2.

## <a name="azure-firewall"></a>Azure Firewall

[Zapora platformy Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) umożliwia tworzenie, wymuszanie i rejestrowanie zasad łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych.

Do zapory można kojarzyć tylko **statyczne** publiczne adresy IP. Dzięki temu zapory zewnętrzne mogą identyfikować ruch pochodzący z sieci wirtualnej. 


## <a name="at-a-glance"></a>W skrócie

W poniższej tabeli przedstawiono właściwości, za pomocą których publiczny adres IP można skojarzyć z zasobem najwyższego poziomu i możliwymi metodami alokacji.

| Zasób najwyższego poziomu | Skojarzenie adresu IP | Dynamiczny | Static |
| --- | --- | --- | --- |
| Maszyna wirtualna |Interfejs sieciowy |Yes |Yes |
| Moduł równoważenia obciążenia dostępny z Internetu |Konfiguracja frontonu |Yes |Yes |
| Brama sieci VPN |Konfiguracja adresu IP bramy |Yes |Nie |
| Brama aplikacji |Konfiguracja frontonu |Tak (tylko wersja 1) |Tak (tylko wersja 2) |
| Azure Firewall | Konfiguracja frontonu | Nie | Yes|

## <a name="limits"></a>Limity

Limity adresów IP są wymienione w pełnym zestawie [limitów dla sieci](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) na platformie Azure. 

Ograniczenia są podzielone według regionu i subskrypcji. [Skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby zwiększyć domyślne limity do maksymalnych limitów w zależności od potrzeb firmy.

## <a name="pricing"></a>Cennik

Publiczne adresy IP mogą być związane z nominalnymi opłatami. Aby dowiedzieć się więcej o cenach adresów IP na platformie Azure, przejrzyj stronę [Cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Następne kroki
* Informacje o [prywatnych adresach IP na platformie Azure](private-ip-addresses.md)
* [Wdrażanie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu witryny Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)

