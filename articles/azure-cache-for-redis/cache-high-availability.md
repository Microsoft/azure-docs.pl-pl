---
title: Wysoka dostępność dla usługi Azure cache for Redis
description: Informacje o usłudze Azure cache for Redis — funkcje i opcje wysokiej dostępności
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 69dec2ce00b263f3536e30ba0a5376e6d922b79c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308357"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Wysoka dostępność dla usługi Azure cache for Redis

Usługa Azure cache for Redis ma wbudowaną wysoką dostępność. Celem jego architektury wysokiej dostępności jest upewnienie się, że zarządzane wystąpienie Redis działa nawet wtedy, gdy jego bazowe maszyny wirtualne mają wpływ na planowane lub nieplanowane przestoje. Zapewnia ona znacznie wyższą stawkę procentową niż osiągane przez hosting Redis na jednej maszynie wirtualnej.

Usługa Azure cache for Redis implementuje wysoką dostępność przy użyciu wielu maszyn wirtualnych, nazywanych *węzłami*, dla pamięci podręcznej. Konfiguruje te węzły w taki sposób, że replikacja danych i tryb failover są wykonywane w sposób skoordynowany. Organizuje ona również operacje konserwacji, takie jak Redis. Dostępne są różne opcje wysokiej dostępności w warstwach Standardowa, Premium i Enterprise:

