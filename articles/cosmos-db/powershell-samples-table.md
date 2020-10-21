---
title: Przykłady Azure PowerShell dla Azure Cosmos DB interfejs API tabel
description: Pobierz przykłady Azure PowerShell, aby wykonywać typowe zadania w Azure Cosmos DB interfejs API tabel
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: d48d16d13bf35636950366cba7cd946f01afcff0
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92285103"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Przykłady Azure PowerShell dla Azure Cosmos DB interfejs API tabel

Poniższa tabela zawiera linki do często używanych skryptów Azure PowerShell dla Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [odwołaniu Azure PowerShell](/powershell/module/az.cosmosdb)dostępne są strony referencyjne dla wszystkich poleceń cmdlet programu PowerShell w programie Azure Cosmos DB. Sprawdź `Az.CosmosDB` regularnie aktualizacje. Możesz również utworzyć rozwidlenie tych przykładów programu PowerShell dla Cosmos DB z naszego repozytorium GitHub, [Cosmos DB przykłady programu PowerShell w witrynie GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Typowe przykłady

|Zadanie | Opis |
|---|---|
|[Aktualizowanie konta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie domyślnego poziomu spójności konta Cosmos DB. |
|[Aktualizowanie regionów konta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie regionów konta Cosmos DB. |
|[Zmiana priorytetu trybu failover lub wyzwolenie trybu failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet pracy w trybie failover dla konta usługi Azure Cosmos lub Wyzwól ręczną pracę awaryjną. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie Wygeneruj klucz konta Azure Cosmos DBego konta. |
|[Tworzenie konta Cosmos za pomocą zapory IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB z włączoną zaporą IP. |
|||

## <a name="table-api-samples"></a>Przykłady interfejs API tabel

|Zadanie | Opis |
|---|---|
|[Tworzenie konta i tabeli](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto i tabelę platformy Azure Cosmos. |
|[Tworzenie konta i tabeli przy użyciu funkcji automatycznego skalowania](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos i automatyczne skalowanie tabeli. |
|[Wyświetlanie listy lub Pobieranie tabel](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetl listę lub Pobierz tabele. |
|[Operacje przepływności](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operacje przepływności dla tabeli, w tym pobieranie, aktualizowanie i Migrowanie między funkcją automatycznego skalowania i standardową przepływność. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||
