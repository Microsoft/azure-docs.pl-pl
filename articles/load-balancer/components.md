---
title: Składniki Azure Load Balancer
description: Przegląd składników Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 84857315e4b6b4375ed5b78520b4c6ff0d66751a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684984"
---
# <a name="azure-load-balancer-components"></a>Składniki Azure Load Balancer

Azure Load Balancer składa się z kilku najważniejszych składników. Można je skonfigurować w ramach subskrypcji za pośrednictwem Azure Portal, interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub szablonów.

## <a name="frontend-ip-configuration"></a>Konfiguracja adresu IP frontonu<a name = "frontend-ip-configurations"></a>

Adres IP Azure Load Balancer. Jest to punkt kontaktu dla klientów. Mogą to być następujące adresy IP:

- **Publiczny adres IP**
- **Prywatny adres IP**

Charakter adresu IP określa **Typ** utworzonego modułu równoważenia obciążenia. Wybór prywatnego adresu IP powoduje utworzenie wewnętrznego modułu równoważenia obciążenia. Wybór publicznego adresu IP powoduje utworzenie publicznego modułu równoważenia obciążenia.

|  | Publiczny moduł równoważenia obciążenia  | Wewnętrzny moduł równoważenia obciążenia |
| ---------- | ---------- | ---------- |
| Konfiguracja adresu IP frontonu| Publiczny adres IP | Prywatny adres IP|
| Opis | Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i port ruchu przychodzącego na prywatny adres IP i port maszyny wirtualnej. Usługa równoważenia obciążenia mapuje ruch w inny sposób wokół ruchu odpowiedzi z maszyny wirtualnej. Można dystrybuować określone typy ruchu między wieloma maszynami wirtualnymi lub usługami, stosując reguły równoważenia obciążenia. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami internetowymi na wiele serwerów internetowych.| Wewnętrzny moduł równoważenia obciążenia dystrybuuje ruch do zasobów znajdujących się w sieci wirtualnej. Platforma Azure ogranicza dostęp do adresów IP frontonu sieci wirtualnej ze zrównoważonym obciążeniem. Adresy IP frontonu i sieci wirtualne nigdy nie są bezpośrednio ujawniane w internetowym punkcie końcowym. Wewnętrzne aplikacje biznesowe są uruchomiane na platformie Azure i dostęp do nich jest uzyskiwany z poziomu platformy Azure lub zasobów lokalnych. |
| Obsługiwane jednostki SKU | Basic, standard | Basic, standard |

![Przykład modułu równoważenia obciążenia warstwowego](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>Pula zaplecza

Grupa maszyn wirtualnych lub wystąpień w zestawie skalowania maszyn wirtualnych obsługujących żądanie przychodzące. Aby zapewnić oszczędne skalowanie w celu spełnienia dużych ilości ruchu przychodzącego, wytyczne obliczeniowe zwykle zaleca się dodanie większej liczby wystąpień do puli zaplecza.

Usługa równoważenia obciążenia natychmiast ponownie konfiguruje siebie za pomocą automatycznej zmiany konfiguracji podczas skalowania wystąpień w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza ponownie konfiguruje moduł równoważenia obciążenia bez dodatkowych operacji. Zakresem puli zaplecza jest dowolna maszyna wirtualna w sieci wirtualnej.

Biorąc pod uwagę sposób projektowania puli zaplecza, należy zaprojektować dla najmniejszej liczby zasobów puli zaplecza, aby zoptymalizować długość operacji zarządzania. Nie ma różnicy w wydajności lub skali danych.

## <a name="health-probes"></a>Sondy kondycji

Sonda kondycji służy do określenia stanu kondycji wystąpień w puli zaplecza. Podczas tworzenia Load Balancer należy skonfigurować sondę kondycji, która może być używana przez Load Balancer, aby określić, czy wystąpienie jest w dobrej kondycji i kierować ruchem do niego.

Można zdefiniować próg złej kondycji dla sond kondycji. Gdy Sonda nie odpowiada, Load Balancer przestaje wysyłać nowe połączenia do wystąpień w złej kondycji. Awaria sondy nie ma wpływu na istniejące połączenia. Połączenie jest kontynuowane do momentu, gdy aplikacja:

- Zamyka przepływ
- Limit czasu bezczynności
- Maszyna wirtualna jest zamykana

Load Balancer oferuje różne typy sond kondycji dla punktów końcowych: TCP, HTTP i HTTPS.

Podstawowa Load Balancer nie obsługuje sond protokołu HTTPS. Podstawowa Load Balancer zamyka wszystkie połączenia TCP (łącznie z ustanowionymi połączeniami).

## <a name="load-balancing-rules"></a>Reguły równoważenia obciążenia

Reguła Load Balancer służy do definiowania sposobu dystrybucji ruchu przychodzącego do **wszystkich** wystąpień w ramach puli zaplecza. Reguła równoważenia obciążenia mapuje daną konfigurację IP frontonu i port na wiele adresów IP zaplecza i portów.

Na przykład jeśli chcesz, aby ruch na porcie 80 (lub innym porcie) adresu IP frontonu był kierowany do portu 80 wszystkich wystąpień zaplecza, użyj reguły równoważenia obciążenia, aby to osiągnąć.

## <a name="inbound-nat-rules"></a>Reguły NAT dla ruchu przychodzącego

Reguła ruchu przychodzącego NAT przesyła przychodzącego ruchu wysyłanego do wybranego adresu IP frontonu i kombinacji portów do **określonej** maszyny wirtualnej lub wystąpienia w puli zaplecza. Przekazywanie portów odbywa się przy użyciu tego samego rozkładu opartego na wykorzystaniu skrótu co Równoważenie obciążenia.

Na przykład jeśli chcesz, aby w ramach sesji Remote Desktop Protocol (RDP) lub Secure Shell (SSH) oddzielić wystąpienia maszyn wirtualnych w puli zaplecza. Wiele wewnętrznych punktów końcowych można zamapować na porty przy użyciu tego samego adresu IP frontonu. Adresy IP frontonu mogą służyć do zdalnego administrowania maszynami wirtualnymi bez dodatkowego pola skoku.

## <a name="outbound-rules"></a>Reguły ruchu wychodzącego

Reguła ruchu wychodzącego konfiguruje wychodzące translatora adresów sieciowych dla wszystkich maszyn wirtualnych lub wystąpień identyfikowanych przez pulę zaplecza. Dzięki temu wystąpienia w zapleczu mogą komunikować się z Internetem lub z innymi punktami końcowymi.

Podstawowa usługa równoważenia obciążenia nie obsługuje reguł ruchu wychodzącego.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z Load Balancer, zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznego](quickstart-load-balancer-standard-public-portal.md) .
- Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
- Informacje o [publicznym adresie IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Informacje o [prywatnym adresie IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Dowiedz się więcej o korzystaniu z [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md).
- Dowiedz się więcej na temat [diagnostyki usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md).
- Więcej informacji [na temat resetowania protokołu TCP w trybie bezczynności](load-balancer-tcp-reset.md).
- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa z regułami równoważenia obciążenia dla portów ha](load-balancer-ha-ports-overview.md).
- Dowiedz się więcej o korzystaniu [z Load Balancer z wieloma konfiguracjami adresów IP frontonu](load-balancer-multivip-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
- Dowiedz się więcej o [typach sond](load-balancer-custom-probe-overview.md#types).
- Dowiedz się więcej o [limitach usługi równoważenia obciążenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Dowiedz się więcej o używaniu [przekazywania portów](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Dowiedz się więcej o [regułach ruchu wychodzącego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
