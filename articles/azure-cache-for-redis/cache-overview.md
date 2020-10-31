---
title: Co to jest usługa Azure Cache for Redis?
description: Dowiedz się więcej o usłudze Azure cache for Redis, dzięki której można włączyć buforowanie pamięci podręcznej, przechowywanie zawartości, buforowanie sesji użytkowników, zadania i kolejkowanie komunikatów oraz transakcje rozproszone.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 3bea474ae61ba4a0213d880934d9536d6ad71796
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131636"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Pamięć podręczna systemu Azure dla usługi Redis zapewnia magazyn danych w pamięci oparty na oprogramowaniu [Redis](https://redis.io/) . Redis poprawia wydajność i skalowalność aplikacji, która jest wielokrotnie wykorzystywana w magazynach danych zaplecza. Jest w stanie przetwarzać duże ilości żądań aplikacji przez zachowywanie często używanych danych w pamięci serwera, które mogą być zapisywane i odczytywane szybko. Redis zapewnia krytyczne rozwiązanie do przechowywania danych o małym opóźnieniu i wysokiej przepływności dla nowoczesnych aplikacji.

Usługa Azure cache for Redis oferuje zarówno Redis typu open source, jak i komercyjne produkty z Redis Labs jako usługę zarządzaną. Zapewnia bezpieczne i dedykowane wystąpienia serwera Redis i pełną zgodność interfejsu API Redis. Usługa jest obsługiwana przez firmę Microsoft, hostowana na platformie Azure i dostępna dla każdej aplikacji w ramach platformy Azure lub poza nią.

Pamięć podręczna platformy Azure dla usługi Redis może być używana jako rozproszone dane lub pamięć podręczna zawartości, magazyn sesji, Broker komunikatów i wiele więcej. Można ją wdrożyć jako autonomiczną lub równolegle z inną usługą Azure Database, taką jak Azure SQL lub Cosmos DB.

## <a name="key-scenarios"></a>Najważniejsze scenariusze
Pamięć podręczna systemu Azure dla usługi Redis umożliwia zwiększenie wydajności aplikacji dzięki obsłudze typowych wzorców architektury aplikacji. Niektóre z najczęstszych obejmują następujące:

