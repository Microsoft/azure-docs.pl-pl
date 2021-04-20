---
title: Wysoka dostępność dla Azure Cache for Redis
description: Dowiedz się więcej Azure Cache for Redis o funkcjach i opcjach wysokiej dostępności
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 6c44c87221442797f063877385ac5eb7f8585850
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719101"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Wysoka dostępność dla Azure Cache for Redis

Azure Cache for Redis ma wbudowaną wysoką dostępność. Celem architektury wysokiej dostępności jest zapewnienie, że zarządzane wystąpienie usługi Redis działa nawet wtedy, gdy planowane lub nieplanowane przestoje mają wpływ na jego bazowe maszyny wirtualne. Zapewnia znacznie większe stawki procentowe niż to, co można osiągnąć, hostowanie usługi Redis na jednej maszynie wirtualnej.

Azure Cache for Redis implementuje wysoką dostępność przy użyciu wielu maszyn wirtualnych nazywanych węzłami *dla* pamięci podręcznej. Konfiguruje te węzły w taki sposób, aby replikacja danych i trybu failover odbywały się w sposób skoordynowany. Umożliwia również organizowanie operacji konserwacji, takich jak stosowanie poprawek oprogramowania Redis. Różne opcje wysokiej dostępności są dostępne w warstwach Standardowa, Premium i Enterprise:

