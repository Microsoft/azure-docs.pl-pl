---
title: Co to jest usługa Azure Database Migration Service?
description: Przegląd Azure Database Migration Service, który zapewnia bezproblemową migrację z wielu źródeł baz danych do platform danych platformy Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 02/20/2020
ms.openlocfilehash: 328c29afee3752ecb11b83f22d67f20aa3a2c93e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963015"
---
# <a name="what-is-azure-database-migration-service"></a>Co to jest usługa Azure Database Migration Service?

Azure Database Migration Service to w pełni zarządzana usługa, która umożliwia bezproblemowe Migrowanie z wielu źródeł baz danych do platform danych platformy Azure z minimalnym czasem przestoju (migracje online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrowanie baz danych na platformę Azure przy użyciu znanych narzędzi

Azure Database Migration Service integruje niektóre funkcje naszych istniejących narzędzi i usług. Zapewnia klientom kompleksowe rozwiązanie o wysokiej dostępności. Usługa używa [Data Migration Assistant](/sql/dma/dma-overview) do generowania raportów oceny, które zawierają zalecenia, które przeprowadzą Cię przez zmiany wymagane przed przeprowadzeniem migracji. Należy wykonać wszelkie wymagane korekty. Gdy wszystko będzie gotowe do rozpoczęcia procesu migracji, Azure Database Migration Service wykonuje wszystkie wymagane kroki. Możesz uruchomić proces migracji i spokojnie pozostawić go bez nadzoru, mając pewność, że podczas jego wykonywania zostaną wybrane najlepsze rozwiązania określone przez firmę Microsoft. 

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

## <a name="regional-availability"></a>Dostępność w regionach

Aby uzyskać aktualne informacje o dostępności regionalnej Azure Database Migration Service, zobacz [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Ceny

Aby uzyskać aktualne informacje o cenach Azure Database Migration Service, zobacz [Azure Database Migration Service Cennik](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Następne kroki

* [Stan scenariuszy migracji obsługiwanych przez Azure Database Migration Service](resource-scenario-status.md).
* [Utwórz wystąpienie Azure Database Migration Service przy użyciu Azure Portal](quickstart-create-data-migration-service-portal.md).
* [Przeprowadź migrację SQL Server do Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
* [Przegląd wymagań wstępnych dotyczących korzystania z Azure Database Migration Service](pre-reqs.md).
* [Często zadawane pytania dotyczące korzystania z Azure Database Migration Service](faq.md).
* [Usługi i narzędzia dostępne dla scenariuszy migracji danych](dms-tools-matrix.md).