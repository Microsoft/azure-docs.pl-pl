---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla programu Azure Cosmos DB Core (SQL)
description: Przykłady interfejsu wiersza polecenia platformy Azure dla programu Azure Cosmos DB Core (SQL)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c7baf4de56edd25537f93095bfa6ae0402214e2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073183"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla programu Azure Cosmos DB Core (SQL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Poniższa tabela zawiera linki do przykładowych skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb)są dostępne strony referencyjne dla wszystkich poleceń CLI Azure Cosmos DB. Przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można również znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Te przykłady wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

Przykłady interfejsu wiersza polecenia platformy Azure dla innych interfejsów API, zobacz przykłady interfejsu wiersza polecenia [dla Cassandra](cli-samples-cassandra.md), [przykłady interfejsu wiersza polecenia dla MongoDB API](cli-samples-mongodb.md), przykłady interfejsu wiersza polecenia [dla Gremlin](cli-samples-gremlin.md), [przykłady interfejsu wiersza polecenia dla tabeli](cli-samples-table.md)

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

## <a name="core-sql-api-samples"></a>Przykłady interfejsu API programu Core (SQL)

|Zadanie | Opis |
|---|---|
| [Tworzenie konta, bazy danych i kontenera usługi Azure Cosmos](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kontener dla podstawowego (SQL) interfejsu API. |
| [Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera z funkcją automatycznego skalowania](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kontener z interfejsem API skalowania automatycznego (SQL). |
| [Operacje przepływności](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Odczytywanie, aktualizowanie i Migrowanie między skalowaniem automatycznym i standardowym przepływności bazy danych i kontenera.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||
