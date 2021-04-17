---
title: Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa
description: Możesz zintegrować usługę Azure Firewall siecią wirtualną z usługą Azure usługa Load Balancer w warstwie Standardowa (publiczną lub wewnętrzną).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505908"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa

Możesz zintegrować usługę Azure Firewall siecią wirtualną z usługą Azure usługa Load Balancer w warstwie Standardowa (publiczną lub wewnętrzną). 

Preferowanym projektem jest zintegrowanie wewnętrznego modułu równoważenia obciążenia z zaporą platformy Azure, ponieważ taki projekt jest znacznie prostszy. Możesz użyć publicznego modułu równoważenia obciążenia, jeśli masz już jeden wdrożony i chcesz zachować go na miejscu. Należy jednak pamiętać o problemie związanym z routingiem asymetrycznym, który może wpłynąć na funkcjonowanie w scenariuszu z publicznym modułem równoważenia obciążenia.

Aby uzyskać więcej informacji na Azure Load Balancer, [zobacz Co to jest Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Publiczny moduł równoważenia obciążenia

W przypadku publicznego równoważenia obciążenia jest on wdrażany przy użyciu publicznego adresu IP frontona.

### <a name="asymmetric-routing"></a>Routing asymetryczny

Routing asymetryczny to miejsce, w którym pakiet przejmuje jedną ścieżkę do miejsca docelowego i przenosi inną ścieżkę podczas powrotu do źródła. Ten problem występuje, gdy podsieć ma trasę domyślną przechodzącą do prywatnego adresu IP zapory i używasz publicznego równoważenia obciążenia. W takim przypadku przychodzący ruch usługi równoważenia obciążenia jest odbierany za pośrednictwem jego publicznego adresu IP, ale ścieżka powrotna przechodzi przez prywatny adres IP zapory. Ponieważ zapora jest stanowa, porzuca pakiet powrotny, ponieważ nie wie o takiej ustanowionej sesji.

### <a name="fix-the-routing-issue"></a>Rozwiązywanie problemu z routingiem

Podczas wdrażania podsieci Azure Firewall jednym krokiem jest utworzenie trasy domyślnej dla podsieci, która kieruje pakiety przez prywatny adres IP zapory znajdujący się w podsieci AzureFirewallSubnet. Aby uzyskać więcej informacji, [zobacz Samouczek: wdrażanie](tutorial-firewall-deploy-portal.md#create-a-default-route)i konfigurowanie Azure Firewall użyciu Azure Portal .

Wprowadzając zaporę do scenariusza z usługą równoważenia obciążenia, chcesz, aby ruch internetowy przechodził przez publiczny adres IP zapory. W tym miejscu zapora stosuje reguły zapory i natłoku adresów SIECIowych pakiety do publicznego adresu IP usługi równoważenia obciążenia. W tym miejscu występuje problem. Pakiety docierają do publicznego adresu IP zapory, ale wracają do zapory za pośrednictwem prywatnego adresu IP (przy użyciu trasy domyślnej).
Aby uniknąć tego problemu, utwórz dodatkową trasę hosta dla publicznego adresu IP zapory. Pakiety przechodzące do publicznego adresu IP zapory są kierowane przez Internet. Pozwala to uniknąć trasy domyślnej do prywatnego adresu IP zapory.

![Routing asymetryczny](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Przykład tabeli tras

Na przykład następujące trasy są dla zapory pod publicznym adresem IP 20.185.97.136 i prywatnym adresem IP 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabela tras](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Przykład reguły nat

W poniższym przykładzie reguła NAT tłumaczy ruch RDP na zaporę na poziomie 20.185.97.136 do usługi równoważenia obciążenia podaną w 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Reguła nat](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondy kondycji

Pamiętaj, że usługa sieci Web musi być uruchomiona na hostach w puli usługi równoważenia obciążenia, jeśli używasz sond kondycji TCP do portu 80 lub sond HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia

W przypadku wewnętrznego równoważenia obciążenia jest on wdrażany przy użyciu prywatnego adresu IP frontona.

W tym scenariuszu nie występuje problem z routingiem asymetrycznym. Pakiety przychodzące docierają do publicznego adresu IP zapory, są tłumaczone na prywatny adres IP usługi równoważenia obciążenia, a następnie wracają do prywatnego adresu IP zapory przy użyciu tej samej ścieżki powrotu.

W związku z tym można wdrożyć ten scenariusz podobnie jak w scenariuszu z publicznym równoważeniem obciążenia, ale bez konieczności użycia trasy hosta z publicznym adresem IP zapory.

Maszyny wirtualne w puli zaplecza mogą mieć wychodzące połączenie internetowe za pośrednictwem Azure Firewall. Skonfiguruj trasę zdefiniowaną przez użytkownika w podsieci maszyny wirtualnej przy użyciu zapory jako następnego przeskoku.


## <a name="additional-security"></a>Dodatkowe zabezpieczenia

Aby jeszcze bardziej zwiększyć bezpieczeństwo scenariusza ze zrównoważonym obciążeniem, można użyć sieciowych grup zabezpieczeń.

Można na przykład utworzyć sieciową grupę danych w podsieci zaplecza, w której znajdują się maszyny wirtualne ze zrównoważonym obciążeniem. Zezwalaj na ruch przychodzący pochodzący z adresu IP/portu zapory.

![Sieciowa grupa zabezpieczeń](media/integrate-lb/nsg-01.png)

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak wdrożyć i skonfigurować Azure Firewall](tutorial-firewall-deploy-portal.md).