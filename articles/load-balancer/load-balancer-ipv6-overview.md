---
title: Omówienie protokołu IPv6 Azure Load Balancer
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z obsługą protokołu IPv6 dla Azure Load Balancer i maszyn wirtualnych o zrównoważonym obciążeniu.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, moduł równoważenia obciążenia platformy Azure, podwójny stos, publiczny adres IP, natywny protokół IPv6, urządzenia przenośne, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 4061a3dbf4dc92d6d412528115d46edc36d20d5e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700693"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Omówienie protokołu IPv6 dla Azure Load Balancer


>[!NOTE] 
>Ta zawartość została zastąpiona przez [protokół IPv6 na potrzeby omówienia sieci wirtualnej platformy Azure](../virtual-network/ipv6-overview.md). Na platformie Azure zalecane są nowe wdrożenia protokołu IPv6 dla funkcji sieci wirtualnych platformy Azure.

>[!NOTE]
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat usługa Load Balancer w warstwie Standardowa, zobacz [Usługa Load Balancer w warstwie Standardowa Omówienie](./load-balancer-overview.md).

Usługi równoważenia obciążenia z podstawową jednostką SKU można wdrożyć przy użyciu adresu IPv6. Oprócz łączności IPv4, zapewnia to następujące możliwości:

* Natywna łączność między publicznymi klientami internetowymi i usługą Azure Virtual Machines (VM) za pośrednictwem modułu równoważenia obciążenia.
* Natywna, kompleksowa łączność z protokołem IPv6 między maszynami wirtualnymi i publicznymi klientami z obsługą protokołu IPv6.

Na poniższej ilustracji przedstawiono funkcje protokołu IPv6 dla Azure Load Balancer.

![Azure Load Balancer przy użyciu protokołu IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po wdrożeniu Klient internetowy z obsługą protokołu IPv4 lub IPv6 może komunikować się z publicznymi adresami IPv4 lub IPv6 (lub nazwami hostów) Load Balancer internetowego na platformie Azure. Moduł równoważenia obciążenia kieruje pakiety IPv6 do prywatnych adresów IPv6 maszyn wirtualnych przy użyciu translatora adresów sieciowych (NAT). Klient internetowy IPv6 nie może komunikować się bezpośrednio z adresem IPv6 maszyn wirtualnych.

## <a name="features"></a>Funkcje

Natywna obsługa protokołu IPv6 dla maszyn wirtualnych wdrożonych za pośrednictwem Azure Resource Manager zapewnia następujące możliwości:

1. Usługi IPv6 o zrównoważonym obciążeniu dla klientów IPv6 w Internecie
2. Natywne punkty końcowe protokołu IPv6 i IPv4 na maszynach wirtualnych ("podwójne piętro")
3. Przychodzące i wychodzące połączenia natywne protokołu IPv6
4. Obsługiwane protokoły, takie jak TCP, UDP i HTTP (S), zapewniają pełny zakres architektur usługi

## <a name="benefits"></a>Korzyści

Ta funkcja zapewnia następujące korzyści:

* Spełnianie przepisów obowiązujących dla instytucji rządowych, które wymagają, aby nowe aplikacje były dostępne tylko dla klientów korzystających
* Umożliwienie deweloperom usług mobilnych i Internetu rzeczy (IOT) korzystania z podwójnego stosu (IPv4 + IPv6) Virtual Machines platformy Azure w celu rozwiązania rosnących rynków usług Mobile & IOT

## <a name="details-and-limitations"></a>Szczegóły i ograniczenia

Szczegóły

* Usługa Azure DNS zawiera rekordy nazw (AAAA) IPv4 a i IPv6 i odpowiada obu rekordom usługi równoważenia obciążenia. Klient wybiera adres (IPv4 lub IPv6) do komunikacji.
* Gdy maszyna wirtualna inicjuje połączenie z publicznym urządzeniem połączonym z Internetem za pomocą protokołu IPv6, źródłowy adres IPv6 maszyny wirtualnej to adres sieciowy przetłumaczony na publiczny adres IPv6 modułu równoważenia obciążenia.
* Maszyny wirtualne z systemem operacyjnym Linux muszą być skonfigurowane do odbierania adresów IP IPv6 za pośrednictwem protokołu DHCP. Wiele obrazów systemu Linux w galerii platformy Azure jest już skonfigurowanych do obsługi protokołu IPv6 bez modyfikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych z systemem Linux](load-balancer-ipv6-for-linux.md)
* Jeśli zdecydujesz się użyć sondy kondycji z modułem równoważenia obciążenia, Utwórz sondę IPv4 i użyj jej z punktami końcowymi IPv4 i IPv6. Jeśli usługa na maszynie wirtualnej ulegnie awarii, punkty końcowe IPv4 i IPv6 są wyłączane.

