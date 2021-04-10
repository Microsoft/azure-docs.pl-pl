---
title: Rozwiązywanie problemów z usługą Azure Load Balancer
description: Dowiedz się, jak rozwiązywać znane problemy z Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029202"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Rozwiązywanie problemów Azure Load Balancer odpowiedzi na ruch zaplecza

Ta strona zawiera informacje dotyczące rozwiązywania problemów dotyczących Azure Load Balancer.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Maszyny wirtualne znajdujące się za Load Balancer nie odpowiadają na ruch na skonfigurowanym porcie danych

Jeśli maszyna wirtualna puli zaplecza jest wyświetlana jako dobra kondycja i reaguje na sondy kondycji, ale nadal nie uczestniczy w równoważeniu obciążenia lub nie odpowiada na ruch danych, może to być spowodowane jedną z następujących przyczyn:
* Maszyna wirtualna puli zaplecza Load Balancer nie nasłuchuje na porcie danych
* Sieciowa Grupa zabezpieczeń blokuje port na maszynie wirtualnej puli zaplecza Load Balancer  
* Uzyskiwanie dostępu do Load Balancer z tej samej maszyny wirtualnej i karty sieciowej
* Uzyskiwanie dostępu do Internetu Load Balancer frontonu z poziomu maszyny wirtualnej puli zaplecza Load Balancer

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Przyczyna 1: Load Balancer maszyna wirtualna puli zaplecza nie nasłuchuje na porcie danych

Jeśli maszyna wirtualna nie odpowiada na ruch danych, może to być spowodowane tym, że port docelowy nie jest otwarty na danej maszynie wirtualnej lub maszyna wirtualna nie nasłuchuje na tym porcie. 

**Sprawdzanie poprawności i rozwiązywanie problemów**

1. Zaloguj się do maszyny wirtualnej zaplecza. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby sprawdzić, czy aplikacja nasłuchuje na porcie danych:  
            netstat-an 
3. Jeśli port nie jest wyświetlany w stanie "nasłuchiwanie", skonfiguruj odpowiedni port odbiornika 
4. Jeśli port jest oznaczony jako nasłuch, Sprawdź aplikację docelową na tym porcie, aby uzyskać ewentualne problemy.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Przyczyna 2: sieciowa Grupa zabezpieczeń blokuje port na maszynie wirtualnej puli zaplecza Load Balancer  

Jeśli co najmniej jedna sieciowa Grupa zabezpieczeń skonfigurowana w podsieci lub na maszynie wirtualnej blokuje źródłowy adres IP lub port, maszyna wirtualna nie będzie mogła odpowiedzieć.

W przypadku publicznego modułu równoważenia obciążenia adres IP klientów internetowych będzie używany do komunikacji między klientami a maszynami wirtualnymi zaplecza modułu równoważenia obciążenia. Upewnij się, że adres IP klientów jest dozwolony w sieciowej grupie zabezpieczeń maszyny wirtualnej zaplecza.

1. Wyświetl listę grup zabezpieczeń sieci skonfigurowanych na maszynie wirtualnej zaplecza. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md)
1. Z listy grup zabezpieczeń sieci Sprawdź, czy:
    - ruch przychodzący lub wychodzący na porcie danych ma zakłócenia. 
    - reguła **odmowa wszystkich** sieci sieciowej grupy zabezpieczeń na karcie sieciowej maszyny wirtualnej lub podsieci o wyższym priorytecie, która zezwala na Load Balancer sondy i ruch (sieciowe grupy zabezpieczeń muszą zezwalać na Load Balancer IP 168.63.129.16, który jest port sondy)
1. Jeśli którakolwiek z reguł blokuje ruch, Usuń i skonfiguruj ponownie te reguły, aby zezwolić na ruch danych.  
1. Sprawdź, czy maszyna wirtualna została teraz uruchomiona, aby odpowiedzieć na sondy kondycji.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Przyczyna 3: uzyskiwanie dostępu do Load Balancer z tej samej maszyny wirtualnej i interfejsu sieciowego 

Jeśli aplikacja hostowana na maszynie wirtualnej zaplecza Load Balancer próbuje uzyskać dostęp do innej aplikacji hostowanej w ramach tej samej maszyny wirtualnej zaplecza za pośrednictwem tego samego interfejsu sieciowego, jest to nieobsługiwany scenariusz i zakończy się niepowodzeniem. 

