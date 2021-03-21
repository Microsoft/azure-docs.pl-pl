---
title: Migrowanie do usługi Azure Cache for Redis
description: Dowiedz się, jak przeprowadzić migrację istniejącej pamięci podręcznej do usługi Azure cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: d63cafb32dc1db0a901ed3e6004446b450db10c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451527"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migrowanie do usługi Azure Cache for Redis
W tym artykule opisano szereg metod migracji istniejącej pamięci podręcznej Redis działającej lokalnie lub w innej usłudze w chmurze do usługi Azure cache dla Redis.

## <a name="migration-scenarios"></a>Scenariusze migracji
Redis Open Source można uruchamiać w wielu środowiskach obliczeniowych. Typowe przykłady obejmują:

- Pamięci podręczne Redis **lokalnie** działające w prywatnych centrach danych.
- **Maszyny wirtualne oparte na chmurze** — Redis pamięci podręczne działające na maszynach wirtualnych platformy Azure, AWS EC2 i tak dalej.
- **Usługi hostingowe** — zarządzane usługi Redis, takie jak AWS ElastiCache.
- **Różne regiony** — Redis pamięci podręcznej znajdujące się w innym regionie świadczenia usługi Azure.

Jeśli masz taką pamięć podręczną, możesz przenieść ją do usługi Azure cache for Redis z minimalnym zakłóceniem lub przestojem.

## <a name="migration-options"></a>Opcje migracji

Istnieją różne sposoby przełączania z jednej pamięci podręcznej na inną. W zależności od lokalizacji pamięci podręcznej i sposobu, w jaki aplikacja współpracuje z nią, jedna metoda będzie bardziej użyteczna niż inne. Niektóre z często używanych strategii migracji przedstawiono poniżej.

   | Opcja       | Zalety | Wady |
   | ------------ | ---------- | ------------- |
   | Tworzenie nowej pamięci podręcznej | Najprostsza do zaimplementowania. | Należy ponownie wypełnić dane do nowej pamięci podręcznej, co może nie współpracować z wieloma aplikacjami. |
   | Eksportuj i Importuj dane za pomocą pliku RDB | Ogólnie zgodne z dowolnymi Redis pamięci podręcznej. | Niektóre dane mogą zostać utracone, jeśli są zapisywane w istniejącej pamięci podręcznej po wygenerowaniu pliku RDB. | 
   | Podwójne zapis danych do dwóch pamięci podręcznych | Brak utraty danych ani przestojów. Nieprzerwane operacje istniejącej pamięci podręcznej. Łatwiejsze testowanie nowej pamięci podręcznej. | Potrzebuje dwóch pamięci podręcznych przez dłuższy czas. | 
   | Programistyczne Migrowanie danych | Pełna kontrola nad sposobem przenoszenia danych. | Wymaga kodu niestandardowego. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Tworzenie nowej pamięci podręcznej platformy Azure dla usługi Redis

To podejście technicznie nie jest migracją. Jeśli utrata danych nie jest istotna, najprostszym sposobem przejścia do usługi Azure cache for Redis jest utworzenie wystąpienia pamięci podręcznej i połączenie z nim aplikacji. Na przykład jeśli używasz Redis jako pamięci podręcznej rekordów bazy danych, możesz łatwo ponownie skompilować pamięć podręczną od podstaw.

Ogólna procedura implementowania tej opcji to:

1. Utwórz nowe wystąpienie usługi Azure cache for Redis.

2. Zaktualizuj aplikację tak, aby korzystała z nowego wystąpienia.

3. Usuń stare wystąpienie Redis.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Eksportuj dane do pliku RDB i zaimportuj je do usługi Azure cache for Redis

