---
title: Tryb failover i stosowanie poprawek — Azure Cache for Redis
description: Dowiedz się więcej na temat trybu failover, poprawek i procesu aktualizacji dla usługi Azure cache for Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: cc7c70fa2e7131f09f621e992d537e0b120061ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210737"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Tryb failover i stosowanie poprawek dla usługi Azure cache for Redis

Aby tworzyć odporne i pomyślne aplikacje klienckie, należy zapoznać się z trybem failover w kontekście usługi Redis w pamięci podręcznej platformy Azure. Tryb failover może być częścią planowanych operacji zarządzania lub może być spowodowany nieplanowanymi awariami sprzętu lub sieci. Typowym zastosowaniem trybu failover pamięci podręcznej jest to, kiedy usługa zarządzania poprawkami pamięci podręcznej platformy Azure dla plików binarnych Redis W tym artykule opisano, jak działa tryb failover, w jaki sposób występuje podczas stosowania poprawek i jak utworzyć odporną aplikację kliencką.

## <a name="what-is-a-failover"></a>Co to jest tryb failover?

Zacznijmy od omówienia trybu failover dla usługi Azure cache for Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Krótkie podsumowanie architektury pamięci podręcznej

Pamięć podręczna jest zbudowana z wielu maszyn wirtualnych z oddzielnymi prywatnymi adresami IP. Każda maszyna wirtualna, znana także jako węzeł, jest połączona z udostępnionym modułem równoważenia obciążenia z pojedynczym wirtualnym adresem IP. Każdy węzeł uruchamia proces serwera Redis i jest dostępny za pomocą nazwy hosta i portów Redis. Każdy węzeł jest traktowany jako węzeł podstawowy lub replika. Gdy aplikacja kliencka nawiązuje połączenie z pamięcią podręczną, ruch przechodzi przez ten moduł równoważenia obciążenia i jest automatycznie kierowany do węzła podstawowego.

W podstawowej pamięci podręcznej pojedynczy węzeł jest zawsze podstawowym. W pamięci podręcznej standardowa lub Premium istnieją dwa węzły: jeden jest wybierany jako podstawowy, a drugi to replika. Ponieważ pamięć podręczna w warstwach Standardowa i Premium ma wiele węzłów, jeden węzeł może być niedostępny, a drugi nadal przetwarza żądania. Klastrowane pamięci podręczne składają się z wielu fragmentów, z których każdy ma odrębne węzły podstawowe i repliki. Jeden fragmentu może nie działać, gdy pozostałe pozostają dostępne.

> [!NOTE]
> Podstawowa pamięć podręczna nie ma wielu węzłów i nie oferuje umowy dotyczącej poziomu usług (SLA) na potrzeby jej dostępności. Podstawowe pamięci podręczne są zalecane tylko w celach deweloperskich i testowych. Użyj pamięci podręcznej standardowa lub Premium do wdrożenia wielowęzłowego, aby zwiększyć dostępność.

### <a name="explanation-of-a-failover"></a>Wyjaśnienie trybu failover

Tryb failover występuje, gdy węzeł repliki promuje się do węzła podstawowego, a stary węzeł podstawowy zamyka istniejące połączenia. Po utworzeniu kopii zapasowej węzła podstawowego następuje zmiana w rolach i obniżanie poziomu, aby stał się repliką. Następnie nawiązuje połączenie z nowym serwerem podstawowym i synchronizuje dane. Przejście w tryb failover może być planowane lub nieplanowane.

*Planowana praca w trybie failover* odbywa się podczas aktualizacji systemu, takich jak poprawki Redis lub uaktualnienia systemu operacyjnego oraz operacje zarządzania, takie jak skalowanie i ponowne uruchamianie. Ponieważ węzły otrzymują zaawansowaną informację o aktualizacji, mogą wspólnie wymieniać role i szybko aktualizować moduł równoważenia obciążenia zmiany. Planowane przełączenie w tryb failover zwykle kończy się krócej niż 1 sekunda.

