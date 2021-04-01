---
title: Migrowanie aplikacji do korzystania z Azure Cosmos DB .NET SDK 2,0 (Microsoft. Azure. Cosmos)
description: Dowiedz się, jak uaktualnić istniejącą aplikację .NET z zestawu SDK V1 do zestawu .NET SDK V2 for Core (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94550087"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Migrowanie aplikacji do korzystania z Azure Cosmos DB .NET SDK V2

> [!IMPORTANT]
> Należy pamiętać, że wersja 3 zestawu SDK platformy .NET jest obecnie dostępna, a plan migracji z wersji 2 do V3 jest dostępny [tutaj](migrate-dotnet-v3.md). Aby dowiedzieć się więcej o Azure Cosmos DB .NET SDK V2, zapoznaj się z [informacjami o wersji](sql-api-sdk-dotnet.md), w [repozytorium usługi .NET GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2), [wskazówkami dotyczącymi wydajności](performance-tips.md)zestawu .NET SDK V2 i [przewodniku rozwiązywania problemów](troubleshoot-dot-net-sdk.md).
>

W tym artykule omówiono niektóre zagadnienia dotyczące uaktualniania istniejącej aplikacji platformy .NET w wersji 1 do Azure Cosmos DB interfejsu API .NET SDK V2 for Core (SQL). Azure Cosmos DB .NET SDK V2 odpowiada `Microsoft.Azure.DocumentDB` przestrzeni nazw. Informacje zawarte w tym dokumencie można wykorzystać w przypadku migrowania aplikacji z dowolnych z następujących Azure Cosmos DB platform .NET do korzystania z zestawu SDK V2 `Microsoft.Azure.Cosmos` :

* Azure Cosmos DB .NET Framework V1 SDK dla interfejsu API SQL
* Azure Cosmos DB zestaw .NET Core SDK V1 dla interfejsu API SQL

## <a name="whats-available-in-the-net-v2-sdk"></a>Co jest dostępne w zestawie SDK platformy .NET V2

Zestaw v2 SDK zawiera wiele ulepszeń użyteczności i wydajności, w tym:

* Obsługa trybu bezpośredniego protokołu TCP dla klientów z systemem innym niż Windows
* Obsługa zapisu w wieloregionach
* Ulepszenia wydajności zapytań
* Obsługa kolekcji geoprzestrzennych/geometrycznych i indeksowania
* Ulepszone ulepszenia diagnostyki transportu bezpośredniego/TCP
* Aktualizacje na stosie transportowym bezpośredniego protokołu TCP w celu zmniejszenia liczby ustanowionych połączeń
* Ulepszenia zmniejszenia opóźnienia w RequestTimeout

Większość logiki ponownych prób i niższych poziomów zestawu SDK pozostaje w znacznym stopniu bez zmian.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Dlaczego należy przeprowadzić migrację do zestawu SDK platformy .NET V2

Oprócz licznych ulepszeń wydajności nowe funkcje inwestycji w najnowszy zestaw SDK nie zostaną przetworzone do starszych wersji.

Ponadto starsze zestawy SDK zostaną zastąpione nowszymi wersjami, a zestaw SDK V1 przejdzie w [tryb konserwacji](sql-api-sdk-dotnet.md). Aby uzyskać najlepsze środowisko programistyczne, zalecamy Migrowanie aplikacji do nowszej wersji zestawu SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Najważniejsze zmiany z zestawu SDK V1 do wersji 2 SDK

### <a name="direct-mode--tcp"></a>Tryb bezpośredni + TCP

Zestaw SDK platformy .NET V2 obsługuje teraz zarówno tryb bezpośrednie, jak i bramę. Tryb bezpośredni obsługuje łączność za pośrednictwem protokołu TCP i zapewnia lepszą wydajność, ponieważ łączy się bezpośrednio z replikami zaplecza przy mniejszej liczbie przeskoków sieci.

Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem dotyczącym trybów łączności Azure Cosmos DB SQL SDK](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Formatowanie tokenu sesji

Zestaw v2 SDK nie używa już formatu tokenu sesji *prostego* , który jest używany w wersji 1, a zamiast tego zestaw SDK używa formatowania *wektora* . Format powinien być konwertowany podczas przekazywania do aplikacji klienckiej z różnymi wersjami, ponieważ formaty nie są zamienne.

Aby uzyskać więcej informacji, zobacz [konwertowanie formatów tokenów sesji w zestawie .NET SDK](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Korzystanie z zestawu SDK procesora kanału informacyjnego platformy .NET

Biblioteka procesora kanału informacyjnego platformy .NET 2.1. x wymaga `Microsoft.Azure.DocumentDB` 2,0 lub nowszej.

Biblioteka 2.1. x ma następujące kluczowe zmiany:

* Ulepszenia stabilności i diagnostyki
* Ulepszona obsługa błędów i wyjątków
* Dodatkowa pomoc techniczna dla partycjonowanych kolekcji dzierżaw
* Zaawansowane rozszerzenia umożliwiające implementację `ChangeFeedDocument` interfejsu i klasy w celu uzyskania dodatkowej obsługi błędów i śledzenia
* Dodano obsługę używania magazynu niestandardowego do utrwalania tokenów kontynuacji na partycję

Aby uzyskać więcej informacji, zobacz [Informacje o wersji](sql-api-sdk-dotnet-changefeed.md)biblioteki procesora źródła zmian.

### <a name="using-the-bulk-executor-library"></a>Korzystanie z biblioteki wykonawców zbiorczych

Biblioteka wykonawczy Zbiorcza w wersji 2 ma obecnie zależność od zestawu SDK Azure Cosmos DB .NET 2.5.1 lub nowszego.

Aby uzyskać więcej informacji, zobacz [Omówienie biblioteki wykonawców zbiorczych Azure Cosmos DB](bulk-executor-overview.md) i [Informacje o wersji](sql-api-sdk-bulk-executor-dot-net.md)biblioteki modułu wykonawczego platformy .NET.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [dodatkowymi wskazówkami dotyczącymi wydajności](sql-api-get-started.md) za pomocą Azure Cosmos DB dla interfejsu SQL API v2 w celu optymalizacji aplikacji w celu osiągnięcia maksymalnej wydajności
* Dowiedz się więcej [na temat tego, co możesz zrobić z zestawem SDK w wersji 2](sql-api-dotnet-samples.md)