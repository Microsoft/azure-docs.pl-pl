---
title: Omówienie portów wysokiej dostępności na platformie Azure
titleSuffix: Azure Load Balancer
description: Więcej informacji na temat równoważenia obciążenia portów o wysokiej dostępności dla wewnętrznego modułu równoważenia obciążenia.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 6f089af71e4d32023e9cebd6613872f7db0eed7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94694963"
---
# <a name="high-availability-ports-overview"></a>Omówienie portów wysokiej dostępności

Usługa Azure usługa Load Balancer w warstwie Standardowa pomaga zrównoważyć obciążenie **wszystkich** przepływów protokołu na **wszystkich** portach jednocześnie, gdy korzystasz z wewnętrznego Load Balancer za pośrednictwem portów ha.

Porty wysokiej dostępności (HA) to typ reguły równoważenia obciążenia, która zapewnia łatwy sposób równoważenia obciążenia **wszystkich** przepływów, które docierają do **wszystkich** portów wewnętrznej usługa Load Balancer w warstwie Standardowa. Podejmowana jest decyzja dotycząca równoważenia obciążenia dla przepływu. Ta akcja jest oparta na następującym połączeniu z pięcioma kolekcjami: źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i protokół

Reguły równoważenia obciążenia portów HA ułatwiają scenariusze krytyczne, takie jak wysoka dostępność i skalowanie dla wirtualnych urządzeń sieciowych (urządzeń WUS) w sieciach wirtualnych. Funkcja może również pomóc w sytuacji, gdy duża liczba portów musi być zrównoważona obciążenia. 

Reguły równoważenia obciążenia portów HA są konfigurowane podczas ustawiania portów frontonu i zaplecza na **0** i protokołu do **wszystkich**. Następnie zasób wewnętrznego modułu równoważenia obciążenia równoważy wszystkie przepływy TCP i UDP niezależnie od numeru portu

## <a name="why-use-ha-ports"></a>Dlaczego warto używać portów HA?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Wirtualne urządzenia sieciowe

Możesz użyć urządzeń WUS, aby pomóc w zabezpieczeniu obciążeń platformy Azure przed wieloma rodzajami zagrożeń bezpieczeństwa. Gdy używasz urządzeń WUS w tych scenariuszach, muszą one być niezawodne i wysoce dostępne i muszą skalować na żądanie.

Można osiągnąć te cele po prostu dodając wystąpienia urządzenie WUS do puli zaplecza wewnętrznego modułu równoważenia obciążenia i konfigurując regułę modułu równoważenia obciążenia dla portów HA.

W przypadku scenariuszy z urządzenie WUS HA, porty HA oferują następujące korzyści:
- Zapewnij szybką pracę w trybie failover w przypadku prawidłowych wystąpień z sondami kondycji dla poszczególnych wystąpień
- Zapewnij wyższą wydajność dzięki skalowaniu w poziomie do *n*-aktywnych wystąpień
- Udostępnianie scenariuszy *n*-aktywnych i aktywnych-pasywnych
- Eliminowanie potrzeb złożonych rozwiązań, takich jak węzły Apache ZooKeeper dla urządzeń monitorujących

Na poniższym diagramie przedstawiono wdrożenie sieci wirtualnej typu Hub i gwiazdy. Szprychy wymuszają tunelowanie ruchu do sieci wirtualnej centrum i przez urządzenie WUS przed opuszczeniem zaufanego obszaru. Urządzeń WUS znajdują się za wewnętrzną usługa Load Balancer w warstwie Standardowa z konfiguracją portów HA. Cały ruch można przetworzyć i przesłać odpowiednio dalej. Gdy program zostanie skonfigurowany jako widoczny na poniższym diagramie, reguła równoważenia obciążenia portów HA również zapewnia symetrię przepływu dla ruchu przychodzącego i wychodzącego.

