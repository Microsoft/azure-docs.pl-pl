---
title: Przykłady Azure PowerShell dla interfejsu API Azure Cosmos DB MongoDB
description: Pobierz przykłady Azure PowerShell, aby wykonywać różne typowe zadania w interfejsie API Azure Cosmos DB dla MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366209"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Przykłady Azure PowerShell dla interfejsu API usługi Azure Cosmos DB MongoDB

Poniższa tabela zawiera linki do przykładowych skryptów Azure PowerShell dla interfejsu API Azure Cosmos DB for MongoDB.

> [!NOTE]
> Obecnie można utworzyć tylko 3,2 wersji (czyli kont przy użyciu punktu końcowego w formacie `*.documents.azure.com`) interfejsu API Azure Cosmos DB dla kont MongoDB przy użyciu szablonów programu PowerShell, interfejsu wiersza polecenia i Menedżer zasobów. Aby utworzyć 3,6 wersji kont, należy zamiast tego użyć Azure Portal.

> [!NOTE]
> Przykłady używają poleceń cmdlet [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management. Należy pamiętać, że `Az.CosmosDB` polecenia cmdlet są nadal dostępne w wersji zapoznawczej i mogą ulec zmianie przed wydaniem. Sprawdź `Az.CosmosDB` regularnie aktualizacje.

| | |
|---|---|
|[Tworzenie konta, bazy danych i kolekcji](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto, bazę danych i kolekcję usługi Azure Cosmos. |
|[Wyświetlanie listy lub pobieranie baz danych lub kolekcji](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetlanie listy lub pobieranie bazy danych lub kolekcji. |
|[Pobierz RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz RU/s dla bazy danych lub kolekcji. |
|[Aktualizowanie RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zaktualizuj RU/s dla bazy danych lub kolekcji. |
|[Aktualizowanie konta lub Dodawanie regionu](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Dodaj region do konta Cosmos. Można również użyć do modyfikacji innych właściwości konta, ale muszą one być oddzielone od zmian w regionach. |
|[Zmiana priorytetu trybu failover lub wyzwolenie trybu failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet pracy w trybie failover dla konta usługi Azure Cosmos lub Wyzwól ręczną pracę awaryjną. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie Wygeneruj klucz konta dla konta usługi Azure Cosmos. |
|[Tworzenie konta Cosmos za pomocą zapory IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto usługi Azure Cosmos z włączoną zaporą IP. |
|||