Redis typu open source definiuje standardowy mechanizm tworzenia migawek zestawu danych pamięci podręcznej i zapisywania go w pliku. Ten plik o nazwie RDB może być odczytywany przez inną pamięć podręczną Redis. [Usługa Azure cache for Redis w warstwie Premium](cache-overview.md#service-tiers) obsługuje importowanie danych do wystąpienia pamięci podręcznej za pośrednictwem plików RDB. Plik RDB służy do transferowania danych z istniejącej pamięci podręcznej do usługi Azure cache for Redis.

> [!IMPORTANT]
> Format pliku RDB może ulec zmianie między wersjami Redis i nie może zachować zgodności z poprzednimi wersjami. Wersja Redis pamięci podręcznej, z której eksportujesz, powinna być równa lub mniejsza niż wersja udostępniona przez pamięć podręczną platformy Azure dla Redis.
>

Ogólna procedura implementowania tej opcji to:

1. Utwórz nowe wystąpienie usługi Azure cache for Redis w warstwie Premium, które ma taki sam rozmiar jak (lub większy) istniejącej pamięci podręcznej.

2. Zapisz migawkę istniejącej pamięci podręcznej Redis. Można [skonfigurować Redis, aby okresowo zapisywać migawki](https://redis.io/topics/persistence) , lub uruchomić proces ręcznie przy użyciu poleceń [Zapisz](https://redis.io/commands/save) lub [BGSAVE](https://redis.io/commands/bgsave) . Plik RDB ma domyślnie nazwę "dump. RDB" i zostanie umieszczony w ścieżce określonej w pliku konfiguracji *Redis. conf* .

    > [!NOTE]
    > W przypadku migrowania danych w usłudze Azure cache for Redis zapoznaj się [z tymi instrukcjami, jak wyeksportować plik RDB](cache-how-to-import-export-data.md) lub użyć [polecenia cmdlet eksportu programu PowerShell](/powershell/module/azurerm.rediscache/export-azurermrediscache) .
    >

3. Skopiuj plik RDB do konta usługi Azure Storage w regionie, w którym znajduje się nowa pamięć podręczna. Dla tego zadania można użyć AzCopy.

4. Zaimportuj plik RDB do nowej pamięci podręcznej za pomocą tych [instrukcji importu](cache-how-to-import-export-data.md) lub [polecenia cmdlet Import programu PowerShell](/powershell/module/azurerm.rediscache/import-azurermrediscache).

5. Zaktualizuj aplikację tak, aby korzystała z nowego wystąpienia pamięci podręcznej.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Zapisuj do dwóch pamięci podręcznych Redis jednocześnie w okresie migracji

Zamiast przenosić dane bezpośrednio między pamięciami podręcznymi, możesz użyć aplikacji do zapisywania danych zarówno w istniejącej pamięci podręcznej, jak i nowej konfiguracji. Aplikacja nadal będzie odczytywać dane z istniejącej pamięci podręcznej. Gdy nowa pamięć podręczna ma niezbędne dane, należy przełączyć aplikację do tej pamięci podręcznej i wycofać starą. Załóżmy na przykład, że używasz Redis jako magazynu sesji, a sesje aplikacji są ważne przez siedem dni. Po dokonaniu zapisu w dwóch pamięciach podręcznych przez tydzień będziesz mieć pewność, że nowa pamięć podręczna będzie zawierać wszystkie niewygasłe informacje o sesji. Możesz bezpiecznie korzystać z niego od tego momentu bez obawy o utratę danych.

Ogólna procedura implementowania tej opcji to:

1. Utwórz nowe wystąpienie usługi Azure cache for Redis w warstwie Premium, które ma taki sam rozmiar jak (lub większy) istniejącej pamięci podręcznej.

2. Zmodyfikuj kod aplikacji, aby zapisywać w nowych i oryginalnych wystąpieniach.

3. Kontynuuj odczytywanie danych z oryginalnego wystąpienia, dopóki nowe wystąpienie nie zostanie wystarczająco wypełnione danymi.

4. Zaktualizuj kod aplikacji, aby czytać i zapisywać tylko z nowego wystąpienia.

5. Usuń oryginalne wystąpienie.

### <a name="migrate-programmatically"></a>Migrowanie programowo

Niestandardowy proces migracji można utworzyć, programowo odczytując dane z istniejącej pamięci podręcznej i pisząc je do usługi Azure cache for Redis. To [Narzędzie Open Source](https://github.com/deepakverma/redis-copy) może służyć do kopiowania danych z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego. To narzędzie jest przydatne do przemieszczania danych między wystąpieniami pamięci podręcznej w różnych regionach pamięci podręcznej platformy Azure. Dostępna jest również [skompilowana wersja](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) . Możesz również znaleźć kod źródłowy, który będzie przydatnym przewodnikiem dotyczącym pisania własnego narzędzia migracji.

> [!NOTE]
> To narzędzie nie jest oficjalnie obsługiwane przez firmę Microsoft. 
>

Ogólna procedura implementowania tej opcji to:

1. Utwórz maszynę wirtualną w regionie, w którym znajduje się istniejąca pamięć podręczna. Jeśli zestaw danych jest duży, wybierz stosunkowo wydajną maszynę wirtualną, aby skrócić czas kopiowania.

2. Utwórz nowe wystąpienie usługi Azure cache for Redis.

3. Opróżniaj dane z nowej pamięci podręcznej, aby upewnić się, że jest ona pusta. Ten krok jest wymagany, ponieważ samo narzędzie do kopiowania nie zastępuje żadnego istniejącego klucza w docelowej pamięci podręcznej.

    > [!IMPORTANT]
    > Upewnij się, że nie można opróżnić źródłowej pamięci podręcznej.
    >

4. Użyj aplikacji, takiej jak narzędzie typu open source powyżej, aby zautomatyzować kopiowanie danych z źródłowej pamięci podręcznej do obiektu docelowego. Należy pamiętać, że proces kopiowania może zająć trochę czasu w zależności od rozmiaru zestawu danych.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna platformy Azure dla warstw usług Redis](cache-overview.md#service-tiers)
* [Importuj dane](cache-how-to-import-export-data.md#import)