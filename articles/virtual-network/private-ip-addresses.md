---
title: Prywatne adresy IP na platformie Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o prywatnych adresach IP na platformie Azure.
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
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223265"
---
# <a name="private-ip-addresses"></a>Prywatne adresy IP
Prywatne adresy IP umożliwiają komunikację między zasobami na platformie Azure. 

Zasoby mogą być następujące:
* Usługi platformy Azure, takie jak:
    * Interfejsy sieciowe maszyny wirtualnej
    * Wewnętrzne moduły równoważenia obciążenia (ILB)
    * Bramy aplikacji
* W [sieci wirtualnej](virtual-networks-overview.md).
* Sieć lokalna za pośrednictwem bramy sieci VPN lub obwodu ExpressRoute.

Prywatne adresy IP umożliwiają komunikację z tymi zasobami bez użycia publicznego adresu.

## <a name="allocation-method"></a>Metoda alokacji

Platforma Azure przypisuje prywatne adresy IP do zasobów z zakresu adresów podsieci sieci wirtualnej, w której znajduje się zasób.

Platforma Azure rezerwuje pierwsze cztery adresy w każdym zakresie adresów podsieci. Adresów nie można przypisać do zasobów. Na przykład jeśli zakres adresów podsieci to 10.0.0.0/16, adresy 10.0.0.0-10.0.0.3 i 10.0.255.255 są niedostępne. Adresy IP w zakresie adresów podsieci mogą być przypisane tylko do jednego zasobu naraz. 

Istnieją dwie metody, w których podano prywatny adres IP:

- **Dynamiczna** — platforma Azure przypisuje następny dostępny nieprzypisany lub niezarezerwowany adres IP z zakresu adresów podsieci. Na przykład platforma Azure przypisze do nowego zasobu adres 10.0.0.10, jeśli adresy 10.0.0.4–10.0.0.9 są już przypisane do innych zasobów. 

    Metoda dynamiczna to domyślna metoda alokacji. Po przypisaniu dynamiczne adresy IP są wydawane, jeśli interfejs sieciowy jest:
    
    * Usunięte
    * Ponownie przypisano do innej podsieci w tej samej sieci wirtualnej.
    * Metoda alokacji została zmieniona na statyczną, a określono inny adres IP. 
    
    Domyślnie platforma Azure przypisuje poprzedni adres dynamicznie przypisany jako adres statyczny po zmianie metody alokacji z dynamicznej na statyczną.

- **Statyczna** — użytkownik wybiera i przypisuje następny dostępny nieprzypisany lub niezarezerwowany adres IP z zakresu adresów podsieci. 

    Na przykład zakres adresów podsieci to 10.0.0.0/16, a adresy 10.0.0.4-10.0.0.9 są przypisywane do innych zasobów. Można przypisać dowolny adres między 10.0.0.10-10.0.255.254. Adresy statyczne są zwalniane tylko w przypadku usunięcia interfejsu sieciowego. 
    
    Platforma Azure przypisuje statyczny adres IP jako dynamiczny adres IP, gdy metoda alokacji zostanie zmieniona. Ponowne przypisanie występuje nawet wtedy, gdy adres nie jest następnym dostępnym w podsieci. Adres zmienia się, gdy interfejs sieciowy jest przypisany do innej podsieci.
    
    Aby przypisać interfejs sieciowy do innej podsieci, należy zmienić metodę alokacji ze statyczna na dynamiczna. Przypisz interfejs sieciowy do innej podsieci, a następnie zmień metodę alokacji z powrotem na statyczną. Przypisz adres IP z zakresu adresów nowej podsieci.
    
## <a name="virtual-machines"></a>Maszyny wirtualne

Co najmniej jeden prywatny adres IP jest przypisany do co najmniej jednego **interfejsu sieciowego**. Interfejsy sieciowe są przypisane do maszyny wirtualnej z [systemem Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Metodę alokacji dla każdego prywatnego adresu IP możesz określić jako dynamiczną lub statyczną.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Wewnętrzne rozpoznawanie nazwy hosta DNS (dla maszyn wirtualnych)

Usługi Azure Virtual Machines są domyślnie skonfigurowane z [serwerami DNS zarządzanymi przez platformę Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) . Można jawnie skonfigurować niestandardowe serwery DNS. Te serwery DNS zapewniają rozpoznawanie nazw wewnętrznych maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.

Mapowanie nazwy hosta na prywatny adres IP maszyny wirtualnej jest dodawane do serwerów DNS zarządzanych przez platformę Azure. 

Nazwa hosta jest mapowana na podstawowy adres IP głównego interfejsu sieciowego, gdy maszyna wirtualna ma:

* Wiele interfejsów sieciowych
* Wiele adresów IP
* Oba

Maszyny wirtualne skonfigurowane przy użyciu systemu DNS zarządzanego przez platformę Azure rozwiązują nazwy hostów w tej samej sieci wirtualnej. Użyj niestandardowego serwera DNS do rozpoznawania nazw hostów maszyn wirtualnych w połączonych sieciach wirtualnych.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Wewnętrzne moduły równoważenia obciążenia (ILB) i bramy aplikacji

Prywatny adres IP można przypisać do konfiguracji **frontonu** :

* [Wewnętrzny moduł równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Usługa Azure Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Ten prywatny adres IP służy jako wewnętrzny punkt końcowy. Wewnętrzny punkt końcowy jest dostępny tylko dla zasobów w sieci wirtualnej i podłączonych do niej sieci zdalnych. Można przypisać dynamiczny lub statyczny adres IP.

## <a name="at-a-glance"></a>W skrócie
W poniższej tabeli przedstawiono właściwość, za pomocą której prywatny adres IP można skojarzyć z zasobem. 

Wyświetlane są również możliwe metody alokacji, które mogą być używane:

* Dynamiczny
* Static

| Zasób najwyższego poziomu | Skojarzenie adresu IP | Dynamiczny | Static |
| --- | --- | --- | --- |
| Maszyna wirtualna |Interfejs sieciowy |Tak |Tak |
| Moduł równoważenia obciążenia |Konfiguracja frontonu |Tak |Tak |
| Brama aplikacji |Konfiguracja frontonu |Tak |Tak |

## <a name="limits"></a>Limity
Limity adresów IP można znaleźć w pełnym zestawie [limitów dla sieci](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) na platformie Azure. Ograniczenia są podzielone według regionu i subskrypcji. [Skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby zwiększyć domyślne limity do maksymalnych limitów w zależności od potrzeb firmy.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [publiczne adresy IP na platformie Azure](public-ip-addresses.md)
* [Wdrażanie maszyny wirtualnej ze statycznym prywatnym adresem IP przy użyciu witryny Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)