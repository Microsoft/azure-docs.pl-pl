---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB API for MongoDB
description: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB API for MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: b17d3b0072d893751586f87d9a4ceb7ac8607416
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342100"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB API for MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Poniższa tabela zawiera linki do przykładowych skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb)są dostępne strony referencyjne dla wszystkich poleceń CLI Azure Cosmos DB. Przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można również znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Te przykłady wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Typowe przykłady

Te przykłady dotyczą wszystkich interfejsów API Azure Cosmos DB

|Zadanie | Opis |
|---|---|
| [Dodawanie regionów lub przełączanie do trybu failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Dodaj region, Zmień priorytet trybu failover i Wyzwól ręczny tryb failover.|
| [Klucze konta i parametry połączenia](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Wyświetl listę kluczy konta, klucze tylko do odczytu, Wygeneruj ponownie klucze i Wyświetl listę parametrów połączenia.|
| [Zabezpieczanie za pomocą zapory IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos z skonfigurowaną zaporą IP.|
| [Zabezpiecz nowe konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos i zabezpiecz je za pomocą punktów końcowych usługi.|
| [Zabezpiecz istniejące konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Zaktualizuj konto usługi Cosmos, aby zabezpieczyć za pomocą punktów końcowych, gdy podsieć jest ostatecznie skonfigurowana.|
|||

## <a name="mongodb-api-samples"></a>Przykłady interfejsu API MongoDB

|Zadanie | Opis |
|---|---|
| [Tworzenie konta, bazy danych i kolekcji usługi Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kolekcję dla interfejsu API MongoDB. |
| [Utwórz konto usługi Azure Cosmos, bazę danych z funkcją automatycznego skalowania i dwie kolekcje z udostępnioną przepływność](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych z funkcją automatycznego skalowania i dwie kolekcje z udostępnioną przepływem dla interfejsu API MongoDB. |
| [Operacje przepływności](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Odczytuj, Aktualizuj i Migruj między funkcją automatycznego skalowania i standardową przepływność w bazie danych i kolekcji.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||
