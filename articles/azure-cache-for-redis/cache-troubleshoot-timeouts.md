---
title: Rozwiązywanie problemów z limitami czasu usługi Azure Cache for Redis
description: Dowiedz się, jak rozwiązywać typowe problemy z przekroczeniem limitu czasu w usłudze Azure cache for Redis, takie jak Redis serwera poprawek i StackExchange. Redis wyjątki dotyczące limitu czasu.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: bf8b20dadd2fcd78657aa6877e796b645332dd94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213449"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Rozwiązywanie problemów z limitami czasu usługi Azure Cache for Redis

W tej sekcji omówiono problemy z limitem czasu rozwiązywania problemów występujących podczas nawiązywania połączenia z usługą Azure cache for Redis.

- [Stosowanie poprawek do serwera Redis](#redis-server-patching)
- [Wyjątki limitu czasu StackExchange. Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Kilka kroków rozwiązywania problemów zawartych w tym przewodniku zawiera instrukcje dotyczące uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z artykułami w sekcji [Informacje dodatkowe](#additional-information) .
>

## <a name="redis-server-patching"></a>Stosowanie poprawek do serwera Redis

Usługa Azure cache for Redis regularnie aktualizuje oprogramowanie serwera w ramach udostępnianych przez nią funkcji usługi zarządzanej. To działanie [poprawiania](cache-failover.md) odbywa się w dużej mierze za sceną. Podczas pracy w trybie failover w przypadku stosowania poprawek do węzłów serwera Redis klienci Redis połączeni z tymi węzłami mogą napotkać tymczasowe limity czasu, ponieważ połączenia są przełączane między tymi węzłami. Zobacz, w [jaki sposób tryb failover ma wpływ na moją aplikację kliencką](cache-failover.md#how-does-a-failover-affect-my-client-application) , aby uzyskać więcej informacji na temat tego, jakie poprawki efektów ubocznych mogą znajdować się w aplikacji oraz jak można poprawić obsługę poprawek zdarzeń.

## <a name="stackexchangeredis-timeout-exceptions"></a>Wyjątki limitu czasu StackExchange. Redis

StackExchange. Redis używa ustawienia konfiguracji o nazwie `synctimeout` dla operacji synchronicznych z wartością domyślną 5000 ms. Jeśli wywołanie synchroniczne nie zostanie ukończone w tym czasie, klient StackExchange. Redis zgłasza błąd limitu czasu podobny do następującego przykładu:

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

Ten komunikat o błędzie zawiera metryki, które mogą ułatwić wskazanie przyczyny i ewentualne rozwiązanie problemu. Poniższa tabela zawiera szczegółowe informacje o metrykach komunikatów o błędach.

| Metryka komunikatu o błędzie | Szczegóły |
| --- | --- |
| powrotu |W ostatnim czasie: 0 poleceń zostało wystawione |
| Menedżer |Menedżer gniazda działa `socket.select` , co oznacza, że prosi o system operacyjny, aby wskazać gniazdo, które ma coś do zrobienia. Czytnik nie jest aktywnie czytany z sieci, ponieważ nie ma znaczenia, czy nie ma nic do zrobienia |
| kolejka |Liczba operacji w toku wynosi 73 |
| qu |6 operacji w toku znajdują się w niewysłanej kolejce i nie została jeszcze zapisywana w sieci wychodzącej |
| qs |67 operacji w toku zostały wysłane do serwera, ale odpowiedź nie jest jeszcze dostępna. Odpowiedź może być `Not yet sent by the server` lub `sent by the server but not yet processed by the client.` |
| składnika |0 z operacji w toku ma przejrzane odpowiedzi, ale nie zostały jeszcze oznaczone jako ukończone, ponieważ oczekują na pętlę ukończenia |
| oznacza |Istnieje aktywny składnik zapisywania (oznacza to, że 6 niewysłanych żądań nie jest ignorowany) bajty/activewriters |
| in |Brak aktywnych czytników i zero bajtów jest dostępnych do odczytu na karcie sieciowej bajty/activereaders |

Aby zbadać możliwe przyczyny główne, można wykonać następujące czynności.

1. Najlepszym rozwiązaniem jest upewnienie się, że do nawiązania połączenia przy użyciu klienta StackExchange. Redis jest używany następujący wzorzec.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z pamięcią podręczną przy użyciu stackexchange. Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Upewnij się, że serwer i aplikacja kliencka znajdują się w tym samym regionie na platformie Azure. Można na przykład otrzymywać limity czasu, gdy pamięć podręczna jest w regionie Wschodnie stany USA, ale klient znajduje się w zachodnich Stanach Zjednoczonych, a żądanie nie zakończy się w `synctimeout` przedziale czasowym lub podczas debugowania z lokalnego komputera deweloperskiego może wystąpić limit czasu. 

    Zdecydowanie zaleca się używanie pamięci podręcznej i klienta w tym samym regionie świadczenia usługi Azure. Jeśli masz scenariusz, który obejmuje wywołania między regionami, należy ustawić `synctimeout` interwał na wartość większą niż domyślny interwał 5000-ms, dołączając `synctimeout` Właściwość w parametrach połączenia. W poniższym przykładzie przedstawiono fragment kodu połączenia dla StackExchange. Redis udostępniony przez pamięć podręczną platformy Azure dla Redis z `synctimeout` 2000 MS.

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. Upewnij się, że używasz najnowszej wersji [pakietu NuGet stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/). Błędy są ciągle naprawiane w kodzie, aby zwiększyć jego niezawodność, tak aby Najnowsza wersja była ważna.
1. Jeśli żądania są związane z ograniczeniami przepustowości na serwerze lub kliencie, trwają one dłużej i mogą spowodować przekroczenie limitu czasu. Aby sprawdzić, czy limit czasu jest równy przepustowości sieci na serwerze, zobacz [ograniczenia przepustowości po stronie serwera](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Aby sprawdzić, czy limit czasu jest równy przepustowości sieci klienta, zobacz [ograniczenia przepustowości po stronie klienta](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Czy jest uzyskiwany procesor CPU związany z serwerem lub klientem?

   - Sprawdź, czy na kliencie są powiązane zasoby procesora CPU. Wysoki procesor CPU może spowodować, że żądanie nie zostanie przetworzone w `synctimeout` interwale i spowoduje przekroczenie limitu czasu żądania. Przechodzenie do większego rozmiaru klienta lub rozpowszechnianie obciążenia może pomóc w kontroli tego problemu.
   - Sprawdź, czy jest wyświetlany procesor CPU związany z serwerem przez monitorowanie [metryki wydajności pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)procesora CPU. Żądania wysyłane w czasie, gdy Redis, są związane z PROCESORem, mogą spowodować przekroczenie limitu czasu tych żądań. Aby rozwiązać ten problem, można rozesłać obciążenie między wieloma fragmentów w pamięci podręcznej Premium lub uaktualnić do większego rozmiaru lub warstwy cenowej. Aby uzyskać więcej informacji, zobacz [ograniczenia przepustowości po stronie serwera](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Czy istnieją polecenia trwające długotrwałe przetwarzanie na serwerze? Długotrwałe polecenia, które zajmują dużo czasu na przetworzenie na serwerze Redis, mogą spowodować przekroczenie limitu czasu. Aby uzyskać więcej informacji na temat długotrwałych poleceń, zobacz [wykonywanie długotrwałych poleceń](cache-troubleshoot-server.md#long-running-commands). Można nawiązać połączenie ze swoim wystąpieniem usługi Azure cache for Redis za pomocą klienta Redis-CLI lub [konsoli Redis](cache-configure.md#redis-console). Następnie uruchom polecenie [SLOWLOG](https://redis.io/commands/slowlog) , aby sprawdzić, czy żądania są wolniejsze niż oczekiwano. Serwery Redis i StackExchange. Redis są zoptymalizowane pod kątem wielu małych żądań, a nie mniej dużych żądań. Dzielenie danych na mniejsze fragmenty może poprawić się w tym miejscu.

    Aby uzyskać informacje na temat nawiązywania połączenia z punktem końcowym protokołu TLS/SSL w pamięci podręcznej przy użyciu Redis-CLI i stunnel, zobacz wpis w blogu [informujący dostawca stanu sesji ASP.NET dla Redis wersja zapoznawcza](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).
1. Duże obciążenie serwera Redis może spowodować przekroczenie limitu czasu. Obciążenie serwera można monitorować przez monitorowanie `Redis Server Load` [metryki wydajności pamięci podręcznej](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Obciążenie serwera 100 (wartość maksymalna) oznacza, że serwer Redis został zajęty, bez czasu bezczynności przetwarzania żądań. Aby sprawdzić, czy niektóre żądania zajmują wszystkie możliwości serwera, uruchom polecenie SlowLog, zgodnie z opisem w poprzednim akapicie. Aby uzyskać więcej informacji, zobacz wysokie użycie procesora CPU/obciążenie serwera.
1. Czy po stronie klienta było jakieś inne zdarzenie, które mogło spowodować Blip sieci? Typowe zdarzenia obejmują: skalowanie liczby wystąpień klienta w górę lub w dół, wdrażanie nowej wersji klienta lub Skalowanie automatyczne. W naszych testach znaleźliśmy, że automatyczne skalowanie lub skalowanie w górę/w dół może spowodować utratę połączenia sieciowego wychodzącego przez kilka sekund. Kod StackExchange. Redis jest odporny na takie zdarzenia i ponownie nawiązuje połączenie. Po ponownym nawiązaniu połączenia wszystkie żądania w kolejce mogą przekroczyć limit czasu.
1. Czy masz duże żądanie poprzedzające kilka małych żądań do pamięci podręcznej, które przekroczyły limit czasu? Parametr `qs` w komunikacie o błędzie informuje o liczbie żądań wysłanych z klienta do serwera, ale nie przetworzył odpowiedzi. Ta wartość może nadal wzrastać, ponieważ StackExchange. Redis używa jednego połączenia TCP i może odczytywać tylko jedną odpowiedź w danym momencie. Pomimo tego, że upłynął limit czasu pierwszej operacji, nie zatrzyma on wysyłania więcej danych do lub z serwera. Inne żądania będą blokowane do momentu zakończenia dużego żądania i mogą spowodować przekroczenie limitu czasu. Jednym z rozwiązań jest zminimalizowanie możliwości przekroczenia limitu czasu przez zapewnienie, że pamięć podręczna jest wystarczająco duża dla obciążenia i dzielenie dużych wartości na mniejsze fragmenty. Innym możliwym rozwiązaniem jest użycie puli `ConnectionMultiplexer` obiektów w kliencie i wybranie najmniejszego ładowania `ConnectionMultiplexer` podczas wysyłania nowego żądania. Ładowanie wielu obiektów połączeń powinno uniemożliwić przekroczenie limitu czasu dla innych żądań.
1. Jeśli używasz programu `RedisSessionStateProvider` , upewnij się, że ustawiono prawidłowy limit czasu ponowień. `retryTimeoutInMilliseconds` powinna być większa niż `operationTimeoutInMilliseconds` , w przeciwnym razie nie wystąpią żadne ponowne próby. W poniższym przykładzie `retryTimeoutInMilliseconds` ustawiono 3000. Aby uzyskać więcej informacji, zobacz [ASP.NET Session State Provider for Azure cache for Redis](cache-aspnet-session-state-provider.md) i [jak używać parametrów konfiguracji dostawcy stanu sesji i wyjściowego dostawcy pamięci podręcznej](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Sprawdź użycie pamięci w pamięci podręcznej platformy Azure dla serwera Redis przez [monitorowanie](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` i `Used Memory` . Jeśli zasady wykluczania są stosowane, Redis uruchamia wykluczenia kluczy, gdy `Used_Memory` osiągnie rozmiar pamięci podręcznej. W idealnym przypadku `Used Memory RSS` powinna być nieco większa niż `Used memory` . Duża różnica oznacza fragmentację pamięci (wewnętrzną lub zewnętrzną). Gdy `Used Memory RSS` jest mniejsza niż `Used Memory` , oznacza to, że część pamięci podręcznej została zamieniony przez system operacyjny. W przypadku wystąpienia tego zamiany można oczekiwać pewnych znaczących opóźnień. Ponieważ Redis nie ma kontroli nad sposobem, w jaki jej alokacje są mapowane na strony pamięci, wysoka `Used Memory RSS` jest często wynikiem wzrostu użycia pamięci. Gdy serwer Redis zwalnia pamięć, Alokator pobiera pamięć, ale może lub nie może przekazać pamięci z powrotem do systemu. Może wystąpić rozbieżność między `Used Memory` wartością i zużycie pamięci zgłoszone przez system operacyjny. Pamięć mogła zostać użyta i wydana przez Redis, ale nie została przestawiona z powrotem do systemu. Aby zmniejszyć problemy z pamięcią, można wykonać następujące czynności:

   - Uaktualnij pamięć podręczną do większego rozmiaru, aby nie uruchamiać ograniczeń pamięci w systemie.
   - Ustaw czasy wygaśnięcia kluczy w taki sposób, aby starsze wartości zostały wykluczone w sposób aktywny.
   - Monitoruj `used_memory_rss` metrykę pamięci podręcznej. Gdy ta wartość zbliża się do rozmiaru pamięci podręcznej, możesz zacząć wyświetlać problemy z wydajnością. Dystrybuuj dane w wielu fragmentówach, jeśli używasz pamięci podręcznej Premium lub przeprowadź uaktualnienie do większego rozmiaru pamięci podręcznej.

   Aby uzyskać więcej informacji, zobacz [wykorzystanie pamięci na serwerze Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta](cache-troubleshoot-client.md)
- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera](cache-troubleshoot-server.md)
- [Jak można sprawdzić i przetestować wydajność mojej pamięci podręcznej?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak monitorować usługę Azure cache for Redis](cache-how-to-monitor.md)
