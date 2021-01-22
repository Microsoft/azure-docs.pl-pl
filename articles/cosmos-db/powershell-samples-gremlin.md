---
title: Przykłady Azure PowerShell dla interfejsu API usługi Azure Cosmos DB Gremlin
description: Pobierz przykłady Azure PowerShell, aby wykonywać typowe zadania w interfejsie API usługi Azure Cosmos DB Gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 393b5988c65b0c3f0ade85f014b1bd9f7d38abdc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679343"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Przykłady Azure PowerShell dla interfejsu API usługi Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Poniższa tabela zawiera linki do często używanych skryptów Azure PowerShell dla Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [odwołaniu Azure PowerShell](/powershell/module/az.cosmosdb)dostępne są strony referencyjne dla wszystkich poleceń cmdlet programu PowerShell w programie Azure Cosmos DB. `Az.CosmosDB`Moduł jest teraz częścią `Az` modułu. [Pobierz i zainstaluj](/powershell/azure/install-az-ps?preserve-view=true&view=azps-5.4.0) najnowszą wersję polecenia AZ module w celu pobrania Azure Cosmos DB poleceń cmdlet. Możesz również uzyskać najnowszą wersję z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). Możesz również utworzyć rozwidlenie tych przykładów programu PowerShell dla Cosmos DB z naszego repozytorium GitHub, [Cosmos DB przykłady programu PowerShell w witrynie GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Typowe przykłady

|Zadanie | Opis |
|---|---|
|[Aktualizowanie konta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie domyślnego poziomu spójności konta Cosmos DB. |
|[Aktualizowanie regionów konta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie regionów konta Cosmos DB. |
|[Zmiana priorytetu trybu failover lub wyzwolenie trybu failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet pracy w trybie failover dla konta usługi Azure Cosmos lub Wyzwól ręczną pracę awaryjną. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie Wygeneruj klucz konta Azure Cosmos DBego konta. |
|[Tworzenie konta Cosmos za pomocą zapory IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB z włączoną zaporą IP. |
|||

## <a name="gremlin-api-samples"></a>Przykłady interfejsu API Gremlin

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, bazy danych i grafu](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, bazę danych i Graf. |
|[Tworzenie konta, bazy danych i grafu przy użyciu funkcji automatycznego skalowania](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, bazę danych i Graf z automatycznym skalowaniem. |
|[Wyświetlanie listy lub pobieranie baz danych lub wykresów](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetl lub Pobierz bazę danych lub Graf. |
|[Operacje przepływności](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operacje przepływności dla bazy danych lub grafu, w tym pobieranie, aktualizowanie i Migrowanie między funkcją automatycznego skalowania i standardową przepływność. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||