<a node="diagram"></a>
![Diagram sieci wirtualnej typu Hub i szprych z urządzeń WUS wdrożonym w trybie HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Jeśli używasz urządzeń WUS, potwierdź swoim dostawcom, jak najlepiej korzystać z portów HA i dowiedzieć się, które scenariusze są obsługiwane.

### <a name="load-balancing-large-numbers-of-ports"></a>Równoważenie obciążenia w dużej liczbie portów

W przypadku aplikacji wymagających równoważenia obciążenia dużej liczby portów można także użyć portów HA. Można uprościć te scenariusze przy użyciu [Usługa Load Balancer w warstwie Standardowa](./load-balancer-overview.md) wewnętrznej z portami ha. Pojedyncza reguła równoważenia obciążenia zastępuje wiele pojedynczych zasad równoważenia obciążenia, po jednej dla każdego portu.

## <a name="region-availability"></a>Dostępność w danym regionie

Funkcja portów HA jest dostępna we wszystkich globalnych regionach platformy Azure.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Jeden, niezmienny adres IP (niebezpośrednie zwrócenie serwera) konfiguracja portów HA na wewnętrznej usługa Load Balancer w warstwie Standardowa

Ta konfiguracja jest podstawową konfiguracją portów HA. Regułę równoważenia obciążenia portów HA można skonfigurować na pojedynczym adresie IP frontonu, wykonując następujące czynności:
1. Podczas konfigurowania usługa Load Balancer w warstwie Standardowa zaznacz pole wyboru **porty ha** w konfiguracji reguły Load Balancer.
2. W przypadku **zmiennoprzecinkowego adresu IP** wybierz pozycję **wyłączone**.

Ta konfiguracja nie zezwala na żadną inną konfigurację reguły równoważenia obciążenia w zasobie bieżącego modułu równoważenia obciążenia. Nie umożliwia także żadnej innej konfiguracji zasobów wewnętrznego modułu równoważenia obciążenia dla danego zestawu wystąpień zaplecza.

Można jednak skonfigurować usługa Load Balancer w warstwie Standardowa publiczną dla wystąpień zaplecza, a także tę regułę portów HA.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Pojedynczy, zmienny adres IP (bezpośredni zwrot serwera) konfiguracja portów HA na wewnętrznej usługa Load Balancer w warstwie Standardowa

Można podobnie skonfigurować moduł równoważenia obciążenia tak, aby korzystał z reguły zrównoważenia obciążenia z **portem ha** z jednym frontonem, ustawiając wartość opcji **zmiennoprzecinkowy adres IP** na **włączone**. 

Korzystając z tej konfiguracji, można dodać bardziej przepływające reguły równoważenia obciążenia IP i/lub publiczny moduł równoważenia obciążenia. Nie można jednak użyć niepływającego adresu IP, konfiguracja równoważenia obciążenia portów o wysokiej dostępności na tej konfiguracji.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Wiele konfiguracji portów HA na usługa Load Balancer w warstwie Standardowa wewnętrznej

Jeśli Twój scenariusz wymaga skonfigurowania więcej niż jednego frontonu portu HA dla tej samej puli zaplecza, można wykonać następujące czynności: 
- Skonfiguruj więcej niż jeden prywatny adres IP frontonu dla jednego wewnętrznego zasobu usługa Load Balancer w warstwie Standardowa.
- Skonfiguruj wiele zasad równoważenia obciążenia, w przypadku których każda reguła ma wybrany unikatowy adres IP frontonu.
- Wybierz opcję **porty ha** , a następnie ustaw **zmiennoprzecinkowy adres IP** na **włączony** dla wszystkich reguł równoważenia obciążenia.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Wewnętrzny moduł równoważenia obciążenia z portami HA i publicznym modułem równoważenia obciążenia w tym samym wystąpieniu zaplecza

Istnieje możliwość skonfigurowania *jednego* publicznego zasobu Usługa Load Balancer w warstwie Standardowa dla zasobów zaplecza oraz jednego wewnętrznego usługa Load Balancer w warstwie Standardowa z portami ha.

## <a name="limitations"></a>Ograniczenia

- Reguły równoważenia obciążenia portów HA są dostępne tylko dla wewnętrznych usługa Load Balancer w warstwie Standardowa.
- Łączenie reguły równoważenia obciążenia portów HA i zasad równoważenia obciążenia z portami niewymaganymi **nie** jest obsługiwane w jednej konfiguracji adresu IP frontonu, chyba że oba mają swobodny adres IP włączony.
- Istniejące fragmenty adresów IP będą przekazywane przez reguły równoważenia obciążenia portów HA do tego samego miejsca docelowego, co pierwszy pakiet.  Fragmentacja IP pakietu UDP lub TCP nie jest obsługiwana.
- Mechanizm symetrii przepływów (głównie dla scenariuszy urządzenie WUS) jest obsługiwany z wystąpieniem zaplecza i jedną kartą sieciową (i konfiguracją pojedynczego adresu IP) tylko wtedy, gdy jest używana jak pokazano na powyższym diagramie i przy użyciu zasad równoważenia obciążenia portów HA. Nie jest ona dostępna w żadnym innym scenariuszu. Oznacza to, że co najmniej dwa zasoby Load Balancer i ich odpowiednie zasady podejmują niezależne decyzje i nigdy nie są koordynowane. Zapoznaj się z opisem i diagramem [wirtualnych urządzeń sieciowych](#nva). W przypadku korzystania z wielu kart sieciowych lub urządzenie WUSi między publiczną i wewnętrzną Load Balancer, symetria przepływu jest niedostępna.  Możliwe jest obejście tego problemu ze źródłem NAT'ing przepływ transferu danych przychodzących do adresu IP urządzenia, aby umożliwić odpowiedzi na te same urządzenie WUS.  Jednak zdecydowanie zalecamy korzystanie z jednej karty sieciowej i używanie architektury referencyjnej pokazanej na powyższym diagramie.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md)
