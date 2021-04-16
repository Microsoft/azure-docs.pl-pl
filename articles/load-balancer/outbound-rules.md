---
title: Reguły ruchu wychodzącego Azure Load Balancer
description: W tym artykule wyjaśniono, jak skonfigurować reguły ruchu wychodzącego w celu kontrolowania ruchu wychodzącego ruchu internetowego za pomocą Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 339bbd7edf48737113de360812165dc8148c5b93
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375869"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Reguły ruchu wychodzącego Azure Load Balancer

Reguły ruchu wychodzącego umożliwiają jawne definiowanie translatora adresów sieciowych (SNAT) dla publicznego standardowego równoważenia obciążenia. Ta konfiguracja umożliwia korzystanie z publicznych adresów IP usługi równoważenia obciążenia w celu zapewnienia wychodzącej łączności internetowej dla wystąpień zaplecza.

Ta konfiguracja umożliwia:

* Maskowanie adresów IP
* Upraszczanie list zezwalania.
* Zmniejsza liczbę zasobów publicznych adresów IP do wdrożenia.

Dzięki regułom ruchu wychodzącego masz pełną deklaratywną kontrolę nad wychodzącą łącznością internetową. Reguły ruchu wychodzącego umożliwiają skalowanie i dostosowywanie tej możliwości do konkretnych potrzeb. 

Reguły ruchu wychodzącego będą stosowane tylko wtedy, gdy maszyna wirtualna zaplecza nie ma publicznego adresu IP na poziomie wystąpienia (ILPIP).

