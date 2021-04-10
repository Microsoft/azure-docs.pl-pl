---
title: Omówienie protokołu IPv6 dla usługi Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Opis IPv6 punktów końcowych IPv6 i ścieżek danych w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: bb7769298940095900da25e61e13612b92bad00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419103"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Co to jest protokół IPv6 dla usługi Azure Virtual Network?

Protokół IPv6 dla usługi Azure Virtual Network (VNet) umożliwia hostowanie aplikacji na platformie Azure z łącznością IPv6 i IPv4 zarówno w ramach sieci wirtualnej, jak i z Internetu. Ze względu na wyczerpanie publicznych adresów IPv4, nowe sieci na potrzeby mobilności i Internet rzeczy (IoT) są często kompilowane w protokole IPv6. Nawet długi ustanowiony usługodawca internetowy i sieci komórkowe są przekształcane na protokół IPv6. Usługi obsługujące tylko protokół IPv4 mogą znajdować się w realnej niekorzystnych wadach na istniejących i powstających rynkach. Dwustosowa łączność z protokołem IPv4/IPv6 umożliwia usługom hostowanym przez platformę Azure przechodzenie między tymi różnicami w ramach tej technologii dzięki globalnie dostępnym usługom z podwójnym stosem, które łatwo łączą się z istniejącym protokołem IPv4 i tymi nowymi urządzeniami IPv6

Oryginalna łączność z protokołem IPv6 na platformie Azure ułatwia Zapewnianie łączności z Internetem przy zastosowaniu podwójnego stosu (IPv4/IPv6) dla aplikacji hostowanych na platformie Azure. Umożliwia proste wdrażanie maszyn wirtualnych z funkcją łączności IPv6 z równoważeniem obciążenia dla połączeń przychodzących i wychodzących. Ta funkcja jest nadal dostępna i więcej informacji można znaleźć [tutaj](../load-balancer/load-balancer-ipv6-overview.md).
Protokół IPv6 dla usługi Azure Virtual Network jest znacznie bardziej zaoferowany — włączenie pełnej architektury rozwiązań IPv6 do wdrożenia na platformie Azure.


Na poniższym diagramie przedstawiono proste wdrożenie podwójnego stosu (IPv4/IPv6) na platformie Azure:

