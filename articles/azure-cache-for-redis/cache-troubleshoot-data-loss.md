---
title: Rozwiązywanie problemów z utratą danych w usłudze Azure Cache for Redis
description: Dowiedz się, jak rozwiązywać problemy z utratą danych w usłudze Azure cache for Redis, takie jak częściowe utrata kluczy, wygaśnięcie klucza lub pełna utrata kluczy.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 29492ee6b7bce50c4807a36d0c252e18e6aadf87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008954"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Rozwiązywanie problemów z utratą danych w usłudze Azure Cache for Redis

W tym artykule omówiono sposób diagnozowania rzeczywistych lub postrzeganych strat danych, które mogą wystąpić w usłudze Azure cache for Redis.

> [!NOTE]
> Kilka kroków rozwiązywania problemów zawartych w tym przewodniku zawiera instrukcje dotyczące uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z artykułami w sekcji [Informacje dodatkowe](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Częściowa utrata kluczy

Usługa Azure cache for Redis nie usuwa losowo kluczy po ich zapisaniu w pamięci. Jednak usuwa klucze w odpowiedzi na zasady wygasania lub wykluczania oraz do jawnych poleceń usuwania kluczy. Klucze, które zostały zapisana w węźle podstawowym w Premium lub standardowej pamięci podręcznej platformy Azure dla wystąpienia Redis, również mogą nie być dostępne od razu. Dane są replikowane z elementu podstawowego do repliki w sposób asynchroniczny i nieblokowany.

Jeśli okaże się, że klucze zniknęły z pamięci podręcznej, należy sprawdzić następujące możliwe przyczyny:

| Przyczyna | Opis |
|---|---|
| [Wygaśnięcie klucza](#key-expiration) | Klucze zostały usunięte ze względu na ustawione limity czasu. |
| [Wykluczenia klucza](#key-eviction) | Klucze są usuwane w obszarze wykorzystanie pamięci. |
| [Usuwanie klucza](#key-deletion) | Klucze są usuwane przez jawne polecenia Delete. |
| [Replikacja asynchroniczna](#async-replication) | Klucze nie są dostępne w replice z powodu opóźnień replikacji danych. |

### <a name="key-expiration"></a>Wygaśnięcie klucza

Usługa Azure cache for Redis usuwa klucz automatycznie, jeśli klucz ma przypisany limit czasu i ten okres został zakończony. Więcej informacji o wygaśnięciu klucza Redis można znaleźć w dokumentacji polecenia [wygasania](https://redis.io/commands/expire) . Wartości limitu czasu można również ustawić za pomocą poleceń [Set](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GetSet](https://redis.io/commands/getset)i other ** \* Store** .

Aby uzyskać statystyki dotyczące liczby wygasłych kluczy, użyj polecenia [info](https://redis.io/commands/info) . `Stats`Sekcja zawiera łączną liczbę wygasłych kluczy. `Keyspace`Sekcja zawiera więcej informacji o liczbie kluczy z limitami czasu i średnim limitem czasu.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Możesz również sprawdzić metryki diagnostyki dla pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między brakującym kluczem a skokiem w kluczach wygasłych. Zapoznaj się z dodatkiem do [debugowania Redis miejsca na dysku](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) , aby uzyskać informacje na temat korzystania z powiadomień o przestrzeni kluczy lub **monitora** w celu debugowania tych typów problemów.

### <a name="key-eviction"></a>Wykluczenia klucza

Pamięć podręczna systemu Azure dla Redis wymaga miejsca w pamięci do przechowywania danych. Klucze są przeczyszczane, aby zwolnić dostępną pamięć w razie potrzeby. Gdy wartości **used_memory** lub **used_memory_rss** w poleceniu [info](https://redis.io/commands/info) są zgodne ze skonfigurowanym ustawieniem **maxmemory** , usługa Azure cache for Redis uruchamia wykluczenia kluczy z pamięci na podstawie [zasad pamięci podręcznej](https://redis.io/topics/lru-cache).

Liczbę wykluczonych kluczy można monitorować za pomocą polecenia [info](https://redis.io/commands/info) :

```
# Stats

evicted_keys:13224
```

Możesz również sprawdzić metryki diagnostyki dla pamięci podręcznej, aby sprawdzić, czy istnieje korelacja między brakującym kluczem a skokiem w wykluczonych kluczach. Zapoznaj się z dodatkiem do [debugowania Redis miejsca na dysku](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) , aby uzyskać informacje na temat korzystania z powiadomień o przestrzeni kluczy lub **monitora** w celu debugowania tych typów problemów.

### <a name="key-deletion"></a>Usuwanie klucza

Redis klienci mogą wydać polecenie [del](https://redis.io/commands/del) lub [HDEL](https://redis.io/commands/hdel) , aby jawnie usunąć klucze z usługi Azure cache dla Redis. Liczbę operacji usuwania można śledzić za pomocą polecenia [info](https://redis.io/commands/info) . Jeśli polecenia **del** lub **HDEL** zostały wywołane, zostaną one wyświetlone w `Commandstats` sekcji.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replikacja asynchroniczna

Wszystkie wystąpienia usługi Azure cache for Redis w warstwie Standardowa lub Premium są skonfigurowane z węzłem podstawowym i co najmniej jedną repliką. Dane są kopiowane z podstawowego do repliki asynchronicznie przy użyciu procesu w tle. W witrynie sieci Web [Redis.IO](https://redis.io/topics/replication) opisano, jak działa replikacja danych Redis. W przypadku scenariuszy, w których klienci zapisują Redis często, częściowe utrata danych może wystąpić, ponieważ ta replikacja nie ma gwarancji natychmiastowej. Na przykład jeśli podstawowy przejdzie w dół *po* zapisaniu przez klienta klucza do niego, ale *zanim* proces w tle ma szansę na wysłanie tego klucza do repliki, klucz zostanie utracony, gdy replika będzie przebiegać jako nowy podstawowy.

## <a name="major-or-complete-loss-of-keys"></a>Główna lub pełna utrata kluczy

Jeśli większość lub wszystkie klucze zniknęły z pamięci podręcznej, należy sprawdzić następujące możliwe przyczyny:

| Przyczyna | Opis |
|---|---|
| [Opróżnianie klucza](#key-flushing) | Klucze zostały przeczyszczone ręcznie. |
| [Nieprawidłowy wybór bazy danych](#incorrect-database-selection) | Usługa Azure cache for Redis jest ustawiona do użycia niedomyślnej bazy danych. |
| [Niepowodzenie wystąpienia Redis](#redis-instance-failure) | Serwer Redis jest niedostępny. |

### <a name="key-flushing"></a>Opróżnianie klucza

Klienci mogą wywołać polecenie [FLUSHDB](https://redis.io/commands/flushdb) , aby usunąć wszystkie klucze w *pojedynczej* bazie danych lub [FLUSHALL](https://redis.io/commands/flushall) , aby usunąć wszystkie klucze ze *wszystkich* baz danych w pamięci podręcznej Redis. Aby dowiedzieć się, czy klucze zostały opróżnione, użyj polecenia [info](https://redis.io/commands/info) . `Commandstats`Sekcja wskazuje, czy wywołano polecenie **Flush** :

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Nieprawidłowy wybór bazy danych

Usługa Azure cache for Redis domyślnie używa bazy danych **DB0** . Jeśli przełączysz się do innej bazy danych (na przykład **DB1**) i spróbujesz odczytać z niej klucze, usługa Azure cache for Redis nie będzie tam znajdować. Każda baza danych jest logicznie oddzielną jednostką i zawiera inny zestaw danych. Użyj polecenia [SELECT](https://redis.io/commands/select) , aby użyć innych dostępnych baz danych i wyszukać klucze w każdym z nich.

### <a name="redis-instance-failure"></a>Niepowodzenie wystąpienia Redis

Redis to magazyn danych w pamięci. Dane są przechowywane na maszynach fizycznych lub wirtualnych, które obsługują pamięć podręczną Redis. Wystąpienie usługi Azure cache for Redis w warstwie Podstawowa działa tylko na jednej maszynie wirtualnej. Jeśli ta maszyna wirtualna nie działa, wszystkie dane przechowywane w pamięci podręcznej zostaną utracone. 

Pamięć podręczna w warstwach Standardowa i Premium zapewnia znacznie wyższą odporność na utratę danych przy użyciu dwóch maszyn wirtualnych w zreplikowanej konfiguracji. W przypadku awarii węzła podstawowego w takiej pamięci podręcznej węzeł repliki przejmuje dane automatycznie. Te maszyny wirtualne znajdują się w oddzielnych domenach dla błędów i aktualizacji, aby zminimalizować prawdopodobieństwo, że oba staną się niedostępne jednocześnie. W przypadku wystąpienia poważnej awarii centrum danych maszyny wirtualne mogą jednak nadal przechodzić do siebie. W tych rzadkich przypadkach Twoje dane zostaną utracone.

Rozważ użycie [trwałości danych Redis](https://redis.io/topics/persistence) i [replikacji geograficznej](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) , aby zwiększyć ochronę danych przed awarią infrastruktury.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera](cache-troubleshoot-server.md)
- [Wybór odpowiedniej warstwy](cache-overview.md#choosing-the-right-tier)
- [Jak monitorować usługę Azure cache for Redis](cache-how-to-monitor.md)
- [Jak można uruchomić polecenia Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
