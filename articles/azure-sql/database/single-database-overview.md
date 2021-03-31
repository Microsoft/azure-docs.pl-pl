---
title: Co to jest pojedyncza baza danych?
description: Informacje o typie zasobu pojedynczej bazy danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343323"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Co to jest pojedyncza baza danych w Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Typ zasobu pojedynczej bazy danych tworzy bazę danych w Azure SQL Database z własnym zestawem zasobów i jest zarządzana za pośrednictwem [serwera](logical-servers.md). W przypadku pojedynczej bazy danych każda baza danych jest izolowana i przenośna. Każda z nich ma swoją własną warstwę usług w [modelu zakupu opartego na](service-tiers-dtu.md) jednostkach DTU lub [modelu zakupu opartego na rdzeń wirtualny](service-tiers-vcore.md) oraz gwarantowany rozmiar obliczeniowy.

> [!IMPORTANT]
> Pojedyncza baza danych jest jednym typem zasobu dla Azure SQL Database. Druga to [elastyczne pule](elastic-pool-overview.md).

## <a name="dynamic-scalability"></a>Skalowalność dynamiczna

Możesz utworzyć swoją pierwszą aplikację w małej, pojedynczej bazie danych przy niskich kosztach w warstwie obliczeniowej bez serwera lub w niewielkim rozmiarze obliczeniowym w warstwie obliczeniowej zainicjowanej. W dowolnym momencie możesz ręcznie lub programowo zmienić [warstwę obliczeniową lub usługę](single-database-scale.md) , aby zaspokoić potrzeby rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

## <a name="single-databases-and-elastic-pools"></a>Pojedyncze bazy danych i elastyczne pule

Pojedynczą bazę danych można przenieść do [puli elastycznej](elastic-pool-overview.md) lub z niej w celu udostępnienia zasobów. W przypadku wielu firm i aplikacji możliwość tworzenia pojedynczych baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza gdy wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. Pule elastyczne zostały zaprojektowane, aby rozwiązać ten problem. Koncepcja jest prosta. Zasoby wydajności są przydzielane do puli, a nie do pojedynczej bazy danych i płatne za zbiorowe zasoby wydajności puli, a nie za wydajność pojedynczej bazy danych.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Korzystasz z wbudowanych narzędzi do [monitorowania wydajności](performance-guidance.md) i [alertów](alerts-insights-configure-portal.md)w połączeniu z ocenami wydajności. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Ponadto SQL Database mogą [emitować metryki i dzienniki zasobów](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) w celu łatwiejszego monitorowania.

## <a name="availability-capabilities"></a>Możliwości dostępności

Pojedyncze bazy danych i pule elastyczne zapewniają wiele cech dostępności. Aby uzyskać więcej informacji, zobacz [charakterystyki dostępności](sql-database-paas-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Różnice w języku Transact-SQL

Większość funkcji języka Transact-SQL, które są używane przez aplikacje, jest w pełni obsługiwana zarówno w Microsoft SQL Server, jak i Azure SQL Database. Na przykład podstawowe składniki SQL, takie jak typy danych, operatory, ciągi, operacje arytmetyczne, logiczne i kursorowe, działają identycznie w SQL Server i SQL Database. Istnieją jednak pewne różnice w języku T-SQL w przypadku elementów języka DDL (definicja danych) i DML (Language operowania danymi), które wystąpiły w instrukcjach i zapytaniach języka T-SQL, które są tylko częściowo obsługiwane (które omówiono w dalszej części tego artykułu).

Ponadto istnieją pewne funkcje i składnia, które nie są obsługiwane, ponieważ Azure SQL Database zaprojektowano w celu izolowania funkcji z zależności od bazy danych Master i systemu operacyjnego. W związku z tym większość działań na poziomie serwera jest nieodpowiedni dla SQL Database. Instrukcje i Opcje języka T-SQL są niedostępne, jeśli skonfigurujesz opcje na poziomie serwera, Skonfiguruj składniki systemu operacyjnego lub określ konfigurację systemu plików. Gdy takie możliwości są wymagane, odpowiednia alternatywa jest często dostępna w inny sposób od SQL Database lub z innej funkcji lub usługi platformy Azure.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database](transact-sql-tsql-differences-sql-server.md).

## <a name="security"></a>Zabezpieczenia

SQL Database zapewnia szeroki zakres [wbudowanych funkcji zabezpieczeń i zgodności](security-overview.md) , aby pomóc aplikacji spełnić różne wymagania dotyczące zabezpieczeń i zgodności.

> [!IMPORTANT]
> Azure SQL Database został certyfikowany pod kątem wielu standardów zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), w którym można znaleźć najbardziej aktualną listę SQL Database certyfikatów zgodności.

## <a name="next-steps"></a>Następne kroki

- Aby szybko rozpocząć pracę z pojedynczą bazą danych, Zacznij od [przewodnika Szybki Start dla jednej bazy danych](quickstart-content-reference-guide.md).
- Aby dowiedzieć się więcej na temat migrowania bazy danych SQL Server na platformę Azure, zobacz [Migrowanie do Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](features-comparison.md).
