---
title: Azure Cache for Redis — często zadawane pytania
description: Poznaj odpowiedzi na często zadawane pytania, wzorce oraz najlepsze rozwiązania dotyczące usługi Azure cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 89a87e1658f413b0a8cd757525450de30277d943
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086884"
---
# <a name="azure-cache-for-redis-faq"></a>Azure Cache for Redis — często zadawane pytania
Poznaj odpowiedzi na często zadawane pytania, wzorce oraz najlepsze rozwiązania dotyczące usługi Azure cache for Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli w tym miejscu nie udzielono odpowiedzi na moje pytanie?
Jeśli pytania nie ma na liście, poinformuj nas o tym, a my pomożemy Ci znaleźć odpowiedź.

* Aby dotrzeć do szerszego grona odbiorców, możesz ogłosić pytanie na stronie pytań i odpowiedzi [firmy Microsoft dotyczącej usługi Azure cache&](https://docs.microsoft.com/answers/topics/azure-cache-redis.html) i skontaktować się z zespołem usługi Azure cache i innymi członkami społeczności.
* Jeśli chcesz utworzyć żądanie dotyczące funkcji, możesz przesłać swoje żądania i pomysły do [usługi Azure cache for Redis](https://feedback.azure.com/forums/169382-cache).
* Możesz również wysłać do nas wiadomość e-mail na temat [zewnętrznej opinii w usłudze Azure cache](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Azure cache for Redis — podstawy
Często zadawane pytania przedstawione w tej sekcji dotyczą niektórych podstaw usługi Azure cache for Redis.

* [Co to jest usługa Azure Cache for Redis?](#what-is-azure-cache-for-redis)
* [Jak rozpocząć pracę z usługą Azure cache for Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Poniższe często zadawane pytania obejmują podstawowe pojęcia i pytania dotyczące usługi Azure cache for Redis oraz odpowiedzi na jedną z innych sekcji często zadawanych pytań.

* [Jakiej pamięci podręcznej platformy Azure dla oferty Redis i rozmiaru należy użyć?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Do jakiej usługi Azure cache for Redis klienci mogą korzystać?](#what-azure-cache-for-redis-clients-can-i-use)
* [Czy istnieje emulator lokalny dla usługi Azure cache for Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak mogę monitorować kondycję i wydajność mojej pamięci podręcznej?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planowanie często zadawanych pytań
* [Jakiej pamięci podręcznej platformy Azure dla oferty Redis i rozmiaru należy użyć?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Pamięć podręczna Azure dla wydajności Redis](#azure-cache-for-redis-performance)
* [W jakim regionie należy znaleźć moją pamięć podręczną?](#in-what-region-should-i-locate-my-cache)
* [Gdzie znajdują się dane z pamięci podręcznej?](#where-do-my-cached-data-reside)
* [Jak naliczane są opłaty za usługę Azure cache for Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Czy mogę używać usługi Azure cache for Redis z chmurą Azure Government, z platformy Azure w Chinach, w chmurze 21Vianet lub Microsoft Azure (Niemcy)?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Często zadawane pytania dotyczące programowania
* [Do czego służy opcja konfiguracji StackExchange. Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Do jakiej usługi Azure cache for Redis klienci mogą korzystać?](#what-azure-cache-for-redis-clients-can-i-use)
* [Czy istnieje emulator lokalny dla usługi Azure cache for Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak można uruchomić polecenia Redis?](#how-can-i-run-redis-commands)
* [Dlaczego usługa Azure cache for Redis ma bibliotekę klas MSDN, jak w przypadku niektórych innych usług platformy Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Czy można używać usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co to są bazy danych Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Często zadawane pytania dotyczące zabezpieczeń
* [Kiedy należy włączyć port bez protokołu TLS/SSL na potrzeby łączenia się z usługą Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Często zadawane pytania dotyczące produkcji
* [Jakie są najlepsze rozwiązania w zakresie produkcji?](#what-are-some-production-best-practices)
* [Jakie kwestie należy wziąć pod uwagę podczas korzystania z typowych poleceń Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak można sprawdzić i przetestować wydajność mojej pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Ważne szczegóły dotyczące wzrostu puli wątków](#important-details-about-threadpool-growth)
* [Włącz serwer GC, aby uzyskać większą przepływność na kliencie przy użyciu StackExchange. Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Zagadnienia dotyczące wydajności dotyczące połączeń](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorowanie pytań i rozwiązywanie problemów
Często zadawane pytania zawarte w tej sekcji dotyczą typowych pytań dotyczących monitorowania i rozwiązywania problemów. Aby uzyskać więcej informacji na temat monitorowania i rozwiązywania problemów z pamięcią podręczną platformy Azure dla wystąpień Redis, zobacz [Jak monitorować usługę Azure cache for Redis](cache-how-to-monitor.md) i różne przewodniki dotyczące rozwiązywania problemów.

* [Jak mogę monitorować kondycję i wydajność mojej pamięci podręcznej?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Dlaczego widzę limity czasu?](#why-am-i-seeing-timeouts)
* [Dlaczego mój klient odłączył się od pamięci podręcznej?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Wcześniejsza pamięć podręczna oferująca często zadawane pytania
* [Które oferty usługi Azure cache są odpowiednie dla mnie?](#which-azure-cache-offerings-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Co to jest usługa Azure Cache for Redis?
[Pamięć podręczna systemu Azure dla usługi Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) jest oparta na popularnej [Redis](https://redis.io/)oprogramowania open source. Zapewnia dostęp do bezpiecznej, dedykowanej pamięci podręcznej platformy Azure dla usługi Redis, zarządzanej przez firmę Microsoft i dostępnej z dowolnej aplikacji na platformie Azure. Aby zapoznać się z bardziej szczegółowym omówieniem, zobacz stronę [usługi Azure cache for Redis](https://azure.microsoft.com/services/cache/) .

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak rozpocząć pracę z usługą Azure cache for Redis?
Istnieje kilka sposobów rozpoczynania pracy z usługą Azure cache for Redis.

* Możesz zapoznać się z jednym z naszych samouczków dostępnych dla [platformy .NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), Java, [Node.js](cache-nodejs-get-started.md)i [języka](cache-java-get-started.md) [Python](cache-python-get-started.md).
* Możesz obejrzeć, [jak tworzyć aplikacje o wysokiej wydajności za pomocą pamięci Podręcznej Microsoft Azure Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Aby zobaczyć, jak korzystać z usługi Redis, można sprawdzić dokumentację klienta dla klientów zgodnych z językiem programowania projektu. Istnieje wielu klientów Redis, których można używać z usługą Azure cache dla Redis. Listę klientów Redis można znaleźć w temacie [https://redis.io/clients](https://redis.io/clients) .

Jeśli nie masz jeszcze konta platformy Azure, możesz:

* [Utworzyć bezpłatne konto platformy Azure ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Otrzymasz środki, które możesz wykorzystać do wypróbowania płatnych usług Azure. Nawet po wyczerpaniu tych środków możesz zachować konto i korzystać z bezpłatnych usług i funkcji platformy Azure.
* [Aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Jakiej pamięci podręcznej platformy Azure dla oferty Redis i rozmiaru należy użyć?
Każda usługa Azure cache for Redis oferuje różne poziomy **rozmiaru**, **przepustowości**, **wysokiej dostępności**i **umowy SLA** .

Poniżej przedstawiono zagadnienia dotyczące wybierania oferty pamięci podręcznej.

* **Pamięć**: warstwy Podstawowa i standardowa oferują 250 MB – 53 GB. Warstwa Premium oferuje do 1,2 TB (jako klaster) lub 120 GB (nieklastrowane). Aby uzyskać więcej informacji, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/).
* **Wydajność sieci**: Jeśli korzystasz z obciążenia, które wymaga dużej przepływności, warstwa Premium oferuje większą przepustowość w porównaniu do wersji Standard lub Basic. Ponadto w każdej warstwie pamięć podręczna o większym rozmiarze ma większą przepustowość ze względu na źródłową maszynę wirtualną, która obsługuje pamięć podręczną. Aby uzyskać więcej informacji, zobacz [poniższą tabelę](#cache-performance).
* **Przepływność**: warstwa Premium oferuje maksymalną dostępną przepływność. Jeśli serwer pamięci podręcznej lub klient osiągnie limity przepustowości, mogą pojawić się limity czasu po stronie klienta. Aby uzyskać więcej informacji, zobacz poniższą tabelę.
* **Wysoka dostępność/umowa SLA**: pamięć podręczna systemu Azure dla Redis gwarantuje, że pamięć podręczna w warstwie Standardowa/Premium jest dostępna przez co najmniej 99,9% czasu. Aby dowiedzieć się więcej o umowach SLA, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Umowa SLA obejmuje tylko łączność z punktami końcowymi pamięci podręcznej. Umowa SLA nie obejmuje ochrony przed utratą danych. Zalecamy używanie funkcji trwałości danych Redis w warstwie Premium w celu zwiększenia odporności na utratę danych.
* **Trwałość danych Redis**: warstwa Premium umożliwia utrwalanie danych w pamięci podręcznej na koncie usługi Azure Storage. W pamięci podręcznej podstawowa/standardowa wszystkie dane są przechowywane tylko w pamięci. Problemy związane z infrastrukturą mogą skutkować potencjalną utratą danych. Zalecamy używanie funkcji trwałości danych Redis w warstwie Premium w celu zwiększenia odporności na utratę danych. Usługa Azure cache for Redis oferuje opcje RDB i kopia zapasowa AOF (wersja zapoznawcza) w przypadku trwałości Redis. Aby uzyskać więcej informacji, zobacz [jak skonfigurować trwałość dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-persistence.md).
* **Klaster Redis**: Aby utworzyć pamięć podręczną o rozmiarze większym niż 120 GB lub fragmentu dane w wielu węzłach Redis, można użyć klastra Redis, który jest dostępny w warstwie Premium. Każdy węzeł składa się z pary pamięci podręcznej podstawowej/repliki w celu zapewnienia wysokiej dostępności. Aby uzyskać więcej informacji, zobacz [jak skonfigurować klastrowanie dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-clustering.md).
* **Ulepszone zabezpieczenia i izolacja sieci**: wdrożenie usługi Azure Virtual Network (VNET) zapewnia ulepszone zabezpieczenia i izolację pamięci podręcznej platformy Azure dla usługi Redis, a także podsieci, zasady kontroli dostępu i inne funkcje w celu dodatkowego ograniczenia dostępu. Aby uzyskać więcej informacji, zobacz [jak skonfigurować obsługę usługi Virtual Network w przypadku pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-vnet.md).
* **Konfigurowanie Redis**: w warstwach Standardowa i Premium można skonfigurować Redis dla powiadomień dotyczących miejsca na dysku.
* **Maksymalna liczba połączeń klientów**: warstwa Premium oferuje maksymalną liczbę klientów, którzy mogą łączyć się z usługą Redis, z większą liczbą połączeń dla pamięci podręcznych o większej wielkości. Klastrowanie nie zwiększa liczby połączeń dostępnych dla klastrowanej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/).
* **Dedykowany rdzeń dla serwera Redis**: w warstwie Premium wszystkie rozmiary pamięci podręcznej mają dedykowany rdzeń dla Redis. W warstwach Podstawowa/standardowa rozmiar C1 i powyżej ma dedykowany rdzeń dla serwera Redis.
* **Redis jest jednowątkowy** , tak aby więcej niż dwa rdzenie nie zapewniały większej korzyści niż dwa rdzenie, ale większe rozmiary maszyn wirtualnych zazwyczaj mają większą przepustowość niż mniejsze rozmiary. Jeśli serwer pamięci podręcznej lub klient osiągnie limity przepustowości, po stronie klienta zostaną wyświetlone limity czasu.
* **Ulepszenia wydajności**: pamięć podręczna w warstwie Premium są wdrażane na sprzęcie z szybszymi procesorami, co zapewnia lepszą wydajność w porównaniu z warstwą podstawowa lub standardowa. Pamięć podręczna warstwy Premium ma wyższą przepływność i mniejsze opóźnienia.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Pamięć podręczna Azure dla wydajności Redis
W poniższej tabeli przedstawiono maksymalne wartości przepustowości zaobserwowane podczas testowania różnych rozmiarów pamięci podręcznej w warstwach Standardowa i Premium przy użyciu `redis-benchmark.exe` maszyny wirtualnej IaaS w usłudze Azure cache for Redis Endpoint. W przypadku przepływności TLS Redis-test jest używany z stunnel do nawiązywania połączenia z usługą Azure cache for Redis Endpoint.

>[!NOTE] 
>Te wartości nie są gwarantowane i nie ma umowy SLA dla tych numerów, ale powinny być typowe. Należy przetestować własną aplikację, aby określić odpowiedni rozmiar pamięci podręcznej dla aplikacji.
>Liczby te mogą ulec zmianie w miarę okresowego ogłaszania nowszych wyników.
>

Z tej tabeli można narysować następujące wnioski:

* Przepływność dla pamięci podręcznych o takim samym rozmiarze jest większa w warstwie Premium w porównaniu do warstwy Standardowa. Na przykład w przypadku pamięci podręcznej 6 GB przepływność P1 to 180 000 żądań na sekundę (RPS pliku) w porównaniu do 100 000 RPS pliku dla C3.
* W przypadku klastrowania Redis przepływność wzrasta liniowo w miarę zwiększania liczby fragmentów (węzłów) w klastrze. Jeśli na przykład utworzysz klaster P4 o wartości 10 fragmentów, dostępna przepływność to 400 000 * 10 = 4 000 000 RPS pliku.
* Przepływność dla większych rozmiarów kluczy jest wyższa w warstwie Premium w porównaniu do warstwy Standardowa.

| Warstwa cenowa | Rozmiar | Rdzenie procesora CPU | Dostępna przepustowość | rozmiar wartości 1 KB | rozmiar wartości 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Standardowe rozmiary pamięci podręcznej** | | |**Megabity na sekundę (MB/s)/megabajty na sekundę (MB/s)** |**Żądania na sekundę (RPS pliku) bez protokołu SSL** |**Żądania na sekundę (RPS pliku) SSL** |
| C0 | 250 MB | Udostępniona | 100/12,5  |  15 000 |   7500 |
| C1 |   1 GB | 1      | 500/62,5  |  38 000 |  20 720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41 000 |  37 000 |
| C3 |   6 GB | 4      | 1000/125  | 100 000 |  90 000 |
| C4 |  13 GB | 2      | 500/62,5  |  60 000 |  55 000 |
| C5 |  26 GB | 4      | 1 000/125 | 102 000 |  93 000 |
| C6 |  53 GB | 8      | 2 000/250 | 126 000 | 120 000 |
| **Rozmiary pamięci podręcznej Premium** | |**Rdzenie procesora CPU na fragmentu** | **Megabity na sekundę (MB/s)/megabajty na sekundę (MB/s)** |**Żądania na sekundę (RPS pliku) bez protokołu SSL, na fragmentu** |**Żądania na sekundę (RPS pliku) SSL, na fragmentu** |
| P1 |   6 GB |  2 | 1 500/187,5 | 180 000 | 172 000 |
| P2 |  13 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P3 |  26 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P4 |  53 GB |  8 | 6 000/750   | 400 000 | 373 000 |
| P5 | 120 GB | 20 | 6 000/750   | 400 000 | 373 000 |

Instrukcje dotyczące konfigurowania stunnel lub pobierania narzędzi Redis, takich jak `redis-benchmark.exe` , można znaleźć w sekcji [jak uruchomić polecenia Redis?](#cache-commands) .

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>W jakim regionie należy znaleźć moją pamięć podręczną?
Aby uzyskać najlepszą wydajność i najmniejsze opóźnienia, zlokalizuj pamięć podręczną platformy Azure dla Redis w tym samym regionie, w którym znajduje się aplikacja kliencka pamięci podręcznej.

### <a name="where-do-my-cached-data-reside"></a>Gdzie znajdują się dane z pamięci podręcznej?
Usługa Azure cache for Redis przechowuje dane aplikacji w pamięci RAM maszyny wirtualnej lub maszyn wirtualnych w zależności od warstwy, która hostuje pamięć podręczną. Dane znajdują się wyłącznie w regionie świadczenia usługi Azure, który został wybrany domyślnie. Istnieją dwa przypadki, w których dane mogą opuścić region:
  1. Po włączeniu trwałości w pamięci podręcznej usługa Azure cache for Redis będzie tworzyć kopie zapasowe danych na koncie usługi Azure Storage, którego jesteś własnym. Jeśli konto magazynu jest dostępne w innym regionie, kopia danych zostanie tam zakończona.
  1. Jeśli skonfigurujesz replikację geograficzną, a pomocnicza pamięć podręczna znajduje się w innym regionie, w przeciwnym razie dane zostaną zreplikowane do tego regionu.

Aby korzystać z tych funkcji, należy jawnie skonfigurować pamięć podręczną platformy Azure dla Redis. Masz również pełną kontrolę nad regionem, w którym znajduje się konto magazynu lub pomocnicza pamięć podręczna.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak naliczane są opłaty za usługę Azure cache for Redis?
W [tym miejscu](https://azure.microsoft.com/pricing/details/cache/)jest dostępna usługa Azure cache for Redis. Cennik jest wyświetlany na stronie cennika jako stawka godzinowa i miesięczna. W pamięci podręcznej są naliczane opłaty za minutę od momentu utworzenia pamięci podręcznej do momentu usunięcia pamięci podręcznej. Nie ma możliwości zatrzymywania ani wstrzymywania rozliczeń pamięci podręcznej.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Czy mogę używać usługi Azure cache for Redis z chmurą Azure Government, z platformy Azure w Chinach, w chmurze 21Vianet lub Microsoft Azure (Niemcy)?
Tak. pamięć podręczna platformy Azure dla usługi Redis jest dostępna w chmurze Azure Government, Azure Chiny i Microsoft Azure (Niemcy). Adresy URL służące do uzyskiwania dostępu do pamięci podręcznej platformy Azure dla Redis i zarządzania nią są inne w tych chmurach w porównaniu z chmurą publiczną

| Chmura   | Sufiks DNS dla Redis            |
|---------|---------------------------------|
| Publiczny  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Niemcy | *. redis.cache.cloudapi.de       |
| Chiny   | *. redis.cache.chinacloudapi.cn  |

Aby uzyskać więcej informacji na temat zagadnień dotyczących używania usługi Azure cache for Redis z innymi chmurami, zobacz następujące linki.

- [Bazy danych Azure Government — usługa Azure cache for Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure Chiny w chmurze 21Vianet — pamięć podręczna platformy Azure dla Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)

Aby uzyskać informacje na temat korzystania z usługi Azure cache for Redis z programem PowerShell w chmurze Azure Government, Azure Chiny i Microsoft Azure (Niemcy), zobacz [jak nawiązać połączenie z innymi chmurami — pamięć podręczna Azure dla programu Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Do czego służy opcja konfiguracji StackExchange. Redis?
StackExchange. Redis ma wiele opcji. W tej sekcji przedstawiono niektóre typowe ustawienia. Aby uzyskać szczegółowe informacje na temat opcji StackExchange. Redis, zobacz [stackexchange. Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Opis | Rekomendacja |
| --- | --- | --- |
| AbortOnConnectFail |Po ustawieniu na wartość true połączenie nie zostanie ponownie nawiązane po awarii sieci. |Ustaw wartość false i zezwól na automatyczne łączenie StackExchange. Redis. |
| ConnectRetry |Liczba powtórzeń prób połączenia podczas początkowego połączenia. |Aby uzyskać wskazówki, zobacz następujące uwagi. |
| ConnectTimeout |Limit czasu dla operacji połączenia w MS. |Aby uzyskać wskazówki, zobacz następujące uwagi. |

Zwykle wartości domyślne klienta są wystarczające. Możesz dostosować opcje w zależności od obciążenia.

* **Ponowne próby**
  * W przypadku ConnectRetry i ConnectTimeout ogólne wskazówki są szybkie i ponawiane. Wskazówki te są oparte na obciążeniu i ile czasu na średnim jest to, aby klient mógł wydać polecenie Redis i odebrać odpowiedź.
  * Zezwól programowi StackExchange. Redis na automatyczne ponowne nawiązywanie połączenia zamiast sprawdzania stanu połączenia i samodzielnego łączenia się. **Unikaj używania właściwości ConnectionMultiplexer. IsConnected**.
  * Snowballing — czasami może wystąpić problem polegający na tym, że ponawianie próby, a ponowne próby Snowball i nigdy nie są odzyskiwane. Jeśli wystąpią Snowballing, należy rozważyć użycie wykładniczego algorytmu ponowienia wycofywania, zgodnie z opisem w temacie [ponowienie ogólnych wskazówek](../best-practices-retry-general.md) opublikowanych przez firmę Microsoft w ramach systemu praktyk &.
  
* **Wartości limitu czasu**
  * Zastanów się nad obciążeniem i ustaw odpowiednie wartości. Jeśli przechowujesz duże wartości, ustaw limit czasu na wyższą wartość.
  * Ustaw `AbortOnConnectFail` wartość false i zezwól na ponowne łączenie stackexchange. Redis.
  * Użyj pojedynczego wystąpienia ConnectionMultiplexer dla aplikacji. Można użyć LazyConnection do utworzenia pojedynczego wystąpienia, które jest zwracane przez właściwość połączenia, jak pokazano w [Połącz z pamięcią podręczną przy użyciu klasy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ustaw `ConnectionMultiplexer.ClientName` Właściwość na unikatową nazwę wystąpienia aplikacji dla celów diagnostycznych.
  * Używaj wielu `ConnectionMultiplexer` wystąpień dla obciążeń niestandardowych.
      * Możesz obsłużyć ten model, jeśli masz różne obciążenia w aplikacji. Na przykład:
      * Możesz mieć jeden multiplekser do celów związanych z dużymi kluczami.
      * Można mieć jeden multiplekser do obsługi małych kluczy.
      * Można ustawić różne wartości limitów czasu połączenia i logikę ponowień dla wszystkich używanych ConnectionMultiplexer.
      * Ustaw `ClientName` Właściwość dla każdego multipleksera, aby ułatwić diagnostykę.
      * Te wskazówki mogą prowadzić do bardziej usprawnionych opóźnień na `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Do jakiej usługi Azure cache for Redis klienci mogą korzystać?
Jednym z wspaniałych Redis jest to, że wielu klientów obsługuje wiele różnych języków deweloperskich. Aby uzyskać aktualną listę klientów, zobacz [Redis clients](https://redis.io/clients). Samouczki, które obejmują kilka różnych języków i klientów, znajdują się w temacie [jak używać usługi Azure cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i elementów równorzędnych w spisie treści.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Czy istnieje emulator lokalny dla usługi Azure cache for Redis?
Nie ma lokalnego emulatora dla usługi Azure cache for Redis, ale można uruchomić wersję MSOpenTech redis-server.exe z [narzędzi wiersza polecenia Redis](https://github.com/MSOpenTech/redis/releases/) na komputerze lokalnym i połączyć się z nią w celu uzyskania podobnego środowiska na potrzeby lokalnego emulatora pamięci podręcznej, jak pokazano w następującym przykładzie:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Opcjonalnie można skonfigurować plik [Redis. conf](https://redis.io/topics/config) , aby lepiej pasował do [domyślnych ustawień pamięci](cache-configure.md#default-redis-server-configuration) podręcznej w pamięci podręcznej platformy Azure online dla Redis w razie potrzeby.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak można uruchomić polecenia Redis?
Można użyć dowolnego polecenia wymienionego w [poleceniach Redis](https://redis.io/commands#) , z wyjątkiem poleceń wymienionych w [poleceniach Redis, które nie są obsługiwane w usłudze Azure cache for Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Istnieje kilka opcji uruchamiania poleceń Redis.

* W przypadku pamięci podręcznej standardowej lub Premium można uruchamiać polecenia Redis za pomocą [konsoli Redis](cache-configure.md#redis-console). Konsola Redis zapewnia bezpieczny sposób uruchamiania poleceń Redis w Azure Portal.
* Można również użyć narzędzi wiersza polecenia Redis. Aby ich użyć, wykonaj następujące czynności:
* Pobierz [narzędzia wiersza polecenia Redis](https://github.com/MSOpenTech/redis/releases/).
* Nawiąż połączenie z pamięcią podręczną przy użyciu polecenia `redis-cli.exe` . Przekaż punkt końcowy pamięci podręcznej przy użyciu przełącznika-h i klucza przy użyciu-a, jak pokazano w następującym przykładzie:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Narzędzia wiersza polecenia Redis nie działają z portem TLS, ale można użyć narzędzia, takiego jak `stunnel` Aby bezpiecznie połączyć narzędzia z portem TLS, postępując zgodnie z instrukcjami w temacie [jak używać narzędzia wiersza polecenia Redis z usługą Azure cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) .
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Dlaczego usługa Azure cache for Redis ma bibliotekę klas MSDN, jak w przypadku niektórych innych usług platformy Azure?
Pamięć podręczna Microsoft Azure dla Redis jest oparta na popularnym magazynie danych open source, Redis. Dostęp do niego może uzyskać wiele różnych [klientów Redis](https://redis.io/clients) w wielu językach programowania. Każdy klient ma własny interfejs API, który wysyła wywołania do usługi Azure cache for Redis za pomocą [poleceń Redis](https://redis.io/commands).

Ponieważ każdy klient różni się od siebie, w witrynie MSDN nie ma jednego scentralizowanego odwołania do klasy, a każdy klient utrzymuje własną dokumentację referencyjną. Oprócz dokumentacji referencyjnej istnieje kilka samouczków przedstawiających sposób rozpoczynania pracy z usługą Azure cache for Redis przy użyciu różnych języków i klientów pamięci podręcznej. Aby uzyskać dostęp do tych samouczków, zobacz [jak używać usługi Azure cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i elementów równorzędnych w spisie treści.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Czy można używać usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP?
Tak, aby używać usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP, określ parametry połączenia dla wystąpienia Redis w usłudze Azure cache `session.save_path` .

> [!IMPORTANT]
> W przypadku używania usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP należy zakodować klucz zabezpieczeń używany do nawiązywania połączenia z pamięcią podręczną, jak pokazano w następującym przykładzie:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Jeśli klucz nie jest zakodowany w adresie URL, może wystąpić wyjątek z komunikatem podobnym do:`Failed to parse session.save_path`
>
>

Aby uzyskać więcej informacji o korzystaniu z usługi Azure cache for Redis jako pamięci podręcznej sesji PHP z klientem PhpRedis, zobacz [program obsługi sesji języka PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co to są bazy danych Redis?

Bazy danych Redis to logiczne rozdzielenie danych w tym samym wystąpieniu Redis. Pamięć podręczna jest udostępniana między wszystkimi bazami danych a rzeczywistym zużyciem pamięci w danej bazie danych zależy od kluczy/wartości przechowywanych w tej bazie danych. Na przykład pamięć podręczna C6 ma 53 GB pamięci, a P5 ma 120 GB. Można wybrać opcję umieszczenia wszystkich 53 GB/120 GB w jednej bazie danych lub podzielić ją między wiele baz danych. 

> [!NOTE]
> W przypadku korzystania z pamięci podręcznej systemu Azure w warstwie Premium dla Redis z włączoną obsługą klastrowania dostępna jest tylko baza danych 0. To ograniczenie jest wewnętrznym ograniczeniem Redis i nie jest specyficzne dla usługi Azure cache for Redis. Aby uzyskać więcej informacji, zobacz [Czy muszę wprowadzić zmiany w aplikacji klienckiej w celu korzystania z klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Kiedy należy włączyć port bez protokołu TLS/SSL na potrzeby łączenia się z usługą Redis?
Serwer Redis nie obsługuje natywnie protokołu TLS, ale pamięć podręczna platformy Azure dla Redisów. Jeśli łączysz się z usługą Azure cache for Redis, a klient obsługuje protokół TLS, taki jak StackExchange. Redis, należy użyć protokołu TLS.

>[!NOTE]
>Port inny niż TLS jest domyślnie wyłączony dla nowej pamięci podręcznej platformy Azure dla wystąpień Redis. Jeśli klient nie obsługuje protokołu TLS, należy włączyć port bez protokołu TLS, postępując zgodnie z instrukcjami w sekcji [porty dostępu](cache-configure.md#access-ports) w artykule [Konfigurowanie pamięci podręcznej w usłudze Azure cache for Redis](cache-configure.md) .
>
>

Narzędzia Redis, takie jak `redis-cli` nie działają z portem TLS, ale można użyć narzędzia, takiego jak `stunnel` Aby bezpiecznie połączyć narzędzia z portem TLS, postępując zgodnie z instrukcjami wyświetlanymi w blogu [dostawca stanu sesji ASP.NET dla Redis wersji zapoznawczej](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) .

Instrukcje dotyczące pobierania narzędzi Redis można znaleźć w sekcji [jak uruchomić polecenia Redis?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Jakie są najlepsze rozwiązania w zakresie produkcji?
* [StackExchange. Redis — najlepsze rozwiązania](#stackexchangeredis-best-practices)
* [Konfiguracja i koncepcje](#configuration-and-concepts)
* [Testowanie wydajności](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange. Redis — najlepsze rozwiązania
* Ustaw `AbortConnect` wartość false, a następnie pozwól, aby usługa ConnectionMultiplexer łączyła się automatycznie. [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Ponownie Użyj ConnectionMultiplexer — nie twórz nowego dla każdego żądania. `Lazy<ConnectionMultiplexer>` [Opisany tutaj](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) wzorzec jest zalecany.
* Redis najlepiej sprawdza się w przypadku mniejszych wartości, więc Rozważ powiększanie większych danych do wielu kluczy. W [tej dyskusji Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)100 KB jest traktowane jako duże. Przeczytaj [ten artykuł](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) , aby zapoznać się z przykładowym problemem, który może być spowodowany przez duże wartości.
* Skonfiguruj [Ustawienia puli wątków](#important-details-about-threadpool-growth) , aby uniknąć przekroczeń limitu czasu.
* Użyj co najmniej wartości domyślnej connectTimeout wynoszącej 5 sekund. Ten interwał daje StackExchange. Redis wystarczająco dużo czasu, aby ponownie nawiązać połączenie w przypadku Blip sieci.
* Należy pamiętać o kosztach wydajności związanych z różnymi operacjami, które są uruchomione. Na przykład `KEYS` polecenie jest operacją o (n) i należy ją uniknąć. [Lokacja Redis.IO](https://redis.io/commands/) zawiera szczegółowe informacje o złożoności czasu dla każdej obsługiwanej operacji. Kliknij każde polecenie, aby zobaczyć złożoność każdej operacji.

#### <a name="configuration-and-concepts"></a>Konfiguracja i koncepcje
* Użyj warstwy Standardowa lub Premium dla systemów produkcyjnych. Warstwa Podstawowa to system z jednym węzłem bez replikacji danych i bez umowy SLA. Ponadto można użyć przynajmniej pamięci podręcznej C1. Pamięć podręczna C0 jest zwykle używana w przypadku prostych scenariuszy tworzenia i testowania.
* Należy pamiętać, że Redis jest magazynem danych **w pamięci** . Przeczytaj [ten artykuł](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , aby poznać scenariusze, w których może wystąpić utrata danych.
* Opracowywanie systemu w taki sposób, aby mógł obsługiwać Blips połączeń [z powodu stosowania poprawek i trybu failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testowanie wydajności
* Zacznij od użycia, `redis-benchmark.exe` Aby uzyskać dostęp do możliwej przepływności przed napisaniem własnych testów wydajności. Ponieważ `redis-benchmark` program nie obsługuje protokołu TLS, przed uruchomieniem testu należy [włączyć port bez protokołu tls przez Azure Portal](cache-configure.md#access-ports) . Aby zapoznać się z przykładami, zobacz [Jak sprawdzić i przetestować wydajność mojej pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Maszyna wirtualna klienta użyta do testowania powinna znajdować się w tym samym regionie co usługa Azure cache for Redis.
* Zalecamy używanie serii maszyn wirtualnych Dv2 dla klienta, ponieważ mają one lepszy sprzęt i powinny dawać najlepsze wyniki.
* Upewnij się, że wybrana maszyna wirtualna klienta ma co najmniej tyle możliwości obliczeniowych i przepustowości jak w przypadku testowanej pamięci podręcznej.
* Jeśli korzystasz z systemu Windows, Włącz opcję wirtualnego skalowania na komputerze klienckim. [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://technet.microsoft.com/library/dn383582.aspx).
* Wystąpienia usługi Redis w warstwie Premium mają lepsze opóźnienia sieci i przepływność, ponieważ działają na lepszym sprzęcie dla obu procesorów i sieci.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jakie kwestie należy wziąć pod uwagę podczas korzystania z typowych poleceń Redis?

* Należy unikać używania niektórych poleceń Redis, których wykonanie trwa długo, chyba że w pełni zrozumiesz wpływ tych poleceń. Na przykład nie należy uruchamiać poleceń [Keys](https://redis.io/commands/keys) w środowisku produkcyjnym. W zależności od liczby kluczy powracanie może potrwać dużo czasu. Redis to jednowątkowy serwer i przetwarza polecenia pojedynczo. Jeśli masz inne polecenia wydane po KLUCZu, nie zostaną one przetworzone do momentu, gdy Redis przetwarza polecenie KEYS. [Lokacja Redis.IO](https://redis.io/commands/) zawiera szczegółowe informacje o złożoności czasu dla każdej obsługiwanej operacji. Kliknij każde polecenie, aby zobaczyć złożoność każdej operacji.
* Rozmiary kluczy — czy należy używać małych kluczy/wartości, czy też dużych wartości klucz/wartość? Jest to zależne od scenariusza. Jeśli w scenariuszu wymagane są większe klucze, można dostosować wartość parametru ConnectionTimeout, a następnie ponowić próbę i dostosować logikę ponowień. W perspektywie serwera Redis mniejsze wartości zapewniają lepszą wydajność.
* Te zagadnienia nie oznacza, że nie można przechowywać większych wartości w Redis; należy pamiętać o następujących kwestiach. Opóźnienia będą wyższe. Jeśli masz jeden zestaw danych, który jest większy i który jest mniejszy, możesz użyć wielu wystąpień ConnectionMultiplexer, z których każda została skonfigurowana z innym zestawem limitów czasu i ponownych prób, zgodnie z opisem w poprzedniej sekcji [Opcje konfiguracji stackexchange. Redis](#cache-configuration) .

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak można sprawdzić i przetestować wydajność mojej pamięci podręcznej?
* [Włącz diagnostykę pamięci podręcznej](cache-how-to-monitor.md#enable-cache-diagnostics), aby móc [monitorować](cache-how-to-monitor.md) jej kondycję. Możesz wyświetlać metryki w Azure Portal, a także [pobierać i przeglądać](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) je przy użyciu wybranych przez siebie narzędzi.
* redis-benchmark.exe można użyć do załadowania testowego serwera Redis.
* Upewnij się, że klient testowania obciążenia i pamięć podręczna platformy Azure dla Redis znajdują się w tym samym regionie.
* Użyj redis-cli.exe i monitoruj pamięć podręczną za pomocą polecenia INFO.
* Jeśli obciążenie powoduje duże fragmentację pamięci, należy skalować w górę do większego rozmiaru pamięci podręcznej.
* Instrukcje dotyczące pobierania narzędzi Redis można znaleźć w sekcji [jak uruchomić polecenia Redis?](#cache-commands)

Poniższe polecenia zapewniają przykład użycia redis-benchmark.exe. Aby uzyskać dokładne wyniki, Uruchom te polecenia z maszyny wirtualnej w tym samym regionie, w którym znajduje się pamięć podręczna.

* Testuj żądania zestawu potokowego przy użyciu ładunku 1K

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testuj potok żądania GET przy użyciu ładunku o mocy 1K.
  Uwaga: aby wypełnić pamięć podręczną, uruchom najpierw określony test

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Ważne szczegóły dotyczące wzrostu puli wątków
W wątkach CLR istnieją dwa typy wątków — wątki "Worker" i "Port zakończenia we/wy" (portu).

* Wątki robocze są używane dla elementów, takich jak przetwarzanie `Task.Run(…)` `ThreadPool.QueueUserWorkItem(…)` metod lub. Te wątki są również używane przez różne składniki w środowisku CLR, gdy działanie musi się zdarzyć w wątku w tle.
* Wątki portu są używane, gdy odbywa się asynchroniczne operacje we/wy, takie jak podczas odczytywania z sieci.

Pula wątków udostępnia nowe wątki procesów roboczych lub wątki zakończenia operacji we/wy na żądanie (bez ograniczania przepustowości), dopóki nie osiągnie ustawienia "minimalne" dla każdego typu wątku. Domyślnie minimalna liczba wątków jest ustawiona na liczbę procesorów w systemie.

Gdy liczba istniejących (zajętych) wątków trafi na "minimalną" liczbę wątków, zostanie ograniczona szybkość, z jaką wprowadza nowe wątki do jednego wątku na 500 milisekund. Zwykle, jeśli system pobiera szereg zadań potrzebnych do wątku portu, będzie on szybko przetwarzał te działania. Jeśli jednak obciążenie serii pracy jest większe niż skonfigurowane ustawienie "minimalne", nastąpi kilka opóźnień podczas przetwarzania części pracy, ponieważ jest ona zależna od jednego z dwóch rzeczy.

1. Istniejący wątek będzie bezpłatny, aby przetworzyć prace.
2. Żaden z istniejących wątków nie jest bezpłatny dla 500 MS, więc zostanie utworzony nowy wątek.

W zasadzie oznacza to, że gdy liczba zajętych wątków jest większa niż min, prawdopodobnie płacisz 500-ms opóźnienia przed przetworzeniem ruchu sieciowego przez aplikację. Należy również pamiętać, że gdy istniejący wątek pozostaje bezczynny przez dłużej niż 15 sekund (w zależności od tego, co zapamiętasz), zostanie oczyszczony i będzie można powtórzyć ten cykl wzrostu i zmniejszania.

Jeśli zobaczysz przykładowy komunikat o błędzie z StackExchange. Redis (kompilacja 1.0.450 lub nowsza), zobaczysz, że teraz jest drukowana Statystyka puli wątków (zobacz szczegóły dotyczące portu i procesu roboczego).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

W poprzednim przykładzie można zobaczyć, że dla wątku portu są sześć zajętych wątków, a system jest skonfigurowany tak, aby zezwalał na cztery minimalne wątki. W takim przypadku klient prawdopodobnie widzi opóźnienia 2 500-MS, ponieważ 6 > 4.

Należy pamiętać, że StackExchange. Redis może trafiać limitów czasu, jeśli wzrost liczby wątków portu lub roboczych zostanie ograniczony.

### <a name="recommendation"></a>Rekomendacja

Mając te informacje, zdecydowanie zalecamy, aby klienci ustawili minimalną wartość konfiguracji dla portu i wątków roboczych na wartość większą niż domyślna. Firma Microsoft nie może podać wskazówek o jednym rozmiarze, które dotyczą tej wartości, ponieważ odpowiednia wartość dla jednej aplikacji będzie prawdopodobnie zbyt wysoka lub niska dla innej aplikacji. To ustawienie może również mieć wpływ na wydajność innych części skomplikowanych aplikacji, więc każdy klient musi dostosować to ustawienie do swoich konkretnych potrzeb. Dobrym miejscem początkowym jest 200 lub 300, a następnie testowanie i dostosowywanie zgodnie z wymaganiami.

Jak skonfigurować to ustawienie:

* Zalecamy zmianę tego ustawienia programowo przy użyciu metody [puli wątków. SetMinThreads — (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) w programie `global.asax.cs` . Na przykład:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > Wartość określona przez tę metodę jest ustawieniem globalnym wpływającym na całą domenę aplikacji. Na przykład jeśli masz maszynę 4-rdzeniową i chcesz ustawić *MinWorkerThreads* i *minIoThreads* na 50 na procesor CPU podczas wykonywania, użyj **puli wątków. SetMinThreads — (200, 200)**.

* Można również określić ustawienie minimalnych wątków przy użyciu [Ustawienia konfiguracji *MinIoThreads* lub *MinWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) w obszarze `<processModel>` elementu konfiguracji w programie `Machine.config` , zwykle znajdującego się pod adresem `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **Ustawienie minimalnej liczby wątków w ten sposób zwykle nie jest zalecane, ponieważ jest to ustawienie na poziomie całego systemu.**

  > [!NOTE]
  > Wartość określona w tym elemencie konfiguracji jest ustawieniem *na rdzeń* . Na przykład jeśli masz maszynę 4-rdzeniową i chcesz, aby ustawienie *minIoThreads* było 200 w czasie wykonywania, użyj `<processModel minIoThreads="50"/>` .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Włącz serwer GC, aby uzyskać większą przepływność na kliencie przy użyciu StackExchange. Redis
Włączenie serwera GC może zoptymalizować klienta i zapewnić lepszą wydajność i przepływność przy użyciu StackExchange. Redis. Aby uzyskać więcej informacji na temat serwera GC i sposobu jego włączania, zobacz następujące artykuły:

* [Aby włączyć serwer GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Podstawy dotyczące odzyskiwania pamięci](/dotnet/standard/garbage-collection/fundamentals)
* [Odzyskiwanie pamięci i wydajność](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Zagadnienia dotyczące wydajności dotyczące połączeń

Każda warstwa cenowa ma różne limity dla połączeń klientów, pamięci i przepustowości. Chociaż każdy rozmiar pamięci podręcznej pozwala *na* określoną liczbę połączeń, każde połączenie z usługą Redis ma powiązane z nim obciążenie. Przykładem takiego obciążenia będzie użycie procesora CPU i pamięci w wyniku szyfrowania TLS/SSL. Maksymalny limit połączeń dla danego rozmiaru pamięci podręcznej zakłada, że pamięć podręczna została załadowana w sposób jasny. Jeśli obciążenie związane z naliczeniem połączenia *i* obciążenie z operacji klienta przekroczy pojemność systemu, pamięć podręczna może mieć problemy z pojemnością, nawet jeśli nie przekroczono limitu połączenia dla bieżącego rozmiaru pamięci podręcznej.

Aby uzyskać więcej informacji na temat różnych limitów połączeń dla każdej warstwy, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/). Aby uzyskać więcej informacji o połączeniach i innych konfiguracjach domyślnych, zobacz [domyślną konfigurację serwera Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak mogę monitorować kondycję i wydajność mojej pamięci podręcznej?
Pamięć podręczna Microsoft Azure wystąpień Redis można monitorować w [Azure Portal](https://portal.azure.com). Możesz wyświetlić metryki, przypiąć wykresy metryk do tablicy startowej, dostosować zakres daty i godziny wykresów monitorowania, dodać i usunąć metryki z wykresów i ustawić alerty w przypadku spełnienia określonych warunków. Aby uzyskać więcej informacji, zobacz [monitorowanie usługi Azure cache for Redis](cache-how-to-monitor.md).

**Menu zasobów** usługi Azure cache for Redis zawiera również kilka narzędzi do monitorowania i rozwiązywania problemów z pamięcią podręczną.

* **Diagnozowanie i rozwiązywanie problemów** zawiera informacje o typowych problemach i strategiach ich rozwiązywania.
* **Kondycja zasobu** obserwuje zasób i informuje o tym, że działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji o usłudze Azure Resource Health, zobacz [Omówienie usługi Azure Resource Health](../resource-health/resource-health-overview.md).
* **Nowe żądanie obsługi** oferuje opcje otwierania żądania obsługi dla pamięci podręcznej.

Te narzędzia umożliwiają monitorowanie kondycji pamięci podręcznej platformy Azure dla wystąpień Redis i ułatwiają zarządzanie aplikacjami buforowania. Aby uzyskać więcej informacji, zobacz sekcję "Obsługa & ustawień rozwiązywania problemów" w temacie [jak skonfigurować usługę Azure cache for Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Dlaczego widzę limity czasu?
Limity czasu są wykonywane na kliencie, który jest używany do komunikowania się z Redis. Gdy polecenie jest wysyłane do serwera Redis, polecenie jest umieszczane w kolejce, a serwer Redis ostatecznie wybiera polecenie i wykonuje je. Jednak klient może przekroczyć limit czasu w trakcie tego procesu, a jeśli zostanie zgłoszony wyjątek na stronie wywołującej. Aby uzyskać więcej informacji na temat rozwiązywania problemów z limitem czasu, zobacz [wyjątki limitów czasu](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions) [po stronie klienta](cache-troubleshoot-client.md) i stackexchange. Redis.

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Dlaczego mój klient odłączył się od pamięci podręcznej?
Poniżej przedstawiono kilka typowych przyczyn odłączenia pamięci podręcznej.

* Przyczyny po stronie klienta
  * Aplikacja kliencka została wdrożona ponownie.
  * Aplikacja kliencka wykonała operację skalowania.
    * W przypadku Cloud Services lub Web Apps może to być spowodowane skalowaniem automatycznym.
  * Zmieniono warstwę sieciową po stronie klienta.
  * Wystąpił błąd przejściowy w kliencie lub w węzłach sieciowych między klientem a serwerem.
  * Osiągnięto limity progu przepustowości.
  * Operacje związane z procesorem CPU trwały zbyt długo.
* Przyczyny po stronie serwera
  * W przypadku standardowej oferty pamięci podręcznej usługa Azure cache for Redis zainicjowała przechodzenie w tryb failover z węzła podstawowego do węzła repliki.
  * Platforma Azure nastąpiła poprawka do wystąpienia, w którym została wdrożona pamięć podręczna
    * Może to dotyczyć aktualizacji serwera Redis lub ogólnej konserwacji maszyn wirtualnych.

### <a name="which-azure-cache-offerings-is-right-for-me"></a>Które oferty usługi Azure cache są odpowiednie dla mnie?
> [!IMPORTANT]
> Zgodnie z [ogłoszeniem](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)2016 usługi Azure Managed Cache Service i Azure pamięć podręczna oparta na roli Service zostały **wycofane** 30 listopada 2016. Naszym rekomendacją jest użycie [usługi Azure cache for Redis](https://azure.microsoft.com/services/cache/). Aby uzyskać informacje na temat migracji, zobacz [Migrowanie z Managed Cache Service do usługi Azure cache for Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Pamięć podręczna platformy Azure dla usługi Redis jest ogólnie dostępna w rozmiarach do 120 GB i ma umowę SLA dotyczącą dostępności 99,9%. Nowa [warstwa Premium](cache-premium-tier-intro.md) oferuje rozmiary do 1,2 TB i obsługę klastrowania, sieci wirtualnej i trwałości, z umową SLA na 99,9%.

Usługa Azure cache for Redis umożliwia klientom korzystanie z bezpiecznej, dedykowanej pamięci podręcznej platformy Azure dla Redis, zarządzanej przez firmę Microsoft. Dzięki tej ofercie uzyskasz dostęp do bogatego zestawu funkcji i ekosystemu zapewnianego przez Redis oraz niezawodnego hostingu i monitorowania od firmy Microsoft.

W przeciwieństwie do tradycyjnych pamięci podręcznych, które dotyczą tylko par klucz-wartość, Redis jest popularne dla wysoce wydajnych typów danych. Redis obsługuje również wykonywanie operacji niepodzielnych na tych typach, takich jak dołączanie do ciągu; zwiększanie wartości w skrócie; wypychanie do listy; obliczenia zbiorowe, związek i różnica; lub pobierając element członkowski z najwyższą klasyfikacją w posortowanym zestawie. Inne funkcje obejmują obsługę transakcji, publikowania/podLua skryptów, kluczy z ograniczonym czasem wygaśnięcia oraz ustawień konfiguracji, aby Redis zachować więcej jak tradycyjną pamięć podręczną.

Innym najważniejszym aspektem do Redis sukcesu jest dobra w dobrej kondycji ekosystem "open source". Jest to odzwierciedlone w różnorodnym zestawie klientów Redis dostępnych w wielu językach. Ten ekosystem i szeroki zakres klientów umożliwiają korzystanie z usługi Azure cache for Redis przez niemal każde obciążenie, które ma zostać skompilowane w ramach platformy Azure.

Aby uzyskać więcej informacji na temat rozpoczynania pracy z usługą Azure cache for Redis, zobacz [jak korzystać z usługi Azure cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i [pamięci podręcznej platformy Azure w celu uzyskania dokumentacji Redis](index.yml).

### <a name="managed-cache-service"></a>Usługa zarządzanej pamięci podręcznej
[Usługa zarządzanej pamięci podręcznej została wycofana 30 listopada 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić archiwalną dokumentację, zapoznaj się z [dokumentacją archiwalną Managed Cache Service](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Pamięć podręczna oparta na roli
[Pamięć podręczna oparta na roli został wycofany 30 listopada 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić archiwalną dokumentację, zapoznaj się z [dokumentacją archiwalną pamięć podręczna oparta na roli](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
