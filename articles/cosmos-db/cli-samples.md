---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB
description: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 954215f04525e850151fdad93af6e7272b41b3df
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498467"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do przykładowych skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb)są dostępne strony referencyjne dla wszystkich poleceń CLI Azure Cosmos DB. Przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można również znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Te przykłady wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Typowe przykłady

Te przykłady dotyczą wszystkich interfejsów API Azure Cosmos DB

|Zadanie | Opis |
|---|---|
| [Dodawanie regionów lub przełączanie do trybu failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Dodaj region, Zmień priorytet trybu failover i Wyzwól ręczny tryb failover.|
| [Klucze konta i parametry połączenia](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Wyświetl listę kluczy konta, klucze tylko do odczytu, Wygeneruj ponownie klucze i Wyświetl listę parametrów połączenia.|
| [Zabezpieczanie za pomocą zapory IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos z skonfigurowaną zaporą IP.|
| [Zabezpiecz nowe konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos i zabezpiecz je za pomocą punktów końcowych usługi.|
| [Zabezpiecz istniejące konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Zaktualizuj konto usługi Cosmos, aby zabezpieczyć za pomocą punktów końcowych, gdy podsieć jest ostatecznie skonfigurowana.|
|||

## <a name="core-sql-api-samples"></a>Przykłady interfejsu API programu Core (SQL)

|Zadanie | Opis |
|---|---|
| [Tworzenie konta, bazy danych i kontenera usługi Azure Cosmos](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kontener dla podstawowego (SQL) interfejsu API. |
| [Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera z funkcją automatycznego skalowania](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kontener z interfejsem API skalowania automatycznego (SQL). |
| [Zmień przepływność](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Zaktualizuj RU/s w bazie danych i kontenerze.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||

## <a name="mongodb-api-samples"></a>Przykłady interfejsu API MongoDB

|Zadanie | Opis |
|---|---|
| [Tworzenie konta, bazy danych i kolekcji usługi Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kolekcję dla interfejsu API MongoDB. |
| [Utwórz konto usługi Azure Cosmos, bazę danych z funkcją automatycznego skalowania i dwie kolekcje z udostępnioną przepływność](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych z funkcją automatycznego skalowania i dwie kolekcje z udostępnioną przepływem dla interfejsu API MongoDB. |
| [Zmień przepływność](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Zaktualizuj RU/s w bazie danych i kolekcji.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||

## <a name="cassandra-api-samples"></a>Przykłady interfejs API Cassandra

|Zadanie | Opis |
|---|---|
| [Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy i tabeli](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, miejsce i tabelę dla interfejs API Cassandra. |
| [Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy i tabeli przy użyciu funkcji automatycznego skalowania](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy Azure Cosmos DB konto, miejsce i tabelę z funkcją automatycznego skalowania dla interfejs API Cassandra. |
| [Zmień przepływność](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Zaktualizuj RU/s na przestrzeni kluczy i tabeli.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||

## <a name="gremlin-api-samples"></a>Przykłady interfejsu API Gremlin

|Zadanie | Opis |
|---|---|
| [Tworzenie konta, bazy danych i grafu usługi Azure Cosmos](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i Graf dla interfejsu API Gremlin. |
| [Tworzenie konta usługi Azure Cosmos, bazy danych i grafu przy użyciu funkcji automatycznego skalowania](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i Graf z funkcją automatycznego skalowania dla interfejsu API Gremlin. |
| [Zmień przepływność](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Zaktualizuj RU/s w bazie danych i grafie.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||

## <a name="table-api-samples"></a>Przykłady interfejs API tabel

|Zadanie | Opis |
|---|---|
| [Tworzenie konta i tabeli platformy Azure Cosmos](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB i tabelę dla interfejs API tabel. |
| [Tworzenie konta i tabeli platformy Azure Cosmos z funkcją automatycznego skalowania](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB i tabelę z funkcją automatycznego skalowania dla interfejs API tabel. |
| [Zmień przepływność](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Zaktualizuj RU/s w tabeli.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||
