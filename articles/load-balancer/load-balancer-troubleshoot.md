---
title: Rozwiązywanie typowych problemów Azure Load Balancer
description: Dowiedz się, jak rozwiązywać typowe problemy z Azure Load Balancer.
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
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028815"
---
# <a name="troubleshoot-azure-load-balancer"></a>Rozwiązywanie problemów z usługą Azure Load Balancer

Ta strona zawiera informacje dotyczące rozwiązywania problemów dotyczących podstawowych i standardowych typowych pytań Azure Load Balancer. Aby uzyskać więcej informacji na temat usługa Load Balancer w warstwie Standardowa, zobacz [Usługa Load Balancer w warstwie Standardowa Omówienie](load-balancer-standard-diagnostics.md).

Gdy Load Balancer łączność jest niedostępna, Najczęstsze objawy są następujące:

- Maszyny wirtualne znajdujące się za Load Balancer nie odpowiadają na sondy kondycji 
- Maszyny wirtualne znajdujące się za Load Balancer nie odpowiadają na ruch na skonfigurowanym porcie

Gdy klienci zewnętrzni do maszyn wirtualnych zaplecza przechodzą przez moduł równoważenia obciążenia, adres IP klientów będzie używany do komunikacji. Upewnij się, że adres IP klientów został dodany do listy dozwolonych sieciowej grupy zabezpieczeń.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Brak łączności wychodzącej ze standardowych wewnętrznych modułów równoważenia obciążenia (ILB)

**Sprawdzanie poprawności i rozwiązywanie problemów**

Standardowe ILB są **domyślnie zabezpieczone**. Podstawowy ILB zezwala na łączenie się z Internetem za pośrednictwem *ukrytego* publicznego adresu IP. Nie jest to zalecane w przypadku obciążeń produkcyjnych, ponieważ adres IP nie jest ani statyczny, ani zablokowany przez sieciowych grup zabezpieczeń. Jeśli ostatnio przeniesiono z podstawowej ILB do standardowej ILB, należy utworzyć publiczny adres IP jawnie za pośrednictwem konfiguracji [tylko wychodzące](egress-only.md) , która blokuje adres IP za pośrednictwem sieciowych grup zabezpieczeń. Można również użyć [bramy translatora adresów sieciowych](../virtual-network/nat-overview.md) w podsieci.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Nie można zmienić portu zaplecza dla istniejącej reguły LB modułu równoważenia obciążenia z zestawem skalowania maszyn wirtualnych wdrożonym w puli zaplecza.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Przyczyna: nie można zmodyfikować portu zaplecza dla reguły równoważenia obciążenia używanej przez sondę kondycji dla modułu równoważenia obciążenia, do którego odwołuje się zestaw skalowania maszyn wirtualnych

**Rozwiązanie** Aby zmienić port, można usunąć sondę kondycji, aktualizując zestaw skalowania maszyn wirtualnych, zaktualizować port, a następnie ponownie skonfigurować sondę kondycji.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Po usunięciu maszyn wirtualnych z puli zaplecza modułu równoważenia obciążenia w dalszym ciągu nadal odbywa się usługa równoważenia obciążenia.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Przyczyna: maszyny wirtualne usunięte z puli zaplecza nie powinny już odbierać ruchu. Niewielka ilość ruchu sieciowego może być związana z magazynem, systemem DNS i innymi funkcjami w ramach platformy Azure.

Aby sprawdzić, można przeprowadzić śledzenie sieci. Nazwa FQDN używana dla kont usługi BLOB Storage jest wyświetlana we właściwościach poszczególnych kont magazynu.  Z poziomu maszyny wirtualnej w ramach subskrypcji platformy Azure możesz wykonać polecenie nslookup, aby określić adres IP platformy Azure przypisany do tego konta magazynu.

## <a name="additional-network-captures"></a>Dodatkowe przechwycenia sieci

Jeśli zdecydujesz się otworzyć zgłoszenie do pomocy technicznej, Zbierz poniższe informacje w celu szybszego rozwiązania problemu. Wybierz pojedynczą maszynę wirtualną zaplecza, aby wykonać następujące testy:

- Aby przetestować odpowiedź na port sondy (przykład: PS ping 10.0.0.4:3389), użyj polecenia programu PS w programie SMS z jednej z maszyn wirtualnych zaplecza, a następnie Zapisz wyniki. 
- Jeśli nie otrzymasz odpowiedzi w tych testach ping, uruchom jednoczesne śledzenie netsh na maszynie wirtualnej zaplecza i testowej maszynie wirtualnej podczas uruchamiania PsPing, a następnie Zatrzymaj śledzenie netsh.

## <a name="load-balancer-in-failed-state"></a>Load Balancer w stanie niepowodzenia

**Rozwiązanie**

- Po zidentyfikowaniu zasobu, który jest w stanie niepowodzenia, przejdź do [Azure Resource Explorer](https://resources.azure.com/) i zidentyfikuj zasób w tym stanie.
- Zaktualizuj przełącznik w prawym górnym rogu, aby wykonać odczyt/zapis.
- Kliknij pozycję Edytuj dla zasobu w stanie Niepowodzenie.
- Kliknij przycisk "wykonane", a następnie pozycję Pobierz, aby upewnić się, że stan aprowizacji został zaktualizowany pomyślnie.
- Następnie można kontynuować inne akcje, ponieważ stan zasobu jest niepowodzenie.

## <a name="next-steps"></a>Następne kroki

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
