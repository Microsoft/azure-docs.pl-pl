---
title: Typy adresów IP na platformie Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat publicznych i prywatnych adresów IP na platformie Azure.
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
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: 64940ee6451ef1a9e153ef4d699bdaed32d4030e
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146357"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Typy adresów IP i metody alokacji na platformie Azure

Do zasobów platformy Azure można przypisać adresy IP w celu komunikowania się z innymi zasobami platformy Azure, siecią lokalną i Internetem. Istnieją dwa typy adresów IP, których można użyć na platformie Azure:

* **Publiczne adresy IP**: używane do komunikacji z Internetem, w tym usług publicznych platformy Azure.
* **Prywatne adresy IP**: używane do komunikacji w ramach sieci wirtualnej platformy Azure i Twojej lokalnej sieci, gdy używasz bramy sieci VPN lub obwodu ExpressRoute w celu rozszerzenia swojej sieci na platformę Azure.

Możesz również utworzyć ciągły zakres statycznych publicznych adresów IP za pośrednictwem publicznego prefiksu adresu IP. [Więcej informacji na temat publicznego prefiksu adresu IP.](public-ip-address-prefix.md)

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Ten artykuł dotyczy modelu wdrażania usługi Resource Manager zalecanego przez firmę Microsoft w przypadku większości nowych wdrożeń zamiast [klasycznego modelu wdrażania](virtual-network-ip-addresses-overview-classic.md).
> 

