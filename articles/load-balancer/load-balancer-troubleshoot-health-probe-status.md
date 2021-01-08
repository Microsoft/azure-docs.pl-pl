---
title: Rozwiązywanie problemów ze stanem sondy kondycji Azure Load Balancer
description: Dowiedz się, jak rozwiązywać znane problemy ze stanem sondowania kondycji Azure Load Balancer.
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
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029212"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Rozwiązywanie problemów ze stanem sondy kondycji Azure Load Balancer

Ta strona zawiera informacje dotyczące rozwiązywania problemów typowych Azure Load Balancer pytań dotyczących sondy kondycji.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Objaw: maszyny wirtualne znajdujące się za Load Balancer nie reagują na sondy kondycji
W przypadku serwerów zaplecza, które mają być uwzględnione w zestawie modułu równoważenia obciążenia, muszą one przekazywać sprawdzanie sondy. Aby uzyskać więcej informacji o sondach kondycji, zobacz [Opis sond Load Balancer](load-balancer-custom-probe-overview.md). 

Maszyny wirtualne puli zaplecza Load Balancer mogą nie odpowiadać na sondy z jednego z następujących powodów: 
- Maszyna wirtualna puli zaplecza Load Balancer jest w złej kondycji 
- Maszyna wirtualna puli zaplecza Load Balancer nie nasłuchuje na porcie sondy 
- Zapora lub sieciowa Grupa zabezpieczeń blokuje port na maszynach wirtualnych puli zaplecza Load Balancer 
- Inne niezgodne konfiguracje w Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Przyczyna 1: Load Balancer maszyny wirtualnej puli zaplecza są w złej kondycji

**Sprawdzanie poprawności i rozwiązywanie problemów**

Aby rozwiązać ten problem, zaloguj się do uczestniczących maszyn wirtualnych i sprawdź, czy stan maszyny wirtualnej jest w dobrej kondycji i czy można odpowiedzieć na **PsPing** lub **TCPing** z innej maszyny wirtualnej w puli. Jeśli maszyna wirtualna jest w złej kondycji lub nie może odpowiedzieć na sondę, należy rozwiązać problem i odzyskać maszynę wirtualną do stanu dobrej kondycji, zanim będzie ona mogła uczestniczyć w równoważeniu obciążenia.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Przyczyna 2: Load Balancer maszyna wirtualna puli zaplecza nie nasłuchuje na porcie sondy
Jeśli maszyna wirtualna jest w dobrej kondycji, ale nie odpowiada na sondę, może to oznaczać, że port sondy nie jest otwarty na uczestniczącej maszynie wirtualnej lub maszyna wirtualna nie nasłuchuje na tym porcie.

**Sprawdzanie poprawności i rozwiązywanie problemów**

1. Zaloguj się do maszyny wirtualnej zaplecza.
2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby sprawdzić, czy aplikacja nasłuchuje na porcie sondy: netstat-an
3. Jeśli stan portu nie jest wyświetlany na liście jako **nasłuchiwanie**, skonfiguruj właściwy port. 
4. Alternatywnie możesz wybrać inny port, który jest wyświetlany jako **nasłuchiwanie** i odpowiednio zaktualizować konfigurację modułu równoważenia obciążenia.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Przyczyna 3: Zapora lub sieciowa Grupa zabezpieczeń blokująca port na maszynach wirtualnych puli zaplecza modułu równoważenia obciążenia
Jeśli Zapora na maszynie wirtualnej blokuje port sondy lub co najmniej jedna sieciowa Grupa zabezpieczeń skonfigurowana w podsieci lub na maszynie wirtualnej, nie zezwala na sondowanie do połączenia z portem, maszyna wirtualna nie może odpowiedzieć na sondę kondycji.

**Sprawdzanie poprawności i rozwiązywanie problemów**

1. Jeśli Zapora jest włączona, sprawdź, czy jest ona skonfigurowana do zezwalania na port sondy. W przeciwnym razie Skonfiguruj zaporę tak, aby zezwalała na ruch na porcie sondowania, i ponownie przetestuj.
2. Z listy grup zabezpieczeń sieci Sprawdź, czy ruch przychodzący lub wychodzący na porcie sondy ma zakłócenia.
3. Sprawdź również, czy reguła **Odmów wszystkich** grup zabezpieczeń sieci na karcie sieciowej maszyny wirtualnej lub podsieci o wyższym priorytecie niż reguła domyślna zezwalająca na sondy lb & ruchem (sieciowe grupy zabezpieczeń muszą zezwalać na Load Balancer IP 168.63.129.16).
4. Jeśli dowolna z tych reguł blokuje ruch sondy, Usuń i ponownie skonfiguruj reguły, aby zezwolić na ruch sondy.  
5. Sprawdź, czy maszyna wirtualna rozpoczęła teraz reagowanie na sondy kondycji.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Przyczyna 4: inne błędy konfiguracji w Load Balancer
Jeśli wszystkie poprzednie przyczyny są sprawdzane poprawnie i są prawidłowo rozwiązywane, a maszyna wirtualna zaplecza nadal nie odpowiada na sondę kondycji, należy ręcznie przetestować łączność i zebrać niektóre ślady w celu zrozumienia łączności.

**Sprawdzanie poprawności i rozwiązywanie problemów**

1. Użyj **Psping** z jednej z pozostałych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: .\psping.exe-t 10.0.0.4:3389) i zapisać wyniki. 
2. Użyj **TCPing** z jednej z pozostałych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: .\tcping.exe 10.0.0.4 3389) i zapisać wyniki. 
3. Jeśli w tych testach ping nie otrzymano odpowiedzi,
    - Uruchom jednoczesne śledzenie netsh na maszynie wirtualnej docelowej puli zaplecza i innej testowej maszynie wirtualnej z tej samej sieci wirtualnej. Teraz uruchom test PsPing przez jakiś czas, Zbierz dane śledzenia sieci, a następnie Zatrzymaj test. 
    - Przeanalizuj przechwytywanie sieci i sprawdź, czy istnieją pakiety przychodzące i wychodzące powiązane z kwerendą ping. 
        - Jeśli na maszynie wirtualnej puli zaplecza nie zaobserwowano żadnych pakietów przychodzących, istnieje potencjalnie Grupa zabezpieczeń sieci lub UDR nieprawidłowo skonfigurowany ruch. 
        - Jeśli na maszynie wirtualnej puli zaplecza nie zaobserwowano żadnych pakietów wychodzących, maszyna wirtualna musi być sprawdzona pod kątem wszelkich niepowiązanych problemów (na przykład aplikacja blokująca port sondy). 
    - Sprawdź, czy pakiety sondy są wymuszane w innym miejscu docelowym (prawdopodobnie za pośrednictwem ustawień UDR) przed osiągnięciem modułu równoważenia obciążenia. Może to spowodować, że ruch nigdy nie dociera do maszyny wirtualnej zaplecza. 
4. Zmień typ sondy (na przykład HTTP na TCP) i skonfiguruj odpowiedni port na listach ACL sieciowych grup zabezpieczeń i zaporze, aby sprawdzić, czy problem dotyczy konfiguracji odpowiedzi sondy. Aby uzyskać więcej informacji na temat konfiguracji sondowania kondycji, zobacz [Konfiguracja sondy kondycji równoważenia obciążenia punktu końcowego](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Następne kroki

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).