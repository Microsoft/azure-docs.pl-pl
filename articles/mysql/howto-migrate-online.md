---
title: Migracja z minimalnym przestojem — Azure Database for MySQL
description: W tym artykule opisano, jak przeprowadzić migrację bazy danych MySQL o minimalnym przestoju do Azure Database for MySQL przy użyciu Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 4c7fe3db3f85824dc2cfbe87fc22b63a59e931ab
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546368"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migracja z minimalnym przestojem do Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Migracje MySQL można przeprowadzić w celu Azure Database for MySQL z minimalnym przestojem przy użyciu nowo wprowadzonej **funkcji ciągłej synchronizacji** dla [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Ta funkcja ogranicza czas przestoju nanoszony przez aplikację.

## <a name="overview"></a>Omówienie
Usługa Azure DMS wykonuje początkowe ładowanie danych lokalnych w celu Azure Database for MySQL, a następnie nieustannie synchronizuje wszelkie nowe transakcje z platformą Azure, gdy aplikacja pozostaje uruchomiona. Po przechwyceniu danych na docelowej stronie platformy Azure aplikacja zostanie zatrzymana przez krótki czas (w minimalnym przestoju). Poczekaj na ostatnią partię danych (od momentu zatrzymania aplikacji do momentu, gdy aplikacja nie będzie efektywnie dostępna do wykonania nowego ruchu), a następnie zaktualizuj parametry połączenia tak, aby wskazywały platformę Azure. Gdy skończysz, Twoja aplikacja będzie aktywna na platformie Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Ciągła synchronizacja z Azure Database Migration Service":::

## <a name="next-steps"></a>Następne kroki
- Obejrzyj wideo z [łatwością Migruj aplikacje MySQL/PostgreSQL do usługi zarządzanej platformy Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), która zawiera demonstrację prezentującą sposób migrowania aplikacji mysql do Azure Database for MySQL.
- Zapoznaj się z samouczkiem [Migrowanie bazy danych MySQL do Azure Database for MySQL online przy użyciu usługi DMS](../dms/tutorial-mysql-azure-mysql-online.md).