*Nieplanowana praca w trybie failover* może wystąpić z powodu awarii sprzętu, awarii sieci lub innych nieoczekiwanych awarii węzła głównego. Węzeł repliki promuje się do podstawowego, ale proces trwa dłużej. Węzeł repliki musi najpierw wykryć, że jego węzeł podstawowy nie jest dostępny, zanim będzie mógł zainicjować proces przełączania do trybu failover. Węzeł repliki musi również sprawdzić, czy ten nieplanowany błąd nie jest przejściowy, czy lokalny, aby uniknąć niepotrzebnego przejścia w tryb failover. To opóźnienie wykrywania oznacza, że nieplanowane przełączenie w tryb failover zwykle kończy się w okresie od 10 do 15 sekund.

## <a name="how-does-patching-occur"></a>Jak występuje poprawka?

Usługa Azure cache for Redis regularnie aktualizuje pamięć podręczną przy użyciu najnowszych funkcji i poprawek platformy. Aby poprawić pamięć podręczną, usługa wykonuje następujące czynności:

1. Usługa zarządzania wybiera jeden węzeł, który ma zostać poprawiony.
1. Jeśli wybrany węzeł jest węzłem podstawowym, odpowiadający mu węzeł repliki wspólnie promuje się do siebie. Promocja jest uznawana za planowane przejście w tryb failover.
1. Wybrany węzeł zostanie uruchomiony ponownie w celu przeprowadzenia nowych zmian i zostanie przywrócony jako węzeł repliki.
1. Węzeł repliki nawiązuje połączenie z węzłem podstawowym i synchronizuje dane.
1. Po zakończeniu synchronizacji danych proces stosowania poprawek powtarza się dla pozostałych węzłów.

Ponieważ stosowanie poprawek jest planowanym trybem failover, węzeł repliki szybko promuje się do podstawowego i rozpoczyna żądania obsługi oraz nowe połączenia. Podstawowe pamięci podręczne nie mają węzła repliki i są niedostępne do momentu ukończenia aktualizacji. Każdy fragmentu klastrowanej pamięci podręcznej jest instalowany oddzielnie i nie zamyka połączeń z innym fragmentu.

> [!IMPORTANT]
> W celu zapobieżenia utracie danych węzły są w tej chwili poprawiane. Pamięć podręczna w warstwie Podstawowa będzie miała utratę danych. Klastrowane pamięci podręczne są poprawiane po jednym fragmentu w danym momencie.

Wiele pamięci podręcznych w tej samej grupie zasobów i w tym samym regionie jest również instalowanych pojedynczo.  Pamięci podręczne, które znajdują się w różnych grupach zasobów lub różnych regionach mogą być jednocześnie poprawiane.

