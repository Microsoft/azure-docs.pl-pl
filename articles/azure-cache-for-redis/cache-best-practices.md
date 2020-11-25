---
title: Najlepsze rozwiązania dotyczące usługi Azure Cache for Redis
description: Dowiedz się, jak efektywnie korzystać z pamięci podręcznej platformy Azure, postępując zgodnie z najlepszymi rozwiązaniami.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 47c8096893742a25904f0f7e688af2fc641166d1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004317"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Najlepsze rozwiązania dotyczące usługi Azure Cache for Redis 
Postępując zgodnie z najlepszymi rozwiązaniami, możesz pomóc zmaksymalizować wydajność i ekonomiczne użycie wystąpienia usługi Azure cache for Redis.

## <a name="configuration-and-concepts"></a>Konfiguracja i koncepcje
 * **Użyj warstwy Standardowa lub Premium dla systemów produkcyjnych.**  Warstwa Podstawowa to jednowęzłowy system bez replikacji danych i Umowa SLA. Ponadto można użyć przynajmniej pamięci podręcznej C1.  Pamięć podręczna C0 jest przeznaczona dla prostych scenariuszy tworzenia i testowania, ponieważ mają współużytkowaną rdzeń procesora CPU, małą ilość pamięci i są podatne na problemy "zakłócenia".

 * **Należy pamiętać, że Redis jest magazynem danych w pamięci.**  W [tym artykule](cache-troubleshoot-data-loss.md) opisano niektóre scenariusze, w których może wystąpić utrata danych.

 * **Opracowywanie systemu w taki sposób, że może on obsługiwać Blips połączeń** [z powodu poprawek i trybu failover](cache-failover.md).

 * **Skonfiguruj [ustawienie zarezerwowane maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , aby zwiększyć czas odpowiedzi systemu** w warunkach ciśnienia pamięci.  Wystarczające ustawienie rezerwacji jest szczególnie ważne w przypadku obciążeń z dużym obciążeniem lub w przypadku przechowywania większych wartości (100 KB lub więcej) w Redis. Należy zacząć od 10% rozmiaru pamięci podręcznej i zwiększyć tę wartość procentową w przypadku obciążeń z dużą ilością zapisu.

 * **Redis najlepiej sprawdza się w przypadku mniejszych wartości**, więc Rozważ powiększanie większych danych do wielu kluczy.  W [tej dyskusji Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)należy wziąć pod uwagę pewne zagadnienia, które należy uwzględnić uważnie.  Przeczytaj [ten artykuł](cache-troubleshoot-client.md#large-request-or-response-size) , aby zapoznać się z przykładowym problemem, który może być spowodowany przez duże wartości.

 * **Znajdź wystąpienie pamięci podręcznej i swoją aplikację w tym samym regionie.**  Łączenie się z pamięcią podręczną w innym regionie może znacznie zwiększyć opóźnienie i ograniczyć niezawodność.  Istnieje możliwość nawiązania połączenia poza platformą Azure, ale nie jest to zalecane, *szczególnie w przypadku korzystania z Redis jako pamięci podręcznej*.  Jeśli używasz Redis jako samego magazynu klucz/wartość, opóźnienie może nie być zasadniczym problemem. 

 * **Ponowne użycie połączeń.**  Tworzenie nowych połączeń jest kosztowne i zwiększa opóźnienia, dlatego należy ponownie używać połączeń, tak jak to możliwe. Jeśli zdecydujesz się na utworzenie nowych połączeń, przed ich wydaniem upewnij się, że zostały zamknięte stare połączenia (nawet w językach zarządzanych, takich jak .NET lub Java).

 * **Skonfiguruj bibliotekę klienta tak, aby korzystała z *limitu czasu połączenia* wynoszącego co najmniej 15 sekund**, co zapewnia czas systemowy do połączenia nawet w wyższych warunkach CPU.  Niewielka wartość limitu czasu połączenia nie gwarantuje, że połączenie jest ustanowione w tym czasie.  Jeśli coś się nie powiedzie (wysoki procesor CPU klienta, wysoki procesor CPU i tak dalej), wówczas wartość limitu czasu połączenia zostanie spowodowana błędem połączenia. Takie zachowanie często sprawia, że zła sytuacja jest gorsza.  W przeciwieństwie do krótszych limitów czasu pogłębić problem, wymuszając system, aby ponownie uruchomić proces próby ponownego nawiązania połączenia, co może prowadzić do *niepowodzenia > pętli ponawiania próby połączenia >* . Zwykle zaleca się pozostawienie limitu czasu połączenia co 15 sekund. Lepszym rozwiązaniem jest ponowienie próby połączenia po 15 lub 20 sekundach, aby szybko się nie przekroczyć. Taka pętla ponawiania może spowodować wydłużenie czasu przestoju, niż w przypadku dłuższego wydłużenia systemu.  
     > [!NOTE]
     > Wskazówki te są specyficzne dla *próby połączenia* i nie są związane z czasem oczekiwania na wykonanie *operacji* , takich jak pobieranie lub Ustawianie.
 
 * **Unikaj kosztownych operacji** — niektóre operacje Redis, takie jak [klucze](https://redis.io/commands/keys) , są *bardzo* kosztowne i powinny być nieuniknione.  Aby uzyskać więcej informacji, zobacz Zagadnienia dotyczące [długotrwałych poleceń](cache-troubleshoot-server.md#long-running-commands)

 * **Korzystanie z szyfrowania TLS** — usługa Azure cache for Redis domyślnie wymaga komunikacji szyfrowanej przy użyciu protokołu TLS.  Protokoły TLS w wersji 1,0, 1,1 i 1,2 są obecnie obsługiwane.  Jednak protokoły TLS 1,0 i 1,1 znajdują się na ścieżce, aby wycofać całe branże, więc Użyj protokołu TLS 1,2, jeśli jest to możliwe.  Jeśli Biblioteka klienta lub narzędzie nie obsługuje protokołu TLS, można włączyć nieszyfrowane połączenia [za pomocą Azure Portal](cache-configure.md#access-ports) lub [interfejsów API zarządzania](/rest/api/redis/redis/update).  W takich przypadkach, gdy połączenia szyfrowane nie są możliwe, będzie zalecane umieszczenie pamięci podręcznej i aplikacji klienckiej w sieci wirtualnej.  Aby uzyskać więcej informacji o portach używanych w scenariuszu pamięci podręcznej sieci wirtualnej, zapoznaj się z tą [tabelą](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
 * **Limit czasu bezczynności** — usługa Azure Redis ma obecnie 10-minutowe przekroczenie limitu czasu bezczynności dla połączeń, więc wartość tego ustawienia powinna być mniejsza niż 10 minut.
 
## <a name="memory-management"></a>Zarządzanie pamięcią
Istnieje kilka rzeczy związanych z użyciem pamięci w wystąpieniu serwera Redis, które warto wziąć pod uwagę.  Oto kilka z nich:

 * **Wybierz [Zasady wykluczania](https://redis.io/topics/lru-cache) , które działają dla aplikacji.**  Zasady domyślne dla usługi Azure Redis to *volatile-LRU*, co oznacza, że tylko klucze mające ustawioną wartość TTL będą kwalifikować się do wykluczenia.  Jeśli żadne klucze nie mają wartości TTL, system nie wykluczają żadnych kluczy.  Jeśli chcesz, aby system zezwalał na każdy klucz, który ma zostać wykluczony w przypadku wykorzystania pamięci, warto rozważyć zasady *AllKeys-LRU* .

 * **Ustaw wartość wygaśnięcia kluczy.**  Wygaśnięcie spowoduje usunięcie kluczy aktywnie, zamiast czekać do momentu wyczerpania pamięci.  Gdy wykluczenie zostanie rozpoczęte ze względu na wykorzystanie pamięci, może to spowodować dodatkowe obciążenie serwera.  Aby uzyskać więcej informacji, zobacz dokumentację poleceń [wygaśnięcia](https://redis.io/commands/expire) i [EXPIREAT](https://redis.io/commands/expireat) .
 
## <a name="client-library-specific-guidance"></a>Wskazówki dotyczące biblioteki klienta
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java — którego klienta należy użyć?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sałata (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Dostawca stanu sesji ASP.NET](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Kiedy można bezpiecznie ponowić próbę?
Niestety, nie ma żadnej prostej odpowiedzi.  Każda aplikacja musi zdecydować, jakie operacje mogą być ponawiane, a które nie.  Każda operacja ma inne wymagania i zależności między kluczami.  Oto kilka rzeczy, które można wziąć pod uwagę:

 * Można pobrać błędy po stronie klienta, nawet jeśli Redis pomyślnie uruchomił polecenie, które prosi o jego uruchomienie.  Na przykład:
     - Limity czasu to koncepcje po stronie klienta.  Jeśli operacja osiągnęła serwer, na serwerze zostanie uruchomione polecenie, nawet jeśli klient nawiąże oczekiwanie.  
     - Gdy w połączeniu z gniazdem wystąpi błąd, nie można sprawdzić, czy operacja rzeczywiście została uruchomiona na serwerze.  Na przykład błąd połączenia może wystąpić po przetworzeniu żądania przez serwer, ale przed odebraniem odpowiedzi przez klienta.
 *  Jak reaguje moja aplikacja, jeśli przypadkowo uruchamiasz tę samą operację dwukrotnie?  Na przykład, co w przypadku zwiększenia liczby całkowitej dwa razy zamiast raz?  Czy moja aplikacja zapisuje się w tym samym kluczu z wielu miejsc?  Co zrobić, jeśli moja logika ponawiania zastępuje wartość ustawioną przez inną część mojej aplikacji?

Jeśli chcesz przetestować działanie kodu w warunkach błędów, rozważ użycie [funkcji ponownego uruchamiania](cache-administration.md#reboot). Ponowny rozruch pozwala zobaczyć, jak Blips połączeń wpływa na aplikację.

## <a name="performance-testing"></a>Testowanie wydajności
 * **Zacznij od użycia `redis-benchmark.exe`** w celu uzyskania potencjalnej przepływności/opóźnienia przed napisaniem własnych testów wydajności.  Dokumentację Redis-testową można [znaleźć tutaj](https://redis.io/topics/benchmarks).  Należy pamiętać, że Redis-testowe nie obsługuje protokołu TLS, dlatego należy [włączyć port bez protokołu TLS w portalu](cache-configure.md#access-ports) przed uruchomieniem testu.  [Zgodną z systemem Windows wersję redis-benchmark.exe można znaleźć tutaj](https://github.com/MSOpenTech/redis/releases)
 * Maszyna wirtualna klienta użyta do testowania powinna znajdować się **w tym samym regionie** co wystąpienie pamięci podręcznej Redis.
 * **Zalecamy używanie serii maszyn wirtualnych Dv2** dla klienta, ponieważ mają one lepszy sprzęt i dają najlepsze wyniki.
 * Upewnij się, że używana maszyna wirtualna klienta ma **co najmniej tyle obliczeń i przepustowości* jako przetestowanej pamięci podręcznej. 
 * Jeśli korzystasz z systemu Windows, **Włącz opcję wirtualnego skalowania** na komputerze klienckim.  [Zobacz tutaj, aby uzyskać szczegółowe informacje](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  Przykładowy skrypt programu PowerShell:
     >PowerShell-ExecutionPolicy unstricted Enable-NetAdapterRSS-Name (Get-ServiceAdapter). Nazwij 
     
 * **Rozważ użycie wystąpień usługi Redis w warstwie Premium**.  Te rozmiary pamięci podręcznej będą mieć większe opóźnienia sieci i przepływność, ponieważ działają na lepszym sprzęcie dla obu procesorów i sieci.
 
     > [!NOTE]
     > Nasze obserwowane wyniki wydajności są [publikowane w tym miejscu](cache-planning-faq.md#azure-cache-for-redis-performance) dla odwołania.   Należy również pamiętać, że protokół SSL/TLS dodaje pewne obciążenie, dzięki czemu możesz uzyskać różne opóźnienia i/lub przepływność, jeśli używasz szyfrowania transportowego.
 
### <a name="redis-benchmark-examples"></a>Przykłady Redis-Benchmark
**Konfiguracja przed testami**: Przygotuj wystąpienie pamięci podręcznej z danymi wymaganymi dla poleceń testowania opóźnienia i przepływności wymienionych poniżej.
> Redis-test-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**W celu przetestowania opóźnienia**: Przetestuj żądania GET przy użyciu ładunku 1K.
> Redis-test-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

**Aby przetestować przepływność:** Potok żądania GET z ładunkiem.
> Redis-test-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50