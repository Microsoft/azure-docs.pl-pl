---
title: Usługa Azure cache for Redis — często zadawane pytania
description: Poznaj odpowiedzi na często zadawane pytania, które ułatwiają planowanie usługi Azure cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 13ba529dd3067ae16167f0d9c14c8f72b982f52c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010873"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Usługa Azure cache for Redis — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące planowania usługi Azure cache for Redis.

## <a name="common-questions-and-answers"></a>Typowe pytania i odpowiedzi
W tej sekcji omówiono następujące często zadawane pytania:

* [Pamięć podręczna Azure dla wydajności Redis](#azure-cache-for-redis-performance)
* [W jakim regionie należy znaleźć moją pamięć podręczną?](#in-what-region-should-i-locate-my-cache)
* [Gdzie znajdują się dane z pamięci podręcznej?](#where-do-my-cached-data-reside)
* [Jak naliczane są opłaty za usługę Azure cache for Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Czy mogę używać usługi Azure cache for Redis z chmurą Azure Government, z platformy Azure w Chinach, w chmurze 21Vianet lub Microsoft Azure (Niemcy)?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

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
| C0 | 250 MB | Shared | 100/12,5  |  15 000 |   7500 |
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

Instrukcje dotyczące konfigurowania stunnel lub pobierania narzędzi Redis, takich jak `redis-benchmark.exe` , można znaleźć w artykule [jak uruchomić polecenia Redis?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>W jakim regionie należy znaleźć moją pamięć podręczną?
Aby uzyskać najlepszą wydajność i najmniejsze opóźnienia, zlokalizuj pamięć podręczną platformy Azure dla Redis w tym samym regionie, w którym znajduje się aplikacja kliencka pamięci podręcznej.

### <a name="where-do-my-cached-data-reside"></a>Gdzie znajdują się dane z pamięci podręcznej?
Usługa Azure cache for Redis przechowuje dane aplikacji w pamięci RAM maszyny wirtualnej lub maszyn wirtualnych w zależności od warstwy, która hostuje pamięć podręczną. Dane znajdują się wyłącznie w regionie świadczenia usługi Azure, który został wybrany domyślnie. Istnieją dwa przypadki, w których dane mogą opuścić region:
* Po włączeniu trwałości w pamięci podręcznej usługa Azure cache for Redis będzie tworzyć kopie zapasowe danych na koncie usługi Azure Storage, którego jesteś własnym. Jeśli konto magazynu jest dostępne w innym regionie, kopia danych zostanie tam zakończona.
* Jeśli skonfigurujesz replikację geograficzną, a pomocnicza pamięć podręczna znajduje się w innym regionie, w przeciwnym razie dane zostaną zreplikowane do tego regionu.

Aby korzystać z tych funkcji, należy jawnie skonfigurować pamięć podręczną platformy Azure dla Redis. Masz również pełną kontrolę nad regionem, w którym znajduje się konto magazynu lub pomocnicza pamięć podręczna.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak naliczane są opłaty za usługę Azure cache for Redis?
W [tym miejscu](https://azure.microsoft.com/pricing/details/cache/)jest dostępna usługa Azure cache for Redis. Cennik jest wyświetlany na stronie cennika jako stawka godzinowa i miesięczna. W pamięci podręcznej są naliczane opłaty za minutę od momentu utworzenia pamięci podręcznej do momentu usunięcia pamięci podręcznej. Nie ma możliwości zatrzymywania ani wstrzymywania rozliczeń pamięci podręcznej.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Czy mogę używać usługi Azure cache for Redis z chmurą Azure Government, z platformy Azure w Chinach, w chmurze 21Vianet lub Microsoft Azure (Niemcy)?
Tak. pamięć podręczna platformy Azure dla usługi Redis jest dostępna w chmurze Azure Government, Azure Chiny i Microsoft Azure (Niemcy). Adresy URL służące do uzyskiwania dostępu do pamięci podręcznej platformy Azure dla Redis i zarządzania nią są inne w tych chmurach w porównaniu z chmurą publiczną

| Chmura   | Sufiks DNS dla Redis            |
|---------|---------------------------------|
| Public  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Niemcy | *. redis.cache.cloudapi.de       |
| Chiny   | *. redis.cache.chinacloudapi.cn  |

Aby uzyskać więcej informacji na temat zagadnień dotyczących używania usługi Azure cache for Redis z innymi chmurami, zobacz następujące linki.

- [Bazy danych Azure Government — usługa Azure cache for Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure Chiny w chmurze 21Vianet — pamięć podręczna platformy Azure dla Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)

Aby uzyskać informacje na temat korzystania z usługi Azure cache for Redis z programem PowerShell w chmurze Azure Government, Azure Chiny i Microsoft Azure (Niemcy), zobacz [jak nawiązać połączenie z innymi chmurami — pamięć podręczna Azure dla programu Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze [Azure cache for Redis — często zadawane pytania](cache-faq.md).
