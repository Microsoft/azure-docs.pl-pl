---
title: Co to jest usługa Azure Cache for Redis?
description: Dowiedz się więcej o usłudze Azure cache for Redis, dzięki której można włączyć buforowanie pamięci podręcznej, przechowywanie zawartości, buforowanie sesji użytkowników, zadania i kolejkowanie komunikatów oraz transakcje rozproszone.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: cd4e7c8e2693c25f3fc092fb53874a97cfd62434
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113208"
---
# <a name="azure-cache-for-redis-description"></a>Opis usługi Azure Cache for Redis

Pamięć podręczna systemu Azure dla usługi Redis zapewnia magazyn danych w pamięci na podstawie [Redis](https://redis.io/)oprogramowania open source. W przypadku użycia jako pamięci podręcznej program Redis poprawia wydajność i skalowalność systemów, które opierają się na zastosowaniach magazynów danych zaplecza. Ulepszona wydajność dzięki kopiowaniu często używanych danych do szybkiego magazynu znajdującego się blisko aplikacji. Dzięki usłudze Azure cache for Redis ten szybki magazyn znajduje się w pamięci, a nie jest ładowany z dysku przez bazę danych.

Pamięć podręczna platformy Azure dla usługi Redis może być używana jako magazyn struktury danych w pamięci, rozproszona baza danych nierelacyjna i Broker komunikatów. Wydajność aplikacji jest zwiększana dzięki wykorzystaniu małego opóźnienia i wysokiej przepustowości aparatu Redis.

Pamięć podręczna systemu Azure dla usługi Redis zapewnia dostęp do bezpiecznej, dedykowanej pamięci podręcznej Redis. Usługa Azure cache for Redis jest zarządzana przez firmę Microsoft, hostowana na platformie Azure i dostępna dla dowolnej aplikacji w ramach platformy Azure lub poza nią. Ponadto usługa Azure Redis dla pamięci podręcznej używa strategii replikacji bezdyskowej, co zwiększa zgodność z branżą kart płatniczych.

## <a name="using-azure-cache-for-redis"></a>Korzystanie z usługi Azure cache for Redis

Pamięć podręczna systemu Azure dla usługi Redis umożliwia zwiększenie wydajności aplikacji dzięki obsłudze typowych wzorców architektury aplikacji. Niektóre z najczęstszych obejmują następujące:

| Wzorce      | Opis                                        |
| ------------ | -------------------------------------------------- |
| [Odkładanie do pamięci podręcznej](cache-web-app-cache-aside-leaderboard.md) | Bazy danych są często zbyt duże, aby można je było załadować bezpośrednio do pamięci podręcznej. Często można użyć wzorca z odkładaniem do [pamięci podręcznej](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) w celu załadowania danych do pamięci podręcznej tylko w razie potrzeby. Gdy system wprowadza zmiany w danych, system może również zaktualizować pamięć podręczną, która następnie jest dystrybuowana do innych klientów. Ponadto system może ustawić wygaśnięcie danych lub użyć zasad wykluczenia do wyzwalania aktualizacji danych w pamięci podręcznej.|
| [Buforowanie zawartości](cache-aspnet-output-cache-provider.md) | Wiele stron sieci Web jest generowanych z szablonów, które używają zawartości statycznej, takiej jak nagłówki, stopki, transparenty. Te elementy statyczne nie powinny być często zmieniane. Użycie pamięci podręcznej w pamięci zapewnia szybki dostęp do zawartości statycznej w porównaniu do magazynów danych zaplecza. Ten wzorzec skraca czas przetwarzania i obciążenie serwera, dzięki czemu serwery sieci Web mogą być bardziej reagujące. Pozwala to zmniejszyć liczbę serwerów potrzebnych do obsługi obciążeń. Pamięć podręczna systemu Azure dla usługi Redis udostępnia dostawcę wyjściowej pamięci podręcznej Redis do obsługi tego wzorca przy użyciu ASP.NET.|
| [Buforowanie sesji użytkownika](cache-aspnet-session-state-provider.md) | Ten wzorzec jest często używany w przypadku koszyków i innych danych historii użytkowników, które aplikacja sieci Web może chcieć skojarzyć z plikami cookie użytkownika. Przechowywanie zbyt wielu danych w pliku cookie może mieć negatywny wpływ na wydajność, ponieważ rozmiar pliku cookie rośnie, a plik jest przekazywany i weryfikowany przy każdym żądaniu. Typowym rozwiązaniem jest użycie pliku cookie jako klucza do wykonywania zapytań dotyczących danych w bazie danych. Skojarzenie informacji z użytkownikiem za pomocą wewnątrzpamięciowej pamięci podręcznej, takiej jak usługa Azure Cache for Redis, jest znacznie szybsze niż interakcja z pełną relacyjną bazą danych. |
| Kolejkowanie zadań i komunikatów | Aplikacje często dodają zadania do kolejki w czasie wykonywania operacji skojarzonych z żądaniem. Dłużej działające operacje są umieszczane w kolejce w celu przetworzenia w sekwencji, często przez inny serwer.  Ta metoda odraczania pracy jest nazywana kolejkowaniem zadań. Pamięć podręczna systemu Azure dla usługi Redis udostępnia kolejkę rozproszoną, która umożliwia włączenie tego wzorca w aplikacji.|
| Transakcje rozproszone | Aplikacje czasami wymagają szeregu poleceń dla magazynu danych zaplecza, aby można było wykonać pojedynczą niepodzielną operację. Wszystkie polecenia muszą się powieść lub wszystkie muszą zostać wycofane do stanu początkowego. Usługa Azure cache for Redis obsługuje wykonywanie partii poleceń jako pojedynczej [transakcji](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Oferta usługi Azure Cache for Redis

Usługa Azure Cache for Redis jest dostępna w następujących warstwach:

| Warstwa | Opis |
|---|---|
Podstawowy | Pamięć podręczna z jednym węzłem. Ta warstwa obsługuje wiele rozmiarów pamięci (250 MB – 53 GB) i idealnie nadaje się do tworzenia i testowania i niekrytycznych obciążeń. Warstwa Podstawowa nie ma umowy dotyczącej poziomu usług (SLA) |
| Standardowa | Replikowana pamięć podręczna w dwóch węzłach: podstawowa/pomocnicza, konfiguracja zarządzana przez platformę Azure z umową SLA o wysokiej dostępności (99,9%) |
| Premium | Warstwa Premium jest warstwą gotową dla przedsiębiorstw. Pamięci podręczne w warstwie Premium obsługują więcej funkcji i mają większą przepływność przy niższych opóźnieniach. Pamięci podręczne w warstwie Premium są wdrażane na mocniejszym sprzęcie, zapewniając większą wydajność w porównaniu do warstw Podstawowa i Standardowa. Ta zaleta oznacza, że przepływność dla pamięci podręcznej o takim samym rozmiarze będzie wyższa w porównaniu do warstwy Standardowa. |

> [!TIP]
> Więcej informacji na temat rozmiaru, przepływności i przepustowości pamięci podręcznych w warstwie Premium znajduje się w [często zadawanych pytaniach na temat usługi Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Pamięć podręczną można skalować do wyższej warstwy po jej utworzeniu. Skalowanie w dół do niższej warstwy nie jest obsługiwane. Aby uzyskać instrukcje skalowania krok po kroku, zobacz [Jak skalować usługę Azure Cache for Redis](cache-how-to-scale.md) i [Jak zautomatyzować operację skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Porównanie funkcji

Strona [Azure Cache for Redis — cennik](https://azure.microsoft.com/pricing/details/cache/) zawiera szczegółowe porównanie wszystkich warstw. W poniższej tabeli opisano niektóre funkcje obsługiwane przez warstwy:

| Opis funkcji | Premium | Standardowa | Podstawowy |
| ------------------- | :-----: | :------: | :---: |
| [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Trwałość danych Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Klaster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Zabezpieczanie za pomocą reguł zapory](cache-configure.md#firewall) |✔|✔|✔|
| Szyfrowanie podczas transferu |✔|✔|✔|
| [Większe bezpieczeństwo i izolacja z użyciem sieci wirtualnej](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Zaplanowane aktualizacje](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Replikacja geograficzna](cache-how-to-geo-replication.md) |✔|-|-|
| [Ponowne uruchamianie](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Następne kroki

* [Szybki start z aplikacją internetową platformy ASP.NET](cache-web-app-howto.md) Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z platformą .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Tworzenie aplikacji platformy .NET, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z platformą .NET Core](cache-dotnet-core-quickstart.md) Tworzenie aplikacji platformy .NET Core, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z platformą Node.js](cache-nodejs-get-started.md) Tworzenie prostej aplikacji platformy Node.js, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z językiem Java](cache-java-get-started.md) Tworzenie prostej aplikacji Java, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z językiem Python](cache-python-get-started.md) Tworzenie aplikacji Python, która korzysta z usługi Azure Cache for Redis.
