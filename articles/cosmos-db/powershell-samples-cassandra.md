---
title: Przykłady Azure PowerShell dla Azure Cosmos DB interfejs API Cassandra
description: Pobierz przykłady Azure PowerShell, aby wykonywać typowe zadania w Azure Cosmos DB interfejs API Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 15e0f221141062b2239b588889036849d3b1d91e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505209"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Przykłady Azure PowerShell dla Azure Cosmos DB interfejs API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Poniższa tabela zawiera linki do często używanych skryptów Azure PowerShell dla Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [odwołaniu Azure PowerShell](/powershell/module/az.cosmosdb)dostępne są strony referencyjne dla wszystkich poleceń cmdlet programu PowerShell w programie Azure Cosmos DB. `Az.CosmosDB`Moduł jest teraz częścią `Az` modułu. [Pobierz i zainstaluj](/powershell/azure/install-az-ps) najnowszą wersję polecenia AZ module w celu pobrania Azure Cosmos DB poleceń cmdlet. Możesz również uzyskać najnowszą wersję z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). Możesz również utworzyć rozwidlenie tych przykładów programu PowerShell dla Cosmos DB z naszego repozytorium GitHub, [Cosmos DB przykłady programu PowerShell w witrynie GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Typowe przykłady

|Zadanie | Opis |
|---|---|
|[Aktualizowanie konta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie domyślnego poziomu spójności konta Cosmos DB. |
|[Aktualizowanie regionów konta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie regionów konta Cosmos DB. |
|[Zmiana priorytetu trybu failover lub wyzwolenie trybu failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet pracy w trybie failover dla konta usługi Azure Cosmos lub Wyzwól ręczną pracę awaryjną. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie Wygeneruj klucz konta Azure Cosmos DBego konta. |
|[Tworzenie konta Cosmos za pomocą zapory IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB z włączoną zaporą IP. |
|||

## <a name="cassandra-api-samples"></a>Przykłady interfejs API Cassandra

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, przestrzeni kluczy i tabeli](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, przestrzeń kluczy i tabelę. |
|[Tworzenie konta, przestrzeni kluczy i tabeli przy użyciu funkcji automatycznego skalowania](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, przestrzeń kluczy i tabelę z funkcją automatycznego skalowania. |
|[Wyświetlanie lub pobieranie miejsc lub tabel](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetlanie lub pobieranie miejsc lub tabel. |
|[Operacje przepływności](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operacje przepływności dla przestrzeni kluczy lub tabeli, w tym pobieranie, aktualizowanie i Migrowanie między funkcją automatycznego skalowania i standardową przepływność. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||