| Opcja | Opis | Dostępność | Standardowa (Standard) | Premium | Przedsiębiorstwa |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standardowa replikacja](#standard-replication)| Konfiguracja replikacji z dwoma węzłami w jednym centrum danych z automatycznym trybem failover | 99,9% (zobacz [szczegóły](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |✔|✔|-|
| [Nadmiarowość stref](#zone-redundancy) | Konfiguracja replikacji wielowęzłowej w systemie AZs z automatycznym trybem failover | Do 99,99% (zobacz [szczegóły](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |-|Wersja zapoznawcza|Wersja zapoznawcza|
| [Replikacja geograficzna](#geo-replication) | Wystąpienia połączonej pamięci podręcznej w dwóch regionach z trybem failover sterowanym przez użytkownika | Do 99,999% (zobacz [szczegóły](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |-|✔|Wersja zapoznawcza|

## <a name="standard-replication"></a>Standardowa replikacja

Pamięć podręczna platformy Azure dla Redis w warstwie Standardowa lub Premium jest domyślnie uruchamiana na parze serwerów Redis. Te dwa serwery są hostowane na dedykowanych maszynach wirtualnych. Redis Open Source umożliwia obsługę żądań zapisu danych tylko jeden serwer. Ten serwer jest węzłem *podstawowym* , natomiast druga *replika*. Po wprowadzeniu w nim węzłów serwera usługa Azure cache for Redis przypisze do nich role podstawowe i repliki. Węzeł podstawowy zazwyczaj jest odpowiedzialny za obsługę zapisu oraz żądania odczytu z klientów Redis. W operacji zapisu zostaje zatwierdzona Nowa klucz i Aktualizacja klucza do pamięci wewnętrznej i natychmiast odpowiedzi na klienta. Przekazuje operację do repliki asynchronicznie.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Konfiguracja replikacji danych":::
   
>[!NOTE]
>Zwykle klient Redis komunikuje się z węzłem podstawowym w pamięci podręcznej Redis dla wszystkich żądań odczytu i zapisu. Niektórych klientów Redis można skonfigurować do odczytu z węzła repliki.
>
>

Jeśli węzeł podstawowy w pamięci podręcznej Redis jest niedostępny, replika zostanie podniesiona do automatycznej nowej głównej. Ten proces jest nazywany *trybem failover*. Replika będzie oczekiwać przez wystarczająco długi czas przed przejęciem w przypadku, gdy węzeł podstawowy odzyska szybkie odzyskiwanie. W przypadku przejścia w tryb failover usługa Azure cache for Redis Inicjuje nową maszynę wirtualną i łączy ją z pamięcią podręczną jako węzłem repliki. Replika wykonuje pełną synchronizację danych z podstawowym, tak że ma kolejną kopię danych pamięci podręcznej.

Węzeł podstawowy może wychodzić z usługi w ramach zaplanowanego działania konserwacji, takiego jak Redis oprogramowanie lub Aktualizacja systemu operacyjnego. Może ona również przestać działać z powodu nieplanowanych zdarzeń, takich jak awarie w podstawowym sprzęcie, oprogramowaniu lub sieci. [Tryb failover i poprawka dla usługi Azure cache for Redis](cache-failover.md) zawierają szczegółowe wyjaśnienie typów trybu failover Redis. Pamięć podręczna platformy Azure dla usługi Redis przejdzie przez wiele przełączeń w tryb failover w okresie istnienia. Architektura wysokiej dostępności została zaprojektowana w celu wprowadzenia tych zmian w pamięci podręcznej, jak to możliwe.

>[!NOTE]
>Poniższe elementy są dostępne jako wersja zapoznawcza.
>
>

Ponadto usługa Azure cache for Redis umożliwia korzystanie z dodatkowych węzłów repliki w warstwie Premium. [Pamięć podręczną z obsługą kilku replik](cache-how-to-multi-replicas.md) można skonfigurować przy użyciu maksymalnie trzech węzłów repliki. Większa liczba replik zwykle zwiększa odporność, ponieważ dodatkowe węzły tworzą kopię zapasową podstawowego. Nawet w przypadku większej liczby replik wystąpienie usługi Azure cache for Redis nadal może być poważnie wpływem na awarie centrum danych lub AZ-Level. Dostępność pamięci podręcznej można zwiększyć przy użyciu wielu replik w połączeniu z [nadmiarowością strefy](#zone-redundancy).

## <a name="zone-redundancy"></a>Nadmiarowość stref

Pamięć podręczna systemu Azure dla usługi Redis obsługuje nadmiarowe konfiguracje stref w warstwach Premium i Enterprise. [Pamięć podręczna nadmiarowa strefy](cache-how-to-zone-redundancy.md) może umieścić węzły w różnych [strefy dostępności platformy Azure](../availability-zones/az-overview.md) w tym samym regionie. Eliminuje to centrum danych lub AZ przestój jako single point of failure i zwiększa ogólną dostępność pamięci podręcznej.

### <a name="premium-tier"></a>Warstwa Premium

>[!NOTE]
>Ten element jest dostępny jako wersja zapoznawcza.
>
>

Na poniższym diagramie przedstawiono strefowo nadmiarową konfigurację dla warstwy Premium:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Konfiguracja nadmiarowości stref":::
   
Usługa Azure cache for Redis dystrybuuje węzły w strefie nadmiarowej pamięci podręcznej w sposób okrężny w przypadku wybrania AZs. Określa również, który węzeł będzie początkowo używany jako podstawowy.

Pamięć podręczna nadmiarowa strefy zapewnia automatyczną pracę w trybie failover. Gdy bieżący węzeł podstawowy jest niedostępny, jedna z replik przejdzie w tryb failover. Jeśli nowy węzeł podstawowy znajduje się w innym elemencie AZ, w aplikacji może wystąpić wyższy czas odpowiedzi w pamięci podręcznej. AZs są rozdzielone geograficznie. Przełączanie z jednego AZ na inny powoduje zmianę odległości fizycznej między lokalizacją, w której znajduje się aplikacja i pamięć podręczna. Ta zmiana wpływa na opóźnienia w sieci między aplikacjami w przypadku usługi w pamięci podręcznej. Dodatkowe opóźnienia powinny należeć do akceptowalnego zakresu dla większości aplikacji. Zalecamy przetestowanie aplikacji, aby upewnić się, że może ona działać prawidłowo w przypadku pamięci podręcznej nadmiarowej strefy.

### <a name="enterprise-tiers"></a>Warstwy Enterprise

Pamięć podręczna w ramach jednej warstwy przedsiębiorstwa jest uruchamiana w klastrze Redis przedsiębiorstwa. W każdym momencie program wymaga nieparzystej liczby węzłów serwera do utworzenia kworum. Domyślnie składa się z trzech węzłów, które są hostowane na dedykowanej maszynie wirtualnej. Pamięć podręczna przedsiębiorstwa ma dwa *węzły danych* o takim samym rozmiarze i jeden mniejszy *węzeł kworum*. Pamięć podręczna programu Enterprise Flash ma trzy węzły danych o takim samym rozmiarze. Klaster przedsiębiorstwa dzieli dane Redis na partycje wewnętrznie. Każda partycja ma *podstawową* i co najmniej jedną *replikę*. Każdy węzeł danych zawiera jedną lub więcej partycji. Klaster przedsiębiorstwa zapewnia, że podstawowy i repliki wszystkich partycji nigdy nie znajdują się w tym samym węźle danych. Partycje umożliwiają replikację danych asynchronicznie z Primaries do odpowiednich replik.

Gdy węzeł danych stanie się niedostępny lub następuje podzielona sieć, nastąpi przejście w tryb failover podobny do przedstawionego w [replikacji standardowej](#standard-replication) . Klaster przedsiębiorstwa korzysta z modelu opartego na kworum, aby określić, które z pozostałych węzłów będą uczestniczyć w nowym kworum. Wspiera również partycje repliki w tych węzłach, aby Primaries je w razie potrzeby.

## <a name="geo-replication"></a>Replikacja geograficzna

[Replikacja geograficzna](cache-how-to-geo-replication.md) jest mechanizmem do łączenia co najmniej dwóch pamięci podręcznej platformy Azure dla wystąpień Redis, zwykle obejmujących dwa regiony platformy Azure. 

### <a name="premium-tier"></a>Warstwa Premium

>[!NOTE]
>Replikacja geograficzna w warstwie Premium jest przeznaczona głównie do odzyskiwania po awarii.
>
>

Dwie wystąpienia pamięci podręcznej warstwy Premium mogą być połączone za pomocą [replikacji geograficznej](cache-how-to-geo-replication.md) , aby można było utworzyć kopię zapasową danych w pamięci podręcznej w innym regionie. Po połączeniu z jednym wystąpieniem zostanie wyznaczono podstawową, połączoną pamięć podręczną, a druga jako pomocnicza połączona pamięć podręczna. Tylko podstawowa pamięć podręczna akceptuje żądania odczytu i zapisu. Dane zapisywane w podstawowej pamięci podręcznej są replikowane do pomocniczej pamięci podręcznej. Aplikacja uzyskuje dostęp do pamięci podręcznej za pomocą oddzielnych punktów końcowych dla podstawowej i pomocniczej pamięci podręcznej. Aplikacja musi wysyłać wszystkie żądania zapisu do podstawowej pamięci podręcznej, gdy zostanie ona wdrożona w wielu regionach świadczenia usługi Azure. Można go odczytać z podstawowej lub pomocniczej pamięci podręcznej. Ogólnie rzecz biorąc, chcesz, aby wystąpienia obliczeniowe aplikacji były odczytywane z najbliższych pamięci podręcznych w celu zmniejszenia opóźnień. Transfer danych między dwoma wystąpieniami pamięci podręcznej jest zabezpieczony przy użyciu protokołu TLS.

Replikacja geograficzna nie zapewnia automatycznej pracy awaryjnej ze względu na problemy z dodanym czasem komunikacji sieciowej między regionami, jeśli pozostała część aplikacji pozostaje w regionie podstawowym. Należy zarządzać i inicjować tryb failover przez odłączenie pomocniczej pamięci podręcznej. Spowoduje to podwyższenie poziomu IT do nowego wystąpienia podstawowego.

### <a name="enterprise-tiers"></a>Warstwy Enterprise

>[!NOTE]
>Ten element jest dostępny jako wersja zapoznawcza.
>
>

Warstwy przedsiębiorstwa obsługują bardziej zaawansowaną postać replikacji geograficznej, nazywaną [aktywną replikacją geograficzną](cache-how-to-active-geo-replication.md). Wykorzystując niezależnie zreplikowane typy danych, oprogramowanie Redis Enterprise obsługuje operacje zapisu w wielu wystąpieniach pamięci podręcznej i zajmuje się scalaniem zmian i rozwiązywaniem konfliktów w razie potrzeby. Co najmniej dwa wystąpienia pamięci podręcznej warstwy przedsiębiorstwa w różnych regionach platformy Azure można przyłączyć do postaci aktywnej buforowanej pamięci podręcznej. Aplikacja korzystająca z takiej pamięci podręcznej może odczytywać i zapisywać wystąpienia rozproszonej pamięci podręcznej za pośrednictwem odpowiednich punktów końcowych. Należy używać co najbliżej każdego wystąpienia obliczeniowego, co zapewnia najniższe opóźnienie. Aplikacja musi również monitorować wystąpienia pamięci podręcznej i przechodzić do innego regionu, jeśli jedno z wystąpień jest niedostępne. Aby uzyskać więcej informacji na temat działania aktywnej replikacji geograficznej, zobacz temat [Active-active Geo-Distriubtion (oparty na CRDTs)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie konfigurowania opcji wysokiej dostępności usługi Azure cache for Redis.

* [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)
* [Dodawanie replik do usługi Azure cache for Redis](cache-how-to-multi-replicas.md)
* [Włącz nadmiarowość strefy dla usługi Azure cache for Redis](cache-how-to-zone-redundancy.md)
* [Konfigurowanie replikacji geograficznej dla usługi Azure cache for Redis](cache-how-to-geo-replication.md)
