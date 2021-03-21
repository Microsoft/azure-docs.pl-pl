---
title: Usługa Azure cache for Redis — często zadawane pytania
description: Poznaj odpowiedzi na często zadawane pytania, które ułatwiają programowanie w usłudze Azure cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: bafd8a9752d2587ec52fe586e442e3bfc86d7537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585772"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Usługa Azure cache for Redis — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące opracowywania usługi Azure cache for Redis.

## <a name="common-questions-and-answers"></a>Typowe pytania i odpowiedzi
W tej sekcji omówiono następujące często zadawane pytania:

* [Jak rozpocząć pracę z usługą Azure cache for Redis?](#how-can-i-get-started-with-azure-cache-for-redis)
* [Do czego służy opcja konfiguracji StackExchange. Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Do jakiej usługi Azure cache for Redis klienci mogą korzystać?](#what-azure-cache-for-redis-clients-can-i-use)
* [Czy istnieje emulator lokalny dla usługi Azure cache for Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak można uruchomić polecenia Redis?](#how-can-i-run-redis-commands)
* [Dlaczego usługa Azure cache for Redis ma odwołanie do biblioteki klas MSDN?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Czy można używać usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co to są bazy danych Redis?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak rozpocząć pracę z usługą Azure cache for Redis?
Istnieje kilka sposobów rozpoczynania pracy z usługą Azure cache for Redis.

* Możesz zapoznać się z jednym z naszych samouczków dostępnych dla [platformy .NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), Java, [Node.js](cache-nodejs-get-started.md)i [języka](cache-java-get-started.md) [Python](cache-python-get-started.md).
* Możesz obejrzeć, [jak tworzyć High-Performance aplikacje przy użyciu pamięci Podręcznej Microsoft Azure dla Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Aby zobaczyć, jak korzystać z usługi Redis, można sprawdzić dokumentację klienta dla klientów zgodnych z językiem programowania projektu. Istnieje wielu klientów Redis, których można używać z usługą Azure cache dla Redis. Listę klientów Redis można znaleźć w temacie [https://redis.io/clients](https://redis.io/clients) .

Jeśli nie masz jeszcze konta platformy Azure, możesz:

* [Utworzyć bezpłatne konto platformy Azure ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Otrzymasz środki, które możesz wykorzystać do wypróbowania płatnych usług Azure. Nawet po wyczerpaniu tych środków możesz zachować konto i korzystać z bezpłatnych usług i funkcji platformy Azure.
* [Aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Do czego służy opcja konfiguracji StackExchange. Redis?
StackExchange. Redis ma wiele opcji. W tej sekcji przedstawiono niektóre typowe ustawienia. Aby uzyskać szczegółowe informacje na temat opcji StackExchange. Redis, zobacz [stackexchange. Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Opis | Zalecenie |
| --- | --- | --- |
| AbortOnConnectFail |Po ustawieniu na wartość true połączenie nie zostanie ponownie nawiązane po awarii sieci. |Ustaw wartość false i zezwól na automatyczne łączenie StackExchange. Redis. |
| ConnectRetry |Liczba powtórzeń prób połączenia podczas początkowego połączenia. |Aby uzyskać wskazówki, zobacz następujące uwagi. |
| ConnectTimeout |Limit czasu dla operacji połączenia w MS. |Aby uzyskać wskazówki, zobacz następujące uwagi. |

Zwykle wartości domyślne klienta są wystarczające. Możesz dostosować opcje w zależności od obciążenia.

* **Ponowne próby**
  * W przypadku ConnectRetry i ConnectTimeout ogólne wskazówki są szybkie i ponawiane. Wskazówki te są oparte na obciążeniu i ile czasu na średnim jest to, aby klient mógł wydać polecenie Redis i odebrać odpowiedź.
  * Zezwól programowi StackExchange. Redis na automatyczne ponowne nawiązywanie połączenia zamiast sprawdzania stanu połączenia i samodzielnego łączenia się. **Unikaj używania właściwości ConnectionMultiplexer. IsConnected**.
  * Snowballing — czasami może wystąpić problem polegający na tym, że ponawianie próby, a ponowne próby Snowball i nigdy nie są odzyskiwane. Jeśli wystąpią Snowballing, należy rozważyć użycie wykładniczego algorytmu ponowienia wycofywania, zgodnie z opisem w temacie [ponowienie ogólnych wskazówek](/azure/architecture/best-practices/transient-faults) opublikowanych przez firmę Microsoft w ramach systemu praktyk &.
  
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

### <a name="how-can-i-run-redis-commands"></a>Jak można uruchomić polecenia Redis?
Można użyć dowolnego polecenia wymienionego w [poleceniach Redis](https://redis.io/commands#) , z wyjątkiem poleceń wymienionych w [poleceniach Redis, które nie są obsługiwane w usłudze Azure cache for Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Istnieje kilka opcji uruchamiania poleceń Redis.

* W przypadku pamięci podręcznej standardowej lub Premium można uruchamiać polecenia Redis za pomocą [konsoli Redis](cache-configure.md#redis-console). Konsola Redis zapewnia bezpieczny sposób uruchamiania poleceń Redis w Azure Portal.
* Można również użyć narzędzi wiersza polecenia Redis. Aby ich użyć, wykonaj następujące czynności:
* Pobierz [narzędzia wiersza polecenia Redis](https://github.com/MSOpenTech/redis/releases/).
* Nawiąż połączenie z pamięcią podręczną przy użyciu polecenia `redis-cli.exe` . Przekaż punkt końcowy pamięci podręcznej przy użyciu przełącznika-h i klucza przy użyciu-a, jak pokazano w następującym przykładzie:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Narzędzia wiersza polecenia Redis nie działają z portem TLS, ale można użyć narzędzia, takiego jak `stunnel` Aby bezpiecznie połączyć narzędzia z portem TLS, postępując zgodnie z instrukcjami w temacie [jak używać narzędzia wiersza polecenia Redis z usługą Azure cache for Redis](./cache-how-to-redis-cli-tool.md) .
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Dlaczego usługa Azure cache for Redis ma odwołanie do biblioteki klas MSDN?
Pamięć podręczna Microsoft Azure dla Redis jest oparta na popularnym magazynie danych open source, Redis. Dostęp do niego może uzyskać wiele różnych [klientów Redis](https://redis.io/clients) w wielu językach programowania. Każdy klient ma własny interfejs API, który wysyła wywołania do usługi Azure cache for Redis za pomocą [poleceń Redis](https://redis.io/commands).

Ponieważ każdy klient różni się od siebie, w witrynie MSDN nie ma jednego scentralizowanego odwołania do klasy, a każdy klient utrzymuje własną dokumentację referencyjną. Oprócz dokumentacji referencyjnej istnieje kilka samouczków przedstawiających sposób rozpoczynania pracy z usługą Azure cache for Redis przy użyciu różnych języków i klientów pamięci podręcznej. Aby uzyskać dostęp do tych samouczków, zobacz [jak używać usługi Azure cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i elementów równorzędnych w spisie treści.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Czy można używać usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP?
Tak, aby używać usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP, określ parametry połączenia dla wystąpienia Redis w usłudze Azure cache `session.save_path` .

> [!IMPORTANT]
> W przypadku używania usługi Azure cache for Redis jako pamięci podręcznej sesji języka PHP należy zakodować klucz zabezpieczeń używany do nawiązywania połączenia z pamięcią podręczną, jak pokazano w następującym przykładzie:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Jeśli klucz nie jest zakodowany w adresie URL, może wystąpić wyjątek z komunikatem podobnym do: `Failed to parse session.save_path`
>

Aby uzyskać więcej informacji o korzystaniu z usługi Azure cache for Redis jako pamięci podręcznej sesji PHP z klientem PhpRedis, zobacz [program obsługi sesji języka PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co to są bazy danych Redis?

Bazy danych Redis to logiczne rozdzielenie danych w tym samym wystąpieniu Redis. Pamięć podręczna jest udostępniana między wszystkimi bazami danych a rzeczywistym zużyciem pamięci w danej bazie danych zależy od kluczy/wartości przechowywanych w tej bazie danych. Na przykład pamięć podręczna C6 ma 53 GB pamięci, a P5 ma 120 GB. Można wybrać opcję umieszczenia wszystkich 53 GB/120 GB w jednej bazie danych lub podzielić ją między wiele baz danych. 

> [!NOTE]
> W przypadku korzystania z pamięci podręcznej systemu Azure w warstwie Premium dla Redis z włączoną obsługą klastrowania dostępna jest tylko baza danych 0. To ograniczenie jest wewnętrznym ograniczeniem Redis i nie jest specyficzne dla usługi Azure cache for Redis. Aby uzyskać więcej informacji, zobacz [Czy muszę wprowadzić zmiany w aplikacji klienckiej w celu korzystania z klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze [Azure cache for Redis — często zadawane pytania](cache-faq.md).