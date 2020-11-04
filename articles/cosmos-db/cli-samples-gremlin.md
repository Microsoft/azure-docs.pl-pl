---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API Gremlin
description: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API Gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ff557e5ca0769b68cb74f4b790e3678da7c3c7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342117"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-gremlin-api"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

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

## <a name="gremlin-api-samples"></a>Przykłady interfejsu API Gremlin

|Zadanie | Opis |
|---|---|
| [Tworzenie konta, bazy danych i grafu usługi Azure Cosmos](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i Graf dla interfejsu API Gremlin. |
| [Tworzenie konta usługi Azure Cosmos, bazy danych i grafu przy użyciu funkcji automatycznego skalowania](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i Graf z funkcją automatycznego skalowania dla interfejsu API Gremlin. |
| [Operacje przepływności](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Odczytuj, Aktualizuj i Migruj między funkcją automatycznego skalowania i standardową przepływność na bazie danych i grafie.|
| [Zablokuj zasoby przed usunięciem](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów.|
|||