**Rozwiązanie** Ten problem można rozwiązać, korzystając z jednej z następujących metod:
* Skonfiguruj oddzielne maszyny wirtualne puli zaplecza dla każdej aplikacji. 
* Skonfiguruj aplikację na maszynach wirtualnych z dwoma KARTAmi sieciowymi, aby każda aplikacja była używana przez własny interfejs sieciowy i adres IP. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Przyczyna 4: uzyskiwanie dostępu do wewnętrznego frontonu Load Balancer z poziomu maszyny wirtualnej puli zaplecza Load Balancer

Jeśli wewnętrzny Load Balancer jest skonfigurowany wewnątrz sieci wirtualnej, a jedna z maszyn wirtualnych zaplecza uczestnika próbuje uzyskać dostęp do wewnętrznej frontonu Load Balancer, mogą wystąpić błędy, gdy przepływ jest mapowany na źródłową maszynę wirtualną. Ten scenariusz nie jest obsługiwany.

**Rozwiązanie** Istnieje kilka sposobów odblokowania tego scenariusza, w tym za pomocą serwera proxy. Oceń Application Gateway lub inne serwery proxy innych firm (na przykład Nginx lub haproxy). Aby uzyskać więcej informacji na temat Application Gateway, zobacz [omówienie Application Gateway](../application-gateway/overview.md)

**Szczegóły** Wewnętrzne moduły równoważenia obciążenia nie przekładają się na wychodzące połączenia przychodzące do frontonu wewnętrznego Load Balancer, ponieważ obie są w prywatnej przestrzeni adresów IP. Publiczne usługi równoważenia obciążenia zapewniają [połączenia wychodzące](load-balancer-outbound-connections.md) z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP. W przypadku wewnętrznych modułów równoważenia obciążenia to podejście pozwala uniknąć potencjalnego wyczerpania portów w ramach unikatowej wewnętrznej przestrzeni adresów IP, w której tłumaczenie nie jest wymagane.

Efekt uboczny polega na tym, że jeśli przepływ wychodzący z maszyny wirtualnej w puli zaplecza próbuje przepływ do frontonu Load Balancer wewnętrznej w swojej puli _i_ jest mapowany do samego siebie, dwa etapy przepływu nie są zgodne. Ponieważ nie są one zgodne, przepływ kończy się niepowodzeniem. Przepływ powiedzie się, jeśli przepływ nie został zmapowany na tę samą maszynę wirtualną w puli zaplecza, która utworzyła przepływ do frontonu.

Gdy przepływ zamapuje się z powrotem do samego siebie, przepływ wychodzący wydaje się pochodzić z maszyny wirtualnej do frontonu, a odpowiedni przepływ przychodzący wydaje się pochodzić z maszyny wirtualnej do samej siebie. Z punktu widzenia systemu operacyjnego gościa elementy przychodzące i wychodzące tego samego przepływu nie pasują do maszyny wirtualnej. Stos TCP nie rozpoznaje tych połówów tego samego przepływu, co jest częścią tego samego przepływu. Źródło i miejsce docelowe nie są zgodne. Gdy przepływ jest mapowany na dowolną inną maszynę wirtualną w puli zaplecza, połowy przepływu są zgodne, a maszyna wirtualna może odpowiedzieć na przepływ.

Objawem tego scenariusza jest sporadyczne przekroczenie limitu czasu połączenia, gdy przepływ powraca do tego samego zaplecza, który pochodzi z przepływu. Typowe obejścia obejmują Wstawianie warstwy proxy za wewnętrzną Load Balancer i używanie reguł stylu bezpośredniego powrotu serwera (DSR). Aby uzyskać więcej informacji, zobacz [wiele frontonów dla Azure Load Balancer](load-balancer-multivip-overview.md).

Wewnętrzny Load Balancer można połączyć z dowolnym serwerem proxy innej firmy lub użyć wewnętrznych [Application Gateway](../application-gateway/overview.md) dla scenariuszy serwera proxy przy użyciu protokołu HTTP/HTTPS. Mimo że można użyć Load Balancer publicznego, aby zmniejszyć ten problem, wynikający z nich scenariusz jest podatny na [wyczerpanie adresów](load-balancer-outbound-connections.md). Należy unikać tego drugiego podejścia, chyba że jest to starannie zarządzane.

## <a name="next-steps"></a>Następne kroki

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).