| Wzorce      | Opis                                        |
| ------------ | -------------------------------------------------- |
| [Pamięć podręczna danych](cache-web-app-cache-aside-leaderboard.md) | Bazy danych są często zbyt duże, aby można je było załadować bezpośrednio do pamięci podręcznej. Często można użyć wzorca z odkładaniem do [pamięci podręcznej](/azure/architecture/patterns/cache-aside) w celu załadowania danych do pamięci podręcznej tylko w razie potrzeby. Gdy system wprowadza zmiany w danych, system może również zaktualizować pamięć podręczną, która następnie jest dystrybuowana do innych klientów. Ponadto system może ustawić wygaśnięcie danych lub użyć zasad wykluczenia do wyzwalania aktualizacji danych w pamięci podręcznej.|
| [Pamięć podręczna zawartości](cache-aspnet-output-cache-provider.md) | Wiele stron sieci Web jest generowanych z szablonów, które używają zawartości statycznej, takiej jak nagłówki, stopki, transparenty. Te elementy statyczne nie powinny być często zmieniane. Użycie pamięci podręcznej w pamięci zapewnia szybki dostęp do zawartości statycznej w porównaniu do magazynów danych zaplecza. Ten wzorzec skraca czas przetwarzania i obciążenie serwera, dzięki czemu serwery sieci Web mogą być bardziej reagujące. Pozwala to zmniejszyć liczbę serwerów potrzebnych do obsługi obciążeń. Pamięć podręczna systemu Azure dla usługi Redis udostępnia dostawcę wyjściowej pamięci podręcznej Redis do obsługi tego wzorca przy użyciu ASP.NET.|
| [Magazyn sesji](cache-aspnet-session-state-provider.md) | Ten wzorzec jest często używany w przypadku koszyków i innych danych historii użytkowników, które aplikacja sieci Web może chcieć skojarzyć z plikami cookie użytkownika. Przechowywanie zbyt wielu danych w pliku cookie może mieć negatywny wpływ na wydajność, ponieważ rozmiar pliku cookie rośnie, a plik jest przekazywany i weryfikowany przy każdym żądaniu. Typowym rozwiązaniem jest użycie pliku cookie jako klucza do wykonywania zapytań dotyczących danych w bazie danych. Skojarzenie informacji z użytkownikiem za pomocą wewnątrzpamięciowej pamięci podręcznej, takiej jak usługa Azure Cache for Redis, jest znacznie szybsze niż interakcja z pełną relacyjną bazą danych. |
| Kolejkowanie zadań i komunikatów | Aplikacje często dodają zadania do kolejki w czasie wykonywania operacji skojarzonych z żądaniem. Dłużej działające operacje są umieszczane w kolejce w celu przetworzenia w sekwencji, często przez inny serwer.  Ta metoda odraczania pracy jest nazywana kolejkowaniem zadań. Pamięć podręczna systemu Azure dla usługi Redis udostępnia kolejkę rozproszoną, która umożliwia włączenie tego wzorca w aplikacji.|
| Transakcje rozproszone | Aplikacje czasami wymagają szeregu poleceń dla magazynu danych zaplecza, aby można było wykonać pojedynczą niepodzielną operację. Wszystkie polecenia muszą się powieść lub wszystkie muszą zostać wycofane do stanu początkowego. Usługa Azure cache for Redis obsługuje wykonywanie partii poleceń jako pojedynczej [transakcji](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Wersje Redis

Usługa Azure cache for Redis obsługuje System OSS Redis w wersji 4. x i, jako wersja zapoznawcza, 6,0. Podjęto decyzję o pominięciu Redis 5,0 w celu przeprowadzenia najnowszej wersji. Wcześniej usługa Azure cache for Redis obsługiwała tylko jedną wersję Redis. Zostanie udostępniona nowsza wersja uaktualnienia i co najmniej jednej starszej stabilnej wersji. Możesz [wybrać, która wersja](cache-how-to-version.md) najlepiej sprawdza się w przypadku aplikacji.

> [!NOTE]
> Redis 6,0 jest obecnie w wersji zapoznawczej. [skontaktuj się z nami](mailto:azurecache@microsoft.com) , jeśli interesują Cię zainteresowania. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Warstwy usług
Usługa Azure Cache for Redis jest dostępna w następujących warstwach:

| Warstwa | Opis |
|---|---|
| Podstawowa | Pamięć podręczna Redis usługi OSS działa na jednej maszynie wirtualnej. Ta warstwa nie ma umowy dotyczącej poziomu usług (SLA) i doskonale nadaje się do tworzenia i testowania oraz obciążeń niekrytycznych. |
| Standardowa | Pamięć podręczna Redis usługi OSS uruchomiona na dwóch maszynach wirtualnych w zreplikowanej konfiguracji. |
| Premium | Wysokiej wydajności pamięci podręczne OSS Redis. Ta warstwa oferuje wyższą przepływność, mniejsze opóźnienia, lepszą dostępność i więcej funkcji. Pamięć podręczna w warstwie Premium jest wdrażana na bardziej zaawansowanych maszynach wirtualnych w porównaniu do tych w przypadku pamięci podręcznych Basic i Standard. |
| Enterprise | Pamięci podręczne o wysokiej wydajności obsługiwane przez oprogramowanie Redis Labs Redis Enterprise. Ta warstwa obsługuje moduły Redis, w tym RediSearch, RedisBloom i RedisTimeSeries. Dodatkowo oferuje jeszcze wyższą dostępność niż warstwa Premium. |
| Lampa błyskowa przedsiębiorstwa | Ekonomiczne duże pamięci podręczne obsługiwane przez oprogramowanie Redis Labs Redis Enterprise. Ta warstwa rozszerza magazyn danych Redis do pamięci nieulotnej, który jest tańszy niż DRAM na maszynie wirtualnej. Zmniejsza całkowity koszt za GB pamięci. |

### <a name="feature-comparison"></a>Porównanie funkcji
[Cennik usługi Azure cache for Redis](https://azure.microsoft.com/pricing/details/cache/) zawiera szczegółowe porównanie poszczególnych warstw. W poniższej tabeli opisano niektóre funkcje obsługiwane przez warstwy:

| Opis funkcji | Podstawowa | Standardowa (Standard) | Premium | Enterprise | Lampa błyskowa przedsiębiorstwa |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Szyfrowanie danych |✔|✔|✔|✔|✔|
| [Izolacja sieciowa](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Skalowanie](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [Nadmiarowość stref](cache-how-to-zone-redundancy.md) |-|-|✔|✔|✔|
| [Replikacja geograficzna](cache-how-to-geo-replication.md) |-|-|✔|-|-|
| [Trwałość danych](cache-how-to-premium-persistence.md) |-|-|✔|-|-|
| [Klaster OSS](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Moduły](https://redis.io/modules) |-|-|-|✔|-|
| [Import/Export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Zaplanowane aktualizacje](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Wybór odpowiedniej warstwy
Podczas wybierania pamięci podręcznej platformy Azure dla warstwy Redis należy wziąć pod uwagę następujące kwestie:

* **Pamięć** : warstwy Podstawowa i standardowa oferują 250 MB – 53 GB; warstwa Premium 6 GB — 1,2 TB; warstwy korporacyjne 12 GB-14 TB.  Aby utworzyć pamięć podręczną warstwy Premium o rozmiarze większym niż 120 GB, można użyć usługi Redis OSS. Aby uzyskać więcej informacji, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/). Aby uzyskać więcej informacji, zobacz [jak skonfigurować klastrowanie dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-clustering.md).
* **Wydajność sieci** : Jeśli korzystasz z obciążenia, które wymaga dużej przepływności, warstwa Premium lub Enterprise oferuje większą przepustowość w porównaniu do warstwy Podstawowa lub standardowa. Ponadto w każdej warstwie pamięć podręczna o większym rozmiarze ma większą przepustowość ze względu na źródłową maszynę wirtualną, która obsługuje pamięć podręczną. Aby uzyskać więcej informacji, zobacz [pamięć podręczna platformy Azure w celu uzyskania wydajności Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Przepływność** : warstwa Premium oferuje maksymalną dostępną przepływność. Jeśli serwer pamięci podręcznej lub klient osiągnie limity przepustowości, mogą pojawić się limity czasu po stronie klienta. Aby uzyskać więcej informacji, zobacz poniższą tabelę.
* **Wysoka dostępność** : usługa Azure cache for Redis zapewnia wiele opcji [wysokiej dostępności](cache-high-availability.md) . Gwarantuje to, że pamięć podręczna w warstwie Standardowa, Premium lub Enterprise jest dostępna zgodnie z naszą umową [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Umowa SLA obejmuje tylko łączność z punktami końcowymi pamięci podręcznej. Umowa SLA nie obejmuje ochrony przed utratą danych. Zalecamy używanie funkcji trwałości danych Redis w warstwie Premium w celu zwiększenia odporności na utratę danych.
* **Trwałość danych** : warstwa Premium umożliwia utrwalanie danych w pamięci podręcznej na koncie usługi Azure Storage. W innych warstwach dane są przechowywane tylko w pamięci. Problemy związane z infrastrukturą mogą skutkować potencjalną utratą danych. Zalecamy używanie funkcji trwałości danych Redis w warstwie Premium w celu zwiększenia odporności na utratę danych. Usługa Azure cache for Redis oferuje opcje RDB i kopia zapasowa AOF (wersja zapoznawcza) w przypadku trwałości Redis. Aby uzyskać więcej informacji, zobacz [jak skonfigurować trwałość dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-persistence.md).
* **Izolacja sieci** : wdrożenia prywatnego linku i Virtual Network (VNET) platformy Azure zapewniają zwiększoną ochronę i izolację ruchu dla pamięci podręcznej platformy Azure dla Redis. Sieć wirtualna umożliwia bardziej ograniczenie dostępu za poorednictwem zasad kontroli dostępu do sieci. Aby uzyskać więcej informacji, zobacz [usługa Azure cache for Redis z prywatnym łączem platformy Azure](cache-private-link.md) i [jak skonfigurować obsługę Virtual Network dla pamięci podręcznej systemu Azure w warstwie Premium dla Redis](cache-how-to-premium-vnet.md).
* **Maksymalna liczba połączeń klientów** : warstwa Premium oferuje maksymalną liczbę klientów, którzy mogą łączyć się z usługą Redis, z większą liczbą połączeń dla pamięci podręcznych o większej wielkości. Klastrowanie nie zwiększa liczby połączeń dostępnych dla klastrowanej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/).
* **Dedykowany rdzeń dla serwera Redis** : wszystkie pamięci podręczne z wyjątkiem C0 Run DEDYKOWANYCH maszyn wirtualnych.
* **Przetwarzanie jednowątkowe** : Redis, według projektu, używa tylko jednego wątku do przetwarzania poleceń. Usługa Azure cache for Redis wykorzystuje również dodatkowe rdzenie dla przetwarzania we/wy. Więcej rdzeni zwiększa wydajność przepływności, nawet jeśli nie może generować skalowania liniowego. Ponadto większe rozmiary maszyn wirtualnych zazwyczaj mają wyższe limity przepustowości niż mniejsze. Dzięki temu można uniknąć nasycenia sieci, co spowoduje przekroczenie limitu czasu w aplikacji.
* **Ulepszenia wydajności** : pamięć podręczna w warstwach Premium i Enterprise jest wdrażana na sprzęcie z szybszymi procesorami, co zapewnia lepszą wydajność w porównaniu z warstwą podstawowa lub standardowa. Pamięć podręczna warstwy Premium ma wyższą przepływność i mniejsze opóźnienia. Aby uzyskać więcej informacji, zobacz [pamięć podręczna platformy Azure w celu uzyskania wydajności Redis](cache-planning-faq.md#azure-cache-for-redis-performance).

Pamięć podręczną można skalować z warstwy Podstawowa do Premium po jej utworzeniu. Skalowanie w dół do niższej warstwy nie jest obsługiwane. Aby uzyskać instrukcje skalowania krok po kroku, zobacz [Jak skalować usługę Azure Cache for Redis](cache-how-to-scale.md) i [Jak zautomatyzować operację skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="enterprise-tier-requirements"></a>Wymagania dotyczące warstwy przedsiębiorstwa

Warstwy przedsiębiorstwa bazują na Redis Enterprise, komercyjnej wersji Redis z Redis Labs. Klienci uzyskają licencję na oprogramowanie i płacisz za nią za pomocą oferty portalu Azure Marketplace. Pamięć podręczna systemu Azure dla usługi Redis ułatwia pozyskiwanie licencji, dzięki czemu nie będzie konieczne osobne wykonanie tej operacji. Do zakupu w witrynie Azure Marketplace wymagane są następujące wymagania wstępne:
* Twoja subskrypcja platformy Azure ma prawidłowy instrument płatniczy. Kredyty na korzystanie z platformy Azure lub bezpłatne subskrypcje MSDN nie są obsługiwane.
* Jesteś właścicielem lub współautorem subskrypcji.
* Twoja organizacja umożliwia [zakupy w witrynie Azure Marketplace](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-azure-marketplace#enabling-azure-marketplace-purchases).
* Jeśli korzystasz z prywatnego rynku, musi on zawierać ofertę Redis Labs Enterprise.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie pamięci podręcznej platformy Azure dla wystąpienia Redis](quickstart-create-redis.md)
* [Tworzenie pamięci podręcznej warstwy przedsiębiorstwa](quickstart-create-redis-enterprise.md)
* [Korzystanie z usługi Azure cache for Redis w aplikacji sieci Web ASP.NET](cache-web-app-howto.md)
* [Korzystanie z usługi Azure cache for Redis w programie .NET Core](cache-dotnet-core-quickstart.md)
* [Użyj usługi Azure cache for Redis w .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Użyj usługi Azure cache for Redis w Node.js](cache-nodejs-get-started.md)
* [Korzystanie z usługi Azure cache for Redis w języku Java](cache-java-get-started.md)
* [Korzystanie z usługi Azure cache for Redis w języku Python](cache-python-get-started.md)