![Diagram wdrażania sieci IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Korzyści

Zalety protokołu IPv6 dla sieci wirtualnej platformy Azure:

- Ułatwia rozszerzanie zasięgów aplikacji hostowanych na platformie Azure na rosnące rynki przenośne i Internet rzeczy.
- Podwójne stosy maszyn wirtualnych IPv4/IPv6 zapewniają największą elastyczność wdrażania usług. Pojedyncze wystąpienie usługi może łączyć się z klientami internetowymi obsługującymi zarówno protokół IPv4, jak i IPv6.
- Kompilacja oparta na długotrwałej, stabilnej łączności z maszyną wirtualną na platformie Azure.
- Domyślnie bezpieczne, ponieważ połączenie IPv6 z Internetem jest nawiązywane tylko wtedy, gdy użytkownik jawnie zażąda go we wdrożeniu.

## <a name="capabilities"></a>Możliwości

Protokół IPv6 dla sieci wirtualnej platformy Azure obejmuje następujące możliwości:

- Klienci platformy Azure mogą definiować własną przestrzeń adresową sieci wirtualnej IPv6, aby sprostać wymaganiom aplikacji, klientom lub bezproblemowo integrować się z lokalną przestrzenią adresów IP.
- Dwie sieci wirtualne (IPv4 i IPv6) z dwoma podsieciami stosu umożliwiają aplikacjom łączenie się z zasobami IPv4 i IPv6 w sieci wirtualnej lub w Internecie.
    > [!IMPORTANT]
    > Podsieci dla protokołu IPv6 muszą mieć wartość dokładnie/64.  Zapewnia to w przyszłości zgodność w celu włączenia routingu podsieci do sieci lokalnej, ponieważ niektóre routery mogą akceptować tylko trasy IPv6/64.  
- Ochrona zasobów przy użyciu reguł IPv6 dla sieciowych grup zabezpieczeń.
    - I ochrona rozproszonego odmowy usługi platformy Azure (DDoS) jest rozszerzona na publiczny adres IP dostępny dla Internetu
- Dostosuj Routing ruchu IPv6 w sieci wirtualnej za pomocą tras User-Defined — szczególnie w przypadku korzystania z wirtualnych urządzeń sieciowych w celu rozszerzenia aplikacji.
- System Linux i Windows Virtual Machines mogą korzystać z protokołu IPv6 dla sieci wirtualnej platformy Azure
- [Standardowa obsługa Load Balancer publicznego IPv6](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) do tworzenia odpornych, skalowalnych aplikacji, takich jak:
    - Opcjonalna sonda kondycji IPv6 w celu określenia, które wystąpienia puli zaplecza są kondycją i w ten sposób mogą odbierać Nowe przepływy.
    - Opcjonalne reguły ruchu wychodzącego zapewniające pełną kontrolę deklaratywną nad łącznością wychodzącą w celu skalowania i dostrajania tej możliwości do konkretnych potrzeb.
    - Opcjonalna wiele konfiguracji frontonu, które umożliwiają pojedynczemu modułowi równoważenia obciążenia używanie wielu publicznych adresów IP IPv6 — ten sam protokół frontonu i port mogą być ponownie używane między adresami frontonu.
    - Opcjonalne porty IPv6 mogą być ponownie używane w wystąpieniach zaplecza przy użyciu funkcji *zmiennoprzecinkowego adresu IP* reguł równoważenia obciążenia 
    - Uwaga: Równoważenie obciążenia nie wykonuje żadnego tłumaczenia protokołu (bez usługi NAT64). 
    - Uwaga: protokół IPv6 może być zrównoważony pod względem podstawowego interfejsu sieciowego (NIC) na maszynach wirtualnych platformy Azure. 
- [Standardowa obsługa Load Balancer wewnętrznego IPv6](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) do tworzenia odpornych aplikacji wielowarstwowych w ramach usługi Azure sieci wirtualnych.   
- Obsługa publicznej Load Balancer publicznych adresów IPv6 w celu zapewnienia zgodności ze starszymi wdrożeniami
- [Publiczne adresy IP i zakresy adresów zastrzeżonego protokołu IPv6](ipv6-public-ip-address-prefix.md) zapewniają stabilne, przewidywalne adresy IPv6, które ułatwiają filtrowanie aplikacji hostowanych na platformie Azure dla Twojej firmy i klientów.
- Publiczny adres IP na poziomie wystąpienia zapewnia łączność z Internetem IPv6 bezpośrednio z indywidualnymi maszynami wirtualnymi.
- [Dodawanie protokołu IPv6 do istniejących wdrożeń obsługujących tylko protokół IPv4](ipv6-add-to-existing-vnet-powershell.md)— ta funkcja umożliwia łatwe dodawanie łączności IPv6 do istniejących wdrożeń obsługujących tylko protokół IPv4 bez konieczności ponownego tworzenia wdrożeń.  W trakcie tego procesu nie ma to oddziaływania na ruch sieciowy IPv4, więc w zależności od aplikacji i systemu operacyjnego może być możliwe dodanie protokołu IPv6 nawet do usług Live Services.    
- Zezwól klientom internetowym na bezproblemowy dostęp do aplikacji dwustosowej przy użyciu dowolnie wybranego protokołu z obsługą Azure DNS dla rekordów IPv6 (AAAA). 
- Twórz podwójne aplikacje stosujące automatyczne skalowanie do obciążenia za pomocą zestawów skalowania maszyn wirtualnych przy użyciu protokołu IPv6.
- [Komunikacja równorzędna Virtual Network (VNET)](virtual-network-peering-overview.md) — zarówno w regionalnym, jak i globalnym obszarze komunikacji równorzędnej — umożliwia bezproblemowe połączenia z podwójnym stosem sieci wirtualnych — oba punkty końcowe IPv4 i IPv6 na maszynach wirtualnych w sieciach równorzędnych będą mogły komunikować się ze sobą. Możesz nawet równorzędnie podwójnie łączyć się z protokołem IPv4 sieci wirtualnych w miarę przenoszenia wdrożeń do podwójnego stosu. 
- Narzędzia do rozwiązywania problemów i diagnostyki protokołu IPv6 są dostępne w przypadku metryk/alertów modułu równoważenia obciążenia oraz funkcji Network Watcher, takich jak przechwytywanie pakietów, dzienniki przepływów sieciowej grupy zabezpieczeń, rozwiązywanie problemów z połączeniami i monitorowanie połączeń.   

## <a name="scope"></a>Zakres
IPv6 dla sieci wirtualnej platformy Azure to fundamentowy zestaw funkcji, który umożliwia klientom hostowanie aplikacji podwójnego stosu (IPv4 + IPv6) na platformie Azure.  Zamierzamy dodać obsługę protokołu IPv6 do większej liczby funkcji sieciowych platformy Azure w miarę upływu czasu i ostatecznie zaoferować wersje podwójnego stosu usługi Azure PaaS Services, ale w międzyczasie wszystkie usługi Azure PaaS są dostępne za pośrednictwem punktów końcowych protokołu IPv4 na podwójnym stosie Virtual Machines.   

## <a name="limitations"></a>Ograniczenia
Bieżąca wersja protokołu IPv6 dla usługi Azure Virtual Network ma następujące ograniczenia:
- Protokół IPv6 dla usługi Azure Virtual Network jest dostępny we wszystkich globalnych regionach dla instytucji rządowych platformy Azure i w Stanach Zjednoczonych przy użyciu wszystkich metod wdrażania.  
- Bram ExpressRoute można używać tylko dla ruchu IPv4 w sieci wirtualnej z włączonym protokołem IPv6.  Obsługa ruchu IPv6 znajduje się w naszym przewodniku.   
- Bram sieci VPN nie można używać w sieci wirtualnej z włączonym protokołem IPv6, bezpośrednio lub za pomocą komunikacji równorzędnej z "UseRemoteGateway".
- Platforma Azure (AKS itp.) nie obsługuje komunikacji IPv6 dla kontenerów. 
- Tylko protokół IPv6 Virtual Machines lub zestawy skalowania Virtual Machines nie są obsługiwane, każda karta sieciowa musi zawierać co najmniej jedną konfigurację adresu IP IPv4. 
- W przypadku dodawania protokołu IPv6 do istniejących wdrożeń IPv4 nie można dodać zakresów IPv6 do sieci wirtualnej z istniejącymi łączami nawigacji zasobów.  
- Serwer DNS do przesyłania dalej jest obsługiwany przez usługę Azure Public DNS już dziś, ale odwrotny serwer DNS nie jest jeszcze obsługiwany.   

## <a name="pricing"></a>Ceny

Opłaty za zasoby i przepustowość platformy IPv6 są naliczone według stawki IPv4. Nie są naliczane żadne dodatkowe ani inne opłaty za protokół IPv6. Możesz znaleźć szczegółowe informacje o cenach [publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/), [przepustowości sieci](https://azure.microsoft.com/pricing/details/bandwidth/)lub [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu interfejsu wiersza polecenia platformy Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu szablonów Menedżer zasobów (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