| Opcja | Opis | Dostępność | Standardowa (Standard) | Premium | Przedsiębiorstwa |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Replikacja standardowa](#standard-replication)| Zreplikowana konfiguracja z dwoma węzłami w jednym centrum danych z automatycznym trybem failover | 99,9% (zobacz [szczegóły](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |✔|✔|-|
| [Nadmiarowość stref](#zone-redundancy) | Konfiguracja replikowana z wieloma węzłami między węzłami z automatycznym trybem failover | Do 99,99% (zobacz [szczegóły](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |-|Wersja zapoznawcza|✔|
| [Replikacja geograficzna](#geo-replication) | Połączone wystąpienia pamięci podręcznej w dwóch regionach z kontrolowanym przez użytkownika trybem failover | Do 99,999% (zobacz [szczegóły](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |-|✔|Wersja zapoznawcza|

## <a name="standard-replication"></a>Replikacja standardowa

Konto Azure Cache for Redis w warstwie Standardowa lub Premium jest domyślnie uruchamiane na parach serwerów Redis. Dwa serwery są hostowane na dedykowanych maszyn wirtualnych. Serwer typu open source Redis umożliwia tylko jednemu serwerowi obsługę żądań zapisu danych. Ten serwer jest *węzłem* podstawowym, a druga *repliką*. Po a incytatorze węzłów serwera Azure Cache for Redis do nich role podstawowe i role repliki. Węzeł podstawowy zazwyczaj jest odpowiedzialny za obsługę zapisu oraz odczytywanie żądań od klientów usługi Redis. W przypadku operacji zapisu zatwierdza nowy klucz i aktualizację klucza w pamięci wewnętrznej i natychmiast odpowiada klientowi. Przekazuje operację asynchronicznie do repliki.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Konfiguracja replikacji danych":::
   
>[!NOTE]
>Zwykle klient usługi Redis komunikuje się z węzłem podstawowym w pamięci podręcznej Redis Cache w celu obsługi wszystkich żądań odczytu i zapisu. Niektórych klientów Redis można skonfigurować do odczytu z węzła repliki.
>
>

Jeśli węzeł podstawowy w pamięci podręcznej Redis cache jest niedostępny, replika automatycznie podniesie się do nowej podstawowej pamięci podręcznej. Ten proces jest nazywany trybu *failover.* Replika będzie oczekiwać przez wystarczająco długi czas przed przejęciem w przypadku szybkiego odzyskania węzła podstawowego. W przypadku pracy w trybie failover Azure Cache for Redis nową maszynę wirtualną i dołącza ją do pamięci podręcznej jako węzeł repliki. Replika wykonuje pełną synchronizację danych z podstawową, aby zawierała kolejną kopię danych pamięci podręcznej.

Węzeł podstawowy może wyjść z usługi w ramach planowanej konserwacji, takiej jak aktualizacja oprogramowania redis lub systemu operacyjnego. Może również przestać działać z powodu nieplanowanych zdarzeń, takich jak awarie podstawowego sprzętu, oprogramowania lub sieci. [Funkcje trybu failover](cache-failover.md) i Azure Cache for Redis zapewniają szczegółowe wyjaśnienie typów trybu failover usługi Redis. W Azure Cache for Redis wiele awarii zostanie przejścia do trybu failover. Architektura wysokiej dostępności została zaprojektowana tak, aby te zmiany w pamięci podręcznej były jak najbardziej niewidoczne dla klientów.

>[!NOTE]
>Poniższe elementy są dostępne w wersji zapoznawczej.
>
>

Ponadto usługa Azure Cache for Redis dodatkowe węzły repliki w warstwie Premium. Pamięć [podręczną z wieloma replikami](cache-how-to-multi-replicas.md) można skonfigurować z maksymalnie trzema węzłami repliki. Większa liczba replik zwykle zwiększa odporność ze względu na tworzenie kopii zapasowej dodatkowych węzłów podstawowych. Nawet w przypadku większej liczby replik Azure Cache for Redis może mieć poważny wpływ na wystąpienie danych lub awarii na poziomie centrum danych lub az. Dostępność pamięci podręcznej można zwiększyć, używając wielu replik w połączeniu z [nadmiarowością strefy](#zone-redundancy).

## <a name="zone-redundancy"></a>Nadmiarowość stref

Azure Cache for Redis konfiguracje strefowo nadmiarowe w warstwach Premium i Enterprise. Strefowo [nadmiarowa pamięć podręczna](cache-how-to-zone-redundancy.md) może umieszczać swoje węzły w różnych [Strefy dostępności platformy Azure](../availability-zones/az-overview.md) w tym samym regionie. Eliminuje to awarii centrum danych lub az jako single point of failure i zwiększa ogólną dostępność pamięci podręcznej.

### <a name="premium-tier"></a>Warstwa Premium

>[!NOTE]
>Ta funkcja jest dostępna w wersji zapoznawczej.
>
>

Na poniższym diagramie przedstawiono strefowo nadmiarową konfigurację warstwy Premium:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Konfiguracja nadmiarowości strefy":::
   
Azure Cache for Redis dystrybuuje węzły w strefowo nadmiarowej pamięci podręcznej w sposób okrężny w wybranych strefach. Określa również, który węzeł będzie początkowo podstawowym.

Strefowo nadmiarowa pamięć podręczna zapewnia automatyczny tryb failover. Gdy bieżący węzeł podstawowy jest niedostępny, jedna z replik przejmie. Jeśli nowy węzeł podstawowy znajduje się w innym węźle podstawowym, w aplikacji może wystąpić większy czas odpowiedzi pamięci podręcznej. Obszary AZ są oddzielone geograficznie. Przełączenie z jednego az do innego zmienia fizyczną odległość między hostami aplikacji i pamięci podręcznej. Ta zmiana ma wpływ na opóźnienia sieci rundy z aplikacji do pamięci podręcznej. Oczekuje się, że dodatkowe opóźnienie spadnie w akceptowalnym zakresie dla większości aplikacji. Zalecamy przetestowanie aplikacji w celu zapewnienia jej wysokiej jakości działania w przypadku strefowo nadmiarowej pamięci podręcznej.

### <a name="enterprise-tiers"></a>Warstwy Enterprise

Pamięć podręczna w obu warstwach Przedsiębiorstwa działa w klastrze usługi Redis Enterprise. Aby można było tworzyć kworum, wymagana jest nieparzysta liczba węzłów serwera przez cały czas. Domyślnie składa się z trzech węzłów, z których każdy jest hostowany na dedykowanej maszynie wirtualnej. Pamięć podręczna przedsiębiorstwa ma dwa węzły danych o takim samym *rozmiarze* i jeden mniejszy *węzeł kworum*. Pamięć Enterprise Flash pamięci podręcznej ma trzy węzły danych o takim samym rozmiarze. Klaster przedsiębiorstwa dzieli dane usługi Redis na partycje wewnętrznie. Każda partycja ma *podstawową i* co najmniej jedną *replikę*. Każdy węzeł danych zawiera co najmniej jedną partycję. Klaster przedsiębiorstwa zapewnia, że repliki podstawowe i repliki dowolnej partycji nigdy nie będą kolokowane w tym samym węźle danych. Partycje replikują dane asynchronicznie z maszyn do odpowiadających im replik.

Gdy węzeł danych stanie się niedostępny lub nastąpi podział sieci, nastąpi tryb failover podobny do opisanego w [tece](#standard-replication) replikacji standardowej. Klaster przedsiębiorstwa używa modelu opartego na kworum, aby określić, które pozostałe węzły będą uczestniczyć w nowym kworum. Ponadto podniesie ona partycje replik w ramach tych węzłów do poziomu ich węzłów w razie potrzeby.

## <a name="geo-replication"></a>Replikacja geograficzna

[Replikacja geograficzna](cache-how-to-geo-replication.md) to mechanizm łączenia co najmniej dwóch wystąpień Azure Cache for Redis, zwykle obejmujących dwa regiony platformy Azure. 

### <a name="premium-tier"></a>Warstwa Premium

>[!NOTE]
>Replikacja geograficzna w warstwie Premium jest przeznaczona głównie do odzyskiwania po awarii.
>
>

Dwa wystąpienia pamięci podręcznej w warstwie Premium można połączyć za pomocą [replikacji](cache-how-to-geo-replication.md) geograficznej, aby można było utworzyć kopię zapasową danych pamięci podręcznej w innym regionie. Po nawiązać ze sobą jedno wystąpienie jest wyznaczane jako podstawowa połączona pamięć podręczna, a drugie jako pomocnicza połączona pamięć podręczna. Tylko podstawowa pamięć podręczna akceptuje żądania odczytu i zapisu. Dane zapisane w podstawowej pamięci podręcznej są replikowane do pomocniczej pamięci podręcznej. Aplikacja uzyskuje dostęp do pamięci podręcznej za pośrednictwem oddzielnych punktów końcowych dla podstawowej i pomocniczej pamięci podręcznej. Aplikacja musi wysyłać wszystkie żądania zapisu do podstawowej pamięci podręcznej, gdy jest wdrażana w wielu regionach świadczenia usługi Azure. Może odczytywać dane z podstawowej lub pomocniczej pamięci podręcznej. Ogólnie rzecz biorąc, chcesz, aby wystąpienia obliczeniowe aplikacji odczytywały dane z najbliższych pamięci podręcznych w celu zmniejszenia opóźnienia. Transfer danych między dwoma wystąpieniami pamięci podręcznej jest zabezpieczony przez zabezpieczenia TLS.

Replikacja geograficzna nie zapewnia automatycznego trybu failover ze względu na obawy związane z dodanym czasem rundy sieci między regionami, jeśli pozostała część aplikacji pozostanie w regionie podstawowym. Musisz zarządzać trybem failover i inicjować go przez odłączenie pomocniczej pamięci podręcznej. Spowoduje to promowanie go jako nowego wystąpienia podstawowego.

### <a name="enterprise-tiers"></a>Warstwy Enterprise

>[!NOTE]
>Ta funkcja jest dostępna w wersji zapoznawczej.
>
>

Warstwy Przedsiębiorstwa obsługują bardziej zaawansowaną postać replikacji geograficznej, [nazywaną aktywną replikacją geograficzną.](cache-how-to-active-geo-replication.md) Korzystając z replikowanych typów danych bez konfliktów, oprogramowanie Redis Enterprise obsługuje zapis w wielu wystąpieniach pamięci podręcznej i w razie potrzeby zajmuje się scaleniem zmian i rozwiązywaniem konfliktów. Co najmniej dwa wystąpienia pamięci podręcznej w warstwie Przedsiębiorstwa w różnych regionach świadczenia usługi Azure mogą być przyłączone do aktywnej pamięci podręcznej z replikacją geograficzną. Aplikacja korzystająca z takiej pamięci podręcznej może odczytywać i zapisywać w wystąpieniach rozproszonej geograficznie pamięci podręcznej za pośrednictwem odpowiednich punktów końcowych. Powinna ona używać wartości najbliższej każdemu wystąpieniu obliczeniowej, co zapewnia najmniejsze opóźnienie. Aplikacja musi również monitorować wystąpienia pamięci podręcznej i przełączać się do innego regionu, jeśli jedno z wystąpień stanie się niedostępne. Aby uzyskać więcej informacji na temat sposobu działania aktywnej replikacji geograficznej, zobacz [Active-Active Geo-Distriubtion (oparte na crdts)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat konfigurowania Azure Cache for Redis opcji wysokiej dostępności.

* [Azure Cache for Redis warstwy usług Premium](cache-overview.md#service-tiers)
* [Dodawanie replik do Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [Włączanie nadmiarowości strefy dla Azure Cache for Redis](cache-how-to-zone-redundancy.md)
* [Konfigurowanie replikacji geograficznej dla Azure Cache for Redis](cache-how-to-geo-replication.md)
