---
title: Azure Load Balancer i Strefy dostępności
titleSuffix: Azure Load Balancer
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z usługą Azure usługa Load Balancer w warstwie Standardowa i Strefy dostępności.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699117"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer i Strefy dostępności

Azure Load Balancer obsługuje scenariusze stref dostępności. Możesz użyć usługa Load Balancer w warstwie Standardowa, aby zwiększyć dostępność w całym scenariuszu przez wyrównanie zasobów do i dystrybucja między strefami.  Zapoznaj się z tym dokumentem, aby poznać te koncepcje i podstawowe wskazówki dotyczące projektowania scenariusza

Load Balancer może być **strefą nadmiarową,** strefą lub **bez stref**. Aby skonfigurować właściwości powiązane ze strefą (wymienione powyżej) dla modułu równoważenia obciążenia, wybierz odpowiedni wymagany typ frontonu.

## <a name="zone-redundant"></a>Strefa nadmiarowa

W regionie z Strefy dostępności usługa Load Balancer w warstwie Standardowa może być strefowo nadmiarowy. Ten ruch jest obsługiwany przez pojedynczy adres IP.

Pojedynczy adres IP frontonu będzie przeżyje awarię strefy. Adres IP frontonu może być używany w celu uzyskania dostępu do wszystkich (bez wpływu) członków puli zaplecza, niezależnie od strefy. Co najmniej jedna strefa dostępności może zakończyć się niepowodzeniem, a ścieżka danych przeżyje, dopóki jedna strefa w regionie pozostanie w dobrej kondycji.

Adres IP frontonu jest obsługiwany jednocześnie przez wiele niezależnych wdrożeń infrastruktury w wielu strefach dostępności. Wszelkie ponowne próby lub ponowna próba ponownego ustanowienia zakończą się pomyślnie w innych strefach, których nie dotyczy awaria strefy.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network translator adresów sieciowych">
</p>

*Ilustracja: moduł równoważenia obciążenia strefowo nadmiarowy*

## <a name="zonal"></a>Strefowych

Istnieje możliwość, że fronton będzie miał gwarancję pojedynczej strefy, która jest znana jako *zona*.  Ten scenariusz oznacza, że wszystkie przepływy ruchu przychodzącego lub wychodzącego są obsługiwane przez jedną strefę w regionie.  Fronton udostępnia losy z kondycją strefy.  Błędy w ścieżce danych nie mają wpływ na awarie w strefach innych niż to, w którym zostało zagwarantowane. Za pomocą stref frontonów można uwidocznić adres IP dla strefy dostępności.  

Ponadto jest obsługiwane korzystanie z stref frontonów bezpośrednio dla punktów końcowych ze zrównoważonym obciążeniem w ramach każdej strefy. Ta konfiguracja pozwala udostępnić punkty końcowe ze zrównoważonym obciążeniem strefy, aby osobno monitorować każdą strefę. W przypadku publicznych punktów końcowych można zintegrować je z produktem równoważenia obciążenia DNS, takim jak [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , i użyć pojedynczej nazwy DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network translator adresów sieciowych">
</p>

*Ilustracja: moduł równoważenia obciążenia w Zona*

W przypadku publicznego frontonu modułu równoważenia obciążenia należy dodać parametr **strefy** do publicznego adresu IP. Ten publiczny adres IP jest przywoływany przez konfigurację adresu IP frontonu używaną przez odpowiednią regułę.

W przypadku frontonu wewnętrznego modułu równoważenia obciążenia Dodaj parametr **strefy** do konfiguracji adresu IP frontonu modułu równoważenia obciążenia. Strefa frontonu gwarantuje adres IP w podsieci do określonej strefy.

## <a name="design-considerations"></a><a name="design"></a> Zagadnienia dotyczące projektowania

Teraz, po zrozumieniu właściwości związanych ze strefą usługa Load Balancer w warstwie Standardowa, następujące zagadnienia dotyczące projektu mogą pomóc podczas projektowania pod kątem wysokiej dostępności.

### <a name="tolerance-to-zone-failure"></a>Odporność na awarie strefy

- **Strefa nadmiarowa** Load Balancer może obsłużyć zasób strefy w każdej strefie przy użyciu jednego adresu IP.  Adres IP może przetrwać co najmniej jedną awarię strefy, o ile co najmniej jedna strefa pozostaje w dobrej kondycji w regionie.
- **Strefa** frontonu to zmniejszenie usługi do pojedynczej strefy i udostępnienie losów z odpowiednią strefą. Jeśli strefa, w której znajduje się wdrożenie, nie działa, wdrożenie nie zatrzyma tego błędu.

Zalecane jest użycie nadmiarowego Load Balancer strefy dla obciążeń produkcyjnych.

### <a name="control-vs-data-plane-implications"></a>Kontrola wpływu na płaszczyzny danych a

Nadmiarowość strefy nie oznacza hitless płaszczyzny danych ani płaszczyzny kontroli. Przepływ nadmiarowy strefy może korzystać z dowolnej strefy, a przepływy będą używać wszystkich stref w dobrej kondycji w regionie. W przypadku awarii strefy nie ma to wpływu na przepływy ruchu korzystające z prawidłowych stref.

Może to wpływać na przepływy ruchu korzystające z strefy w czasie awarii strefy, ale aplikacje mogą je odzyskać. Ruch kontynuuje się w strefach zdrowych w regionie podczas ponownej transmisji, gdy platforma Azure połączyła się z awarią strefy.

Przejrzyj [wzorce projektowania w chmurze platformy Azure](/azure/architecture/patterns/) , aby zwiększyć odporność aplikacji na błędy.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [strefy dostępności](../availability-zones/az-overview.md)
- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md)
- Dowiedz się, jak [równoważyć obciążenie maszyn wirtualnych w strefie przy użyciu usługa Load Balancer w warstwie Standardowa zona](./quickstart-load-balancer-standard-public-cli.md)
- Dowiedz się, jak [równoważyć obciążenie maszyn wirtualnych między strefami przy użyciu strefy nadmiarowej usługa Load Balancer w warstwie Standardowa](./quickstart-load-balancer-standard-public-cli.md)
- Dowiedz się więcej o [wzorcach projektowych chmury platformy Azure](/azure/architecture/patterns/) w celu zwiększenia odporności aplikacji na scenariusze błędów.