![Load Balancer reguł ruchu wychodzącego](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Za pomocą reguł ruchu wychodzącego można jawnie zdefiniować zachowanie **wychodzącej sieci SNAT.**

Reguły ruchu wychodzącego umożliwiają kontrolowanie:

* **Które maszyny wirtualne są tłumaczone na które publiczne adresy IP.**
     * Dwie reguły to pula zaplecza 1, która używa niebieskiego adresu IP 1 i 2, a pula zaplecza 2 używa żółtego prefiksu IP.
* **Sposób przydzielania wychodzących portów SNAT.**
     * Jeśli pula zaplecza 2 jest jedyną pulą, która nawiązała połączenia wychodzące, należy przekazać wszystkie porty SNAT do puli zaplecza 2 i żadne do puli zaplecza 1.
* **Protokoły, dla których ma być zapewniane tłumaczenie wychodzące.**
     * Jeśli pula zaplecza 2 wymaga portów UDP dla ruchu wychodzącego, a pula zaplecza 1 wymaga protokołu TCP, nadaj portom TCP port 1, a portowi UDP 2.
* **Jaki czas trwania należy użyć dla limitu czasu bezczynności połączenia wychodzącego (4–120 minut).**
     * W przypadku długotrwałych połączeń z zachowaj dostęp rezerwuj bezczynne porty dla długotrwałych połączeń na maksymalnie 120 minut. Załóżmy, że nieaktualne połączenia są porzucone, a porty wydania w ciągu 4 minut dla nowych połączeń 
* **Czy wysłać resetowania protokołu TCP w przypadku przeoczania limitu czasu bezczynności.**
     * Czy w przypadku przekierowania bezczynnych połączeń wysyłamy protokół TCP RST do klienta i serwera, aby wiedzieć, że przepływ został porzucony?

## <a name="outbound-rule-definition"></a>Definicja reguły ruchu wychodzącego

Reguły ruchu wychodzącego mają tę samą znajomą składnię co w przypadku równoważenia obciążenia i reguł NAT dla ruchu przychodzącego: **pula** zaplecza parametrów  +    +  **frontendu.** 

Reguła ruchu wychodzącego konfiguruje  translę na frontonie dla wszystkich maszyn wirtualnych zidentyfikowanych przez pulę _zaplecza._  

Parametry _zapewniają_ dodatkową ładną kontrolę nad wychodzącym algorytmem NAT.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skalowanie wychodzącego nat z wieloma adresami IP

Każdy dodatkowy adres IP zapewnia frontonie dodatkowe 64 000 portów efemeralnych dla usługi równoważenia obciążenia, które mogą być używane jako porty SNAT. 

Planowanie scenariuszy na dużą skalę przy użyciu wielu adresów IP. Użyj reguł ruchu wychodzącego, aby [ograniczyć wyczerpanie SNAT.](troubleshoot-outbound-connection.md#snatexhaust) 

Można również użyć prefiksu [publicznego adresu IP bezpośrednio](./load-balancer-outbound-connections.md#outboundrules) z regułą ruchu wychodzącego. 

Prefiks publicznego adresu IP zwiększa skalowanie wdrożenia. Prefiks można dodać do listy zezwalających przepływów pochodzących z zasobów platformy Azure. Konfigurację adresu IP frontonia w ramach usługi równoważenia obciążenia można skonfigurować tak, aby odwoływała się do prefiksu publicznego adresu IP.  

Równoważenie obciążenia ma kontrolę nad prefiksem publicznego adresu IP. Reguła ruchu wychodzącego będzie automatycznie używać wszystkich publicznych adresów IP zawartych w prefiksie publicznego adresu IP dla połączeń wychodzących. 

Każdy z adresów IP w ramach prefiksu publicznego adresu IP zapewnia dodatkowe 64 000 portów efemeralnych na adres IP, które mogą być używane przez usługę równoważenia obciążenia jako porty SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Limit czasu bezczynności przepływu wychodzącego i resetowanie protokołu TCP

Reguły ruchu wychodzącego zapewniają parametr konfiguracji w celu kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasowania go do potrzeb aplikacji. Limity czasu bezczynności dla ruchu wychodzącego domyślnie to 4 minuty. Aby uzyskać więcej informacji, zobacz [configure idle timeouts](load-balancer-tcp-idle-timeout.md)(Konfigurowanie limitów czasu bezczynności). 

Domyślnym zachowaniem równoważenia obciążenia jest dyskretne upuszczenie przepływu po osiągnięciu limitu czasu bezczynności dla ruchu wychodzącego. Parametr `enableTCPReset` umożliwia przewidywalne zachowanie i kontrolę aplikacji. Parametr określa, czy wysyłać dwukierunkowe resetowanie protokołu TCP (TCP RST) po przeoczaniu limitu czasu bezczynności dla ruchu wychodzącego. 

Aby uzyskać [szczegółowe informacje, w](./load-balancer-tcp-reset.md) tym informacje o dostępności regionu, zapoznaj się z tematem Resetowanie protokołu TCP w przypadku limitu czasu bezczynności.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Jawne zabezpieczanie i kontrolowanie łączności wychodzącej

Reguły równoważenia obciążenia zapewniają automatyczne programowanie ruchu wychodzącego NAT. Niektóre scenariusze korzystają lub wymagają wyłączenia automatycznego programowania ruchu wychodzącego NAT przez regułę równoważenia obciążenia. Wyłączenie za pośrednictwem reguły umożliwia kontrolowanie lub uściślianie zachowania.  

Tego parametru można użyć na dwa sposoby:

1. Zapobieganie przychodzącym adresom IP dla wychodzącej sieci SNAT. Wyłącz wychodzącą funkcję SNAT w regułę równoważenia obciążenia.
  
2. Dostrajanie parametrów **SNAT ruchu wychodzącego** adresu IP używanego jednocześnie dla ruchu przychodzącego i wychodzącego. Aby reguła ruchu wychodzącego przejmie kontrolę, należy wyłączyć automatyczny wychodzący nat. Aby zmienić alokację portów SNAT adresu używanego również dla ruchu przychodzącego, `disableOutboundSnat` parametr musi mieć wartość true. 

Operacja konfigurowania reguły ruchu wychodzącego nie powiedzie się, jeśli spróbujesz ponownie zdefiniować adres IP używany dla ruchu przychodzącego.  Najpierw wyłącz wychodzący nat dla reguły równoważenia obciążenia.

>[!IMPORTANT]
> Maszyna wirtualna nie będzie mieć łączności wychodzącej, jeśli ustawisz ten parametr na wartość true i nie masz reguły ruchu wychodzącego definiującej łączność wychodzącą.  Niektóre operacje maszyny wirtualnej lub aplikacji mogą zależeć od tego, czy łączność wychodząca jest dostępna. Upewnij się, że rozumiesz zależności scenariusza i przejmujesz wpływ tej zmiany.

Czasami tworzenie przepływu wychodzącego przez maszynę wirtualną jest niepożądane. Może być wymagane zarządzanie tym, które miejsca docelowe odbierają przepływy wychodzące lub które miejsca docelowe rozpoczynają przepływy przychodzące. Sieciowe [grupy zabezpieczeń mogą zarządzać](../virtual-network/network-security-groups-overview.md) lokalizacjami docelowymi, do których dociera maszyna wirtualna. Użyj sieciowych sieciowych organizacji do zarządzania tym, które publiczne miejsca docelowe uruchamiają przepływy przychodzące.

Podczas stosowania sieciowej grupy zabezpieczeń do maszyny wirtualnej ze zrównoważonym obciążeniem należy zwrócić uwagę na tagi usługi [i](../virtual-network/network-security-groups-overview.md#service-tags) domyślne [reguły zabezpieczeń.](../virtual-network/network-security-groups-overview.md#default-security-rules) 

Upewnij się, że maszyna wirtualna może odbierać żądania sondy kondycji Azure Load Balancer.

Jeśli sieciowa grupy dostępności blokowały żądania sondy kondycji z AZURE_LOADBALANCER domyślnego tagu, sonda kondycji maszyny wirtualnej nie powiedzie się i maszyna wirtualna zostanie oznaczona jako niedostępna. Równoważenie obciążenia przestaje wysyłać nowe przepływy do tej maszyny wirtualnej.

## <a name="scenarios-with-outbound-rules"></a>Scenariusze z regułami ruchu wychodzącego
        

### <a name="outbound-rules-scenarios"></a>Scenariusze reguł ruchu wychodzącego


* Skonfiguruj połączenia wychodzące do określonego zestawu publicznych ip lub prefiksów.
* [Zmodyfikuj alokację portów SNAT.](load-balancer-outbound-connections.md)
* Włącz tylko ruch wychodzący.
* Wychodzący nat dla maszyn wirtualnych (bez ruchu przychodzącego).
* Wychodzący nat dla wewnętrznego standardowego równoważenia obciążenia.
* Włącz oba protokoły protokołu TCP & UDP dla ruchu wychodzącego NAT przy użyciu publicznego standardowego równoważenia obciążenia.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenariusz 1. Konfigurowanie połączeń wychodzących do określonego zestawu publicznych adresu IP lub prefiksu


#### <a name="details"></a>Szczegóły


Ten scenariusz pozwala dostosować połączenia wychodzące do połączeń pochodzących z zestawu publicznych adresów IP. Dodawanie publicznych adresów IP lub prefiksów do listy zezwalania lub odmowy na podstawie pochodzenia.


Ten publiczny adres IP lub prefiks może być taki sam jak używany przez regułę równoważenia obciążenia. 


Aby użyć innego publicznego adresu IP lub prefiksu niż używany przez regułę równoważenia obciążenia: 


1. Utwórz publiczny prefiks IP lub publiczny adres IP.
2. Tworzenie publicznego standardowego równoważenia obciążenia 
3. Utwórz frontonie odwołujące się do prefiksu publicznego adresu IP lub publicznego adresu IP, który chcesz użyć. 
4. Ponowne użycie puli zaplecza lub utworzenie puli zaplecza i umieść maszyny wirtualne w puli zaplecza publicznego równoważenia obciążenia
5. Skonfiguruj regułę ruchu wychodzącego w publicznym równoważeniu obciążenia, aby włączyć nat dla ruchu wychodzącego dla maszyn wirtualnych przy użyciu frontonia. Nie zaleca się używania reguły równoważenia obciążenia dla ruchu wychodzącego, wyłączania wychodzącej funkcji SNAT w regułę równoważenia obciążenia.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Scenariusz 2. Modyfikowanie [alokacji portów SNAT](load-balancer-outbound-connections.md)


#### <a name="details"></a>Szczegóły


Reguły ruchu wychodzącego mogą być służące do dostrajania automatycznej alokacji portów [SNAT na podstawie rozmiaru puli zaplecza.](load-balancer-outbound-connections.md#preallocatedports) 


Jeśli wystąpi wyczerpanie SNAT, zwiększ liczbę portów [SNAT](load-balancer-outbound-connections.md) z wartości domyślnej 1024. 


Każdy publiczny adres IP współtwoczy maksymalnie 64 000 portów efemeralnych. Liczba maszyn wirtualnych w puli zaplecza określa liczbę portów dystrybuowanych do poszczególnych maszyn wirtualnych. Jedna maszyna wirtualna w puli zaplecza ma dostęp do maksymalnie 64 000 portów. W przypadku dwóch maszyn wirtualnych reguła ruchu wychodzącego może mieć maksymalnie 32 000 portów [SNAT](load-balancer-outbound-connections.md) (2x 32 000 = 64 000). 


Reguły ruchu wychodzącego mogą być używane do dostrajania portów SNAT podanych domyślnie. Podajesz więcej lub mniej niż zapewnia domyślna alokacja portów [SNAT.](load-balancer-outbound-connections.md) Każdy publiczny adres IP z frontonia reguły ruchu wychodzącego przyczynia się do 64 000 portów efemeralnych do użycia jako [porty SNAT.](load-balancer-outbound-connections.md) 


Równoważenie obciążenia zapewnia [porty SNAT](load-balancer-outbound-connections.md) w wielokrotnościach 8. W przypadku podania wartości, która nie może być podzielna przez 8, operacja konfiguracji zostanie odrzucona. Każda reguła równoważenia obciążenia i reguła NAT dla ruchu przychodzącego będą korzystać z zakresu 8 portów. Jeśli reguła równoważenia obciążenia lub reguły NAT dla ruchu przychodzącego ma ten sam zakres co inny zakres 8, dodatkowe porty nie będą używane.


Jeśli spróbujemy nadać więcej portów [SNAT](load-balancer-outbound-connections.md) niż jest dostępnych na podstawie liczby publicznych adresów IP, operacja konfiguracji zostanie odrzucona. Jeśli na przykład na maszynę wirtualną zostanie nadanych 10 000 portów, a siedem maszyn wirtualnych w puli zaplecza ma jeden publiczny adres IP, konfiguracja zostanie odrzucona. Siedem pomnożonych przez 10 000 przekracza limit 64 000 portów. Dodaj więcej publicznych adresów IP do frontendu reguły ruchu wychodzącego, aby włączyć scenariusz. 


Przywróć [domyślną alokację portów,](load-balancer-outbound-connections.md#preallocatedports) określając 0 jako liczbę portów. Pierwsze 50 wystąpień maszyn wirtualnych otrzyma 1024 porty, 51–100 wystąpień maszyn wirtualnych otrzyma maksymalnie 512 wystąpień. Aby uzyskać więcej informacji na temat domyślnej alokacji portów SNAT, zobacz [tabelę alokacji portów SNAT.](./load-balancer-outbound-connections.md#preallocatedports)


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenariusz 3. Włączanie tylko ruchu wychodzącego


#### <a name="details"></a>Szczegóły


Użyj publicznego standardowego równoważenia obciążenia, aby zapewnić wychodzący nat dla grupy maszyn wirtualnych. W tym scenariuszu użyj reguły ruchu wychodzącego samodzielnie, bez żadnych dodatkowych skonfigurowanych reguł.


> [!NOTE]
> **Translator adresów sieciowych usługi Azure Virtual Network** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez konieczności użycia usługi równoważenia obciążenia. Aby [uzyskać więcej informacji, zobacz Co](../virtual-network/nat-overview.md) to jest Translator adresów sieciowych usługi Azure Virtual Network?.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenariusz 4. NaT dla ruchu wychodzącego tylko dla maszyn wirtualnych (bez ruchu przychodzącego)


> [!NOTE]
> **Translator adresów sieciowych usługi Azure Virtual Network** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez konieczności użycia usługi równoważenia obciążenia. Zobacz [Co to jest Translator adresów sieciowych usługi Azure Virtual Network?](../virtual-network/nat-overview.md) , aby uzyskać więcej informacji.

#### <a name="details"></a>Szczegóły


W tym scenariuszu Azure Load Balancer reguły ruchu wychodzącego i Translator adresów sieciowych usługi Virtual Network są dostępne dla ruchu wychodzącego z sieci wirtualnej.


1. Utwórz publiczny adres IP lub prefiks.
2. Utwórz publiczny standardowy usługę równoważenia obciążenia. 
3. Utwórz fronton skojarzony z publicznym adresem IP lub prefiksem przeznaczonym dla ruchu wychodzącego.
4. Utwórz pulę zaplecza dla maszyn wirtualnych.
5. Umieść maszyny wirtualne w puli zaplecza.
6. Skonfiguruj regułę ruchu wychodzącego, aby włączyć wychodzący nat.



Użyj prefiksu lub publicznego adresu IP do skalowania [portów SNAT.](load-balancer-outbound-connections.md) Dodaj źródło połączeń wychodzących do listy zezwalania lub odmowy.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenariusz 5. NaT ruchu wychodzącego dla wewnętrznego standardowego równoważenia obciążenia


> [!NOTE]
> **Translator adresów sieciowych usługi Azure Virtual Network** może zapewnić łączność wychodzącą dla maszyn wirtualnych przy użyciu wewnętrznego standardowego równoważenia obciążenia. Zobacz [Co to jest Translator adresów sieciowych usługi Azure Virtual Network? aby](../virtual-network/nat-overview.md) uzyskać więcej informacji.

#### <a name="details"></a>Szczegóły


Łączność wychodząca nie jest dostępna dla wewnętrznego standardowego równoważenia obciążenia, dopóki nie zostanie jawnie zadeklarowana za pośrednictwem publicznych ip lub Translator adresów sieciowych usługi Virtual Network na poziomie wystąpienia albo przez skojarzenie elementów członkowskich puli zaplecza z konfiguracją usługi równoważenia obciążenia tylko dla ruchu wychodzącego. 


Aby uzyskać więcej informacji, zobacz Konfiguracja usługi równoważenia obciążenia [tylko dla ruchu wychodzącego.](./egress-only.md)




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenariusz 6. Włączanie obu protokołów TCP & UDP dla wychodzącego nat z publicznym standardowym równoważeniem obciążenia


#### <a name="details"></a>Szczegóły


W przypadku korzystania z publicznego standardowego równoważenia obciążenia zapewniany automatyczny wychodzący nat odpowiada protokołowi transportu reguły równoważenia obciążenia. 


1. Wyłącz [wychodzącą funkcję SNAT](load-balancer-outbound-connections.md) dla reguły równoważenia obciążenia. 
2. Skonfiguruj regułę ruchu wychodzącego dla tego samego usługi równoważenia obciążenia.
3. Ponownie użyj puli zaplecza używanej już przez maszyny wirtualne. 
4. Określ wartość "protocol": "All" jako część reguły ruchu wychodzącego. 


Jeśli są używane tylko reguły NAT dla ruchu przychodzącego, nie jest dostarczany żaden wychodzący nat. 


1. Umieść maszyny wirtualne w puli zaplecza.
2. Definiowanie co najmniej jednej konfiguracji adresu IP frontonia z publicznymi adresami IP lub prefiksem publicznego adresu IP 
3. Skonfiguruj regułę ruchu wychodzącego dla tego samego usługi równoważenia obciążenia. 
4. Określ wartość "protocol": "All" jako część reguły ruchu wychodzącego


## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba portów efemeralnych do wykorzystania na adres IP frontonia wynosi 64 000.
- Zakres konfigurowalnego limitu czasu bezczynności dla ruchu wychodzącego wynosi od 4 do 120 minut (od 240 do 7200 sekund).
- W przypadku ruchu wychodzącego NAT równoważenie obciążenia nie obsługuje ruchu ICMP.
- Reguły ruchu wychodzącego można stosować tylko do podstawowej konfiguracji adresu IP karty sieciowej.  Nie można utworzyć reguły ruchu wychodzącego dla pomocniczego adresu IP maszyny wirtualnej lub urządzenia WUS. Obsługiwanych jest wiele karty sieciowe.
- Aby zapewnić łączność **wychodzącą,** wszystkie maszyny wirtualne w zestawie dostępności muszą zostać dodane do puli zaplecza. 
- Aby zapewnić łączność wychodzącą, wszystkie maszyny wirtualne w zestawie skalowania maszyn wirtualnych muszą zostać dodane do puli zaplecza. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md)
- Zobacz często [zadawane pytania dotyczące Azure Load Balancer](load-balancer-faqs.md)
