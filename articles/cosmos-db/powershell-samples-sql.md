---
title: Przykłady Azure PowerShell dla interfejsu API Azure Cosmos DB-SQL (rdzeń)
description: Pobierz przykłady Azure PowerShell, aby wykonywać różne typowe zadania w Azure Cosmos DB kontach interfejsu API SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: fc4ec916e71f2fcfd3b411420879d42b2fa90f18
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563850"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Przykłady Azure PowerShell dla interfejsu API Azure Cosmos DB-SQL (rdzeń)

Poniższa tabela zawiera linki do często używanych skryptów Azure PowerShell dla interfejsu API Azure Cosmos DB for SQL (Core). Jeśli chcesz utworzyć rozwidlenie przykładów programu PowerShell dla Cosmos DB z naszego repozytorium GitHub, odwiedź [Cosmos DB przykłady programu PowerShell w witrynie GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Aby uzyskać dodatkowe przykłady Cosmos DB programu PowerShell dla interfejsu API i dokumentacji języka SQL (rdzeń), zobacz [Zarządzanie zasobami Azure Cosmos DB interfejsu API SQL przy użyciu programu PowerShell](manage-with-powershell.md). Aby uzyskać Cosmos DB przykładów programu PowerShell dla innych interfejsów API, zobacz [interfejs API Cassandra](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)i [interfejs API tabel](powershell-samples-table.md).

> [!NOTE]
> Przykłady używają poleceń cmdlet [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management. Sprawdź `Az.CosmosDB` regularnie aktualizacje.

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, bazy danych i kontenera](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB, bazę danych i kontener. |
|[Tworzenie kontenera z dużym kluczem partycji](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz kontener z dużym kluczem partycji. |
|[Wyświetlanie listy lub pobieranie baz danych lub kontenerów](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetlanie listy lub pobieranie bazy danych lub kontenerów. |
|[Pobierz RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz RU/s dla bazy danych lub kontenera. |
|[Aktualizowanie RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zaktualizuj RU/s dla bazy danych lub kontenera. |
|[Tworzenie kontenera bez zasad indeksu](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Utwórz kontener usługi Azure Cosmos z wyłączonymi zasadami indeksu.|
|[Aktualizowanie konta](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie domyślnego poziomu spójności konta Cosmos DB. |
|[Aktualizowanie regionów konta](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie regionów konta Cosmos DB. |
|[Zmiana priorytetu trybu failover lub wyzwolenie trybu failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet pracy w trybie failover dla konta usługi Azure Cosmos lub Wyzwól ręczną pracę awaryjną. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie Wygeneruj klucz konta Azure Cosmos DBego konta. |
|[Tworzenie konta Cosmos za pomocą zapory IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB z włączoną zaporą IP. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/sql/powershell-sql-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||