Ze względu na to, że pełna synchronizacja danych jest wykonywana przed powtórzeniem procesu, podczas korzystania z pamięci podręcznej standardowej lub Premium nie występuje utrata danych. Możesz zwiększyć ochronę przed utratą danych, [eksportując](cache-how-to-import-export-data.md#export) dane i włączając [trwałość](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Dodatkowe obciążenie pamięci podręcznej

Zawsze, gdy nastąpi przełączenie w tryb failover, warstwy pamięci podręcznej standardowa i Premium muszą replikować dane z jednego węzła do drugiego. Ta replikacja powoduje wzrost obciążenia zarówno pamięci serwera, jak i procesora CPU. Jeśli wystąpienie pamięci podręcznej jest już silnie załadowane, aplikacje klienckie mogą powodować zwiększone opóźnienia. W skrajnych przypadkach aplikacje klienckie mogą otrzymywać wyjątki przekroczenia limitu czasu. Aby zmniejszyć wpływ tego dodatkowego obciążenia, [Skonfiguruj](cache-configure.md#memory-policies) ustawienia pamięci podręcznej `maxmemory-reserved` .

## <a name="how-does-a-failover-affect-my-client-application"></a>Jak działa przejście w tryb failover na moją aplikację kliencką?

Liczba błędów widzianych przez aplikację kliencką zależy od liczby operacji oczekujących na to połączenie w momencie przejścia w tryb failover. Wszystkie połączenia, które są kierowane przez węzeł, który zamknął połączenie, zobaczą błędy. Wiele bibliotek klienckich może generować różne typy błędów w przypadku przerwania połączeń, w tym wyjątków limitu czasu, wyjątków połączeń lub wyjątków gniazda. Liczba i typ wyjątków zależy od tego, gdzie znajduje się w ścieżce kodu żądania, gdy pamięć podręczna zamknie połączenia. Na przykład operacja wysyłająca żądanie, ale nie otrzymała odpowiedzi, gdy nastąpi przejście w tryb failover, może uzyskać wyjątek limitu czasu. Nowe żądania dotyczące zamkniętego obiektu połączenia odbierają wyjątki połączeń do momentu pomyślnego ponownego nawiązania połączenia.

Większość bibliotek klienta próbuje ponownie nawiązać połączenie z pamięcią podręczną, jeśli są skonfigurowane do tego celu. Nieprzewidziane usterki mogą jednak czasami umieścić obiekty biblioteki w stanie nieodwracalnym. Jeśli błędy będą przechowywane dłużej niż w wstępnie skonfigurowanym czasie, należy ponownie utworzyć obiekt połączenia. W Microsoft.NET i innych językach zorientowanych na obiektach, ponowne tworzenie połączenia bez ponownego uruchamiania aplikacji można wykonać przy użyciu [ \<T\> wzorca z opóźnieniem](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Jak mogę zrobić, aby moja aplikacja była odporna?

Ponieważ nie można całkowicie uniknąć przełączenia w tryb failover, należy napisać aplikacje klienckie pod kątem odporności na przerwy połączeń i Nieudane żądania. Mimo że większość bibliotek klienta automatycznie ponownie nawiązuje połączenie z punktem końcowym pamięci podręcznej, kilka z nich próbuje ponowić Nieudane żądania. W zależności od scenariusza aplikacji może być przydatne użycie logiki ponawiania z wycofywania.

Aby przetestować odporność aplikacji klienta, należy [przeprowadzić ponowny rozruch](cache-administration.md#reboot) jako wyzwalacz ręczny dla przerw w połączeniach. Ponadto zaleca się [zaplanowanie aktualizacji](cache-administration.md#schedule-updates) w pamięci podręcznej. Poinformuj usługę zarządzania, aby stosowała poprawki środowiska uruchomieniowego Redis podczas określonych tygodniowych okien. Te okna są zwykle okresów, gdy ruch aplikacji klienckich jest niski, aby uniknąć potencjalnych zdarzeń.

### <a name="can-i-be-notified-in-advance-of-a-planned-maintenance"></a>Czy mogę otrzymywać powiadomienia z wyprzedzeniem o planowanej konserwacji?

Usługa Azure cache for Redis teraz publikuje powiadomienia w kanale publikowania/subskrybowania o nazwie [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) około 30 sekund przed planowanymi aktualizacjami. Są to powiadomienia w czasie wykonywania i są one przeznaczone specjalnie dla aplikacji, które mogą używać wyłączników do obejścia pamięci podręcznej lub poleceń bufora, na przykład podczas planowanych aktualizacji. Nie jest mechanizmem, który może powiadamiać o dniach lub godzinach z wyprzedzeniem.

### <a name="client-network-configuration-changes"></a>Sieć klienta — zmiany konfiguracji

Niektóre zmiany w konfiguracji sieci po stronie klienta mogą wyzwalać błędy "Brak dostępnego połączenia". Takie zmiany mogą obejmować:

- Zamienianie wirtualnego adresu IP aplikacji klienckiej między miejscami przejściowymi i produkcyjnymi.
- Skalowanie rozmiaru lub liczby wystąpień aplikacji.

Takie zmiany mogą spowodować problem z łącznością, który jest krótszy niż jedna minuta. Aplikacja kliencka prawdopodobnie utraci połączenie z innymi zasobami sieci zewnętrznej oprócz usługi Azure cache for Redis.

## <a name="next-steps"></a>Następne kroki

- [Zaplanuj aktualizacje](cache-administration.md#schedule-updates) pamięci podręcznej.
- Przetestowanie odporności aplikacji przy użyciu [ponownego uruchomienia](cache-administration.md#reboot).
- [Konfigurowanie](cache-configure.md#memory-policies) rezerwacji pamięci i zasad.
