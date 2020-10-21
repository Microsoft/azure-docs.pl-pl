---
title: Szablony Menedżer zasobów dla Azure Cosmos DB interfejs API Cassandra
description: Utwórz i skonfiguruj Azure Cosmos DB interfejs API Cassandra za pomocą szablonów Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 0ca022d91a43a4edc7619cdd860ee6283dc3b520
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92285026"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejs API Cassandra Azure Cosmos DB przy użyciu szablonów Azure Resource Manager

W tym artykule dowiesz się, jak używać szablonów Azure Resource Manager, aby ułatwić wdrażanie kont Azure Cosmos DB, miejsc i tabel oraz zarządzanie nimi.

W tym artykule przedstawiono przykłady tylko dla kont interfejs API Cassandra, aby znaleźć przykłady dla innych kont typu interfejsu API, zobacz: Używanie szablonów Azure Resource Manager z interfejsem API Azure Cosmos DB dla [SQL](templates-samples-sql.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md), artykuły [tabeli](templates-samples-table.md) .

> [!IMPORTANT]
>
> * Nazwy kont są ograniczone do 44 znaków, wszystkie małe litery.
> * Aby zmienić wartości przepływności, wdróż ponownie szablon z zaktualizowanymi jednostkami RU/s.
> * Po dodaniu lub usunięciu lokalizacji na koncie usługi Azure Cosmos nie można jednocześnie modyfikować innych właściwości. Te operacje należy wykonać oddzielnie.

Aby utworzyć dowolny z poniższych zasobów Azure Cosmos DB, Skopiuj poniższy przykładowy szablon do nowego pliku JSON. Opcjonalnie można utworzyć plik JSON parametrów do użycia podczas wdrażania wielu wystąpień tego samego zasobu z różnymi nazwami i wartościami. Istnieje wiele sposobów wdrażania szablonów Azure Resource Manager, w tym [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) i [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Konto usługi Azure Cosmos dla Cassandra z obsługą funkcji automatycznego skalowania

Ten szablon służy do tworzenia konta usługi Azure Cosmos w dwóch regionach z opcjami spójności i trybu failover przy użyciu przestrzeni kluczy i tabeli skonfigurowanej do automatycznego skalowania przepływności. Ten szablon jest również dostępny dla jednego kliknięcia przycisku Wdróż z galerii szablonów szybkiego startu platformy Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Konto usługi Azure Cosmos dla Cassandra przy użyciu standardowej, alokowanej przepływności

Ten szablon służy do tworzenia konta usługi Azure Cosmos w dwóch regionach z opcjami spójności i trybu failover przy użyciu przestrzeni kluczy i tabeli skonfigurowanej na potrzeby standardowej przepływności. Ten szablon jest również dostępny dla jednego kliknięcia przycisku Wdróż z galerii szablonów szybkiego startu platformy Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

* [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
* [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
