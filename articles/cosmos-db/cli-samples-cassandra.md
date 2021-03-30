---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB interfejs API Cassandra
description: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB interfejs API Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2dbae196b981b792b47e6c12e500398f772d8451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342134"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB interfejs API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

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

## <a name="cassandra-api-samples"></a>Przykłady interfejs API Cassandra

|Zadanie | Opis |
|---|---|
| [Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy i tabeli](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, miejsce i tabelę dla interfejs API Cassandra. |
| [Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy i tabeli przy użyciu funkcji automatycznego skalowania](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy Azure Cosmos DB konto, miejsce i tabelę z funkcją automatycznego skalowania dla interfejs API Cassandra. |
| [Operacje przepływności](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Odczytuj, Aktualizuj i Migruj między funkcją automatycznego skalowania i standardową przepływność na przestrzeni kluczy i tabeli.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||
