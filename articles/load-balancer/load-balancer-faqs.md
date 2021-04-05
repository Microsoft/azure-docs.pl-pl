---
title: Często zadawane pytania — Azure Load Balancer
description: Odpowiedzi na często zadawane pytania dotyczące Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3752a36d22f879b95b02bd49436be78212fe56a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576045"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer często zadawane pytania

## <a name="what-types-of-load-balancer-exist"></a>Jakie typy Load Balancer istnieją?
Wewnętrzne moduły równoważenia obciążenia, które równoważą ruch w obrębie sieci wirtualnej i zewnętrznych usług równoważenia obciążenia, które równoważą ruch do i z połączonego z Internetem punktu końcowego. Aby uzyskać więcej informacji, zobacz [typy Load Balancer](components.md#frontend-ip-configurations). 

Dla obu tych typów platforma Azure oferuje podstawową jednostkę SKU i standardową jednostkę SKU, która ma różne możliwości funkcjonalne, wydajnościowe, dotyczące zabezpieczeń i śledzenia kondycji. Te różnice zostały wyjaśnione w naszym artykule [porównawczym jednostki SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Jak przeprowadzić uaktualnienie z warstwy Podstawowa do usługa Load Balancer w warstwie Standardowa?
Zapoznaj się z artykułem " [uaktualnienie z warstwy Podstawowa do Standard](upgrade-basic-standard.md) ", aby uzyskać zautomatyzowany skrypt i wskazówki dotyczące uaktualniania jednostki SKU Load Balancer.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Jakie są różne opcje równoważenia obciążenia na platformie Azure?
Zobacz [Przewodnik po technologii modułu równoważenia obciążenia](/azure/architecture/guide/technology-choices/load-balancing-overview)  dla dostępnych usług równoważenia obciążenia i zalecane zastosowania dla każdej z nich.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Gdzie mogę znaleźć szablony Load Balancer ARM?
Zobacz [listę Azure Load Balancer szablonów szybkiego startu](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) dla szablonów usługi ARM typowych wdrożeń.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Jak różnią się przychodzące reguły NAT od reguł równoważenia obciążenia?
Reguły NAT są używane do określania zasobu zaplecza, do którego ma być kierowany ruch. Na przykład skonfigurowanie określonego portu usługi równoważenia obciążenia do wysyłania ruchu RDP do określonej maszyny wirtualnej. Reguły równoważenia obciążenia służą do określania puli zasobów zaplecza, do której ma być kierowany ruch, co pozwala zrównoważyć obciążenie dla każdego wystąpienia. Na przykład reguła modułu równoważenia obciążenia może kierować pakiety TCP na porcie 80 modułu równoważenia obciążenia w puli serwerów sieci Web.

## <a name="what-is-ip-1686312916"></a>Co to jest 168.63.129.16 IP?
Wirtualny adres IP dla hosta oznaczonego jako infrastruktura platformy Azure Load Balancer, z którego pochodzą sondy kondycji platformy Azure. Podczas konfigurowania wystąpień zaplecza muszą zezwalać na ruch z tego adresu IP, aby pomyślnie reagować na sondy kondycji. Ta reguła nie współdziała z dostępem do Load Balancer frontonu. Jeśli nie używasz Azure Load Balancer, możesz zastąpić tę regułę. Więcej informacji na temat tagów usługi można znaleźć [tutaj](../virtual-network/service-tags-overview.md#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Czy można używać globalnej komunikacji równorzędnej sieci wirtualnej z podstawową Load Balancer?
Nie. Podstawowa Load Balancer nie obsługuje globalnej komunikacji równorzędnej sieci wirtualnej. Zamiast tego można użyć usługa Load Balancer w warstwie Standardowa. Zapoznaj się z artykułem [uaktualnienie z warstwy Podstawowa do standardowa](upgrade-basic-standard.md) w celu zapewnienia bezproblemowego uaktualnienia.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Jak mogę wykryć publiczny adres IP, którego używa maszyna wirtualna platformy Azure?

Istnieje wiele sposobów określenia publicznego źródłowego adresu IP połączenia wychodzącego. OpenDNS zapewnia usługę, która może wyświetlać publiczny adres IP maszyny wirtualnej.
Za pomocą polecenia nslookup można wysłać zapytanie DNS dla nazwy myip.opendns.com do OpenDNS rozpoznawania nazw. Usługa zwraca źródłowy adres IP, który został użyty do wysłania zapytania. Po uruchomieniu następującego zapytania z maszyny wirtualnej odpowiedź jest publicznym adresem IP używanym dla tej maszyny wirtualnej:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>Czy mogę dodać maszynę wirtualną z tego samego zestawu dostępności do różnych pul zaplecza Load Balancer?
Nie, nie jest to możliwe.

## <a name="what-is-the-maximum-data-throughput-that-can-be-achieved-via-an-azure-load-balancer"></a>Jaka jest maksymalna przepływność danych, którą można osiągnąć za pośrednictwem Azure Load Balancer?
Ponieważ usługa Azure LB jest modułem równoważenia obciążenia sieci przekazującym, ograniczenia przepływności są podyktowane przez typ maszyny wirtualnej używanej w puli zaplecza. Aby dowiedzieć się więcej na temat innych informacji dotyczących przepływności sieci, odnoszą się do [przepływności sieci maszyn wirtualnych](../virtual-network/virtual-machine-network-throughput.md).

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Jak działają połączenia z usługą Azure Storage w tym samym regionie?
Połączenia wychodzące za pośrednictwem powyższych scenariuszy nie są wymagane do nawiązania połączenia z magazynem w tym samym regionie, w którym znajduje się maszyna wirtualna. Jeśli nie chcesz tego robić, użyj sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń), jak wyjaśniono powyżej. Łączność wychodząca jest wymagana w przypadku łączności z magazynem w innych regionach. Podczas nawiązywania połączenia z magazynem z maszyny wirtualnej w tym samym regionie źródłowy adres IP w dziennikach diagnostycznych magazynu będzie adresem wewnętrznym dostawcy, a nie publicznym adresem IP maszyny wirtualnej. Jeśli chcesz ograniczyć dostęp do konta magazynu do maszyn wirtualnych w co najmniej jednej podsieci Virtual Network w tym samym regionie, użyj [Virtual Network punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md) , a nie publicznego adresu IP podczas konfigurowania zapory konta magazynu. Po skonfigurowaniu punktów końcowych usługi zobaczysz Virtual Network prywatny adres IP w dziennikach diagnostycznych magazynu, a nie na wewnętrznym adresie dostawcy.

## <a name="does-azure-load-balancer-support-tlsssl-termination"></a>Czy Azure Load Balancer obsługuje zakończenie protokołu TLS/SSL?
Nie, Azure Load Balancer nie obsługuje obecnie zakończenia, ponieważ jest to przejście przez moduł równoważenia obciążenia sieci. Jeśli aplikacja wymaga tego, Application Gateway może być potencjalnym rozwiązaniem.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Jakie są najlepsze praktyki w odniesieniu do łączności wychodzącej?
Usługa Load Balancer w warstwie Standardowa i Standard publiczny adres IP wprowadzają możliwości i różne zachowania łączności wychodzącej. Nie są one takie same jak podstawowe jednostki SKU. Jeśli chcesz mieć łączność wychodzącą podczas pracy z standardowymi jednostkami SKU, musisz jawnie zdefiniować ją przy użyciu standardowych publicznych adresów IP lub standardowych Load Balancer publicznych. Obejmuje to tworzenie łączności wychodzącej przy użyciu wewnętrznego usługa Load Balancer w warstwie Standardowa. Zalecamy, aby zawsze używać reguł ruchu wychodzącego dla standardowej Load Balancer publicznej. Oznacza to, że w przypadku użycia wewnętrznego usługa Load Balancer w warstwie Standardowa należy wykonać kroki w celu utworzenia łączności wychodzącej dla maszyn wirtualnych w puli zaplecza, jeśli jest wymagana łączność wychodząca. W kontekście łączności wychodzącej, pojedynczej autonomicznej maszyny wirtualnej, wszystkie maszyny wirtualne w zestawie dostępności, wszystkie wystąpienia w VMSS działają jako Grupa. Oznacza to, że jeśli jedna maszyna wirtualna w zestawie dostępności jest skojarzona ze standardową jednostką SKU, wszystkie wystąpienia maszyn wirtualnych w tym zestawie dostępności są teraz zgodne z tymi samymi regułami, tak jakby były one skojarzone ze standardową jednostką SKU, nawet jeśli pojedyncze wystąpienie nie jest bezpośrednio skojarzone z nim. To zachowanie jest również zaobserwowane w przypadku autonomicznej maszyny wirtualnej z wieloma kartami interfejsu sieciowego podłączonymi do modułu równoważenia obciążenia. Jeśli jedna karta sieciowa zostanie dodana jako autonomiczna, będzie miała takie samo zachowanie. Uważnie przejrzyj ten cały dokument, aby poznać ogólne koncepcje, przejrzyj [Usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md) pod kątem różnic między jednostkami SKU i przejrzyj [reguły ruchu wychodzącego](load-balancer-outbound-connections.md#outboundrules).
Użycie reguł ruchu wychodzącego umożliwia precyzyjne sterowanie wszystkimi aspektami łączności wychodzącej.
 
## <a name="next-steps"></a>Następne kroki
Jeśli Twoje pytanie nie jest wymienione powyżej, Prześlij opinię na temat tej strony wraz z pytaniem. Spowoduje to utworzenie problemu usługi GitHub dla zespołu produktu, aby upewnić się, że otrzymasz odpowiedzi na wszystkie nasze pytania klientów.
