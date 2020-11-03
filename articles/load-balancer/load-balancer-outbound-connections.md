---
title: Azure Load Balancer wychodzącego serwera proxy
description: Opisuje, jak Azure Load Balancer jest używany jako serwer proxy dla wychodzącej łączności z Internetem
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 185bb47677e978a3098f39024995da6399f90658
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241773"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Azure Load Balancer wychodzącego serwera proxy

Moduł równoważenia obciążenia platformy Azure może służyć jako serwer proxy dla wychodzącej łączności z Internetem. Moduł równoważenia obciążenia zapewnia łączność wychodzącą dla wystąpień zaplecza. 

Ta konfiguracja używa **translatora adresów sieciowych (Resources)** . Podzbiór danych ponownie zapisuje adres IP zaplecza na publicznym adresie IP modułu równoważenia obciążenia. 

Przydziały adresów IP włączają **podszywającanie** się w ramach wystąpienia zaplecza. Takie zamaskowane uniemożliwia ze źródeł zewnętrznych adresowanie do wystąpień zaplecza. 

Udostępnianie adresu IP między wystąpieniami zaplecza zmniejsza koszt statycznych publicznych adresów IP i obsługuje scenariusze, takie jak uproszczenie listy dozwolonych adresów IP z ruchem ze znanych publicznych adresów IP. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Udostępnianie portów między zasobami

Jeśli zasoby zaplecza modułu równoważenia obciążenia nie mają publicznych adresów IP na poziomie wystąpienia (ILPIP), ustanawiają łączność wychodzącą za pośrednictwem adresu IP frontonu publicznego modułu równoważenia obciążenia.

Porty są używane do generowania unikatowych identyfikatorów używanych do obsługi różnych przepływów. Internet używa pięciu krotek w celu zapewnienia tego rozróżnienia.

Pięć krotek składa się z:

* Docelowy adres IP
* Port docelowy
* Źródłowy adres IP
* Port źródłowy i protokół w celu zapewnienia tego rozróżnienia.

Jeśli port jest używany do połączeń przychodzących, będzie miał **odbiornik** dla przychodzących żądań połączeń na tym porcie i nie będzie można go używać dla połączeń wychodzących. 

Aby nawiązać połączenie wychodzące, należy użyć **portu tymczasowych** w celu zapewnienia lokalizacji docelowej z portem, na którym należy się komunikować i obsługiwać różne przepływy ruchu. 

Każdy adres IP ma 65 535 portów. Pierwsze porty 1024 są zarezerwowane jako **porty systemowe** . Każdy port może być używany dla połączeń przychodzących lub wychodzących dla protokołów TCP i UDP. 

Z pozostałych portów platforma Azure przyznaje 64 000 do użycia jako tymczasowe **porty** . Po dodaniu adresu IP jako konfiguracji adresu IP frontonu można użyć tych portów tymczasowych dla tego elementu.

Za pomocą reguł ruchu wychodzącego te porty mogą być dystrybuowane do wystąpień zaplecza, aby umożliwić im Udostępnianie publicznych adresów IP modułu równoważenia obciążenia dla połączeń wychodzących.

Sieci na hoście dla każdego wystąpienia zaplecza będą podłączane do pakietów, które są częścią połączenia wychodzącego. Host ponownie zapisuje źródłowy adres IP na jednym z publicznych adresów IP. Host ponownie zapisuje port źródłowy każdego pakietu wychodzącego do jednego z portów źródłowego źródła danych.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Porty wydechowe

Każde połączenie z tym samym docelowym adresem IP i portem docelowym będzie używać portu. To połączenie utrzymuje odrębny **przepływ ruchu** z wystąpienia zaplecza lub **klienta** do **serwera** . Ten proces nadaje serwerowi unikatowy port, na którym ma być adresowany ruch. Bez tego procesu komputer kliencki nie rozpoznaje tego, z którego przepływu jest częścią pakietu.

Wyobraź sobie, że ma wiele przeglądarek https://www.microsoft.com :

* Docelowy adres IP = 23.53.254.142
* Port docelowy = 443
* Protokół = TCP

Bez innych portów docelowych dla ruchu zwrotnego (port wiązania używanego do ustanowienia połączenia) klient nie będzie miał możliwości oddzielenia jednego wyniku zapytania od innego.

Połączenia wychodzące mogą być przełączane. Wystąpienie zaplecza może być przydzieloną za mało portów. Bez włączenia **ponownego użycia połączenia** jest zwiększane ryzyko **wyczerpania portów** .

Jeśli nastąpi wyczerpanie portów, nowe połączenia wychodzące z docelowym adresem IP będą kończyć się niepowodzeniem. Połączenia będą kończyły się powodzeniem, gdy port będzie dostępny. Ta wyczerpanie występuje, gdy porty 64 000 z adresu IP są rozkładane w postaci cienkiej w wielu wystąpieniach zaplecza. Aby uzyskać wskazówki dotyczące [rozwiązywania problemów](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)z wyczerpaniem portów z poziomu adresów sieciowych  

