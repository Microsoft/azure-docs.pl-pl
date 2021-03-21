---
title: Składniki Azure Load Balancer
description: Przegląd składników Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: 6bf090cde7262fdae9c98ef55227bf2925937dbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739835"
---
# <a name="azure-load-balancer-components"></a>Składniki Azure Load Balancer

Azure Load Balancer zawiera kilka najważniejszych składników. Te składniki można skonfigurować w ramach subskrypcji za pośrednictwem:

* Azure Portal
* Interfejs wiersza polecenia platformy Azure
* Azure PowerShell
* Szablony usługi Resource Manager

## <a name="frontend-ip-configuration"></a>Konfiguracja adresu IP frontonu <a name = "frontend-ip-configurations"></a>

Adres IP Azure Load Balancer. Jest to punkt kontaktu dla klientów. Mogą to być następujące adresy IP:

- **Publiczny adres IP**
- **Prywatny adres IP**

Charakter adresu IP określa **Typ** utworzonego modułu równoważenia obciążenia. Wybór prywatnego adresu IP powoduje utworzenie wewnętrznego modułu równoważenia obciążenia. Wybór publicznego adresu IP powoduje utworzenie publicznego modułu równoważenia obciążenia.

|  | Publiczny moduł równoważenia obciążenia  | Wewnętrzny moduł równoważenia obciążenia |
| ---------- | ---------- | ---------- |
| **Konfiguracja adresu IP frontonu**| Publiczny adres IP | Prywatny adres IP|
| **Opis** | Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i port ruchu przychodzącego na prywatny adres IP i port maszyny wirtualnej. Usługa równoważenia obciążenia mapuje ruch w inny sposób wokół ruchu odpowiedzi z maszyny wirtualnej. Można dystrybuować określone typy ruchu między wieloma maszynami wirtualnymi lub usługami, stosując reguły równoważenia obciążenia. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami internetowymi na wiele serwerów internetowych.| Wewnętrzny moduł równoważenia obciążenia dystrybuuje ruch do zasobów znajdujących się w sieci wirtualnej. Platforma Azure ogranicza dostęp do adresów IP frontonu sieci wirtualnej ze zrównoważonym obciążeniem. Adresy IP frontonu i sieci wirtualne nigdy nie są bezpośrednio ujawniane w internetowym punkcie końcowym. Wewnętrzne aplikacje biznesowe są uruchomiane na platformie Azure i dostęp do nich jest uzyskiwany z poziomu platformy Azure lub zasobów lokalnych. |
| **Obsługiwane jednostki SKU** | Basic, standard | Basic, standard |

![Przykład modułu równoważenia obciążenia warstwowego](./media/load-balancer-overview/load-balancer.png)

