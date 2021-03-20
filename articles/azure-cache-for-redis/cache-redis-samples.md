---
title: Przykłady usługi Azure Cache for Redis
description: 'Dowiedz się, jak używać usługi Azure cache for Redis z następującymi przykładami kodu: łączenie z pamięcią podręczną, odczytywanie i zapisywanie danych w pamięci podręcznej, ASP.NET Azure cache for Redis Providers.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88212284"
---
# <a name="azure-cache-for-redis-samples"></a>Przykłady usługi Azure Cache for Redis
Ten temat zawiera listę pamięci podręcznej systemu Azure dla przykładów Redis, obejmujących scenariusze, takie jak nawiązywanie połączenia z pamięcią podręczną, odczytywanie i zapisywanie danych w pamięci podręcznej oraz korzystanie z usługi ASP.NET Azure cache for Redis Providers. Niektóre przykłady są projektami do pobrania, a niektóre zawierają wskazówki krok po kroku i zawierają fragmenty kodu, ale nie łączą się z projektem do pobrania.

## <a name="hello-world-samples"></a>Przykłady dla środowiska Hello World
W przykładach w tej sekcji przedstawiono podstawowe informacje dotyczące nawiązywania połączenia z usługą Azure cache for Redis oraz odczytywanie i zapisywanie danych w pamięci podręcznej przy użyciu różnych języków i klientów Redis.

Przykład [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) pokazuje, jak wykonywać różne operacje pamięci podręcznej przy użyciu klienta platformy .NET [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) .

Ten przykład pokazuje, jak:

* Użyj różnych opcji połączenia
* Odczyt i zapis obiektów do i z pamięci podręcznej przy użyciu operacji synchronicznych i asynchronicznych
* Użyj poleceń Redis MGET/MSET, aby zwrócić wartości określonych kluczy
* Wykonywanie operacji transakcyjnych Redis
* Pracuj z listami Redis i posortowanymi zestawami
* Przechowywanie obiektów .NET przy użyciu serializatorów JsonConvert
* Używanie zestawów Redis do zaimplementowania tagowania
* Współpraca z klastrem Redis

Aby uzyskać więcej informacji, zobacz dokumentację [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) w witrynie GitHub, a aby uzyskać więcej scenariuszy użycia, zobacz testy jednostkowe [stackexchange. Redis. Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) .

[Jak korzystać z usługi Azure cache for Redis za pomocą języka Python](cache-python-get-started.md) pokazuje, jak rozpocząć pracę z usługą Azure cache dla Redis przy użyciu języka Python i klienta [Redis-PR](https://github.com/andymccurdy/redis-py) .

[Współpraca z obiektami platformy .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) pokazuje, jak serializować obiekty .NET, aby można było je napisać i odczytać z pamięci podręcznej platformy Azure dla wystąpienia Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Korzystanie z usługi Azure cache for Redis jako skalowalnego w poziomie planu wieloskładnikowego dla sygnalizującego ASP.NET
[Użycie pamięci podręcznej platformy Azure dla usługi Redis jako planu wielostronnego skalowania w poziomie dla](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) próbnika ASP.net pokazuje, jak można użyć usługi Azure cache dla Redis jako testowego planu. Aby uzyskać więcej informacji na temat planów wieloplanowych, zobacz [sygnalizującer skalowania with Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Przykład kwerendy klienta usługi Azure cache for Redis
Ten przykład ilustruje porównanie wydajności dostępu do danych z pamięci podręcznej i uzyskiwania dostępu do danych z magazynu trwałości. Ten przykład ma dwa projekty.

* [Demonstracja, jak usługa Azure cache for Redis może zwiększyć wydajność przez buforowanie danych](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Wypełnianie bazy danych i pamięci podręcznej dla pokazu](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Stan sesji ASP.NET i buforowanie danych wyjściowych
[Użycie usługi Azure cache for Redis do przechowywania przykładu ASP.NET sessionState i OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) pokazuje, jak używać usługi Azure cache for Redis do przechowywania sesji ASP.NET i wyjściowej pamięci podręcznej przy użyciu dostawców sessionState i OutputCache dla Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Zarządzanie usługą Azure cache for Redis za pomocą usługi MAML
Przykład [zarządzania pamięcią podręczną platformy Azure dla usługi Redis przy użyciu bibliotek zarządzania platformy Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) pokazuje, jak można użyć bibliotek zarządzania platformy Azure do zarządzania — (Tworzenie/aktualizowanie/usuwanie) pamięci podręcznej. 

## <a name="custom-monitoring-sample"></a>Niestandardowy przykład monitorowania
Przykład [dostępu do pamięci podręcznej platformy Azure dla danych monitorowania Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstruje, jak uzyskać dostęp do danych monitorowania dla swojej pamięci podręcznej platformy Azure dla Redis poza portalem Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Klon w stylu Twitter zapisany przy użyciu języka PHP i Redis
Przykładem [Retwis](https://github.com/SyntaxC4-MSFT/retwis) jest Redis Hello World. Jest to minimalny klon sieci społecznościowej w serwisie Twitter zapisany przy użyciu Redis i PHP przy użyciu klienta [Predis](https://github.com/nrk/predis) . Kod źródłowy został zaprojektowany tak, aby był bardzo prosty i w tym samym czasie, aby pokazać różne struktury danych Redis.

## <a name="bandwidth-monitor"></a>Monitor przepustowości
Przykład [monitora przepustowości](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) pozwala monitorować przepustowość używaną na kliencie. Aby zmierzyć przepustowość, uruchom próbkę na komputerze klienckim pamięci podręcznej, wykonaj wywołania do pamięci podręcznej i obserwuj przepustowość zgłoszoną przez próbkę monitora przepustowości.
