---
title: Stan scenariusza migracji bazy danych
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej o stanie scenariuszy migracji obsługiwanych przez Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: f06c1a055b2f08259bf5318afe3c969d763bbbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818624"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stan scenariuszy migracji obsługiwanych przez Azure Database Migration Service

Azure Database Migration Service jest przeznaczony do obsługi różnych scenariuszy migracji (par źródłowych/docelowych) zarówno w przypadku migracji w trybie offline (jeden raz), jak i w trybie online (ciągła synchronizacja). Zakres scenariuszy zapewniany przez Azure Database Migration Service jest rozszerzany w czasie. Nowe scenariusze są regularnie dodawane. W tym artykule określono scenariusze migracji obecnie obsługiwane przez usługę Azure Database Migration Service oraz stan (prywatna wersja zapoznawcza, publiczna wersja zapoznawcza lub ogólna dostępność) dla każdego scenariusza.

## <a name="offline-versus-online-migrations"></a>Migracje offline i online

Za Azure Database Migration Service można wykonać migrację w trybie offline lub online. W *przypadku* migracji w trybie offline przestój aplikacji rozpoczyna się w tym samym czasie, w których rozpoczyna się migracja. Aby ograniczyć czas przestoju do czasu wymaganego do przeciętych do nowego środowiska po zakończeniu migracji, użyj migracji *online.* Zaleca się przetestowanie migracji w trybie offline w celu określenia, czy przestój jest dopuszczalny. Jeśli nie, wykonaj migrację w trybie online.

## <a name="migration-scenario-status"></a>Stan scenariusza migracji

Stan scenariuszy migracji obsługiwanych przez Azure Database Migration Service zależy od czasu. Ogólnie rzecz biorąc, scenariusze są po raz pierwszy wydane w prywatnej **wersji zapoznawczej**. Po prywatnej wersji zapoznawczej stan scenariusza zmieni się na **publiczną.** Azure Database Migration Service mogą wypróbować scenariusze migracji w publicznej wersji zapoznawczej bezpośrednio z interfejsu użytkownika. Rejestracja nie jest wymagana.  Jednak scenariusze migracji w publicznej wersji zapoznawczej mogą nie być dostępne we wszystkich regionach i mogą zostać poddane dodatkowym zmianom przed wydaniem końcowym. Po publicznej wersji zapoznawczej stan scenariusza zmieni się na **ogólnie dostępną.** Ogólna dostępność to końcowy stan wydania, a funkcjonalność jest kompletna i dostępna dla wszystkich użytkowników.

## <a name="migration-scenario-support"></a>Obsługa scenariuszy migracji

W poniższych tabelach przedstawiono scenariusze migracji obsługiwane w przypadku korzystania z Azure Database Migration Service.

> [!NOTE]
> Jeśli scenariusz wymieniony jako obsługiwany poniżej nie jest wyświetlany w interfejsie użytkownika, skontaktuj się z aliasem Ask [Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) (Poproś o alias migracji bazy danych platformy Azure), aby uzyskać dodatkowe informacje.

> [!IMPORTANT]
> Aby wyświetlić wszystkie scenariusze obecnie obsługiwane przez Azure Database Migration Service prywatnej wersji zapoznawczej, zobacz [witrynę DMS (wersja zapoznawcza).](https://aka.ms/dms-preview)

### <a name="offline-one-time-migration-support"></a>Obsługa migracji w trybie offline (raz)

W poniższej tabeli przedstawiono Azure Database Migration Service migracji offline.

| Cel  | Element źródłowy | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **Maszyna wirtualna Azure SQL** | SQL Server | ✔ | Ogólna dostępność |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Ogólna dostępność |
| **Azure DB dla MySQL** | MySQL | ✔ |   |
|   | RDS MySQL | X |   |
| **Azure DB for PostgreSQL — pojedynczy serwer** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL — Hiperskala (Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Obsługa migracji w trybie online (ciągła synchronizacja)

W poniższej tabeli przedstawiono Azure Database Migration Service migracji online.

| Cel  | Element źródłowy | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | X |  |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Maszyna wirtualna Azure SQL** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Ogólna dostępność |
| **Azure DB dla MySQL** | MySQL | ✔ | Ogólna dostępność |
|   | RDS MySQL | ✔ | Ogólna dostępność |
| **Azure DB for PostgreSQL — pojedynczy serwer** | PostgreSQL | ✔ | Ogólna dostępność |
|   | Azure DB for PostgreSQL — pojedynczy serwer | ✔ | Ogólna dostępność |
|   | RDS PostgreSQL | ✔ | Ogólna dostępność |
|   | Oracle | ✔ | Publiczna wersja zapoznawcza (przestarzała po 1 maja 2021 r.) |
| **Azure DB for PostgreSQL — Hiperskala (Citus)** | PostgreSQL | ✔ | Ogólna dostępność |
|   | RDS PostgreSQL | ✔ | Ogólna dostępność |

> [!IMPORTANT]
> Scenariusz migracji "Oracle to Azure Database for PostgreSQL" (obecnie w wersji zapoznawczej) nie będzie już dostępny po 1 maja 2021 r. Będziemy nadal zapewniać pomoc techniczną za pośrednictwem alternatywnych narzędzi (takich jak Ora2pg) i zapewniać najlepsze środowisko migracji dla migracji Oracle do PostgreSQL. Aby uzyskać najlepsze rozwiązania dotyczące migracji, zobacz Oracle to Azure Database for PostgreSQL migration guide (Przewodnik migracji z bazy [danych Oracle Azure Database for PostgreSQL migracji).](https://aka.ms/OracletoPGguide)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie Azure Database Migration Service i dostępności regionalnej, zobacz artykuł [Co to jest Azure Database Migration Service](dms-overview.md).