Moduł równoważenia obciążenia może mieć wiele adresów IP frontonu. Dowiedz się więcej na temat [wielu frontonów](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>Pula zaplecza

Grupa maszyn wirtualnych lub wystąpień w zestawie skalowania maszyn wirtualnych obsługujących żądanie przychodzące. Aby zapewnić oszczędne skalowanie w celu spełnienia dużych ilości ruchu przychodzącego, wytyczne obliczeniowe zwykle zaleca się dodanie większej liczby wystąpień do puli zaplecza.

Usługa równoważenia obciążenia natychmiast ponownie konfiguruje siebie za pomocą automatycznej zmiany konfiguracji podczas skalowania wystąpień w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza ponownie konfiguruje moduł równoważenia obciążenia bez dodatkowych operacji. Zakresem puli zaplecza jest dowolna maszyna wirtualna w sieci wirtualnej.

Biorąc pod uwagę sposób projektowania puli zaplecza, należy zaprojektować dla najmniejszej liczby zasobów puli zaplecza, aby zoptymalizować długość operacji zarządzania. Nie ma różnicy w wydajności lub skali danych.

## <a name="health-probes"></a>Sondy kondycji

Sonda kondycji służy do określenia stanu kondycji wystąpień w puli zaplecza. Podczas tworzenia modułu równoważenia obciążenia Skonfiguruj sondę kondycji, aby można było używać usługi równoważenia obciążenia.  Sonda kondycji określi, czy wystąpienie jest w dobrej kondycji i może odbierać ruch.

Można zdefiniować próg złej kondycji dla sond kondycji. Gdy Sonda nie odpowiada, moduł równoważenia obciążenia przestaje wysyłać nowe połączenia do wystąpień w złej kondycji. Awaria sondy nie ma wpływu na istniejące połączenia. Połączenie jest kontynuowane do momentu, gdy aplikacja:

- Zamyka przepływ
- Limit czasu bezczynności
- Maszyna wirtualna jest zamykana

Moduł równoważenia obciążenia oferuje różne typy sond kondycji dla punktów końcowych: TCP, HTTP i HTTPS. [Dowiedz się więcej o Load Balancer sondy kondycji](load-balancer-custom-probe-overview.md).

Podstawowa usługa równoważenia obciążenia nie obsługuje sond protokołu HTTPS. Podstawowa usługa równoważenia obciążenia zamyka wszystkie połączenia TCP (łącznie z ustalonymi połączeniami).

## <a name="load-balancing-rules"></a>Reguły równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu przychodzącego do **wszystkich** wystąpień w ramach puli zaplecza. Reguła równoważenia obciążenia mapuje daną konfigurację i port IP frontonu na wiele adresów IP i portów zaplecza.

Na przykład użyj reguły równoważenia obciążenia dla portu 80, aby skierować ruch z adresu IP frontonu do portu 80 wystąpień zaplecza.

:::image type="content" source="./media/load-balancer-components/lbrules.png" alt-text="Diagram odwołań reguł modułu równoważenia obciążenia" border="false":::

*Ilustracja: reguły Load-Balancing*

## <a name="high-availability-ports"></a>Porty wysokiej dostępności

Reguła modułu równoważenia obciążenia skonfigurowana z opcją **"Protokół-All i port-0"**. 

Ta reguła umożliwia pojedynczej regule równoważenia obciążenia wszystkich przepływów TCP i UDP, które docierają do wszystkich portów wewnętrznej usługa Load Balancer w warstwie Standardowa. 

Podejmowana jest decyzja dotycząca równoważenia obciążenia dla przepływu. Ta akcja jest oparta na następującym połączeniu z pięcioma krotkami: 

1. 
    źródłowy adres IP
  
2. port źródłowy
3. docelowy adres IP
4. port docelowy
5. protokol

Reguły równoważenia obciążenia portów HA ułatwiają scenariusze krytyczne, takie jak wysoka dostępność i skalowanie dla wirtualnych urządzeń sieciowych (urządzeń WUS) w sieciach wirtualnych. Funkcja może pomóc w przypadku, gdy duża liczba portów musi być zrównoważona obciążenia.

<p align="center">
  <img src="./media/load-balancer-components/harules.svg" alt="Figure depicts how Azure Load Balancer directs all frontend ports to three instances of all backend ports" width="512" title="Reguły portów HA">
</p>

*Ilustracja: reguły portów HA*

Dowiedz się więcej o [portach ha](load-balancer-ha-ports-overview.md).

## <a name="inbound-nat-rules"></a>Reguły NAT dla ruchu przychodzącego

Przychodzące reguły NAT przesyłają ruch przychodzący do kombinacji portów i adresów IP frontonu. Ruch jest wysyłany do **określonej** maszyny wirtualnej lub wystąpienia w puli zaplecza. Przekazywanie portów odbywa się przy użyciu tego samego rozkładu opartego na wykorzystaniu skrótu co Równoważenie obciążenia.

:::image type="content" source="./media/load-balancer-components/inboundnatrules.png" alt-text="Diagram referencyjny reguły NAT dla ruchu przychodzącego" border="false":::

*Ilustracja: reguły NAT dla ruchu przychodzącego*

Reguły NAT dla ruchu przychodzącego w kontekście Virtual Machine Scale Sets są pulami NAT dla ruchu przychodzącego. Dowiedz się więcej o [składnikach Load Balancer i zestawie skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Reguły ruchu wychodzącego

Reguła ruchu wychodzącego konfiguruje wychodzące translatora adresów sieciowych dla wszystkich maszyn wirtualnych lub wystąpień identyfikowanych przez pulę zaplecza. Ta reguła umożliwia wystąpienie w zapleczu komunikację (wychodzącą) z Internetem lub innymi punktami końcowymi.

Dowiedz się więcej o [połączeniach wychodzących i regułach](load-balancer-outbound-connections.md).

Podstawowa usługa równoważenia obciążenia nie obsługuje reguł ruchu wychodzącego.

:::image type="content" source="./media/load-balancer-components/outbound-rules.png" alt-text="Diagram odwołań reguły ruchu wychodzącego" border="false":::

*Ilustracja: reguły ruchu wychodzącego*

## <a name="limitations"></a>Ograniczenia

- Więcej informacji na temat [limitów](../azure-resource-manager/management/azure-subscription-service-limits.md) modułu równoważenia obciążenia 
- Moduł równoważenia obciążenia zapewnia Równoważenie obciążenia i przekazywanie portów dla określonych protokołów TCP lub UDP. Reguły równoważenia obciążenia i reguły NAT dla ruchu przychodzącego obsługują protokoły TCP i UDP, ale nie są to adresy IP, w tym protokół ICMP.
- Przepływ wychodzący z maszyny wirtualnej zaplecza do frontonu wewnętrznej Load Balancer zakończy się niepowodzeniem.
- Reguła modułu równoważenia obciążenia nie może obejmować dwóch sieci wirtualnych.  Frontony i ich wystąpienia zaplecza muszą znajdować się w tej samej sieci wirtualnej.  
- Fragmenty adresów IP przekazywania nie są obsługiwane w regułach równoważenia obciążenia. Fragmentacja IP pakietów UDP i TCP nie jest obsługiwana w regułach równoważenia obciążenia. Reguły równoważenia obciążenia portów HA mogą służyć do przesyłania dalej istniejących fragmentów adresów IP. Aby uzyskać więcej informacji, zobacz [Omówienie portów wysokiej dostępności](load-balancer-ha-ports-overview.md).
- Można mieć tylko 1 Load Balancer publiczny i 1 wewnętrzny Load Balancer na zestaw dostępności

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z Load Balancer, zobacz [Tworzenie publicznego modułu równoważenia obciążenia](quickstart-load-balancer-standard-public-portal.md) .
- Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
- Informacje o [publicznym adresie IP](../virtual-network/virtual-network-public-ip-address.md)
- Informacje o [prywatnym adresie IP](../virtual-network/private-ip-addresses.md)
- Dowiedz się więcej o korzystaniu z usługi [równoważenia obciążenia w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md).
- Dowiedz się więcej na temat [diagnostyki usługi równoważenia obciążenia w warstwie Standardowa](load-balancer-standard-diagnostics.md).
- Więcej informacji [na temat resetowania protokołu TCP w trybie bezczynności](load-balancer-tcp-reset.md).
- Dowiedz się więcej [na temat standardowego modułu równoważenia obciążenia z regułami równoważenia obciążenia portów o wysokiej dostępności](load-balancer-ha-ports-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
- Dowiedz się więcej o [limitach usługi równoważenia obciążenia](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer).
- Dowiedz się więcej o używaniu [przekazywania portów](./tutorial-load-balancer-port-forwarding-portal.md).
