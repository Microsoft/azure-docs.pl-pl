---
title: Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa
description: Zaporę platformy Azure można zintegrować z siecią wirtualną przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa (publicznej lub wewnętrznej).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: 3bde4c11e9dc34be13efb25864fe75054d22bddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91363107"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa

Zaporę platformy Azure można zintegrować z siecią wirtualną przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa (publicznej lub wewnętrznej). 

Preferowanym projektem jest zintegrowanie wewnętrznego modułu równoważenia obciążenia z zaporą platformy Azure, ponieważ taki projekt jest znacznie prostszy. Możesz użyć publicznego modułu równoważenia obciążenia, jeśli masz już jeden wdrożony i chcesz zachować go na miejscu. Należy jednak pamiętać o problemie związanym z routingiem asymetrycznym, który może wpłynąć na funkcjonowanie w scenariuszu z publicznym modułem równoważenia obciążenia.

Aby uzyskać więcej informacji na temat Azure Load Balancer, zobacz [co to jest Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Publiczny moduł równoważenia obciążenia

W przypadku publicznego modułu równoważenia obciążenia moduł równoważenia obciążenia jest wdrażany przy użyciu publicznego adresu IP frontonu.

### <a name="asymmetric-routing"></a>Routing asymetryczny

Routing asymetryczny polega na tym, że pakiet przyjmuje jedną ścieżkę do miejsca docelowego i pobiera kolejną ścieżkę podczas powrotu do źródła. Ten problem występuje, gdy podsieć ma domyślną trasę do prywatnego adresu IP zapory i korzysta z publicznego modułu równoważenia obciążenia. W takim przypadku ruch przychodzącego modułu równoważenia obciążenia jest odbierany za pośrednictwem jego publicznego adresu IP, ale ścieżka zwrotna przechodzi przez prywatny adres IP zapory. Ponieważ Zapora jest stanowa, opuszcza pakiet zwracany, ponieważ Zapora nie wie o takiej ustanowionej sesji.

### <a name="fix-the-routing-issue"></a>Rozwiąż problem z routingiem

Po wdrożeniu zapory platformy Azure w podsieci, jeden krok to utworzenie trasy domyślnej dla podsieci kieruje pakiety przez prywatny adres IP zapory znajdujący się w AzureFirewallSubnet. Aby uzyskać więcej informacji, zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Gdy wprowadzisz zaporę do scenariusza modułu równoważenia obciążenia, chcesz, aby Twój ruch internetowy był używany przez publiczny adres IP zapory. Z tego miejsca Zapora stosuje reguły zapory i translator adresów sieciowych pakietów na publiczny adres IP modułu równoważenia obciążenia. Jest to miejsce, w którym występuje problem. Pakiety docierają do publicznego adresu IP zapory, ale Wróć do zapory za pomocą prywatnego adresu IP (przy użyciu trasy domyślnej).
Aby uniknąć tego problemu, należy utworzyć dodatkową trasę hosta dla publicznego adresu IP zapory. Pakiety przechodzące do publicznego adresu IP zapory są kierowane przez Internet. Pozwala to uniknąć przełączenia trasy domyślnej do prywatnego adresu IP zapory.

![Routing asymetryczny](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Przykład tabeli tras

Na przykład następujące trasy dotyczą zapory pod publicznym adresem IP 20.185.97.136 i prywatnym adresem IP 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabela tras](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Przykład reguły NAT

W poniższym przykładzie reguła NAT tłumaczy ruch RDP na zaporę w 20.185.97.136 przez moduł równoważenia obciążenia w 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Reguła NAT](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondy kondycji

Należy pamiętać, że usługa sieci Web jest uruchomiona na hostach w puli modułu równoważenia obciążenia, jeśli sondy kondycji protokołu TCP są używane do portów 80 lub sond protokołu HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia

W przypadku wewnętrznego modułu równoważenia obciążenia moduł równoważenia obciążenia jest wdrażany przy użyciu prywatnego adresu IP frontonu.

W tym scenariuszu nie ma żadnego asymetrycznego problemu z routingiem. Pakiety przychodzące docierają do publicznego adresu IP zapory, są tłumaczone na prywatny adres IP usługi równoważenia obciążenia, a następnie wraca do prywatnego adresu IP zapory przy użyciu tej samej ścieżki zwracanej.

Tak więc można wdrożyć ten scenariusz podobny do scenariusza publicznego modułu równoważenia obciążenia, ale bez potrzeby trasy hosta publicznego adresu IP zapory.

>[!NOTE]
>Maszyny wirtualne w puli zaplecza nie będą miały wychodzącej łączności z Internetem przy użyciu tej konfiguracji. </br> Aby uzyskać więcej informacji na temat zapewniania łączności wychodzącej, zobacz: </br> **[Połączenia wychodzące na platformie Azure](../load-balancer/load-balancer-outbound-connections.md)**</br> Opcje zapewniania łączności: </br> **[Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego](../load-balancer/egress-only.md)** </br> [**Co to jest Virtual Network translator adresów sieciowych?**](../virtual-network/nat-overview.md)


## <a name="additional-security"></a>Dodatkowe zabezpieczenia

Aby dodatkowo zwiększyć bezpieczeństwo scenariusza z równoważeniem obciążenia, możesz użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń).

Można na przykład utworzyć sieciowej grupy zabezpieczeń w podsieci zaplecza, w której znajdują się maszyny wirtualne o zrównoważonym obciążeniu. Zezwalaj na ruch przychodzący pochodzący z adresu IP/portu zapory.

![Sieciowa grupa zabezpieczeń](media/integrate-lb/nsg-01.png)

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [grupy zabezpieczeń](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).