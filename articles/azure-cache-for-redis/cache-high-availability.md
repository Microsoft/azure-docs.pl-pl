---
title: Wysoka dostępność dla usługi Azure cache for Redis
description: Informacje o usłudze Azure cache for Redis — funkcje i opcje wysokiej dostępności
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu
ms.openlocfilehash: 145be11436eb4d0c4f6b892e5239ccacd838d780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654166"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Wysoka dostępność dla usługi Azure cache for Redis

Usługa Azure cache for Redis ma wbudowaną wysoką dostępność. Celem jego architektury wysokiej dostępności jest upewnienie się, że zarządzane wystąpienie Redis działa nawet wtedy, gdy jego bazowe maszyny wirtualne mają wpływ na planowane lub nieplanowane przestoje. Zapewnia ona znacznie wyższą stawkę procentową niż osiągane przez hosting Redis na jednej maszynie wirtualnej.

Usługa Azure cache for Redis implementuje wysoką dostępność przy użyciu wielu maszyn wirtualnych, nazywanych *węzłami*, dla pamięci podręcznej. Konfiguruje te węzły w taki sposób, że replikacja danych i tryb failover są wykonywane w sposób skoordynowany. Organizuje ona również operacje konserwacji, takie jak Redis. Dostępne są różne opcje wysokiej dostępności w warstwach Standardowa i Premium:

