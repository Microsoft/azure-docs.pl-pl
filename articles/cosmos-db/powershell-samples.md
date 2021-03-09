---
title: Przykłady Azure PowerShell dla interfejsu API Azure Cosmos DB Core (SQL)
description: Pobierz przykłady Azure PowerShell, aby wykonywać typowe zadania w interfejsie API Azure Cosmos DB for Core (SQL)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: ab9904127d085113ba09bf6fcd3616842dade14d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503314"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Przykłady Azure PowerShell dla interfejsu API Azure Cosmos DB Core (SQL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Poniższa tabela zawiera linki do często używanych skryptów Azure PowerShell dla Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [odwołaniu Azure PowerShell](/powershell/module/az.cosmosdb)dostępne są strony referencyjne dla wszystkich poleceń cmdlet programu PowerShell w programie Azure Cosmos DB. `Az.CosmosDB`Moduł jest teraz częścią `Az` modułu. [Pobierz i zainstaluj](/powershell/azure/install-az-ps) najnowszą wersję polecenia AZ module w celu pobrania Azure Cosmos DB poleceń cmdlet. Możesz również uzyskać najnowszą wersję z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). Możesz również utworzyć rozwidlenie tych przykładów programu PowerShell dla Cosmos DB z naszego repozytorium GitHub, [Cosmos DB przykłady programu PowerShell w witrynie GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Dla poleceń cmdlet programu PowerShell dla innych interfejsów API zobacz [przykłady programu PowerShell dla Cassandra](powershell-samples-cassandra.md), przykłady programu PowerShell dla [interfejsu API MongoDB](powershell-samples-mongodb.md), [przykłady programu PowerShell dla usługi Gremlin](powershell-samples-gremlin.md), [przykłady dla programu](powershell-samples-table.md) PowerShell dla tabeli

## <a name="common-samples"></a>Typowe przykłady

|Zadanie | Opis |
|---|---|
|[Aktualizowanie konta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie domyślnego poziomu spójności konta Cosmos DB. |
|[Aktualizowanie regionów konta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie regionów konta Cosmos DB. |
|[Zmiana priorytetu trybu failover lub wyzwolenie trybu failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet pracy w trybie failover dla konta usługi Azure Cosmos lub Wyzwól ręczną pracę awaryjną. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie Wygeneruj klucz konta Azure Cosmos DBego konta. |
|[Tworzenie konta Cosmos za pomocą zapory IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB z włączoną zaporą IP. |
|||

## <a name="core-sql-api-samples"></a>Przykłady interfejsu API programu Core (SQL)

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, bazy danych i kontenera](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB, bazę danych i kontener. |
|[Tworzenie konta, bazy danych i kontenera z funkcją automatycznego skalowania](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB, bazę danych i kontener przy użyciu funkcji automatycznego skalowania. |
|[Tworzenie kontenera z dużym kluczem partycji](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz kontener z dużym kluczem partycji. |
|[Tworzenie kontenera bez zasad indeksu](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Utwórz kontener usługi Azure Cosmos z wyłączonymi zasadami indeksu.|
|[Wyświetlanie listy lub pobieranie baz danych lub kontenerów](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetlanie listy lub pobieranie bazy danych lub kontenerów. |
|[Operacje przepływności](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operacje przepływności dla bazy danych lub kontenera, w tym pobieranie, aktualizowanie i Migrowanie między funkcją automatycznego skalowania i standardową przepływność. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||
