---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API MongoDB
description: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: af510e5d29e494e96f6df6143fe012db20fb6ca4
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509461"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API MongoDB

Poniższa tabela zawiera linki do przykładowych skryptów interfejsu wiersza polecenia platformy Azure służących do Azure Cosmos DB interfejsu API MongoDB. W [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb)są dostępne strony referencyjne dla wszystkich poleceń CLI Azure Cosmos DB. Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Obecnie można utworzyć tylko 3,2 wersji (czyli kont przy użyciu punktu końcowego w formacie `*.documents.azure.com` ) interfejsu API Azure Cosmos DB dla kont MongoDB przy użyciu szablonów programu PowerShell, interfejsu wiersza polecenia i Menedżer zasobów. Aby utworzyć 3,6 wersji kont, należy zamiast tego użyć Azure Portal.

| |  |
|---|---|
| [Tworzenie konta, bazy danych i kolekcji usługi Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kolekcję dla interfejsu API MongoDB. |
| [Zmień przepływność](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Zaktualizuj RU/s w bazie danych i kolekcji.|
| [Dodawanie regionów lub przełączanie do trybu failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Dodaj region, Zmień priorytet trybu failover i Wyzwól ręczny tryb failover.|
| [Klucze konta i parametry połączenia](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Wyświetl listę kluczy konta, klucze tylko do odczytu, Wygeneruj ponownie klucze i Wyświetl listę parametrów połączenia.|
| [Zabezpieczanie za pomocą zapory IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos z skonfigurowaną zaporą IP.|
| [Zabezpiecz nowe konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos i zabezpiecz je za pomocą punktów końcowych usługi.|
| [Zabezpiecz istniejące konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Zaktualizuj konto usługi Cosmos, aby zabezpieczyć za pomocą punktów końcowych, gdy podsieć jest ostatecznie skonfigurowana.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||