Ograniczenia

* Nie można dodać reguł równoważenia obciążenia IPv6 do Azure Portal. Reguły można tworzyć tylko za pomocą szablonu, interfejsu wiersza polecenia, programu PowerShell.
* Nie można uaktualnić istniejących maszyn wirtualnych do korzystania z adresów IPv6. Należy wdrożyć nowe maszyny wirtualne.
* Pojedynczy adres IPv6 można przypisać do jednego interfejsu sieciowego w każdej maszynie wirtualnej.
* Publicznych adresów IPv6 nie można przypisać do maszyny wirtualnej. Można je przypisać tylko do modułu równoważenia obciążenia.
* Nie można skonfigurować wyszukiwania odwrotnego systemu DNS dla publicznych adresów IPv6.
* Maszyny wirtualne z adresami IPv6 nie mogą należeć do usługi w chmurze platformy Azure. Mogą być połączone z usługą Azure Virtual Network (VNet) i komunikować się ze sobą przy użyciu adresów IPv4.
* Prywatne adresy IPv6 można wdrożyć na poszczególnych maszynach wirtualnych w grupie zasobów, ale nie można ich wdrożyć w grupie zasobów za pomocą zestawów skalowania.
* Maszyny wirtualne platformy Azure nie mogą łączyć się za pośrednictwem protokołu IPv6 z innymi maszynami wirtualnymi, innymi usługami platformy Azure lub urządzeniami lokalnymi. Mogą komunikować się tylko z modułem równoważenia obciążenia platformy Azure za pośrednictwem protokołu IPv6. Mogą jednak komunikować się z tymi innymi zasobami przy użyciu protokołu IPv4.
* Ochrona sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla protokołu IPv4 jest obsługiwana w przypadku wdrożeń z podwójnym stosem (IPv4 + IPv6). Sieciowych grup zabezpieczeń nie mają zastosowania do punktów końcowych protokołu IPv6.
* Punkt końcowy protokołu IPv6 na maszynie wirtualnej nie jest narażony bezpośrednio z Internetem. Znajduje się za modułem równoważenia obciążenia. Tylko porty określone w regułach modułu równoważenia obciążenia są dostępne za pośrednictwem protokołu IPv6.
* Zmiana parametru IdleTimeout dla protokołu IPv6 **nie jest obecnie obsługiwana**. Wartość domyślna to cztery minuty.
* Zmiana parametru loadDistributionMethod protokołu IPv6 nie jest **obecnie obsługiwana**.
* Protokół IPv6 dla podstawowego Load Balancer jest zablokowany do **dynamicznej** jednostki SKU.  Protokół IPv6 dla usługa Load Balancer w warstwie Standardowa jest zablokowany do **statycznej** jednostki SKU.
* Usługa NAT64 (tłumaczenie adresów IPv6 na IPv4) nie jest obsługiwana.
* Dołączanie pomocniczej karty sieciowej odwołującej się do podsieci IPv6 do puli zaplecza **nie jest obecnie obsługiwane**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wdrożyć moduł równoważenia obciążenia przy użyciu protokołu IPv6.

* [Dostępność adresów IPv6 według regionów](https://go.microsoft.com/fwlink/?linkid=828357)
* [Wdrażanie modułu równoważenia obciążenia za pomocą protokołu IPv6 przy użyciu szablonu](load-balancer-ipv6-internet-template.md)
* [Wdrażanie modułu równoważenia obciążenia przy użyciu protokołu IPv6 za pomocą Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Wdrażanie modułu równoważenia obciążenia za pomocą protokołu IPv6 przy użyciu interfejsu wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)