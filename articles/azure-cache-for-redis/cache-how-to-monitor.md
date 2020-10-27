---
title: Jak monitorować usługę Azure cache for Redis
description: Dowiedz się, jak monitorować kondycję i wydajność swojej pamięci podręcznej platformy Azure dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 93ac9a0b8766da70a55ac04f864fe48106fe8774
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536746"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Jak monitorować usługę Azure cache for Redis

Usługa Azure cache for Redis używa [Azure monitor](../azure-monitor/index.yml) , aby zapewnić kilka opcji monitorowania wystąpień pamięci podręcznej. Możesz wyświetlić metryki, przypiąć wykresy metryk do tablicy startowej, dostosować zakres daty i godziny wykresów monitorowania, dodać i usunąć metryki z wykresów i ustawić alerty w przypadku spełnienia określonych warunków. Te narzędzia umożliwiają monitorowanie kondycji pamięci podręcznej platformy Azure dla wystąpień Redis i ułatwiają zarządzanie aplikacjami buforowania.

Metryki usługi Azure cache dla wystąpień Redis są zbierane za pomocą polecenia [info](https://redis.io/commands/info) Redis około dwa razy na minutę i automatycznie przechowywane przez 30 dni (zobacz [Eksportowanie metryk pamięci podręcznej](#export-cache-metrics) w celu skonfigurowania innych zasad przechowywania), aby mogły być wyświetlane na wykresach metryk i ocenione przez reguły alertów. Aby uzyskać więcej informacji na temat różnych wartości informacji używanych dla każdej metryki pamięci podręcznej, zobacz [Dostępne metryki i interwały raportowania](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Aby wyświetlić metryki pamięci podręcznej, [Przejdź](cache-configure.md#configure-azure-cache-for-redis-settings) do wystąpienia pamięci podręcznej w [Azure Portal](https://portal.azure.com).  Usługa Azure cache for Redis zawiera niektóre wbudowane wykresy w bloku **Przegląd** i blok **metryk Redis** . Każdy wykres można dostosować przez dodanie lub usunięcie metryk oraz zmianę interwału raportowania.

![Metryki Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Wyświetl wstępnie skonfigurowane wykresy metryk

Blok **przeglądu** zawiera następujące wstępnie skonfigurowane wykresy monitorowania.

* [Wykresy monitorowania](#monitoring-charts)
* [Wykresy użycia](#usage-charts)

### <a name="monitoring-charts"></a>Wykresy monitorowania

Sekcja **monitorowanie** w bloku **Przegląd** zawiera wartości **trafień i chybień** , **pobieranie i zestawy** , **połączenia** oraz całkowite wykresy **poleceń** .

![Wykresy monitorowania](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Wykresy użycia

Sekcja **użycie** w bloku **Przegląd** ma **Redis obciążenia serwera** , **użycie pamięci** , **przepustowość sieci** i wykresy **użycia procesora CPU** , a także wyświetla **warstwę cenową** dla wystąpienia pamięci podręcznej.

![Wykresy użycia](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Warstwa cenowa** zawiera warstwę cenową pamięci podręcznej i może służyć do [skalowania](cache-how-to-scale.md) pamięci podręcznej do innej warstwy cenowej.

## <a name="view-metrics-with-azure-monitor"></a>Wyświetlanie metryk przy użyciu usługi Azure monitor

Aby wyświetlić metryki Redis i utworzyć wykresy niestandardowe przy użyciu Azure Monitor, kliknij pozycję **metryki** w **menu zasób** i Dostosuj wykres przy użyciu żądanych metryk, interwału raportowania, typu wykresu i innych.

![Metryki Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Aby uzyskać więcej informacji na temat pracy z metrykami przy użyciu Azure Monitor, zobacz [Omówienie metryk w Microsoft Azure](../azure-monitor/platform/data-platform.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Eksportowanie metryk pamięci podręcznej

Domyślnie metryki pamięci podręcznej w Azure Monitor są [przechowywane przez 30 dni](../azure-monitor/platform/data-platform-metrics.md) , a następnie usuwane. Aby zachować metryki pamięci podręcznej przez dłużej niż 30 dni, można [wyznaczyć konto magazynu](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) i określić zasady **przechowywania (dni)** dla metryk pamięci podręcznej. 

Aby skonfigurować konto magazynu dla metryk pamięci podręcznej:

1. Na stronie **usługi Azure cache for Redis** w obszarze nagłówek **monitorowania** wybierz pozycję **Diagnostyka** .
2. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne** .
3. Nadaj nazwę ustawieniom.
4. Zaznacz pozycję **Zarchiwizuj na koncie magazynu** . W przypadku wysyłania danych diagnostycznych do konta magazynu będą naliczone zwykłe stawki za dane związane z magazynowaniem i transakcjami.
4. Wybierz pozycję **Konfiguruj** , aby wybrać konto magazynu, w którym mają być przechowywane metryki pamięci podręcznej.
5. W obszarze pole **Metryka** nagłówka tabeli obok elementów wiersza, które mają być przechowywane, takich jak **AllMetrics** . Określ zasady **przechowywania (dni)** . Maksymalne dozwolone okresy przechowywania to **365 dni** . Jeśli jednak chcesz zachować dane metryk w nieskończoność, ustaw wartość **przechowywanie (w dniach)** na **0** .
6. Kliknij pozycję **Zapisz** .


![Diagnostyka Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Oprócz archiwizowania metryk pamięci podręcznej do magazynu można także [przesyłać strumieniowo do centrum zdarzeń lub wysyłać je do dzienników Azure monitor](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>

Aby uzyskać dostęp do metryk, można je wyświetlić w Azure Portal, jak opisano wcześniej w tym artykule, a także uzyskać do nich dostęp za pomocą [interfejsu API REST metryk Azure monitor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Jeśli zmienisz konta magazynu, dane na wcześniej skonfigurowanym koncie magazynu pozostaną dostępne do pobrania, ale nie będą wyświetlane w Azure Portal.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Dostępne metryki i interwały raportowania

Metryki pamięci podręcznej są raportowane przy użyciu kilku interwałów raportowania, w tym **ostatnich godzin** , **dzisiaj** , **ostatnich tygodni** i **niestandardowych** . Blok **metryk** dla każdego wykresu metryk zawiera wartości średnie, minimum i maksimum dla każdej metryki na wykresie, a niektóre metryki zawierają sumę dla interwału raportowania. 

Każda Metryka zawiera dwie wersje. Jedna Metryka mierzy wydajność dla całej pamięci podręcznej, a dla pamięci podręcznej korzystającej z [klastrowania](cache-how-to-premium-clustering.md)druga wersja metryki, która obejmuje `(Shard 0-9)` nazwę mierzy wydajność dla jednego fragmentu w pamięci podręcznej. Na przykład jeśli pamięć podręczna ma cztery fragmentów, `Cache Hits` to łączna liczba trafień dla całej pamięci podręcznej i `Cache Hits (Shard 3)` jest to tylko liczba trafień dla tego fragmentu pamięci podręcznej.

> [!NOTE]
> Nawet wtedy, gdy pamięć podręczna jest bezczynna bez podłączonych aktywnych aplikacji klienckich, może zostać wyświetlona część działania pamięci podręcznej, na przykład połączone Klienci, użycie pamięci i operacje. To działanie jest normalne w trakcie działania wystąpienia usługi Azure cache for Redis.
> 
> 

| Metryka | Opis |
| --- | --- |
| Trafienia w pamięci podręcznej |Liczba pomyślnych wyszukiwań kluczy w określonym interwale raportowania. Ta liczba jest mapowana na wartość `keyspace_hits` z Redis [info](https://redis.io/commands/info) . |
| Opóźnienie pamięci podręcznej (wersja zapoznawcza) | Opóźnienie pamięci podręcznej obliczone na podstawie opóźnienia międzywęzłowego pamięci podręcznej. Ta Metryka jest mierzona w mikrosekundach i ma trzy wymiary: `Avg` , `Min` , i `Max` , które reprezentują średnią, minimalną i maksymalną liczbę opóźnień pamięci podręcznej odpowiednio do określonego interwału raportowania. |
| Chybienia w pamięci podręcznej |Liczba nieudanych wyszukiwań kluczy w określonym interwale raportowania. Ta liczba jest mapowana na wartość `keyspace_misses` z REDIS info. Chybienia w pamięci podręcznej nie muszą oznaczać, że występuje problem z pamięcią podręczną. Na przykład w przypadku korzystania ze wzorca programowania z obsługą pamięci podręcznej aplikacja najpierw szuka elementu w pamięci podręcznej. Jeśli element nie istnieje (chybień w pamięci podręcznej), element zostanie pobrany z bazy danych i dodany do pamięci podręcznej za następnym razem. Chybienia w pamięci podręcznej są normalnym zachowaniem wzorca programowania w pamięci podręcznej. Jeśli liczba chybień w pamięci podręcznej jest większa niż oczekiwana, należy zapoznać się z logiką aplikacji, która wypełnia i odczytuje dane z pamięci podręcznej. Jeśli elementy są usuwane z pamięci podręcznej ze względu na wykorzystanie pamięci, może wystąpić kilka chybień pamięci podręcznej, ale lepszym rozwiązaniem jest monitorowanie pod kątem wykorzystania pamięci `Used Memory` lub `Evicted Keys` . |
| Odczyt pamięci podręcznej |Ilość danych odczytanych z pamięci podręcznej w megabajtach na sekundę (MB/s) podczas określonego interwału raportowania. Ta wartość pochodzi z kart interfejsu sieciowego, które obsługują maszynę wirtualną obsługującą pamięć podręczną, i nie Redis określonych. **Ta wartość odpowiada przepustowości sieci używanej przez tę pamięć podręczną. Jeśli chcesz skonfigurować alerty dla limitów przepustowości sieci po stronie serwera, utwórz je przy użyciu tego `Cache Read` licznika. [Poniższa tabela](cache-planning-faq.md#azure-cache-for-redis-performance) zawiera szczegółowe limity przepustowości dla różnych warstw i rozmiarów cenowych pamięci podręcznej.** |
| Zapis w pamięci podręcznej |Ilość danych zapisywana w pamięci podręcznej w megabajtach na sekundę (MB/s) podczas określonego interwału raportowania. Ta wartość pochodzi z kart interfejsu sieciowego, które obsługują maszynę wirtualną obsługującą pamięć podręczną, i nie Redis określonych. Ta wartość odpowiada przepustowości sieci danych wysyłanych do pamięci podręcznej z klienta programu. |
| Połączeni klienci |Liczba połączeń klienta z pamięcią podręczną w określonym interwale raportowania. Ta liczba jest mapowana na wartość `connected_clients` z REDIS info. Po osiągnięciu [limitu połączenia](cache-configure.md#default-redis-server-configuration) kolejne próby połączenia z pamięcią podręczną zakończą się niepowodzeniem. Nawet jeśli nie ma aktywnych aplikacji klienckich, nadal może istnieć kilka wystąpień połączonych klientów z powodu wewnętrznych procesów i połączeń. |
| Procesor CPU |Użycie procesora CPU w pamięci podręcznej platformy Azure dla serwera Redis jako wartość procentowa w określonym interwale raportowania. Ta wartość jest mapowana na `\Processor(_Total)\% Processor Time` licznik wydajności systemu operacyjnego. |
| błędy | Określone błędy i problemy z wydajnością, które mogą występować w pamięci podręcznej w określonym interwale raportowania. Ta Metryka ma osiem wymiarów reprezentujących różne typy błędów, ale w przyszłości może być dodanych. Reprezentowane typy błędów są następujące: <br/><ul><li>Tryb failover — gdy pamięć podręczna zostanie przełączona w tryb **pracy awaryjnej** (podrzędna prom do podstawowego)</li><li>**Datastrata** — w przypadku utraty danych w pamięci podręcznej</li><li>**UnresponsiveClients** — gdy klienci nie odczytują danych z serwera wystarczająco szybko</li><li>**Kopia zapasowa AOF** — w przypadku problemów związanych z TRWAŁOŚCIą kopia zapasowa AOF</li><li>**RDB** — w przypadku wystąpienia problemu związanego z TRWAŁOŚCIą RDB</li><li>**Importuj** — w przypadku problemu związanego z IMPORTem RDB</li><li>**Eksport** — w przypadku problemu związanego z EKSPORTem RDB</li></ul> |
| Wykluczone klucze |Liczba elementów wykluczonych z pamięci podręcznej w określonym interwale raportowania ze względu na `maxmemory` limit. Ta liczba jest mapowana na wartość `evicted_keys` z REDIS info. |
| Wygasłe klucze |Liczba elementów, które wygasły z pamięci podręcznej w określonym interwale raportowania. Ta wartość jest mapowana na `expired_keys` podstawie polecenia REDIS info.|
| Pobrania |Liczba operacji pobierania z pamięci podręcznej w określonym interwale raportowania. Ta wartość jest sumą następujących wartości z polecenia Redis info All:,,,, `cmdstat_get` , `cmdstat_hget` `cmdstat_hgetall` `cmdstat_hmget` `cmdstat_mget` `cmdstat_getbit` i `cmdstat_getrange` , i jest równoważna z sumą trafień w pamięci podręcznej i chybień w okresie raportowania. |
| Liczba operacji na sekundę | Całkowita liczba poleceń przetworzonych na sekundę przez serwer pamięci podręcznej w określonym interwale raportowania.  Ta wartość mapuje na "instantaneous_ops_per_sec" z Redis INFO. |
| Ładowanie serwera Redis |Procent cykli, w których serwer Redis jest zajęty przetwarzaniem i nie oczekuje na bezczynność komunikatów. Jeśli licznik osiągnie wartość 100, oznacza to, że serwer Redis osiągnął limit wydajności, a procesor CPU nie może przetwarzać pracy szybciej. Jeśli widzisz duże obciążenie serwera Redis, zobaczysz wyjątki limitu czasu na kliencie. W takim przypadku należy rozważyć skalowanie w górę lub partycjonowanie danych w wielu pamięciach podręcznych. |
| Zestawy |Liczba operacji ustawiania w pamięci podręcznej w określonym interwale raportowania. Ta wartość jest sumą następujących wartości z polecenia Redis info All:,,,,,,,,, `cmdstat_set` `cmdstat_hset` `cmdstat_hmset` `cmdstat_hsetnx` `cmdstat_lset` `cmdstat_mset` `cmdstat_msetnx` `cmdstat_setbit` `cmdstat_setex` `cmdstat_setrange` i `cmdstat_setnx` . |
| Łączna liczba kluczy  | Maksymalna liczba kluczy w pamięci podręcznej w okresie ostatniej sprawozdawczej. Ta liczba jest mapowana na wartość `keyspace` z REDIS info. Ze względu na ograniczenie podstawowego systemu metryk dla pamięci podręcznej z włączoną obsługą klastrowania, funkcja łączna liczba kluczy zwraca maksymalną liczbę kluczy fragmentu, która miała maksymalną liczbę kluczy w interwale raportowania.  |
| Łączna liczba operacji |Całkowita liczba poleceń przetworzonych przez serwer pamięci podręcznej w określonym interwale raportowania. Ta wartość jest mapowana na `total_commands_processed` podstawie polecenia REDIS info. Gdy pamięć podręczna Azure for Redis jest używana wyłącznie w przypadku publikowania/obsługi, nie będą dostępne żadne metryki dla `Cache Hits` ,, `Cache Misses` `Gets` lub `Sets` , ale będą to `Total Operations` metryki odzwierciedlające użycie pamięci podręcznej dla operacji publikowania/podprocedur. |
| Użyta pamięć |Ilość pamięci podręcznej używana dla par klucz/wartość w pamięci podręcznej w MB w określonym interwale raportowania. Ta wartość jest mapowana na `used_memory` podstawie polecenia REDIS info. Ta wartość nie obejmuje metadanych ani fragmentacji. |
| Procentowe użycie pamięci | % Całkowitej ilości pamięci używanej w określonym interwale raportowania.  Ta wartość odwołuje się `used_memory` do wartości z polecenia REDIS info w celu obliczenia wartości procentowej. |
| Używana pamięć RSS |Ilość pamięci podręcznej używana w MB podczas określonego interwału raportowania, w tym fragmentacja i metadane. Ta wartość jest mapowana na `used_memory_rss` podstawie polecenia REDIS info. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alerty

Można skonfigurować odbieranie alertów w oparciu o metryki i dzienniki aktywności. Usługa Azure Monitor umożliwia skonfigurowanie alertu powodującego wykonywanie następujących czynności po jego wyzwoleniu:

* Wysyłanie powiadomienia e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Aby skonfigurować reguły alertów dla pamięci podręcznej, kliknij pozycję **reguły alertów** w **menu zasób** .

![Monitorowanie](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Aby uzyskać więcej informacji o konfigurowaniu i używaniu alertów, zobacz [Omówienie alertów](../azure-monitor/platform/alerts-classic-portal.md).

## <a name="activity-logs"></a>Dzienniki aktywności
Dzienniki aktywności zapewniają wgląd w operacje wykonane w pamięci podręcznej platformy Azure dla wystąpień Redis. Wcześniej była znana jako "dzienniki inspekcji" lub "dzienniki operacyjne". Korzystając z dzienników aktywności, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) podejmowanych w pamięci podręcznej platformy Azure dla wystąpień Redis. 

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji odczytu (GET).
>

Aby wyświetlić dzienniki aktywności pamięci podręcznej, kliknij pozycję **dzienniki aktywności** w **menu zasób** .

Aby uzyskać więcej informacji na temat dzienników aktywności, zobacz [Omówienie dziennika aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).