Jeśli znasz klasyczny model wdrażania, sprawdź [różnice w adresowaniu IP między wersją klasyczną i usługą Resource Manager](/previous-versions/azure/virtual-network/virtual-network-ip-addresses-overview-classic#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Publiczne adresy IP

Publiczne adresy IP umożliwiają zasobom internetowym komunikowanie się w ramach ruchu przychodzącego z zasobami platformy Azure. Publiczne adresy IP umożliwiają również zasobom platformy Azure komunikowanie się w ramach ruchu wychodzącego z Internetem i publicznymi usługami platformy Azure za pomocą adresu IP przypisanego do zasobu. Ten adres jest dedykowany zasobowi, dopóki nie cofniesz jego przypisania. Jeśli publiczny adres IP nie jest przypisany do zasobu, zasób nadal może łączyć się z Internetem w ramach komunikacji wychodzącej, ale platforma Azure dynamicznie przypisuje dostępny adres IP, który nie jest dedykowany dla zasobu. Aby uzyskać więcej informacji na temat połączeń wychodzących na platformie Azure, zobacz [Understanding outbound connections (Opis połączeń wychodzących)](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W usłudze Azure Resource Manager [publiczny adres IP](virtual-network-public-ip-address.md) jest zasobem, który ma własne właściwości. Niektóre zasoby, z którymi możesz powiązać zasób publicznego adresu IP, to:

* Interfejsy sieciowe maszyny wirtualnej
* Moduły równoważenia obciążenia dostępne z Internetu
* Bramy sieci VPN
* Bramy aplikacji
* Azure Firewall

### <a name="ip-address-version"></a>Wersja adresu IP

Publiczne adresy IP są tworzone przy użyciu adresu IPv4 lub IPv6. 

### <a name="sku"></a>SKU

Publiczne adresy IP są tworzone przy użyciu jednej z następujących jednostek SKU:

>[!IMPORTANT]
> Dla zasobów modułu równoważenia obciążenia i publicznego adresu IP należy użyć zgodnych jednostek SKU. Nie można mieć kombinacji podstawowych i standardowych zasobów SKU. Nie można dołączyć autonomicznych maszyn wirtualnych, maszyn wirtualnych w zasobie zestawu dostępności lub zasobów zestawu skalowania maszyn wirtualnych jednocześnie do obu jednostek SKU.  W nowych projektach należy rozważyć użycie standardowych zasobów SKU.  Zapoznaj się z tematem [Usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), aby uzyskać szczegółowe informacje.

#### <a name="basic"></a>Podstawowy

Wszystkie publiczne adresy IP utworzone przed wprowadzeniem jednostek SKU są publicznymi adresami IP opartymi na podstawowej jednostce SKU. Od momentu wprowadzenia jednostki SKU masz opcję określania, którą jednostką SKU ma być publiczny adres IP. Podstawowe adresy SKU:

- Są przypisywane przy użyciu metody alokacji statycznej lub dynamicznej.
- Ich dostosowywalny limit czasu bezczynności dla przepływu opartego na ruchu przychodzącym wynosi od 4 do 30 minut przy domyślnej wartości 4 minut, a stały limit czasu bezczynności dla przepływu opartego na ruchu wychodzącym wynosi 4 minuty.
- Są domyślnie otwarte.  Użycie sieciowych grup zabezpieczeń do ograniczania ruchu przychodzącego lub wychodzącego jest zalecane, ale opcjonalne.
- Są przypisywane do zasobów platformy Azure, do których można przypisać publiczny adres IP, takich jak interfejsy sieciowe, bramy VPN Gateway, bramy Application Gateway i moduły równoważenia obciążenia dostępne z Internetu.
- Nie obsługują scenariuszy ze strefą dostępności.  Dla scenariuszy obejmujących strefę dostępności należy użyć standardowego publicznego adresu IP jednostki SKU. Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Omówienie stref dostępności) oraz [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standardowa

Standardowe publiczne adresy IP jednostek SKU:

- Zawsze używają metody alokacji statycznej.
- Ich dostosowywalny limit czasu bezczynności dla przepływu opartego na ruchu przychodzącym wynosi od 4 do 30 minut przy domyślnej wartości 4 minut, a stały limit czasu bezczynności dla przepływu opartego na ruchu wychodzącym wynosi 4 minuty.
- Domyślnie są zabezpieczone i zamknięte dla przychodzącego ruchu sieciowego. Dozwolony przychodzący ruch sieciowy należy jawnie umieścić na liście dozwolonych z użyciem [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups).
- Przypisane do interfejsów sieciowych, standardowych publicznych modułów równoważenia obciążenia lub bram aplikacji. Aby uzyskać więcej informacji na temat usługi Load Balancer w warstwie Standardowa, zobacz [Usługa Azure Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Domyślnie strefowo nadmiarowe i opcjonalnie strefowe (mogą zostać utworzone jako strefowe i gwarantowane w określonej strefie dostępności). Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Omówienie stref dostępności) oraz [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Próba obsługi komunikacji przychodzącej za pomocą standardowego zasobu SKU będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups), a następnie jawnego zezwolenia na żądany ruch przychodzący.

> [!NOTE]
> W przypadku korzystania z [usługi metadanych wystąpienia IMDS](../virtual-machines/windows/instance-metadata-service.md)są dostępne tylko publiczne adresy IP z podstawową jednostką SKU. Standardowa jednostka SKU nie jest obsługiwana.

### <a name="allocation-method"></a>Metoda alokacji

Zarówno podstawowe, jak i standardowe publiczne adresy IP jednostek SKU obsługują *statyczną* metodę alokacji.  Adres IP jest przypisywany do zasobu w czasie jego tworzenia, a zwalniany po usunięciu zasobu.

Podstawowe publiczne adresy IP jednostek SKU obsługują również *dynamiczną* metodę alokacji, która jest metodą domyślną, jeśli metoda alokacji nie zostanie określona.  Wybranie *dynamicznej* metody alokacji dla podstawowego zasobu publicznego adresu IP oznacza, że adres IP **nie** jest przydzielany w czasie tworzenia zasobu.  Publiczny adres IP jest przydzielany podczas kojarzenia publicznego adresu IP z maszyną wirtualną lub podczas umieszczania pierwszego wystąpienia maszyny wirtualnej w puli zaplecza podstawowego modułu równoważenia obciążenia.   Adres IP jest zwalniany, gdy zatrzymasz (lub usuniesz) zasób.  Na przykład po zwolnieniu z zasobu A adres IP można przypisać do innego zasobu. Jeśli adres IP zostanie przypisany do innego zasobu po zatrzymaniu zasobu A, po ponownym uruchomieniu zasobu A zostanie przypisany inny adres IP. Jeśli zmienisz metodę alokacji zasobu podstawowego publicznego adresu IP ze *statycznej* na *dynamiczną*, adres zostanie zwolniony. Aby zapewnić, żeby adres IP skojarzonego zasobu pozostawał taki sam, należy ustawić metodę alokacji jawnie jako *statyczną*. Statyczny adres IP jest przypisywany natychmiast.

> [!NOTE]
> Nawet gdy ustawisz metodę alokacji jako *statyczną*, nie możesz określić rzeczywistego adresu IP przypisanego do publicznego zasobu adresu IP. Platforma Azure przypisuje adres IP z puli adresów IP dostępnych w lokalizacji platformy Azure, w której zasób jest tworzony.
>

Statyczne publiczne adresy IP są powszechnie używane w następujących scenariuszach:

* Gdy musisz aktualizować reguły zapory, aby komunikować się z zasobami platformy Azure.
* Rozpoznawanie nazw DNS, gdzie zmiana adresu IP wymaga aktualizacji rekordów A.
* Zasoby platformy Azure komunikują się z innymi aplikacjami lub usługami, które korzystają z modelu zabezpieczeń opartych na adres IP.
* Używane są certyfikaty TLS/SSL połączone z adresem IP.

> [!NOTE]
> Platforma Azure przydziela publiczne adresy IP z zakresu unikatowego dla każdego regionu w każdej chmurze platformy Azure. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).
>

### <a name="dns-hostname-resolution"></a>Rozpoznawanie nazw hostów DNS
Możesz określić etykietę nazwy domeny DNS dla publicznego zasobu adresu IP, który tworzy mapowanie witryny *domainnamelabel*.*location*.cloudapp.azure.com na publiczny adres IP na serwerach DNS zarządzanych przez platformę Azure. Jeśli na przykład utworzysz publiczny zasób adresu IP mający **contoso** jako wartość parametru *domainnamelabel* w *lokalizacji* platformy Azure w **Zachodnich stanach USA**, w pełni kwalifikowana nazwa domeny (FQDN) **contoso.westus.cloudapp.azure.com** zostanie rozpoznana jako publiczny adres IP zasobu.

> [!IMPORTANT]
> Każda utworzona etykieta nazwy domeny musi być unikatowa w swojej lokalizacji na platformie Azure.  
>

### <a name="dns-best-practices"></a>Najlepsze rozwiązania dotyczące systemu DNS
Jeśli kiedykolwiek trzeba przeprowadzić migrację do innego regionu, nie można migrować nazwy FQDN publicznego adresu IP. Najlepszym rozwiązaniem jest użycie nazwy FQDN w celu utworzenia rekordu CNAME domeny niestandardowej wskazującego na publiczny adres IP na platformie Azure. Jeśli zachodzi potrzeba przejścia na inny publiczny adres IP, będzie wymagał aktualizacji rekordu CNAME zamiast ręcznego aktualizowania nazwy FQDN do nowego adresu. Dla rekordu DNS można użyć [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) lub zewnętrznego dostawcy DNS. 

### <a name="virtual-machines"></a>Maszyny wirtualne

Możesz skojarzyć publiczny adres IP z maszyną wirtualną systemu [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), przypisując go do **interfejsu sieciowego**. Do maszyny wirtualnej możesz przypisać dynamiczny lub statyczny publiczny adres IP. Dowiedz się więcej o [przypisywaniu adresów IP do interfejsów sieciowych](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Moduły równoważenia obciążenia dostępne z Internetu

Publiczny adres IP utworzony przy użyciu dowolnej jednostki [SKU](#sku) możesz skojarzyć z usługą [Azure Load Balancer](../load-balancer/load-balancer-overview.md), przypisując go do konfiguracji **frontonu** modułu równoważenia obciążenia. Publiczny adres IP służy jako wirtualny adres IP (VIP) o zrównoważonym obciążeniu. Do frontonu modułu równoważenia obciążenia możesz przypisać dynamiczny lub statyczny publiczny adres IP. Do frontonu modułu równoważenia obciążenia możesz także przypisać wiele publicznych adresów IP, co umożliwia scenariusze z [wieloma adresami VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , takie jak środowisko wielodostępne z witrynami sieci Web opartymi na protokole TLS. Aby uzyskać więcej informacji na temat jednostek SKU usługi Azure Load Balancer, zobacz [Azure load balancer standard SKU (Usługa Azure Load Balancer — standardowa jednostka SKU)](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="vpn-gateways"></a>Bramy sieci VPN

Usługa [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) służy do łączenia sieci wirtualnej platformy Azure z innymi sieciami wirtualnymi platformy Azure lub siecią lokalną. Publiczny adres IP jest przypisywany do bramy VPN Gateway w celu umożliwienia komunikacji z siecią zdalną. Do bramy sieci VPN możesz przypisać tylko *dynamiczny* podstawowy publiczny adres IP.

### <a name="application-gateways"></a>Bramy aplikacji

Publiczny adres IP możesz skojarzyć z usługą [Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure, przypisując go do konfiguracji **frontonu** bramy. Ten publiczny adres IP służy jako adres VIP o zrównoważonym obciążeniu. Można przypisać *dynamiczny* podstawowy publiczny adres IP do konfiguracji frontonu bramy aplikacji w wersji 1 i tylko *statyczny* standardowy adres jednostki SKU do konfiguracji frontonu w wersji 2.

### <a name="at-a-glance"></a>W skrócie
W poniższej tabeli przedstawiono określone właściwości, za pomocą których publiczny adres IP można skojarzyć z zasobem najwyższego poziomu, oraz ewentualne metody alokacji (dynamicznej lub statycznej), których można użyć.

| Zasób najwyższego poziomu | Skojarzenie adresu IP | Dynamiczny | Statyczny |
| --- | --- | --- | --- |
| Maszyna wirtualna |Interfejs sieciowy |Yes |Yes |
| Moduł równoważenia obciążenia dostępny z Internetu |Konfiguracja frontonu |Yes |Yes |
| Brama sieci VPN |Konfiguracja adresu IP bramy |Yes |Nie |
| Brama aplikacji |Konfiguracja frontonu |Tak (tylko wersja 1) |Tak (tylko wersja 2) |

## <a name="private-ip-addresses"></a>Prywatne adresy IP
Prywatne adresy IP umożliwiają zasobom platformy Azure komunikację z innymi zasobami w [sieci wirtualnej](virtual-networks-overview.md) lub sieci lokalnej za pomocą bramy sieci VPN lub obwodu ExpressRoute bez użycia adresu IP dostępnego w Internecie.

W modelu wdrażania przy użyciu usługi Azure Resource Manager prywatny adres IP jest skojarzony z następującymi typami zasobów platformy Azure:

* Interfejsy sieciowe maszyny wirtualnej
* Wewnętrzne moduły równoważenia obciążenia (ILB)
* Bramy aplikacji

### <a name="allocation-method"></a>Metoda alokacji

Prywatny adres IP jest przydzielany z zakresu adresów w podsieci sieci wirtualnej, w której wdrożono zasób. Platforma Azure rezerwuje pierwsze cztery adresy w każdym zakresie adresów podsieci, a więc te adresy nie mogą być przypisywane do zasobów. Na przykład jeśli zakres adresów podsieci to 10.0.0.0/16, adresy 10.0.0.0-10.0.0.3 i 10.0.255.255 nie mogą być przypisane do zasobów. Adresy IP w zakresie adresów podsieci mogą być przypisane tylko do jednego zasobu naraz. 

Istnieją dwie metody przydzielania prywatnego adresu IP:

- **Dynamiczna** — platforma Azure przypisuje następny dostępny nieprzypisany lub niezarezerwowany adres IP z zakresu adresów podsieci. Na przykład platforma Azure przypisze do nowego zasobu adres 10.0.0.10, jeśli adresy 10.0.0.4–10.0.0.9 są już przypisane do innych zasobów. Metoda dynamiczna to domyślna metoda alokacji. Po przypisaniu dynamiczne adresy IP są zwalniane, tylko jeśli interfejs sieciowy zostanie usunięty, przypisany do innej podsieci w tej samej sieci wirtualnej, lub metoda alokacji zostanie zmieniona na Statyczna i zostanie podany inny adres IP. Domyślnie platforma Azure przypisuje poprzedni adres dynamicznie przypisany jako adres statyczny po zmianie metody alokacji z dynamicznej na statyczną.
- **Statyczna** — użytkownik wybiera i przypisuje następny dostępny nieprzypisany lub niezarezerwowany adres IP z zakresu adresów podsieci. Jeśli na przykład zakres adresów podsieci to 10.0.0.0/16, a adresy 10.0.0.4–10.0.0.9 są już przypisane do innych zasobów, można przypisać dowolny adres między 10.0.0.10–10.0.255.254. Adresy statyczne są zwalniane tylko w przypadku usunięcia interfejsu sieciowego. Jeśli zmienisz metodę alokacji na dynamiczną, platforma Azure dynamicznie przypisze wcześniej przypisany statyczny adres IP jako adres dynamiczny, nawet jeśli adres nie jest następnym dostępnym adresem w zakresie adresów podsieci. Adres zmieni się także, jeśli interfejs sieciowy zostanie przypisany do innej podsieci w tej samej sieci wirtualnej, ale aby przypisać interfejs sieciowy do innej podsieci, musisz najpierw zmienić metodę alokacji ze statycznej na dynamiczną. Po przypisaniu interfejsu sieciowego do innej podsieci możesz zmienić metodę alokacji z powrotem na statyczną i przypisać adres IP z zakresu adresów nowej podsieci.

### <a name="virtual-machines"></a>Maszyny wirtualne

Co najmniej jeden prywatny adres IP jest przypisywany do **interfejsu sieciowego** maszyny wirtualnej z systemem [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Metodę alokacji dla każdego prywatnego adresu IP możesz określić jako dynamiczną lub statyczną.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Wewnętrzne rozpoznawanie nazwy hosta DNS (dla maszyn wirtualnych)

Wszystkie maszyny wirtualne platformy Azure są domyślnie skonfigurowane z [serwerami DNS zarządzanymi przez platformę Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution), chyba że jawnie skonfigurujesz niestandardowe serwery DNS. Te serwery DNS zapewniają rozpoznawanie nazw wewnętrznych maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.

Podczas tworzenia maszyny wirtualnej mapowanie nazwy hosta na jego prywatny adres IP jest dodawane do serwerów DNS zarządzanych przez platformę Azure. W przypadku maszyny wirtualnej z wieloma interfejsami sieciowymi lub wieloma konfiguracjami adresów IP dla interfejsu sieciowego nazwa hosta jest mapowana na prywatny adres IP głównej konfiguracji adresu IP dla głównego interfejsu sieciowego.

Maszyny wirtualne skonfigurowane przy użyciu serwerów DNS zarządzanych przez platformę Azure będą mogły rozpoznać nazwy hostów wszystkich maszyn wirtualnych w ramach ich sieci wirtualnej według ich prywatnych adresów IP. Aby rozpoznawać nazwy hostów maszyn wirtualnych w połączonych sieciach wirtualnych, musisz używać niestandardowego serwera DNS.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Wewnętrzne moduły równoważenia obciążenia (ILB) i bramy aplikacji

Prywatny adres IP możesz przypisać do konfiguracji **frontonu**[wewnętrznego modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) lub [usługi Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten prywatny adres IP służy jako wewnętrzny punkt końcowy dostępny tylko dla zasobów w ramach jego sieci wirtualnej i sieci zdalnych podłączonych do sieci wirtualnej. Do konfiguracji frontonu możesz przypisać dynamiczny albo statyczny prywatny adres IP.

### <a name="at-a-glance"></a>W skrócie
W poniższej tabeli przedstawiono określone właściwości, za pomocą których prywatny adres IP można skojarzyć z zasobem najwyższego poziomu, oraz ewentualne metody alokacji (dynamicznej lub statycznej), których można użyć.

| Zasób najwyższego poziomu | Skojarzenie adresu IP | Dynamiczny | Statyczny |
| --- | --- | --- | --- |
| Maszyna wirtualna |Interfejs sieciowy |Yes |Yes |
| Moduł równoważenia obciążenia |Konfiguracja frontonu |Yes |Yes |
| Brama aplikacji |Konfiguracja frontonu |Yes |Yes |

## <a name="limits"></a>Limity
Ograniczenia nakładane na adresowanie IP zostały wymienione w pełnym zestawieniu [ograniczeń dla sieci](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) na platformie Azure. Ograniczenia są podzielone według regionu i subskrypcji. [Kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), możesz zwiększyć domyślne limity do maksimum w zależności od potrzeb biznesowych.

## <a name="pricing"></a>Cennik
Publiczne adresy IP mogą być związane z nominalnymi opłatami. Aby dowiedzieć się więcej o cenach adresów IP na platformie Azure, przejrzyj stronę [Cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu witryny Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
* [Wdrażanie maszyny wirtualnej ze statycznym prywatnym adresem IP przy użyciu witryny Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