| Opcja | Opis | Dostępność | Standardowa (Standard) | Premium |
| ------------------- | ------- | ------- | :------: | :---: |
| [Standardowa replikacja](#standard-replication)| Konfiguracja replikacji z dwoma węzłami w jednym centrum danych lub strefie dostępności (AZ) z automatycznym trybem failover | 99,9% |✔|✔|
| [Wiele replik](#multiple-replicas) | Konfiguracja replikacji wielowęzłowej w co najmniej jednym AZs z automatycznym trybem failover | 99,95% (z nadmiarowością stref) |-|✔|
| [Nadmiarowość stref](#zone-redundancy) | Konfiguracja replikacji wielowęzłowej w systemie AZs z automatycznym trybem failover | 99,95% (z wieloma replikami) |-|✔|
| [Replikacja geograficzna](#geo-replication) | Wystąpienia połączonej pamięci podręcznej w dwóch regionach z trybem failover sterowanym przez użytkownika | 99,9% (dla jednego regionu) |-|✔|

## <a name="standard-replication"></a>Standardowa replikacja

Pamięć podręczna platformy Azure dla Redis w warstwie Standardowa lub Premium jest domyślnie uruchamiana na parze serwerów Redis. Te dwa serwery są hostowane na dedykowanych maszynach wirtualnych. Redis Open Source umożliwia obsługę żądań zapisu danych tylko jeden serwer. Ten serwer jest węzłem *podstawowym* , natomiast druga *replika*. Po wprowadzeniu w nim węzłów serwera usługa Azure cache for Redis przypisze do nich role podstawowe i repliki. Węzeł podstawowy zazwyczaj jest odpowiedzialny za obsługę zapisu oraz żądania odczytu z klientów Redis. W operacji zapisu zostaje zatwierdzona Nowa klucz i Aktualizacja klucza do pamięci wewnętrznej i natychmiast odpowiedzi na klienta. Przekazuje operację do repliki asynchronicznie.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Konfiguracja replikacji danych":::
   
>[!NOTE]
>Zwykle klient Redis komunikuje się z węzłem podstawowym w pamięci podręcznej Redis dla wszystkich żądań odczytu i zapisu. Niektórych klientów Redis można skonfigurować do odczytu z węzła repliki.
>
>

Jeśli węzeł podstawowy w pamięci podręcznej Redis jest niedostępny, replika zostanie podniesiona do automatycznej nowej głównej. Ten proces jest nazywany *trybem failover*. Replika będzie oczekiwać przez wystarczająco długi czas przed przejęciem w przypadku, gdy węzeł podstawowy odzyska szybkie odzyskiwanie. W przypadku przejścia w tryb failover usługa Azure cache for Redis Inicjuje nową maszynę wirtualną i łączy ją z pamięcią podręczną jako węzłem repliki. Replika wykonuje pełną synchronizację danych z podstawowym, tak że ma kolejną kopię danych pamięci podręcznej.

Węzeł podstawowy może wychodzić z usługi w ramach zaplanowanego działania konserwacji, takiego jak Redis oprogramowanie lub Aktualizacja systemu operacyjnego. Może ona również przestać działać z powodu nieplanowanych zdarzeń, takich jak awarie w podstawowym sprzęcie, oprogramowaniu lub sieci. [Tryb failover i poprawka dla usługi Azure cache for Redis](cache-failover.md) zawierają szczegółowe wyjaśnienie typów trybu failover Redis. Pamięć podręczna platformy Azure dla usługi Redis przejdzie przez wiele przełączeń w tryb failover w okresie istnienia. Architektura wysokiej dostępności została zaprojektowana w celu wprowadzenia tych zmian w pamięci podręcznej, jak to możliwe.

## <a name="multiple-replicas"></a>Wiele replik

>[!NOTE]
>Ten element jest dostępny jako wersja zapoznawcza.
>
>

Pamięć podręczna systemu Azure dla usługi Redis umożliwia dodanie dodatkowych węzłów repliki w warstwie Premium. [Pamięć podręczną z obsługą kilku replik](cache-how-to-multi-replicas.md) można skonfigurować przy użyciu maksymalnie trzech węzłów repliki. Większa liczba replik zwykle zwiększa odporność, ponieważ dodatkowe węzły tworzą kopię zapasową podstawowego. Nawet w przypadku większej liczby replik wystąpienie usługi Azure cache for Redis nadal może być poważnie wpływem na awarię centrum danych lub cały czas. Dostępność pamięci podręcznej można zwiększyć przy użyciu wielu replik w połączeniu z [nadmiarowością strefy](#zone-redundancy).

## <a name="zone-redundancy"></a>Nadmiarowość stref

>[!NOTE]
>Ten element jest dostępny jako wersja zapoznawcza.
>
>

Pamięć podręczna systemu Azure dla usługi Redis obsługuje nadmiarowe konfiguracje stref w warstwie Premium. [Pamięć podręczna nadmiarowa strefy](cache-how-to-zone-redundancy.md) może umieścić węzły w różnych [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) w tym samym regionie. Eliminuje to centrum danych lub AZ przestój jako single point of failure i zwiększa ogólną dostępność pamięci podręcznej.

Na poniższym diagramie przedstawiono konfigurację nadmiarową strefy:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Konfiguracja replikacji danych":::
   
Usługa Azure cache for Redis dystrybuuje węzły w strefie nadmiarowej pamięci podręcznej w sposób okrężny w przypadku wybrania AZs. Określa również, który węzeł będzie początkowo używany jako podstawowy.

Pamięć podręczna nadmiarowa strefy zapewnia automatyczną pracę w trybie failover. Gdy bieżący węzeł podstawowy jest niedostępny, jedna z replik przejdzie w tryb failover. Jeśli nowy węzeł podstawowy znajduje się w innym elemencie AZ, w aplikacji może wystąpić wyższy czas odpowiedzi w pamięci podręcznej. AZs są rozdzielone geograficznie. Przełączanie z jednego AZ na inny powoduje zmianę odległości fizycznej między lokalizacją, w której znajduje się aplikacja i pamięć podręczna. Ta zmiana wpływa na opóźnienia w sieci między aplikacjami w przypadku usługi w pamięci podręcznej. Dodatkowe opóźnienia powinny należeć do akceptowalnego zakresu dla większości aplikacji. Zalecamy przetestowanie aplikacji, aby upewnić się, że może ona działać prawidłowo w przypadku pamięci podręcznej nadmiarowej strefy.

## <a name="geo-replication"></a>Replikacja geograficzna

Replikacja geograficzna jest przeznaczona głównie do odzyskiwania po awarii. Umożliwia skonfigurowanie wystąpienia usługi Azure cache for Redis w innym regionie świadczenia usługi Azure, aby utworzyć kopię zapasową podstawowej pamięci podręcznej. [Konfigurowanie replikacji geograficznej dla usługi Azure cache for Redis](cache-how-to-geo-replication.md) zawiera szczegółowy opis działania replikacji geograficznej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie konfigurowania opcji wysokiej dostępności usługi Azure cache for Redis.

* [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)
* [Dodawanie replik do usługi Azure cache for Redis](cache-how-to-multi-replicas.md)
* [Włącz nadmiarowość strefy dla usługi Azure cache for Redis](cache-how-to-zone-redundancy.md)
* [Konfigurowanie replikacji geograficznej dla usługi Azure cache for Redis](cache-how-to-geo-replication.md)
