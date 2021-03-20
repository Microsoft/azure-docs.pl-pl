---
title: Migracja z minimalnym przestojem do Azure Database for PostgreSQL-pojedynczego serwera
description: W tym artykule opisano, jak przeprowadzić migrację bazy danych PostgreSQL o minimalnym przestoju do Azure Database for PostgreSQL-pojedynczego serwera przy użyciu Azure Database Migration Service.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489799"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migracja z minimalnym przestojem do Azure Database for PostgreSQL-pojedynczego serwera
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

Migracje PostgreSQL można przeprowadzić w celu Azure Database for PostgreSQL z minimalnym przestojem przy użyciu nowo wprowadzonej **funkcji ciągłej synchronizacji** dla [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Ta funkcja ogranicza czas przestoju nanoszony przez aplikację.

## <a name="overview"></a>Omówienie
Usługa Azure DMS wykonuje początkowe ładowanie danych lokalnych w celu Azure Database for PostgreSQL, a następnie nieustannie synchronizuje wszelkie nowe transakcje z platformą Azure, gdy aplikacja pozostaje uruchomiona. Po przechwyceniu danych na docelowej stronie platformy Azure aplikacja zostanie zatrzymana przez krótki czas (w minimalnym przestoju). Poczekaj na ostatnią partię danych (od momentu zatrzymania aplikacji do momentu, gdy aplikacja nie będzie efektywnie dostępna do wykonania nowego ruchu), a następnie zaktualizuj parametry połączenia tak, aby wskazywały platformę Azure. Gdy skończysz, Twoja aplikacja będzie aktywna na platformie Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Ciągła synchronizacja z Azure Database Migration Service":::

## <a name="next-steps"></a>Następne kroki
- Zobacz [modernizację aplikacji wideo za pomocą Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), która zawiera demonstrację prezentującą sposób migrowania aplikacji PostgreSQL do Azure Database for PostgreSQL.
- Zapoznaj się z samouczkiem [Migrowanie PostgreSQL do Azure Database for PostgreSQL online przy użyciu usługi DMS](../dms/tutorial-postgresql-azure-postgresql-online.md).