W przypadku połączeń TCP moduł równoważenia obciążenia będzie używać jednego portu dla każdego docelowego adresu IP i portu. Ta Multiuse umożliwia wiele połączeń z tym samym docelowym adresem IP z tym samym portem. Ta Multiuse jest ograniczona, jeśli połączenie nie ma różnych portów docelowych.

W przypadku połączeń UDP moduł równoważenia obciążenia używa algorytmu **NAT z ograniczeniami portów** , który wykorzystuje jeden port dla docelowego adresu IP, niezależnie od portu docelowego. 

Port jest ponownie używany dla nieograniczonej liczby połączeń. Port jest używany ponownie tylko wtedy, gdy docelowy adres IP lub port jest inny.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Alokacja portu

Każdy publiczny adres IP przypisany jako adres IP frontonu modułu równoważenia obciążenia ma 64 000 portów przystawcy adresów dla swoich członków puli zaplecza. Porty nie mogą być współużytkowane z członkami puli zaplecza. Zakres portów źródłowego przesyłania adresów sieciowych może być używany tylko przez pojedyncze wystąpienie wewnętrznej bazy danych, aby upewnić się, że pakiety powrotne są poprawnie kierowane. 

Zaleca się użycie jawnej reguły ruchu wychodzącego w celu skonfigurowania alokacji portów dla translatora adresów sieciowych. Ta zasada pozwoli zmaksymalizować liczbę portów dla wszystkich wystąpień zaplecza dostępnych dla połączeń wychodzących. 

W przypadku korzystania z automatycznej alokacji przychodzącego obiektu odruchowego za pomocą reguły równoważenia obciążenia tabela alokacji określi przydział portów.

W poniższej <a name="snatporttable"></a> tabeli przedstawiono alokacje wstępne portów adresów sieciowych dla warstw rozmiaru puli zaplecza:

| Rozmiar puli (wystąpienia maszyn wirtualnych) | Wstępnie przydzielony porty dla konfiguracji protokołu IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

>[!NOTE]
> Jeśli masz pulę zaplecza o maksymalnym rozmiarze 10, każde wystąpienie może mieć 64000/10 = 6 400 portów, jeśli zdefiniujesz jawną regułę wychodzącą. Zgodnie z powyższą tabelą każda z nich będzie miała 1 024 tylko w przypadku wybrania opcji alokacja automatyczna.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Reguły ruchu wychodzącego i translatora adresów sieciowych Virtual Network

Azure Load Balancer reguły ruchu wychodzącego i Virtual Network translatora adresów sieciowych są opcjami dostępnymi w sieci wirtualnej.

Aby uzyskać więcej informacji o regułach ruchu wychodzącego, zobacz [reguły ruchu wychodzącego](outbound-rules.md).

Aby uzyskać więcej informacji na temat usługi Azure Virtual Network translator adresów sieciowych, zobacz [co to jest usługa azure Virtual Network translator adresów sieciowych](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Ograniczenia

*   Porty zostaną wydane po 15 sekundach, jeśli zostanie odebrana lub wysłana wartość **Właściwości RST**
*   Porty zostaną wydane po 240 sekundach, jeśli **FINACK** zostanie odebrana lub wysłana
*   Gdy połączenie jest bezczynne, a żadne nowe pakiety nie są wysyłane, porty zostaną wydane po 4 – 120 minutach.
  * Ten próg można skonfigurować za pośrednictwem reguł ruchu wychodzącego.
*   Każdy adres IP zapewnia 64 000 portów, które mogą być używane na potrzeby protokołu reportowego.
*   Każdy port może być używany zarówno dla połączeń TCP, jak i UDP z docelowym adresem IP
  * Port przychodzący protokołu UDP jest wymagany, czy port docelowy jest unikatowy. Dla każdego połączenia UDP z docelowym adresem IP jest używany jeden port protokołu IPSec.
  * W przypadku wielu połączeń z tym samym docelowym adresem IP można użyć portu protokołu IPSec.
*   Wyczerpanie elementu podrzędnego jest wykonywane, gdy wystąpienie wewnętrznej bazy danych jest uruchomione z danego portu. Moduł równoważenia obciążenia nadal może mieć nieużywane porty. Jeśli porty przystosowane do wystąpienia zaplecza przekroczą określone porty, nie będzie można nawiązać nowych połączeń wychodzących.

## <a name="next-steps"></a>Następne kroki

*   [Rozwiązywanie problemów z błędami połączenia wychodzącego z powodu wyczerpania elementu wiązania](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Przejrzyj metryki dotyczące translatora adresów sieciowych](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) i zapoznaj się z poprawnym sposobem filtrowania, dzielenia i wyświetlania.

